# PO Backlog Agents

Sei agenti per Claude Code che controllano il backlog di un Product Owner su Notion e Jira. Leggono e propongono, il PO decide.

Six Claude Code agents that review a Product Owner's backlog on Notion and Jira. They read and propose, the PO decides.

**Cosa ha dimostrato la demo**

- Ha trovato errori che nessuno aveva previsto: contraddizioni tra criteri di accettazione introdotte dalle stesse correzioni, e una soglia di 5 secondi nei criteri che nessuno aveva giustificato.
- sprint-prep ha bocciato 5 candidati su 5 sulla Definition of Ready, invece di riempire lo sprint.
- backlog-health ha trovato tutti i 7 difetti piantati nei dati di prova, compreso un duplicato tra Notion e Jira.

Esempi di report reali: [salute del backlog](output/2026-11-13_backlog-health.md) · [preparazione dello sprint](output/2026-11-13_sprint-prep.md) · [impatti di compliance](output/2026-11-12_fintech-compliance.md)

**Provalo in 2 minuti, senza connettori.** Apri la cartella in Claude Code e scrivi: "Lancia backlog-health su `backlog/sample/2026-11-12_snapshot.json`". Poi confronta il report con l'elenco dei difetti in `backlog/sample/README.md`.

Progettato da un Product Owner e costruito con Claude Code in tre giorni.

