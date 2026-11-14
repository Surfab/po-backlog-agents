# Conto Business, backlog del Product Owner

Sistema di subagent per il lavoro da Product Owner Scrum sul prodotto "Conto Business" (fintech di pagamenti per PMI italiane: apertura conto online, carte aziendali, bonifici istantanei, incasso e riconciliazione delle fatture).

## Ambiente di lavoro

- Claude Code dal tab Code dell'app Claude Desktop, non da terminale.
- **Questa cartella non è un repository git e deve restare così.** Niente `git init`, niente worktree.
- I tool esterni sono connettori dell'app, non server aggiunti con `claude mcp add`.
- **Demo: tutti i dati sono fittizi.** Persone, numeri, ticket e ricerche sono inventati.
- Output in italiano.
- Non si eliminano file né item: si spostano o si archiviano.

## Regola base

Gli agent leggono e propongono. Il Product Owner decide. **Nessuna scrittura su nessun tool senza un OK esplicito, una modifica alla volta.**

## Architettura

1. **Adapter** (`./adapters/<tool>.md`): come si legge e si scrive un tool, mappa dei campi e degli stati.
2. **Modello comune** (`./schema/backlog-item.schema.json`): formato unico degli item.
3. **Agent di analisi** (`.claude/agents/`): leggono solo `./backlog/AAAA-MM-GG_snapshot.json`.

Aggiungere un tool: `./adapters/README.md`.

## Tool attivi

| Tool | Adapter | Modalità oggi | Contiene |
|---|---|---|---|
| Notion | `adapters/notion.md` | connettore | database "Discovery" nella pagina "PAY Product" |
| Jira (spazio PAY, chiave DEMO) | `adapters/jira.md` | connettore (ripiego: CSV in `backlog/import/`) | epiche, story, bug |
| Altri tool | `adapters/csv.md` | CSV in `backlog/import/` | secondo il file |

## Fonte di verità

| Tipo di item | Tool | Conseguenza |
|---|---|---|
| opportunity, ricerca, ipotesi, PRD | Notion | si modifica solo su Notion |
| epic, story, bug, task, spike | Jira | si modifica solo su Jira |

Se lo stesso contenuto esiste in due tool, vince la fonte di verità per quel tipo. L'altro item va collegato o chiuso, mai tenuto allineato a mano.

## Agent

| Agent | Quando |
|---|---|
| backlog-sync | per primo, a inizio sessione |
| backlog-health | dopo ogni sync |
| story-refiner | su story indicate per id, prima del refinement |
| prioritizer | prima del planning |
| fintech-compliance | prima che un item entri in sprint |
| sprint-prep | 1-2 giorni prima del planning, o a fine sprint |

## Scritture

- Gli agent scrivono le modifiche proposte in `./output/AAAA-MM-GG_proposte.md`, una riga per modifica: id, tool, campo, valore attuale, valore proposto, motivo.
- Le applica solo la sessione principale. Per ogni riga: controlla la fonte di verità, traduci campo e valore con l'adapter del tool, mostra la chiamata che stai per fare, aspetta l'OK, esegui, annota l'esito in `./output/AAAA-MM-GG_change-log.md`.
- Una modifica alla volta. Un OK vale per una riga, non per le successive.
- Per un tool in modalità CSV non c'è scrittura: la proposta resta nel file e la applica il PO a mano.
- Tutti i tool di scrittura dei connettori sono in "ask" in `.claude/settings.json`.

## Formato di output degli agent

1. Sintesi in 3 righe.
2. Problemi: Critico, Medio, Basso, ognuno con l'id.
3. Azioni proposte.
4. Dati mancanti.

## Formato story

"Come <utente> voglio <azione> per ottenere <beneficio>."

L'utente è una persona del dominio: titolare di PMI, addetto all'amministrazione, dipendente con carta aziendale, commercialista. Acceptance criteria in Gherkin (Dato, Quando, Allora). Criteri INVEST.

## Scala di stima

Story point Fibonacci: 1, 2, 3, 5, 8, 13. **Oltre 13 la story va divisa.** Stima il team, non il PO e non gli agent.

## WSJF

WSJF = (business value + time criticality + risk reduction) / job size

- I quattro valori usano la scala 1, 2, 3, 5, 8, 13.
- Business value, time criticality e risk reduction li assegna il PO, su Notion per le opportunità.
- Job size delle story: i loro story point.
- Job size di opportunità ed epiche: valore esplicito nel campo "Job Size" dell'opportunità su Notion, assegnato dal PO con il team. L'epica Jira che realizza l'opportunità eredita tutti e quattro i valori. La somma dei figli non si usa: un'epica contiene anche bug e task non stimati, e le story cambiano con gli split.
- Se un valore manca, il punteggio non si calcola.
- Il WSJF si calcola su opportunità ed epiche. Le story dentro un'epica le ordina a mano il PO.

