# <Nome del prodotto>, backlog del Product Owner

_Una riga su cosa fa il prodotto e per chi. La leggono tutti gli agent: è il contesto di dominio._

<Descrizione del prodotto in 1-2 frasi: utenti, funzioni principali, settore.>

## Ambiente di lavoro

_Dove gira il sistema, lingua dei report e natura dei dati. Gli agent di analisi leggono da qui lingua e "dati reali o fittizi"._

- Claude Code dal tab Code dell'app Claude Desktop, sessione locale sulla cartella del progetto.
- I tool esterni sono connettori dell'app.
- **Dati: <reali | fittizi (demo)>.**
- Output in <lingua>.
- Non si eliminano file né item: si spostano o si archiviano.

## Regola base

_Chi decide. Non cambiarla senza un motivo forte: è la garanzia che nessun agent scrive da solo sui tool._

Gli agent leggono e propongono. Il Product Owner decide. **Nessuna scrittura su nessun tool senza un OK esplicito, una modifica alla volta.**

## Architettura

_I tre livelli del sistema. Di solito non si cambia._

1. **Adapter** (`./adapters/<tool>.md`): come si legge e si scrive un tool, mappa dei campi e degli stati.
2. **Modello comune** (`./schema/backlog-item.schema.json`): formato unico degli item.
3. **Agent di analisi** (`.claude/agents/`): leggono solo `./backlog/AAAA-MM-GG_HHMM_snapshot.json` (uno per sync, mai sovrascritto). Ogni report cita il file esatto che ha letto.

Aggiungere un tool: `./adapters/README.md`.

## Tool attivi

_Un tool per riga. backlog-sync legge solo i tool elencati qui._

| Tool | Adapter | Modalità oggi | Contiene |
|---|---|---|---|
| <Tool 1, per esempio Jira (chiave <CHIAVE>)> | `adapters/<tool-1>.md` | <connettore | CSV in `backlog/import/`> | <per esempio epiche, story, bug> |
| <Tool 2, per esempio Notion> | `adapters/<tool-2>.md` | <connettore | CSV> | <per esempio opportunità di discovery> |

## Fonte di verità

_Quale tool comanda per ogni tipo di item. Serve a backlog-health per i duplicati e alla sessione principale per le scritture._

| Tipo di item | Tool | Conseguenza |
|---|---|---|
| <opportunity, ricerca, ipotesi, PRD> | <Tool 2> | si modifica solo su <Tool 2> |
| <epic, story, bug, task, spike> | <Tool 1> | si modifica solo su <Tool 1> |

Se lo stesso contenuto esiste in due tool, vince la fonte di verità per quel tipo. L'altro item va collegato o chiuso, mai tenuto allineato a mano.

## Parametri del backlog

_Valori numerici letti dagli agent di analisi. Tieni i nomi della prima colonna esattamente così: gli agent li cercano per nome. Se una riga manca, l'agent la segnala tra i dati mancanti invece di usare un default._

| Parametro | Valore |
|---|---|
| Item fermi | <N> giorni senza aggiornamenti, rispetto alla data dello snapshot |
| Team | <N> sviluppatori, <N> QA |
| Durata dello sprint | <N> settimane |
| Sprint per la velocity | <N> sprint chiusi |

## Collegamento discovery-delivery

_Come si riconosce un item di discovery validato e come si collega all'item di delivery che lo realizza. Usato da backlog-health (duplicati, item validati senza collegamento) e dal prioritizer (eredità). Se il progetto non ha discovery separata, scrivi "Non si usa"._

- Un item di <tool di discovery> è validato quando `stato_originale` = "<valore dello stato>".
- Un item validato deve avere un item di delivery collegato: <dipendenza `realizza` | campo `extra["<nome campo>"]`>.
- Un item di discovery e l'item di delivery che lo realizza non sono duplicati.

## Agent

_Quando si lancia ogni agent. Aggiungi qui anche gli agent di dominio._

| Agent | Quando |
|---|---|
| backlog-sync | per primo, a inizio sessione |
| backlog-health | dopo ogni sync |
| story-refiner | su story indicate per id, prima del refinement |
| prioritizer | prima del planning |
| sprint-prep | 1-2 giorni prima del planning, o a fine sprint |
| <agent di dominio> | <quando> |

## Agent di dominio

_Agent specifici del settore (per esempio compliance, sicurezza, accessibilità). Gli agent generici rimandano a loro. Se non ce ne sono, scrivi "Nessuno"._