[🇮🇹 Italiano](#italiano) · [🇬🇧 English](#english)

---

## Italiano

> I nomi di persona e i dati sono inventati.
>
> Il sistema non fornisce consulenza legale.

Un sistema di agenti per Claude Code che aiuta un Product Owner Scrum a tenere in ordine il backlog. Legge i dati da più tool (oggi Notion e Jira), li porta in un formato unico e li fa analizzare da sei agenti specializzati. Gli agenti leggono e propongono. Il Product Owner decide. Nessuna scrittura su nessun tool parte senza un OK esplicito.

È una demo: prodotto, persone, numeri e ticket sono inventati.

**In breve**

- Sei agenti: sincronizzazione, salute del backlog, rifinitura delle story, priorità WSJF, impatti di compliance fintech, preparazione dello sprint.
- Architettura a tre livelli: aggiungere un tool vuol dire scrivere un adapter, senza toccare gli agenti.
- Provato su dati reali: un database Notion e uno spazio Jira Cloud, prima in una sessione cloud e poi sul Mac.
- Ogni modifica proposta finisce in un file. Le applica la sessione principale, dopo il tuo OK, e la registra in un change-log.

---

### Configurazione

Questa copia pubblica non contiene identificativi reali: al loro posto ci sono dei segnaposti. Prima di usare il sistema, sostituiscili con i valori del tuo account.

| Segnaposto | Che cos'è | Dove cambiarlo |
|---|---|---|
| `TUO-SITO.atlassian.net` | il tuo sito Jira Cloud (usato come `cloudId` e negli URL delle issue) | `adapters/jira.md` |
| `mcp__<ID-CONNETTORE-NOTION>__` | prefisso dei tool del connettore Notion nella tua sessione | `.claude/agents/backlog-sync.md` (riga `tools:`), `.claude/settings.json`, `adapters/notion.md` |
| `mcp__<ID-CONNETTORE-ATLASSIAN>__` | prefisso dei tool del connettore Atlassian nella tua sessione | `.claude/agents/backlog-sync.md` (riga `tools:`), `.claude/settings.json`, `adapters/jira.md` |
| `<URL-PAGINA-NOTION>` | URL della pagina Notion che contiene il database | `adapters/notion.md` |
| `<URL-DATABASE-NOTION>` | URL del database "Discovery" | `adapters/notion.md` |
| `collection://<ID-DATA-SOURCE-NOTION>` | identificativo della data source del database | `adapters/notion.md` (sezione "Dove sono i dati" e query standard) |
| `<URL-NOTION-PAYDISC-1>` … `<URL-NOTION-PAYDISC-5>` | URL delle singole pagine delle opportunità | niente da cambiare a mano: sono negli snapshot in `backlog/`, che backlog-sync rigenera |

Note:

- Finché i prefissi dei connettori non sono sostituiti, i permessi in `.claude/settings.json` e i tool di backlog-sync non corrispondono a nessun tool reale. Come trovare il prefisso giusto è spiegato nella sezione 6.
- `TUO-SITO.atlassian.net` compare anche negli snapshot in `backlog/` e in un change-log in `output/`: sono dati di esempio, non configurazione.
- La chiave dello spazio Jira (`DEMO`) e gli id dei campi custom (`customfield_…`) in `adapters/jira.md` valgono per il sito della demo: sul tuo sito vanno verificati.
- Gli snapshot in `backlog/` e i report in `output/` sono l'esito delle sessioni demo e servono come esempio.

---

### Indice

1. [Come si usa](#1-come-si-usa)
2. [Come funziona](#2-come-funziona)
3. [Struttura della cartella](#3-struttura-della-cartella)
4. [I sei agenti](#4-i-sei-agenti)
5. [Le scritture: proposte, OK, change-log](#5-le-scritture-proposte-ok-change-log)
6. [Connettori, nomi dei tool e permessi](#6-connettori-nomi-dei-tool-e-permessi)
7. [I dati demo](#7-i-dati-demo)
8. [Regole di prodotto](#8-regole-di-prodotto)
9. [Aggiungere un tool](#9-aggiungere-un-tool)
10. [Cosa è verificato e cosa no](#10-cosa-è-verificato-e-cosa-no)
11. [Limiti noti](#11-limiti-noti)
12. [Problemi incontrati e come si risolvono](#12-problemi-incontrati-e-come-si-risolvono)
13. [Storia del progetto](#13-storia-del-progetto)
14. [Prossimi passi](#14-prossimi-passi)

---

### 1. Come si usa

#### Prima volta

1. Apri la cartella del progetto in Claude Desktop. Funziona anche su iCloud Drive. Dentro devono esserci `CLAUDE.md`, `README.md`, `adapters/`, `backlog/`, `output/`, `schema/` e la cartella nascosta `.claude/`. Nel Finder le cartelle nascoste si vedono con **Cmd+Maiusc+punto**.
2. Su iCloud: clic destro sulla cartella, "Mantieni scaricato". Se iCloud libera spazio e lascia i file solo online, Claude li trova vuoti.
3. In Claude Desktop, tab **Code**, apri una sessione **locale** su quella cartella. Non una sessione cloud: da cloud la cartella non è raggiungibile.
4. Controlla che i connettori Notion e Atlassian siano collegati (Impostazioni, Connettori).

#### Ogni sessione di lavoro

Scrivi le richieste in linguaggio normale. Esempi:

| Quando | Cosa scrivi |
|---|---|
| Inizio sessione | "Lancia backlog-sync" |
| Subito dopo | "Lancia backlog-health sullo snapshot di oggi" |
| Prima del refinement | "Lancia story-refiner su DEMO-14 e DEMO-15" |
| Prima che una story entri in sprint | "Lancia fintech-compliance su DEMO-5" |
| Prima del planning | "Lancia prioritizer", poi "Lancia sprint-prep su PAY Sprint 1" |
| Per applicare le proposte | "Mostrami le proposte in attesa" e poi "OK per la riga 3" |

L'ordine consigliato è sempre: **sync, poi analisi, poi decisioni**. Gli agenti di analisi leggono solo lo snapshot: se non rifai il sync, lavorano su dati vecchi.

#### Cosa leggere dopo ogni agente

Ogni agente scrive un report in `output/AAAA-MM-GG_<agent>.md`, sempre con lo stesso formato:

1. Sintesi in 3 righe.
2. Problemi divisi in Critico, Medio, Basso, ognuno con l'id dell'item.
3. Azioni proposte.
4. Dati mancanti.

Le modifiche concrete che propone finiscono in `output/AAAA-MM-GG_proposte.md`.

---

### 2. Come funziona

Il sistema ha tre livelli.

```
   Notion          Jira           CSV
     |               |              |
  adapter         adapter        adapter        Livello 1: come si legge ogni tool
     \               |             /
      \              |            /
       backlog-sync (unico agent con accesso ai tool, solo lettura)
                     |
     backlog/AAAA-MM-GG_snapshot.json           Livello 2: formato unico
                     |
   backlog-health  story-refiner  prioritizer   Livello 3: analisi
   fintech-compliance  sprint-prep
                     |
        output/report e output/proposte
                     |
   Sessione principale + OK del PO  ------>  scritture su Notion o Jira
                     |
        output/change-log
```

**Livello 1, adapter.** Un file per tool in `adapters/`. Dice quale connettore usare, quali tool sono di lettura e quali di scrittura, quali query lanciare e come tradurre campi e stati nel formato comune.

**Livello 2, modello comune.** `schema/backlog-item.schema.json` definisce lo snapshot. Ogni item ha gli stessi campi qualunque sia il tool di origine: id nel formato `tool:chiave`, tipo, titolo, descrizione, acceptance criteria, stato normalizzato (todo, in_progress, blocked, done) e stato originale, priorità, stima, valori WSJF, sprint, parent, dipendenze, label, date. Tutto quello che il tool ha in più finisce in `extra`, così non si perde niente. Quello che backlog-sync non sa tradurre finisce in `campi_non_mappati`, invece di essere indovinato.

**Livello 3, agenti di analisi.** Leggono solo lo snapshot. Non sanno da quale tool arrivano i dati, e per questo non vanno toccati quando cambi o aggiungi un tool.

**Fonte di verità.** La discovery (opportunità, ricerche, ipotesi, PRD) vive su Notion. La delivery (epiche, story, bug, task) vive su Jira. Se lo stesso contenuto esiste in due tool, vince la fonte di verità e l'altro item si collega o si chiude, mai si tiene allineato a mano. Il collegamento tra i due mondi è il campo "Epica Jira" sull'opportunità Notion.

---

### 3. Struttura della cartella

```
conto-business/
├── README.md                      questo file
├── CLAUDE.md                      istruzioni di progetto, lette da Claude a ogni avvio
├── .claude/
│   ├── settings.json              permessi: cosa è consentito, cosa chiede conferma, cosa è vietato
│   └── agents/
│       ├── backlog-sync.md
│       ├── backlog-health.md
│       ├── story-refiner.md
│       ├── prioritizer.md
│       ├── fintech-compliance.md
│       └── sprint-prep.md
├── adapters/
│   ├── README.md                  come aggiungere un tool in 5 passi
│   ├── notion.md
│   ├── jira.md
│   └── csv.md
├── schema/
│   └── backlog-item.schema.json   formato dello snapshot
├── backlog/
│   ├── AAAA-MM-GG_snapshot.json   snapshot normalizzati, uno per giorno
│   ├── import/
│   │   └── jira_PAY.csv           export di esempio, usato solo come ripiego
│   └── sample/
│       ├── 2026-11-12_snapshot.json   10 item fittizi con difetti piantati
│       └── README.md                  elenco dei difetti, per provare gli agent
└── output/
    ├── AAAA-MM-GG_<agent>.md      report degli agent
    ├── AAAA-MM-GG_proposte.md     modifiche proposte, con colonna "stato"
    └── AAAA-MM-GG_change-log.md   modifiche applicate
```

Claude non crea repository git né worktree: `settings.json` vieta `git init` e `git worktree`. La cartella di lavoro originale non usa git; questo repository GitHub ne è una copia per la pubblicazione. Se cloni il repository, commit e push restano una tua decisione.

---

### 4. I sei agenti

Tutti hanno `model: inherit`, cioè usano il modello della sessione.

| Agente | Quando | Cosa fa | Tool |
|---|---|---|---|
| **backlog-sync** | A inizio sessione | Legge ogni tool attivo in sola lettura, normalizza e scrive lo snapshot. Se un connettore non risponde ripiega sul CSV. Dichiara i campi che non sa mappare | Read, Glob, Write, 3 tool Notion di lettura, 2 tool Jira di lettura |
| **backlog-health** | Dopo ogni sync | Duplicati anche tra tool, item fermi da oltre 4 settimane, story senza criteri o stima, stime fuori scala, dipendenze rotte, epiche vuote, item senza epica, opportunità validate senza epica, item bloccati senza motivo, contraddizioni tra criteri | Read, Grep, Glob, Write |
| **story-refiner** | Prima del refinement, su id indicati | Riscrive la story nel formato "Come… voglio… per…", verifica INVEST, scrive i criteri in Gherkin, propone uno split se supera 13 punti o non è testabile | Read, Grep, Glob, Write |
| **prioritizer** | Prima del planning | Calcola il WSJF su opportunità ed epiche, mostra calcolo e razionale, rifiuta di calcolare se manca un valore | Read, Grep, Glob, Write |
| **fintech-compliance** | Prima che un item entri in sprint | Segnala impatti su PSD2 e SCA, Verification of Payee, AML, KYC e KYB, GDPR, DORA, carte, SDD, SDI, audit trail. Non cita articoli di legge: indica il rischio e con chi verificarlo | Read, Grep, Glob, Write |
| **sprint-prep** | 1-2 giorni prima del planning | Bozza di sprint goal, verifica della Definition of Ready sui candidati, capacità rispetto alla velocity degli ultimi 3 sprint, scalette di review e retrospettiva | Read, Grep, Glob, Write |

Solo backlog-sync tocca i connettori, e solo in lettura. Gli altri cinque lavorano sui file.

Gli agenti scrivono solo in `backlog/` (backlog-sync) e in `output/` (tutti gli altri). Il frontmatter di un agente non può limitare la scrittura a una cartella: il vincolo sta nelle istruzioni di ogni agente e nei permessi.

---

### 5. Le scritture: proposte, OK, change-log

Il ciclo di una modifica:

1. Un agente scrive una riga in `output/AAAA-MM-GG_proposte.md`:
   `| id | tool | campo | valore attuale | valore proposto | motivo | stato |`
   con stato "in attesa".
2. Chiedi alla sessione principale di applicarla.
3. La sessione principale controlla la fonte di verità, traduce campo e valore con l'adapter, **ti mostra la chiamata esatta** e aspetta il tuo OK.
4. Esegue, rilegge l'item dal tool per verificare, annota l'esito in `output/AAAA-MM-GG_change-log.md` e segna la riga come applicata.

Regole:

- Un OK vale per una riga, non per le successive. Puoi dare un OK cumulativo, ma va detto in modo esplicito ("OK per le righe 2, 3 e 4").
- Per un tool in modalità CSV non c'è scrittura: la proposta resta nel file e la applichi tu a mano.
- Non si elimina niente, né file né item. Si archivia, si chiude o si sposta.
- Quando `editJiraIssue` modifica una descrizione, la riscrive per intero. La sessione principale deve rileggere la descrizione attuale da Jira prima di preparare la chiamata, non usare il testo dello snapshot, che è normalizzato.

---

### 6. Connettori, nomi dei tool e permessi

#### Il prefisso dei tool cambia con l'ambiente

Lo stesso tool ha un nome diverso a seconda di dove gira la sessione:

| Ambiente | Notion | Atlassian |
|---|---|---|
| Claude Desktop, sessione locale (quello in uso) | `mcp__<ID-CONNETTORE-NOTION>__` | `mcp__<ID-CONNETTORE-ATLASSIAN>__` |
| Sessione cloud | `mcp__Notion__` | `mcp__Atlassian_MCP__` |

Il codice nel prefisso desktop è l'identificativo del connettore installato sul tuo account. Resta stabile finché non scolleghi e ricolleghi il connettore. **Se ricolleghi un connettore, controlla subito i prefissi** e aggiornali in questi quattro file:

- `.claude/agents/backlog-sync.md` (riga `tools:`)
- `.claude/settings.json`
- `adapters/notion.md`
- `adapters/jira.md`

#### Tool usati

| Connettore | Lettura (solo backlog-sync) | Scrittura (solo sessione principale, sempre con OK) |
|---|---|---|
| Notion | `notion-query-data-sources`, `notion-fetch`, `notion-search` | `notion-update-page`, `notion-create-pages`, `notion-update-data-source` e gli altri elencati in `settings.json` |
| Atlassian | `searchJiraIssuesUsingJql`, `getJiraIssue` | `editJiraIssue`, `createJiraIssue`, `transitionJiraIssue`, `addOrEditJiraIssueComment`, `executeWrite` (per esempio `createJiraIssueLink`) |

`executeDestructive` è vietato, con una sola eccezione: l'operazione `manageJiraSprint` per creare uno sprint o aggiungervi issue. Il connettore la classifica come distruttiva perché la stessa operazione chiude anche gli sprint, cosa irreversibile.

#### Permessi in `.claude/settings.json`

- **allow:** lettura di file e i 5 tool di lettura dei connettori.
- **ask:** tutti i tool di scrittura di Notion (38) e di Atlassian (9). Claude chiede conferma prima di ognuno.
- **deny:** `git init` e `git worktree`.

---

### 7. I dati demo

#### Scenario

Fintech di pagamenti per PMI italiane. Prodotto "Conto Business": apertura conto online, carte aziendali, bonifici istantanei, incasso e riconciliazione delle fatture. Team di 6 sviluppatori e 1 QA, sprint di 2 settimane.

#### Notion

Pagina **PAY Product**, database **Discovery**. Proprietà: Nome, Tipo, Stato, Priorità, Area, Problema, Evidenze, Business Value, Time Criticality, Risk Reduction, Job Size, Epica Jira, Opp ID, Aggiornato il.

| Id | Opportunità | Stato | BV | TC | RR | Job Size | Epica Jira |
|---|---|---|---|---|---|---|---|
| PAYDISC-1 | Verifica del beneficiario prima del bonifico istantaneo | Validata | 13 | 13 | 8 | 8 | DEMO-2 |
| PAYDISC-2 | Riconciliazione automatica fatture SDI con gli incassi | Validata | 8 | 5 | 3 | 13 | DEMO-9 |
| PAYDISC-3 | Onboarding KYB in meno di 10 minuti | In ricerca | 13 | 5 | 8 | 13 | DEMO-3 |
| PAYDISC-4 | Limiti di spesa per singola carta aziendale | Da esplorare | | | | | |
| PAYDISC-5 | Incasso ricorrente con SDD per abbonamenti B2B | In attesa | 5 | 3 | 2 | 5 | |

#### Jira

Sito `TUO-SITO.atlassian.net`. Spazio di nome **PAY**, ma con **chiave DEMO**: le issue si chiamano DEMO-n. Board "DEMO board" (id 1). Spazio team-managed, stati in italiano: Da completare, In corso, In revisione, Completata.

| Chiave | Tipo | Titolo | Epica | Note |
|---|---|---|---|---|
| DEMO-1 | Task | Demo | | Item di prova, completato |
| DEMO-2 | Epic | Verification of Payee sui bonifici istantanei | | In corso |
| DEMO-3 | Epic | Onboarding KYB digitale | | |
| DEMO-4 | Epic | Carte virtuali usa e getta | | Vuota, senza opportunità di origine |
| DEMO-5 | Story | Mostrare il nome del titolare dell'IBAN prima della conferma | DEMO-2 | In corso, 5 punti, in sprint. Sviluppo dietro feature flag in attesa del parere |
| DEMO-6 | Story | Caricare online i documenti societari | DEMO-3 | In sprint, senza stima |
| DEMO-7 | Bug | Bonifico istantaneo resta in stato pending dopo il timeout | DEMO-2 | 3 punti, in sprint, flag Impediment senza causa |
| DEMO-8 | Story | Abbinare in automatico un incasso alla fattura corrispondente (caso esatto) | DEMO-9 | 5 punti, in sprint |
| DEMO-9 | Epic | Riconciliazione automatica fatture SDI con gli incassi | | Contiene le regole di riconciliazione |
| DEMO-10 | Task | Parere di compliance, DPO e legal sulla verifica del beneficiario | DEMO-2 | 2 punti, in sprint |
| DEMO-11 | Story | Dati dell'impresa dalla visura | DEMO-3 | |
| DEMO-12 | Story | Dichiarare il titolare effettivo | DEMO-3 | Bloccata da DEMO-11 |
| DEMO-13 | Story | Invio della pratica KYB, verifica ed esito | DEMO-3 | Bloccata da DEMO-6, 11, 12 |
| DEMO-14 | Story | Abbinare a mano gli incassi non abbinati | DEMO-9 | Collegata a DEMO-15 |
| DEMO-15 | Story | Registrare gli incassi parziali | DEMO-9 | |
| DEMO-16 | Story | Abbinare un incasso a più fatture | DEMO-9 | Bloccata da DEMO-8. Al rilascio sostituisce il criterio 3 di DEMO-8 |

Sprint **PAY Sprint 1**, dal 16 al 27 novembre 2026, non ancora avviato: DEMO-5, 6, 7, 8, 10.

#### Campi Jira mappati

| Campo | Id su questo sito |
|---|---|
| Story point estimate | `customfield_10016` |
| Sprint | `customfield_10020` |
| Flagged (Impediment) | `customfield_10021` |
| Team | `customfield_10001` |
| Data di inizio | `customfield_10015` |

Gli acceptance criteria non hanno un campo: stanno nella descrizione, sotto il titolo `## Acceptance criteria`. I criteri ancora in attesa di un parere stanno sotto `## Criteri proposti, in attesa del parere di compliance` e lo snapshot li tiene separati.

#### Valori inventati

Alcuni numeri non vengono da un team o da un PO reale. Li ho inseriti io per far girare la demo, e sono dichiarati in `output/2026-11-13_change-log.md`:

- Job Size di PAYDISC-1, 2, 3, 5 e Risk Reduction di PAYDISC-3.
- Story point di DEMO-7, 8, 10.

Gli agenti lo sanno e lo segnalano come violazione della regola "stima il team".

#### Dati di prova offline

`backlog/sample/` contiene 10 item fittizi con difetti piantati apposta (un duplicato tra tool, una story da 21 punti, un'opportunità validata senza epica, un item fermo da 6 settimane, un'epica vuota). Servono a provare gli agenti senza connettori. L'elenco dei difetti è in `backlog/sample/README.md`.

---

### 8. Regole di prodotto

Stanno in `CLAUDE.md` e valgono per tutti gli agenti:

- **Formato story:** "Come <utente> voglio <azione> per ottenere <beneficio>", con un utente del dominio (titolare di PMI, addetto all'amministrazione, dipendente con carta, commercialista). Criteri in Gherkin. Criteri INVEST.
- **Stima:** story point Fibonacci 1, 2, 3, 5, 8, 13. Oltre 13 la story si divide. Stima il team, non il PO né gli agenti.
- **WSJF:** (business value + time criticality + risk reduction) / job size, tutto sulla scala 1-13. Si calcola su opportunità ed epiche. Il job size è il valore esplicito "Job Size" dell'opportunità su Notion, non la somma dei figli. L'epica collegata eredita i quattro valori. Le story dentro un'epica le ordina a mano il PO. Se manca un valore, il punteggio non si calcola.
- **Definition of Ready** e **Definition of Done:** 8 punti ciascuna, con attenzione a compliance, audit trail e dati personali fuori dai log.
- **Glossario:** KYC, KYB, AML, SCA, PSD2 (AISP e PISP), bonifico istantaneo SEPA, Verification of Payee, SDD, chargeback, IBAN virtuale, riconciliazione, SDI.

#### Regole di riconciliazione (in DEMO-9)

Scritte una volta sola nell'epica, e le story ci fanno riferimento:

- Una fattura ha un totale e un residuo. Stati: da incassare, incassata in parte, incassata.
- Incasso uguale al residuo: fattura incassata. Inferiore: incassata in parte. Superiore: nessun abbinamento, l'eccedenza è indicata.
- Annullamento o storno: il residuo risale e lo stato si ricalcola.
- Fattura candidata: fattura aperta il cui numero è nella causale dell'incasso.
- Abbinamento automatico solo per il caso esatto (e, dopo DEMO-16, per più fatture con somma esatta). Ogni altro abbinamento, compresi i parziali, lo fa l'addetto.

#### Classifica WSJF con i dati attuali

| # | Opportunità ed epica | WSJF |
|---|---|---|
| 1 | Verifica del beneficiario (PAYDISC-1 / DEMO-2) | 4,3 |
| 2 | Onboarding KYB (PAYDISC-3 / DEMO-3) | 2,0 |
| 3 | Incasso ricorrente SDD (PAYDISC-5) | 2,0 |
| 4 | Riconciliazione fatture (PAYDISC-2 / DEMO-9) | 1,2 |

Non calcolabili: PAYDISC-4 (nessun valore) e DEMO-4 (nessuna opportunità di origine). La classifica dimostra che il meccanismo funziona, non che l'ordine sia giusto: i job size sono inventati.

---

### 9. Aggiungere un tool

La procedura completa è in `adapters/README.md`. In sintesi:

1. Collega il connettore e chiedi a Claude l'elenco esatto dei tool, divisi in lettura e scrittura. Senza connettore: esporta un CSV in `backlog/import/<tool>_<progetto>.csv`.
2. Leggi 2-3 item reali per scoprire nomi dei campi, id dei campi custom e valori degli stati.
3. Scrivi `adapters/<tool>.md` copiando la struttura di `notion.md`.
4. Aggiungi i tool di lettura a backlog-sync, quelli di scrittura in "ask" in `settings.json`, il tool in `CLAUDE.md`.
5. Lancia backlog-sync e controlla lo snapshot, poi backlog-health.

Gli agenti di analisi non si toccano.

---

### 10. Cosa è verificato e cosa no

**Verificato su dati reali**

- Lettura via connettore di Notion (5 item) e Jira (16 item), sul Mac, con snapshot valido rispetto allo schema.
- Ripiego su CSV quando il connettore Jira non era raggiungibile.
- Tutti e sei gli agenti, ognuno con il suo frontmatter.
- Mappa dei campi Jira: story point, sprint, flag, parent, link tra issue, date.
- Scrittura con OK e rilettura: creazione di issue, modifica di descrizioni e stime, cambi di stato, sprint, link tra issue, commenti, aggiornamento di proprietà Notion.
- backlog-health ha trovato tutti i difetti piantati e, in più, contraddizioni tra criteri introdotte durante le correzioni.

**Non verificato**

- Il calcolo della velocity: non esiste nessuno sprint chiuso.
- Il passaggio di una story completa attraverso la Definition of Done.
- La lettura del corpo delle pagine Notion e dei commenti Jira: backlog-sync non lo fa.
- La scrittura su un tool in modalità CSV: per definizione non c'è.

---

### 11. Limiti noti

- Le modifiche a `CLAUDE.md` e agli agenti valgono dalla sessione successiva: in quella in corso gli agenti ricevono la copia letta all'avvio.
- La velocity si calcola solo con almeno 3 sprint chiusi.
- La priorità Jira esiste ma non compare nella schermata di creazione: ogni issue nasce "Medium" e il valore non dice nulla.
- `generato_il` nello snapshot usa un'ora convenzionale (23:59:59Z) quando l'ora reale non è nota.
- Il collegamento Notion-Jira è un campo di testo ("Epica Jira"): niente garantisce che la chiave esista. backlog-health lo controlla.
- "Aggiornato il" su Notion cambia a ogni modifica, anche minima: un item può sembrare vivo per un refuso corretto.
- DEMO-1 è incluso nello snapshot come item di prova, già completato.

---

### 12. Problemi incontrati e come si risolvono

| Sintomo | Causa | Soluzione |
|---|---|---|
| backlog-sync non trova i tool dei connettori | Prefisso diverso tra cloud e desktop | Aggiornare i prefissi nei 4 file elencati nella sezione 6 |
| Gli agenti non compaiono tra quelli disponibili | Sessione aperta sulla cartella sbagliata, per esempio una `conto-business` dentro un'altra | Aprire la sessione sulla cartella che contiene `CLAUDE.md` |
| Mancano agenti e permessi dopo aver copiato i file | La cartella `.claude` è nascosta ed è rimasta indietro | Cmd+Maiusc+punto nel Finder e copiarla |
| Una sessione cloud non vede la cartella sul Mac | Le cartelle su iCloud Drive (sotto `~/Library`) non si collegano a una sessione cloud | Usare una sessione locale |
| Tutti i connettori danno errore 503 o timeout insieme | Disservizio temporaneo del servizio che instrada le chiamate | Aspettare e riprovare. Non scollegare il connettore: cambierebbe il prefisso |
| "This connector can't be used with the current credential" | Visto durante lo stesso disservizio | Riaprire la sessione dopo che il servizio è tornato |
| Le regole nuove di `CLAUDE.md` non vengono applicate | Gli agenti leggono la copia di inizio sessione | Riaprire la sessione, oppure chiedere all'agente di rileggere il file dal disco |
| Correggere un criterio ne rompe un altro | Regole sparse in più story | Scrivere la regola una volta nell'epica e far riferire lì le story |

---

### 13. Storia del progetto

**12 novembre 2026, sessione cloud**

- Verificati i connettori: Notion raggiungibile, Atlassian non collegato.
- Creati su Notion la pagina PAY Product e il database Discovery con 5 opportunità.
- Scritti schema, adapter, i sei agenti, `CLAUDE.md`, `settings.json` e i 10 item di esempio.
- Primo sync: Notion via connettore, Jira via CSV.
- Collegato Atlassian a un sito Jira Cloud gratuito. Adapter Jira riscritto sui campi reali.
- Create 7 issue su Jira, poi stime, stati, flag. backlog-health ha trovato tutti i difetti piantati.
- Creata l'epica DEMO-9 per la riconciliazione e risolto il duplicato tra Notion e Jira.
- story-refiner su DEMO-6 e DEMO-8, fintech-compliance su DEMO-5 e DEMO-7.

**13 novembre 2026**

- Criteri di compliance su DEMO-5 e task di parere DEMO-10.
- Nuova regola WSJF con Job Size esplicito su Notion.
- Split applicati: DEMO-6 e DEMO-8 ristrette, create DEMO-11 fino a DEMO-16.
- Creato PAY Sprint 1. Prioritizer ha prodotto la prima classifica, sprint-prep ha bocciato tutti i candidati sulla Definition of Ready.
- Sistema portato sul Mac. Corretti i prefissi dei tool, primo sync locale riuscito.
- Creati 5 link di dipendenza tra issue. Tre giri di backlog-health sulla riconciliazione, fino a zero contraddizioni.

**14 novembre 2026**

- Regole di riconciliazione completate in DEMO-9 (fattura candidata, abbinamento automatico) e criterio di storno corretto in DEMO-8.

---

### 14. Prossimi passi

Aperti di proposito, per il planning:

- DEMO-6 in sprint senza stima.
- DEMO-7 in sprint, bloccato senza causa.
- 10 proposte in attesa e 7 lacune nei criteri, per il refinement con il team.

Per usarlo davvero, servono:

1. Stime fatte dal team, al posto di quelle inventate.
2. Almeno tre sprint chiusi, per la velocity.
3. Il parere di compliance su DEMO-5 e DEMO-10.

Miglioramenti utili al sistema:

- Una validazione automatica dello snapshot dopo ogni sync.
- Una skill `/applica-proposte` per il ciclo proposta, OK, scrittura, change-log, oggi descritto solo in `CLAUDE.md`.
- Confronto tra due snapshot, per vedere cosa è cambiato tra un refinement e l'altro.

---

## English

> All names of people and all data are fictional.
>
> The system does not provide legal advice.

**In short**

- A set of Claude Code subagents that helps a Scrum Product Owner keep a backlog healthy.
- It reads Notion and Jira through connectors, normalizes every item into one JSON snapshot, and runs six specialized agents on it.
- The agents cover sync, backlog health, story refinement, WSJF prioritization, fintech compliance impacts and sprint preparation.
- Agents only read and propose: every write to a tool needs an explicit OK from the Product Owner and is recorded in a change-log.
- Demo project: product, people, numbers and tickets are fictional.

**Language note.** The agents, `CLAUDE.md`, the adapters and all agent output are written in Italian. So are the demo data: Notion properties, Jira statuses and issue titles. In the tables below, titles are translated; on Notion and Jira they are in Italian. File and field names are left as they are, for example `proposte.md` (proposals) and `campi_non_mappati` (unmapped fields).

An agent system for Claude Code that helps a Scrum Product Owner keep the backlog in order. It reads data from several tools (Notion and Jira today), brings them into a single format and has six specialized agents analyze them. The agents read and propose. The Product Owner decides. No write to any tool happens without an explicit OK.

It is a demo: product, people, numbers and tickets are fictional.

**At a glance**

- Six agents: sync, backlog health, story refinement, WSJF prioritization, fintech compliance impacts, sprint preparation.
- Three-layer architecture: adding a tool means writing an adapter, without touching the agents.
- Tested on real tools: a Notion database and a Jira Cloud space, first in a cloud session and then on the Mac.
- Every proposed change goes into a file. The main session applies it after your OK and records it in a change-log.

---

### Setup

This public copy contains no real identifiers: placeholders take their place. Before using the system, replace them with the values from your own account.

| Placeholder | What it is | Where to change it |
|---|---|---|
| `TUO-SITO.atlassian.net` | your Jira Cloud site (used as `cloudId` and in issue URLs) | `adapters/jira.md` |
| `mcp__<ID-CONNETTORE-NOTION>__` | tool prefix of the Notion connector in your session | `.claude/agents/backlog-sync.md` (`tools:` line), `.claude/settings.json`, `adapters/notion.md` |
| `mcp__<ID-CONNETTORE-ATLASSIAN>__` | tool prefix of the Atlassian connector in your session | `.claude/agents/backlog-sync.md` (`tools:` line), `.claude/settings.json`, `adapters/jira.md` |
| `<URL-PAGINA-NOTION>` | URL of the Notion page that holds the database | `adapters/notion.md` |
| `<URL-DATABASE-NOTION>` | URL of the "Discovery" database | `adapters/notion.md` |
| `collection://<ID-DATA-SOURCE-NOTION>` | identifier of the database's data source | `adapters/notion.md` ("Dove sono i dati" section and standard query) |
| `<URL-NOTION-PAYDISC-1>` … `<URL-NOTION-PAYDISC-5>` | URLs of the single opportunity pages | nothing to change by hand: they live in the snapshots in `backlog/`, which backlog-sync regenerates |

Notes:

- Until the connector prefixes are replaced, the permissions in `.claude/settings.json` and backlog-sync's tools match no real tool. Section 6 explains how to find the right prefix.
- `TUO-SITO.atlassian.net` also appears in the snapshots in `backlog/` and in a change-log in `output/`: they are sample data, not configuration.
- The Jira space key (`DEMO`) and the custom field ids (`customfield_…`) in `adapters/jira.md` apply to the demo site: check them on your own site.
- The snapshots in `backlog/` and the reports in `output/` are the results of the demo sessions and serve as examples.

---

### Contents

1. [How to use it](#1-how-to-use-it)
2. [How it works](#2-how-it-works)
3. [Folder structure](#3-folder-structure)
4. [The six agents](#4-the-six-agents)
5. [Writes: proposals, OK, change-log](#5-writes-proposals-ok-change-log)
6. [Connectors, tool names and permissions](#6-connectors-tool-names-and-permissions)
7. [Demo data](#7-demo-data)
8. [Product rules](#8-product-rules)
9. [Adding a tool](#9-adding-a-tool)
10. [What is verified and what is not](#10-what-is-verified-and-what-is-not)
11. [Known limits](#11-known-limits)
12. [Problems found and how to fix them](#12-problems-found-and-how-to-fix-them)
13. [Project history](#13-project-history)
14. [Next steps](#14-next-steps)

---

### 1. How to use it

#### First time

1. Open the project folder in Claude Desktop. It also works on iCloud Drive. It must contain `CLAUDE.md`, `README.md`, `adapters/`, `backlog/`, `output/`, `schema/` and the hidden `.claude/` folder. In Finder, hidden folders show up with **Cmd+Shift+period**.
2. On iCloud: right-click the folder, "Keep Downloaded". If iCloud frees up space and leaves the files online only, Claude finds them empty.
3. In Claude Desktop, **Code** tab, open a **local** session on that folder. Not a cloud session: the folder cannot be reached from the cloud.
4. Check that the Notion and Atlassian connectors are connected (Settings, Connectors).

#### Every working session

Write your requests in plain language. The agents expect Italian, for example:

| When | What you write | Meaning |
|---|---|---|
| Start of session | "Lancia backlog-sync" | Run backlog-sync |
| Right after | "Lancia backlog-health sullo snapshot di oggi" | Run backlog-health on today's snapshot |
| Before refinement | "Lancia story-refiner su DEMO-14 e DEMO-15" | Run story-refiner on DEMO-14 and DEMO-15 |
| Before a story enters a sprint | "Lancia fintech-compliance su DEMO-5" | Run fintech-compliance on DEMO-5 |
| Before planning | "Lancia prioritizer", then "Lancia sprint-prep su PAY Sprint 1" | Run prioritizer, then sprint-prep on PAY Sprint 1 |
| To apply proposals | "Mostrami le proposte in attesa", then "OK per la riga 3" | Show pending proposals, then OK for row 3 |

The recommended order is always: **sync, then analysis, then decisions**. The analysis agents read only the snapshot: if you skip the sync, they work on old data.

#### What to read after each agent

Each agent writes a report to `output/AAAA-MM-GG_<agent>.md` (AAAA-MM-GG = YYYY-MM-DD), always in the same format:

1. Summary in 3 lines.
2. Problems grouped as Critical, Medium, Low, each with the item id.
3. Proposed actions.
4. Missing data.

The concrete changes it proposes go into `output/AAAA-MM-GG_proposte.md`.

---

### 2. How it works

The system has three layers.

```
   Notion          Jira           CSV
     |               |              |
  adapter         adapter        adapter        Layer 1: how each tool is read
     \               |             /
      \              |            /
       backlog-sync (the only agent with tool access, read-only)
                     |
     backlog/AAAA-MM-GG_snapshot.json           Layer 2: single format
                     |
   backlog-health  story-refiner  prioritizer   Layer 3: analysis
   fintech-compliance  sprint-prep
                     |
        output/reports and output/proposals
                     |
   Main session + PO's OK  ------>  writes to Notion or Jira
                     |
        output/change-log
```

**Layer 1, adapters.** One file per tool in `adapters/`. It says which connector to use, which tools read and which write, which queries to run and how to translate fields and statuses into the common format.

**Layer 2, common model.** `schema/backlog-item.schema.json` defines the snapshot. Every item has the same fields whatever its source tool: id in the `tool:key` format, type, title, description, acceptance criteria, normalized status (todo, in_progress, blocked, done) and original status, priority, estimate, WSJF values, sprint, parent, dependencies, labels, dates. Anything extra the tool has goes into `extra`, so nothing is lost. Whatever backlog-sync cannot translate goes into `campi_non_mappati`, instead of being guessed.

**Layer 3, analysis agents.** They read only the snapshot. They do not know which tool the data come from, so they do not need to change when you change or add a tool.

**Source of truth.** Discovery (opportunities, research, hypotheses, PRDs) lives on Notion. Delivery (epics, stories, bugs, tasks) lives on Jira. If the same content exists in two tools, the source of truth wins and the other item is linked or closed, never kept in sync by hand. The bridge between the two worlds is the "Epica Jira" (Jira epic) field on the Notion opportunity.

---

### 3. Folder structure

```
conto-business/
├── README.md                      this file
├── CLAUDE.md                      project instructions, read by Claude at every start
├── .claude/
│   ├── settings.json              permissions: what is allowed, what asks first, what is denied
│   └── agents/
│       ├── backlog-sync.md
│       ├── backlog-health.md
│       ├── story-refiner.md
│       ├── prioritizer.md
│       ├── fintech-compliance.md
│       └── sprint-prep.md
├── adapters/
│   ├── README.md                  how to add a tool in 5 steps
│   ├── notion.md
│   ├── jira.md
│   └── csv.md
├── schema/
│   └── backlog-item.schema.json   snapshot format
├── backlog/
│   ├── AAAA-MM-GG_snapshot.json   normalized snapshots, one per day
│   ├── import/
│   │   └── jira_PAY.csv           sample export, used only as a fallback
│   └── sample/
│       ├── 2026-11-12_snapshot.json   10 fictional items with planted defects
│       └── README.md                  list of the defects, to test the agents
└── output/
    ├── AAAA-MM-GG_<agent>.md      agent reports
    ├── AAAA-MM-GG_proposte.md     proposed changes, with a "stato" (status) column
    └── AAAA-MM-GG_change-log.md   applied changes
```

Claude does not create git repositories or worktrees: `settings.json` denies `git init` and `git worktree`. The original working folder does not use git; this GitHub repository is a copy made for publishing. If you clone it, commits and pushes stay your decision.

---

### 4. The six agents

All of them have `model: inherit`, so they use the session's model.

| Agent | When | What it does | Tools |
|---|---|---|---|
| **backlog-sync** | At the start of a session | Reads every active tool read-only, normalizes and writes the snapshot. If a connector does not answer, falls back to CSV. Declares the fields it cannot map | Read, Glob, Write, 3 Notion read tools, 2 Jira read tools |
| **backlog-health** | After every sync | Duplicates, also across tools; items idle for over 4 weeks; stories without criteria or estimate; estimates off the scale; broken dependencies; empty epics; items without an epic; validated opportunities without an epic; items blocked without a reason; contradictions between criteria | Read, Grep, Glob, Write |
| **story-refiner** | Before refinement, on given ids | Rewrites the story as "As a… I want… so that…", checks INVEST, writes the criteria in Gherkin, proposes a split if it exceeds 13 points or is not testable | Read, Grep, Glob, Write |
| **prioritizer** | Before planning | Computes WSJF on opportunities and epics, shows the calculation and the rationale, refuses to compute if a value is missing | Read, Grep, Glob, Write |
| **fintech-compliance** | Before an item enters a sprint | Flags impacts on PSD2 and SCA, Verification of Payee, AML, KYC and KYB, GDPR, DORA, cards, SDD, SDI, audit trail. Does not quote articles of law: it states the risk and who to check it with | Read, Grep, Glob, Write |
| **sprint-prep** | 1-2 days before planning | Draft sprint goal, Definition of Ready check on the candidates, capacity against the velocity of the last 3 sprints, review and retrospective agendas | Read, Grep, Glob, Write |

Only backlog-sync touches the connectors, and only to read. The other five work on files.

The agents write only to `backlog/` (backlog-sync) and to `output/` (all the others). An agent's frontmatter cannot restrict writing to a folder: the constraint lives in each agent's instructions and in the permissions.

---

### 5. Writes: proposals, OK, change-log

The life of a change:

1. An agent writes a row to `output/AAAA-MM-GG_proposte.md`:
   `| id | tool | field | current value | proposed value | reason | status |`
   with status "in attesa" (pending).
2. You ask the main session to apply it.
3. The main session checks the source of truth, translates field and value with the adapter, **shows you the exact call** and waits for your OK.
4. It runs the call, reads the item back from the tool to verify it, logs the outcome in `output/AAAA-MM-GG_change-log.md` and marks the row as applied.

Rules:

- An OK covers one row, not the following ones. You can give a combined OK, but it must be explicit ("OK for rows 2, 3 and 4").
- For a tool in CSV mode there is no write: the proposal stays in the file and you apply it by hand.
- Nothing is deleted, neither files nor items. Things are archived, closed or moved.
- When `editJiraIssue` changes a description, it rewrites it in full. The main session must read the current description from Jira before preparing the call, not use the snapshot text, which is normalized.

---

### 6. Connectors, tool names and permissions

#### The tool prefix changes with the environment

The same tool has a different name depending on where the session runs:

| Environment | Notion | Atlassian |
|---|---|---|
| Claude Desktop, local session (the one in use) | `mcp__<ID-CONNETTORE-NOTION>__` | `mcp__<ID-CONNETTORE-ATLASSIAN>__` |
| Cloud session | `mcp__Notion__` | `mcp__Atlassian_MCP__` |

The code in the desktop prefix is the identifier of the connector installed on your account. It stays stable until you disconnect and reconnect the connector. **If you reconnect a connector, check the prefixes right away** and update them in these four files:

- `.claude/agents/backlog-sync.md` (`tools:` line)
- `.claude/settings.json`
- `adapters/notion.md`
- `adapters/jira.md`

#### Tools used

| Connector | Read (backlog-sync only) | Write (main session only, always with an OK) |
|---|---|---|
| Notion | `notion-query-data-sources`, `notion-fetch`, `notion-search` | `notion-update-page`, `notion-create-pages`, `notion-update-data-source` and the others listed in `settings.json` |
| Atlassian | `searchJiraIssuesUsingJql`, `getJiraIssue` | `editJiraIssue`, `createJiraIssue`, `transitionJiraIssue`, `addOrEditJiraIssueComment`, `executeWrite` (for example `createJiraIssueLink`) |

`executeDestructive` is forbidden, with one exception: the `manageJiraSprint` operation, to create a sprint or add issues to it. The connector classifies it as destructive because the same operation also closes sprints, which cannot be undone.

#### Permissions in `.claude/settings.json`

- **allow:** reading files and the 5 connector read tools.
- **ask:** all Notion (38) and Atlassian (9) write tools. Claude asks for confirmation before each one.
- **deny:** `git init` and `git worktree`.

---

### 7. Demo data

#### Scenario

A payments fintech for Italian SMEs. The "Conto Business" product: online account opening, company cards, instant transfers, invoice collection and reconciliation. A team of 6 developers and 1 QA, 2-week sprints.

#### Notion

Page **PAY Product**, database **Discovery**. Properties (in Italian): Nome (name), Tipo (type), Stato (status), Priorità (priority), Area, Problema (problem), Evidenze (evidence), Business Value, Time Criticality, Risk Reduction, Job Size, Epica Jira (Jira epic), Opp ID, Aggiornato il (updated on).

| Id | Opportunity | Status | BV | TC | RR | Job Size | Jira epic |
|---|---|---|---|---|---|---|---|
| PAYDISC-1 | Payee verification before an instant transfer | Validated | 13 | 13 | 8 | 8 | DEMO-2 |
| PAYDISC-2 | Automatic reconciliation of SDI invoices with incoming payments | Validated | 8 | 5 | 3 | 13 | DEMO-9 |
| PAYDISC-3 | KYB onboarding in under 10 minutes | In research | 13 | 5 | 8 | 13 | DEMO-3 |
| PAYDISC-4 | Spending limits per company card | To explore | | | | | |
| PAYDISC-5 | Recurring collection via SDD for B2B subscriptions | On hold | 5 | 3 | 2 | 5 | |

#### Jira

Site `TUO-SITO.atlassian.net`. Space named **PAY**, but with **key DEMO**: issues are called DEMO-n. Board "DEMO board" (id 1). Team-managed space, statuses in Italian: Da completare (to do), In corso (in progress), In revisione (in review), Completata (done).

| Key | Type | Title | Epic | Notes |
|---|---|---|---|---|
| DEMO-1 | Task | Demo | | Test item, done |
| DEMO-2 | Epic | Verification of Payee on instant transfers | | In progress |
| DEMO-3 | Epic | Digital KYB onboarding | | |
| DEMO-4 | Epic | Disposable virtual cards | | Empty, no source opportunity |
| DEMO-5 | Story | Show the IBAN holder's name before confirmation | DEMO-2 | In progress, 5 points, in sprint. Built behind a feature flag while awaiting the opinion |
| DEMO-6 | Story | Upload company documents online | DEMO-3 | In sprint, no estimate |
| DEMO-7 | Bug | Instant transfer stays pending after the timeout | DEMO-2 | 3 points, in sprint, Impediment flag with no cause |
| DEMO-8 | Story | Automatically match a payment to its invoice (exact case) | DEMO-9 | 5 points, in sprint |
| DEMO-9 | Epic | Automatic reconciliation of SDI invoices with incoming payments | | Holds the reconciliation rules |
| DEMO-10 | Task | Compliance, DPO and legal opinion on payee verification | DEMO-2 | 2 points, in sprint |
| DEMO-11 | Story | Company data from the business register extract | DEMO-3 | |
| DEMO-12 | Story | Declare the beneficial owner | DEMO-3 | Blocked by DEMO-11 |
| DEMO-13 | Story | KYB application submission, check and outcome | DEMO-3 | Blocked by DEMO-6, 11, 12 |
| DEMO-14 | Story | Manually match unmatched payments | DEMO-9 | Linked to DEMO-15 |
| DEMO-15 | Story | Record partial payments | DEMO-9 | |
| DEMO-16 | Story | Match one payment to several invoices | DEMO-9 | Blocked by DEMO-8. On release it replaces criterion 3 of DEMO-8 |

Sprint **PAY Sprint 1**, from 16 to 27 November 2026, not started yet: DEMO-5, 6, 7, 8, 10.

#### Mapped Jira fields

| Field | Id on this site |
|---|---|
| Story point estimate | `customfield_10016` |
| Sprint | `customfield_10020` |
| Flagged (Impediment) | `customfield_10021` |
| Team | `customfield_10001` |
| Start date | `customfield_10015` |

Acceptance criteria have no field of their own: they sit in the description, under the heading `## Acceptance criteria`. Criteria still awaiting an opinion sit under `## Criteri proposti, in attesa del parere di compliance` (proposed criteria, awaiting compliance opinion), and the snapshot keeps them separate.

#### Made-up values

Some numbers do not come from a real team or PO. I entered them to make the demo run, and they are declared in `output/2026-11-13_change-log.md`:

- Job Size of PAYDISC-1, 2, 3, 5 and Risk Reduction of PAYDISC-3.
- Story points of DEMO-7, 8, 10.

The agents know this and flag it as a breach of the "the team estimates" rule.

#### Offline test data

`backlog/sample/` holds 10 fictional items with deliberately planted defects (a duplicate across tools, a 21-point story, a validated opportunity without an epic, an item idle for 6 weeks, an empty epic). They let you try the agents without connectors. The list of defects is in `backlog/sample/README.md`.

---

### 8. Product rules

They live in `CLAUDE.md` and apply to every agent:

- **Story format:** "As a <user> I want <action> so that <benefit>", with a domain user (SME owner, accounting clerk, employee with a company card, accountant). Criteria in Gherkin. INVEST criteria.
- **Estimates:** Fibonacci story points 1, 2, 3, 5, 8, 13. Above 13 the story is split. The team estimates, not the PO and not the agents.
- **WSJF:** (business value + time criticality + risk reduction) / job size, all on the 1-13 scale. Computed on opportunities and epics. Job size is the explicit "Job Size" value of the opportunity on Notion, not the sum of the children. The linked epic inherits all four values. Stories inside an epic are ordered by hand by the PO. If a value is missing, the score is not computed.
- **Definition of Ready** and **Definition of Done:** 8 points each, with attention to compliance, audit trail and personal data kept out of logs.
- **Glossary:** KYC, KYB, AML, SCA, PSD2 (AISP and PISP), SEPA instant transfer, Verification of Payee, SDD, chargeback, virtual IBAN, reconciliation, SDI.

#### Reconciliation rules (in DEMO-9)

Written once in the epic, and the stories refer to them:

- An invoice has a total and an outstanding balance. Statuses: to be collected, partly collected, collected.
- Payment equal to the balance: invoice collected. Lower: partly collected. Higher: no match, the excess is shown.
- Cancellation or reversal: the balance goes back up and the status is recalculated.
- Candidate invoice: an open invoice whose number appears in the payment reference.
- Automatic matching only for the exact case (and, after DEMO-16, for several invoices with an exact sum). Every other match, partial ones included, is done by the clerk.

#### WSJF ranking with the current data

| # | Opportunity and epic | WSJF |
|---|---|---|
| 1 | Payee verification (PAYDISC-1 / DEMO-2) | 4.3 |
| 2 | KYB onboarding (PAYDISC-3 / DEMO-3) | 2.0 |
| 3 | Recurring SDD collection (PAYDISC-5) | 2.0 |
| 4 | Invoice reconciliation (PAYDISC-2 / DEMO-9) | 1.2 |

Not computable: PAYDISC-4 (no values) and DEMO-4 (no source opportunity). The ranking shows that the mechanism works, not that the order is right: the job sizes are made up.

---

### 9. Adding a tool

The full procedure is in `adapters/README.md`. In short:

1. Connect the connector and ask Claude for the exact list of tools, split into read and write. Without a connector: export a CSV to `backlog/import/<tool>_<project>.csv`.
2. Read 2-3 real items to find field names, custom field ids and status values.
3. Write `adapters/<tool>.md`, copying the structure of `notion.md`.
4. Add the read tools to backlog-sync, the write tools to "ask" in `settings.json`, and the tool to `CLAUDE.md`.
5. Run backlog-sync and check the snapshot, then backlog-health.

The analysis agents stay untouched.

---

### 10. What is verified and what is not

**Verified on real tools**

- Reading Notion (5 items) and Jira (16 items) through connectors, on the Mac, with a snapshot valid against the schema.
- Fallback to CSV when the Jira connector could not be reached.
- All six agents, each with its own frontmatter.
- Jira field map: story points, sprint, flag, parent, issue links, dates.
- Writes with an OK and read-back: issue creation, changes to descriptions and estimates, status changes, sprints, issue links, comments, Notion property updates.
- backlog-health found every planted defect and, on top of that, contradictions between criteria introduced while fixing them.

**Not verified**

- The velocity calculation: no sprint has been closed.
- A full story going through the Definition of Done.
- Reading the body of Notion pages and Jira comments: backlog-sync does not do it.
- Writing to a tool in CSV mode: by definition there is none.

---

### 11. Known limits

- Changes to `CLAUDE.md` and to the agents take effect from the next session: in the current one the agents get the copy read at startup.
- Velocity is computed only with at least 3 closed sprints.
- Jira priority exists but is not on the create screen: every issue starts as "Medium" and the value means nothing.
- `generato_il` (generated at) in the snapshot uses a conventional time (23:59:59Z) when the real time is unknown.
- The Notion-Jira link is a text field ("Epica Jira"): nothing guarantees the key exists. backlog-health checks it.
- "Aggiornato il" (updated on) on Notion changes with every edit, however small: an item can look alive because of a fixed typo.
- DEMO-1 is included in the snapshot as a test item, already done.

---

### 12. Problems found and how to fix them

| Symptom | Cause | Fix |
|---|---|---|
| backlog-sync cannot find the connector tools | Different prefix between cloud and desktop | Update the prefixes in the 4 files listed in section 6 |
| The agents do not show up among the available ones | Session opened on the wrong folder, for example a `conto-business` inside another one | Open the session on the folder that contains `CLAUDE.md` |
| Agents and permissions missing after copying the files | The `.claude` folder is hidden and was left behind | Cmd+Shift+period in Finder, then copy it |
| A cloud session cannot see the folder on the Mac | iCloud Drive folders (under `~/Library`) do not attach to a cloud session | Use a local session |
| All connectors return 503 or time out at once | Temporary outage of the service that routes the calls | Wait and retry. Do not disconnect the connector: the prefix would change |
| "This connector can't be used with the current credential" | Seen during the same outage | Reopen the session once the service is back |
| New rules in `CLAUDE.md` are not applied | The agents read the copy from the start of the session | Reopen the session, or ask the agent to read the file again from disk |
| Fixing one criterion breaks another | Rules scattered across several stories | Write the rule once in the epic and have the stories refer to it |

---

### 13. Project history

**12 November 2026, cloud session**

- Checked the connectors: Notion reachable, Atlassian not connected.
- Created the PAY Product page and the Discovery database with 5 opportunities on Notion.
- Wrote the schema, the adapters, the six agents, `CLAUDE.md`, `settings.json` and the 10 sample items.
- First sync: Notion through the connector, Jira through CSV.
- Connected Atlassian to a free Jira Cloud site. Rewrote the Jira adapter on the real fields.
- Created 7 issues on Jira, then estimates, statuses, flags. backlog-health found every planted defect.
- Created epic DEMO-9 for reconciliation and resolved the duplicate between Notion and Jira.
- story-refiner on DEMO-6 and DEMO-8, fintech-compliance on DEMO-5 and DEMO-7.

**13 November 2026**

- Compliance criteria on DEMO-5 and opinion task DEMO-10.
- New WSJF rule with an explicit Job Size on Notion.
- Splits applied: DEMO-6 and DEMO-8 narrowed, DEMO-11 to DEMO-16 created.
- Created PAY Sprint 1. Prioritizer produced the first ranking, sprint-prep rejected every candidate on the Definition of Ready.
- Moved the system to the Mac. Fixed the tool prefixes, first local sync succeeded.
- Created 5 dependency links between issues. Three rounds of backlog-health on reconciliation, down to zero contradictions.

**14 November 2026**

- Reconciliation rules completed in DEMO-9 (candidate invoice, automatic matching) and reversal criterion fixed in DEMO-8.

---

### 14. Next steps

Left open on purpose, for planning:

- DEMO-6 in sprint without an estimate.
- DEMO-7 in sprint, blocked with no cause.
- 10 pending proposals and 7 gaps in the criteria, for refinement with the team.

To use it for real, it needs:

1. Estimates made by the team, instead of the made-up ones.
2. At least three closed sprints, for velocity.
3. The compliance opinion on DEMO-5 and DEMO-10.

Useful improvements to the system:

- Automatic validation of the snapshot after every sync.
- A `/applica-proposte` (apply proposals) skill for the proposal, OK, write, change-log cycle, today described only in `CLAUDE.md`.
- A comparison between two snapshots, to see what changed from one refinement to the next.