## Definition of Ready

Una story è pronta per lo sprint se:

1. È scritta nel formato story, con utente e beneficio chiari.
2. Ha acceptance criteria in Gherkin, verificabili dal QA.
3. È stimata dal team e non supera 13 punti.
4. È collegata a un'epica.
5. Le dipendenze sono note, collegate e non bloccanti per lo sprint.
6. Gli impatti di compliance e sicurezza sono stati valutati (passaggio da fintech-compliance, e parere di compliance o legal se è emerso un rischio critico).
7. Dati di test e ambienti necessari sono disponibili.
8. Mockup o contratto API disponibili, se servono.

## Definition of Done

Una story è finita se:

1. Tutti gli acceptance criteria sono verificati dal QA.
2. Il codice è revisionato da almeno un altro sviluppatore e integrato.
3. I test automatici passano, compresi quelli nuovi sui casi di errore.
4. Le operazioni sensibili (pagamenti, accessi, modifiche ai dati del cliente) lasciano traccia nell'audit trail.
5. Nessun dato personale o di pagamento finisce nei log applicativi.
6. I controlli di sicurezza previsti sono superati.
7. Documentazione e note di rilascio sono aggiornate.
8. La funzione è rilasciabile (anche dietro feature flag) e il PO l'ha accettata.

## Limiti noti

- Una modifica a questo file o a un agent vale per gli agent solo dalla sessione successiva: nella sessione in corso ricevono la copia letta all'avvio. Dopo una modifica, riapri la sessione o chiedi all'agent di rileggere il file dal disco.
- Il frontmatter di un agent non può limitare Write a una cartella. Il vincolo su `./backlog/` e `./output/` sta nelle istruzioni e nei permessi.
- backlog-sync non legge il corpo delle pagine Notion né i commenti Jira.
- Senza almeno 3 sprint chiusi la velocity non si calcola.
- Creare uno sprint su Jira richiede `executeDestructive` con l'operazione `manageJiraSprint`: è l'unica eccezione ammessa, vedi `adapters/jira.md`.

## Glossario di dominio

Definizioni operative, per capirsi. Non sono definizioni legali.

- **KYC** (Know Your Customer): identificazione e verifica dell'identità di una persona fisica prima di aprire un rapporto.
- **KYB** (Know Your Business): lo stesso per una società. Comprende i dati dell'impresa, i poteri di firma e il titolare effettivo.
- **AML** (Anti-Money Laundering): l'insieme dei controlli contro il riciclaggio. Verifica del cliente, monitoraggio delle operazioni, segnalazione di quelle sospette.
- **SCA** (Strong Customer Authentication): autenticazione forte del cliente, con almeno due fattori tra qualcosa che sa, qualcosa che ha, qualcosa che è. Richiesta per accessi al conto e disposizioni di pagamento, con alcune esenzioni.
- **PSD2**: direttiva europea sui servizi di pagamento. Introduce la SCA e apre i conti a terze parti autorizzate.
  - **AISP** (Account Information Service Provider): terza parte che, con il consenso del cliente, legge saldi e movimenti.
  - **PISP** (Payment Initiation Service Provider): terza parte che, con il consenso del cliente, dispone un pagamento dal suo conto.
- **Bonifico istantaneo SEPA** (SCT Inst): bonifico in euro accreditato in pochi secondi, ogni giorno e a ogni ora. Una volta eseguito non si revoca.
- **Verification of Payee** (VoP): controllo, prima dell'invio, che il nome del beneficiario indicato corrisponda al titolare dell'IBAN. L'esito può essere: corrisponde, simile, non corrisponde, verifica non disponibile.
- **SDD** (SEPA Direct Debit): addebito diretto. Il creditore incassa dal conto del debitore sulla base di un mandato. Esistono lo schema per consumatori (Core) e quello tra imprese (B2B).
- **Chargeback**: contestazione di un pagamento con carta, avviata dal titolare tramite la sua banca, che può portare allo storno dell'importo.
- **IBAN virtuale**: IBAN aggiuntivo collegato a un conto principale. Serve a riconoscere da chi arriva un incasso, ad esempio un IBAN per cliente o per fattura.
- **Riconciliazione**: abbinamento tra i movimenti del conto e i documenti contabili (fatture emesse e ricevute), per sapere cosa è stato pagato e cosa no.
- **SDI** (Sistema di Interscambio): il sistema dell'Agenzia delle Entrate attraverso cui passano le fatture elettroniche in Italia.

Per ogni dubbio normativo: gli agent indicano il rischio e cosa verificare con compliance o legal. Non citano articoli di legge a memoria.