| Agent | Quando | Per quali item |
|---|---|---|
| <nome-agent> | <per esempio prima che un item entri in sprint> | <per esempio item che toccano dati personali o pagamenti> |

## Scritture

_Il ciclo proposta, OK, scrittura, change-log. Di solito non si cambia._

- Gli agent scrivono le modifiche proposte in `./output/AAAA-MM-GG_proposte.md`, una riga per modifica: id, tool, campo, valore attuale, valore proposto, motivo.
- Le applica solo la sessione principale. Per ogni riga: controlla la fonte di verità, traduci campo e valore con l'adapter del tool, mostra la chiamata che stai per fare, aspetta l'OK, esegui, annota l'esito in `./output/AAAA-MM-GG_change-log.md`.
- Una modifica alla volta. Un OK vale per una riga, non per le successive.
- Per un tool in modalità CSV non c'è scrittura: la proposta resta nel file e la applica il PO a mano.
- Tutti i tool di scrittura dei connettori sono in "ask" in `.claude/settings.json`. Quelli che eliminano dati sono in "deny": non si elimina niente.

## Formato di output degli agent

_Uguale per tutti gli agent. Non cambiarlo: i report restano confrontabili tra progetti._

1. Sintesi in 3 righe.
2. Problemi: Critico, Medio, Basso, ognuno con l'id.
3. Azioni proposte.
4. Dati mancanti.

## Formato story

_Formato, persone del dominio, formato dei criteri, criteri di qualità e casi limite. Lo usa story-refiner._

"<Formato, per esempio: Come <utente> voglio <azione> per ottenere <beneficio>.>"

L'utente è una persona del dominio: <persona 1>, <persona 2>, <persona 3>. Acceptance criteria in <formato, per esempio Gherkin (Dato, Quando, Allora)>. Criteri <criteri di qualità, per esempio INVEST>.

Casi limite da coprire negli acceptance criteria: <per esempio importi, timeout, stati del pagamento>.

## Scala di stima

_Unità e valori ammessi. backlog-health segnala le stime fuori scala, story-refiner propone lo split oltre il massimo._

<Unità, per esempio story point Fibonacci>: <valori, per esempio 1, 2, 3, 5, 8, 13>. **Oltre <massimo> la story va divisa.** Stima il team, non il PO e non gli agent.

## Prioritizzazione

_Il framework e tutte le sue regole, scritte in modo esplicito. Il prioritizer non ha regole proprie: applica solo quello che c'è qui._

Framework: <nome, per esempio WSJF>.

<Formula, per esempio WSJF = (business value + time criticality + risk reduction) / job size>

- Scala dei valori: <valori>.
- Si calcola su: <tipi di item, per esempio opportunità ed epiche>.
- Item senza punteggio: <per esempio le story, che il PO ordina dentro l'epica>.
- Chi assegna i valori e dove: <per esempio il PO, sul tool di discovery>.
- Campi nello snapshot: <per esempio `wsjf.business_value`, `wsjf.time_criticality`, `wsjf.risk_reduction`, `wsjf.job_size`>.
- Eredità: <per esempio l'item di delivery che realizza un item di discovery ne eredita i valori; i due stanno su una riga sola. Oppure "Nessuna">.
- Dimensione degli item: <per esempio la somma delle stime dei figli non si usa; se la supera di molto, problema Medio>.
- Valori mancanti: se ne manca anche uno, il punteggio non si calcola.
- Parità: <regola, per esempio prima l'item con time criticality più alta>.

## Definition of Ready

_Elenco numerato. sprint-prep segna ogni punto ok o no per ogni item candidato._

Una story è pronta per lo sprint se:

1. <criterio>
2. <criterio>
3. <criterio>

## Definition of Done

_Elenco numerato. Serve al refinement e alla review._

Una story è finita se:

1. <criterio>
2. <criterio>
3. <criterio>

## Limiti noti

_Cosa il sistema non fa o fa con riserva. Aggiornalo quando scopri un limite._

- Una modifica a questo file o a un agent vale per gli agent solo dalla sessione successiva.
- Il frontmatter di un agent non può limitare Write a una cartella. Il vincolo su `./backlog/` e `./output/` sta nelle istruzioni e nei permessi.
- <altri limiti del progetto>

## Glossario di dominio

_Termini del settore, con definizioni operative. Aiuta tutti gli agent a leggere le story. Non sono definizioni legali._

- **<Termine>**: <definizione in una riga>.
