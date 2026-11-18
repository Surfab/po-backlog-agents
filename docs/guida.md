# Guida completa

[README](../README.md) · [English version](guide-en.md)

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
- Quattro agenti sono generici: regole e valori li leggono da `CLAUDE.md`. Per un progetto nuovo c'è un plugin di Claude Code (sezione 15).

---

## Configurazione

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

## Indice

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
15. [Il plugin per altri progetti](#15-il-plugin-per-altri-progetti)

---

## 1. Come si usa

### Prima volta

1. Apri la cartella del progetto in Claude Desktop. Funziona anche su iCloud Drive. Dentro devono esserci `CLAUDE.md`, `README.md`, `adapters/`, `backlog/`, `output/`, `schema/` e la cartella nascosta `.claude/`. Nel Finder le cartelle nascoste si vedono con **Cmd+Maiusc+punto**.
2. Su iCloud: clic destro sulla cartella, "Mantieni scaricato". Se iCloud libera spazio e lascia i file solo online, Claude li trova vuoti.
3. In Claude Desktop, tab **Code**, apri una sessione **locale** su quella cartella. Non una sessione cloud: da cloud la cartella non è raggiungibile.
4. Controlla che i connettori Notion e Atlassian siano collegati (Impostazioni, Connettori).

### Ogni sessione di lavoro

Scrivi le richieste in linguaggio normale. Esempi:

| Quando | Cosa scrivi |
|---|---|
| Inizio sessione | "Lancia backlog-sync" |
| Subito dopo | "Lancia backlog-health sullo snapshot di oggi" |
| Prima del refinement | "Lancia story-refiner su `DEMO-14` e `DEMO-15`" |
| Prima che una story entri in sprint | "Lancia domain-review su `DEMO-5`" |
| Prima del planning | "Lancia prioritizer", poi "Lancia sprint-prep su `PAY Sprint 1`" |
| Per applicare le proposte | "Mostrami le proposte in attesa" e poi "OK per la riga 3" |

**Gli esempi usano segnaposto.** `DEMO-14`, `DEMO-15` e `DEMO-5` stanno per le chiavi delle issue Jira, `PAY Sprint 1` per il nome dello sprint. Sostituiscili con quelli del tuo progetto, per esempio `CHECKOUT-142` e `Sprint 12`. Nei dati della demo corrispondono a issue e sprint reali dello spazio Jira di prova.

L'ordine consigliato è sempre: **sync, poi analisi, poi decisioni**. Gli agenti di analisi leggono solo lo snapshot: se non rifai il sync, lavorano su dati vecchi.

### Cosa leggere dopo ogni agente

Ogni agente scrive un report in `output/AAAA-MM-GG_<agent>.md`, sempre con lo stesso formato:

1. Sintesi in 3 righe.
2. Problemi divisi in Critico, Medio, Basso, ognuno con l'id dell'item.
3. Azioni proposte.
4. Dati mancanti.

Le modifiche concrete che propone finiscono in `output/AAAA-MM-GG_proposte.md`.

---

## 2. Come funziona

Il sistema ha tre livelli.

```
   Notion          Jira           CSV
     |               |              |
  adapter         adapter        adapter        Livello 1: come si legge ogni tool
     \               |             /
      \              |            /
       backlog-sync (unico agent con accesso ai tool, solo lettura)
                     |
     backlog/AAAA-MM-GG_HHMM_snapshot.json      Livello 2: formato unico
                     |
   backlog-health  story-refiner  prioritizer   Livello 3: analisi
   domain-review  sprint-prep
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

**Uno snapshot per sync.** Il nome contiene data e ora reali, per esempio `2026-11-14_0935_snapshot.json`, e un file esistente non si sovrascrive mai. Ogni report cita il file esatto che ha letto, così un'analisi si può rifare sugli stessi dati.

**Fonte di verità.** La discovery (opportunità, ricerche, ipotesi, PRD) vive su Notion. La delivery (epiche, story, bug, task) vive su Jira. Se lo stesso contenuto esiste in due tool, vince la fonte di verità e l'altro item si collega o si chiude, mai si tiene allineato a mano. Il collegamento tra i due mondi è il campo "Epica Jira" sull'opportunità Notion.

---

## 3. Struttura della cartella

```
po-backlog-agents/
├── README.md                      presentazione breve
├── CHANGELOG.md                   modifiche al repository
├── docs/
│   ├── guida.md                   questa guida
│   └── guide-en.md                la guida in inglese
├── CLAUDE.md                      istruzioni di progetto, lette da Claude a ogni avvio
├── TEST-generici.md               test di non regressione degli agenti generici, con gli esiti
├── .claude/
│   ├── settings.json              permessi: cosa è consentito, cosa chiede conferma, cosa è vietato
│   └── agents/
│       ├── backlog-sync.md
│       ├── backlog-health.md
│       ├── story-refiner.md
│       ├── prioritizer.md
│       ├── domain-review.md
│       └── sprint-prep.md
├── TEST-plugin.md                 test del plugin, da eseguire in una sessione nuova
├── .claude-plugin/
│   └── marketplace.json           marketplace "surfab", con il plugin po-backlog-agents
├── plugin/                        il plugin, installabile in qualsiasi progetto
│   ├── .claude-plugin/plugin.json nome, versione, descrizione
│   ├── agents/                    i quattro agenti generici e domain-review
│   ├── skills/nuovo-progetto/     la skill che prepara un progetto
│   └── templates/                 CLAUDE.template.md, settings, backlog-sync, checklist,
│                                  schema, adapter di base e modelli di Jira e Notion, modello e esempio delle regole di dominio
├── domain/
│   └── fintech.md                 regole di dominio lette da domain-review
├── versioni-precedenti/
│   └── agents/fintech-compliance.md   il vecchio agente di dominio, non più usato
├── adapters/
│   ├── README.md                  come aggiungere un tool in 5 passi
│   ├── notion.md
│   ├── jira.md
│   └── csv.md
├── schema/
│   └── backlog-item.schema.json   formato dello snapshot
├── backlog/
│   ├── AAAA-MM-GG_HHMM_snapshot.json   snapshot normalizzati, uno per sync, mai sovrascritti
│   ├── 2026-11-12_snapshot.json   snapshot vecchi, con il nome senza ora
│   ├── 2026-11-13_snapshot.json
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

## 4. I sei agenti

Tutti hanno `model: inherit`, cioè usano il modello della sessione.

| Agente | Quando | Cosa fa | Tool |
|---|---|---|---|
| **backlog-sync** | A inizio sessione | Legge ogni tool attivo in sola lettura, normalizza e scrive lo snapshot con data e ora reali nel nome. Se il file esiste già si ferma. Se un connettore non risponde ripiega sul CSV. Dichiara i campi che non sa mappare | Read, Glob, Write, 3 tool Notion di lettura, 2 tool Jira di lettura |
| **backlog-health** | Dopo ogni sync | Duplicati anche tra tool, item fermi oltre la soglia di `CLAUDE.md`, story senza criteri o stima, stime fuori scala, dipendenze rotte, epiche vuote, item senza epica, item validati senza collegamento alla delivery, item bloccati senza motivo, valori di priorità mancanti, contraddizioni tra criteri | Read, Grep, Glob, Write |
| **story-refiner** | Prima del refinement, su id indicati | Riscrive la story nel formato di `CLAUDE.md` ("Come… voglio… per…"), verifica INVEST, scrive i criteri in blocchi Gherkin con uno `Scenario` per caso, propone uno split se supera il massimo della scala o non è testabile | Read, Grep, Glob, Write |
| **prioritizer** | Prima del planning | Applica il framework della sezione Prioritizzazione di `CLAUDE.md` (qui WSJF su opportunità ed epiche), mostra calcolo e razionale, rifiuta di calcolare se manca un valore | Read, Grep, Glob, Write |
| **domain-review** | Prima che un item entri in sprint | Applica le regole di `domain/fintech.md`: impatti su PSD2 e SCA, Verification of Payee, AML, KYC e KYB, GDPR, DORA, carte, SDD, SDI, audit trail, beneficiari e perimetro della verifica, scadenze, contesto dell'item. Non cita articoli di legge: indica il rischio e con chi verificarlo | Read, Grep, Glob, Write |
| **sprint-prep** | 1-2 giorni prima del planning | Bozza di sprint goal, verifica della Definition of Ready sui candidati, capacità rispetto alla velocity degli ultimi sprint chiusi (quanti, lo dice `CLAUDE.md`), scalette di review e retrospettiva | Read, Grep, Glob, Write |

Solo backlog-sync tocca i connettori, e solo in lettura. Gli altri cinque lavorano sui file.

### Agenti generici e agenti di dominio

backlog-health, story-refiner, prioritizer e sprint-prep sono **generici**: non contengono regole di Conto Business. Formato story, scala di stima, framework di priorità, soglie, team e durata dello sprint li leggono da `CLAUDE.md`. Se un valore manca, non usano un default: saltano il controllo che ne dipende e lo scrivono tra i dati mancanti, con la sezione di `CLAUDE.md` da completare. Il test di non regressione in `TEST-generici.md` ha verificato che su Conto Business danno gli stessi risultati della versione precedente.

domain-review è l'**agente di dominio**: non contiene regole proprie, le legge dal file indicato nella sezione Agent di dominio di `CLAUDE.md`, qui `domain/fintech.md` (sezione 15). Gli agenti generici rimandano a lui quando un item rientra nella sezione Ambito di quel file. Fino al 17 novembre 2026 la demo usava fintech-compliance, un agente con le regole di pagamento scritte dentro: è spostato in `versioni-precedenti/agents/` e i suoi controlli sono tutti in `domain/fintech.md`. I report vecchi in `output/` portano ancora il suo nome.

backlog-sync resta specifico del progetto, perché la riga `tools:` contiene i prefissi dei connettori. Nel plugin c'è come `plugin/templates/backlog-sync.template.md`, con i segnaposto.

### Quale snapshot leggono gli agenti di analisi

Se il PO non ne indica uno, leggono il più recente in `backlog/`, ordinando per data e ora. I file vecchi senza ora nel nome si confrontano solo per data. Se un file vecchio e uno nuovo hanno la stessa data, chiedono al PO quale leggere.

Se in `backlog/` non c'è nessuno snapshot, le due versioni si comportano in modo diverso, ed è una scelta voluta:

- **Agenti del plugin** (`plugin/agents/`): si fermano e chiedono al PO di lanciare backlog-sync. In un progetto reale analizzare dati di esempio al posto del backlog vero darebbe report sbagliati senza che nessuno se ne accorga.
- **Agenti di Conto Business** (`.claude/agents/`): ripiegano su `backlog/sample/`. Conto Business è una demo, e chi la prova senza connettori deve poter lanciare gli agenti subito sui dati di esempio.

In entrambi i casi la sintesi del report cita il percorso esatto del file letto e il suo `generato_il`.

backlog-sync riceve dalla sessione principale data e ora reali (`date "+%Y-%m-%dT%H:%M:%S%z"`). Se il prompt non contiene l'ora, non scrive lo snapshot e la chiede.

Gli agenti scrivono solo in `backlog/` (backlog-sync) e in `output/` (tutti gli altri). Il frontmatter di un agente non può limitare la scrittura a una cartella: il vincolo sta nelle istruzioni di ogni agente e nei permessi.

---

## 5. Le scritture: proposte, OK, change-log

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

## 6. Connettori, nomi dei tool e permessi

### Il prefisso dei tool cambia con l'ambiente

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

### Tool usati

| Connettore | Lettura (solo backlog-sync) | Scrittura (solo sessione principale, sempre con OK) |
|---|---|---|
| Notion | `notion-query-data-sources`, `notion-fetch`, `notion-search` | `notion-update-page`, `notion-create-pages`, `notion-update-data-source` e gli altri elencati in `settings.json` |
| Atlassian | `searchJiraIssuesUsingJql`, `getJiraIssue` | `editJiraIssue`, `createJiraIssue`, `transitionJiraIssue`, `addOrEditJiraIssueComment`, `executeWrite` (per esempio `createJiraIssueLink`) |

`executeDestructive` resta in "ask", non in "deny". Un permesso in `settings.json` vale per il tool intero e non distingue un'operazione dall'altra, quindi il limite sta nelle istruzioni (`CLAUDE.md` e `adapters/jira.md`): è ammesso solo per l'operazione `manageJiraSprint`, per creare uno sprint o aggiungervi issue. Il connettore la classifica come distruttiva perché la stessa operazione chiude anche gli sprint, cosa irreversibile. Ogni chiamata chiede comunque conferma.

### Permessi in `.claude/settings.json`

- **allow:** lettura di file e i 5 tool di lettura dei connettori.
- **ask:** i tool di scrittura di Notion (36) e di Atlassian (9). Claude chiede conferma prima di ognuno.
- **deny:** i due tool che eliminano pagine e database Notion, `git init` e `git worktree`.

---

## 7. I dati demo

### Scenario

Fintech di pagamenti per PMI italiane. Prodotto "Conto Business": apertura conto online, carte aziendali, bonifici istantanei, incasso e riconciliazione delle fatture. Team di 6 sviluppatori e 1 QA, sprint di 2 settimane.

### Notion

Pagina **PAY Product**, database **Discovery**. Proprietà: Nome, Tipo, Stato, Priorità, Area, Problema, Evidenze, Business Value, Time Criticality, Risk Reduction, Job Size, Epica Jira, Opp ID, Aggiornato il.

| Id | Opportunità | Stato | BV | TC | RR | Job Size | Epica Jira |
|---|---|---|---|---|---|---|---|
| PAYDISC-1 | Verifica del beneficiario prima del bonifico istantaneo | Validata | 13 | 13 | 8 | 8 | DEMO-2 |
| PAYDISC-2 | Riconciliazione automatica fatture SDI con gli incassi | Validata | 8 | 5 | 3 | 13 | DEMO-9 |
| PAYDISC-3 | Onboarding KYB in meno di 10 minuti | In ricerca | 13 | 5 | 8 | 13 | DEMO-3 |
| PAYDISC-4 | Limiti di spesa per singola carta aziendale | Da esplorare | | | | | |
| PAYDISC-5 | Incasso ricorrente con SDD per abbonamenti B2B | In attesa | 5 | 3 | 2 | 5 | |

### Jira

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

### Campi Jira mappati

| Campo | Id su questo sito |
|---|---|
| Story point estimate | `customfield_10016` |
| Sprint | `customfield_10020` |
| Flagged (Impediment) | `customfield_10021` |
| Team | `customfield_10001` |
| Data di inizio | `customfield_10015` |

Gli acceptance criteria non hanno un campo: stanno nella descrizione, sotto il titolo `## Acceptance criteria`. I criteri ancora in attesa di un parere stanno sotto `## Criteri proposti, in attesa del parere di compliance` e lo snapshot li tiene separati.

### Valori inventati

Alcuni numeri non vengono da un team o da un PO reale. Li ho inseriti io per far girare la demo, e sono dichiarati in `output/2026-11-13_change-log.md`:

- Job Size di PAYDISC-1, 2, 3, 5 e Risk Reduction di PAYDISC-3.
- Story point di DEMO-7, 8, 10.

Gli agenti lo sanno e lo segnalano come violazione della regola "stima il team".

### Dati di prova offline

`backlog/sample/` contiene 10 item fittizi con difetti piantati apposta (un duplicato tra tool, una story da 21 punti, un'opportunità validata senza epica, un item fermo da 6 settimane, un'epica vuota). Servono a provare gli agenti senza connettori. L'elenco dei difetti è in `backlog/sample/README.md`.

---

## 8. Regole di prodotto

Stanno in `CLAUDE.md` e valgono per tutti gli agenti. Gli agenti generici le cercano per nome di sezione, quindi i nomi delle sezioni non si cambiano.

- **Parametri del backlog:** item fermi dopo 28 giorni senza aggiornamenti, team di 6 sviluppatori e 1 QA, sprint di 2 settimane, velocity sugli ultimi 3 sprint chiusi.
- **Collegamento discovery-delivery:** un'opportunità Notion è validata quando lo stato è "Validata", e da quel momento deve avere un'epica Jira collegata (dipendenza `realizza` o campo "Epica Jira"). Opportunità ed epica collegate non sono duplicati.
- **Agent di dominio:** file di regole `domain/fintech.md`, letto da domain-review per gli item che toccano pagamenti, identità del cliente, dati personali, carte o integrazioni con terzi.
- **Formato story:** "Come <utente> voglio <azione> per ottenere <beneficio>", con un utente del dominio (titolare di PMI, addetto all'amministrazione, dipendente con carta, commercialista). Criteri INVEST. Acceptance criteria in blocchi Gherkin, uno `Scenario` per caso con `Dato`, `Quando`, `Allora` su righe separate: una lista numerata in prosa non basta. Casi limite da coprire: importi, timeout, stati del pagamento.
- **Stima:** story point Fibonacci 1, 2, 3, 5, 8, 13. Oltre 13 la story si divide. Stima il team, non il PO né gli agenti.
- **Prioritizzazione:** framework WSJF, (business value + time criticality + risk reduction) / job size, tutto sulla scala 1-13. Si calcola su opportunità ed epiche. Il job size è il valore esplicito "Job Size" dell'opportunità su Notion, non la somma dei figli. L'epica collegata eredita i quattro valori. Le story dentro un'epica le ordina a mano il PO. Se manca un valore, il punteggio non si calcola. A parità viene prima l'item con time criticality più alta.
- **Definition of Ready** e **Definition of Done:** 8 punti ciascuna, con attenzione a compliance, audit trail e dati personali fuori dai log.
- **Glossario:** KYC, KYB, AML, SCA, PSD2 (AISP e PISP), bonifico istantaneo SEPA, Verification of Payee, SDD, chargeback, IBAN virtuale, riconciliazione, SDI.

### Regole di riconciliazione (in DEMO-9)

Scritte una volta sola nell'epica, e le story ci fanno riferimento:

- Una fattura ha un totale e un residuo. Stati: da incassare, incassata in parte, incassata.
- Incasso uguale al residuo: fattura incassata. Inferiore: incassata in parte. Superiore: nessun abbinamento, l'eccedenza è indicata.
- Annullamento o storno: il residuo risale e lo stato si ricalcola.
- Fattura candidata: fattura aperta il cui numero è nella causale dell'incasso.
- Abbinamento automatico solo per il caso esatto (e, dopo DEMO-16, per più fatture con somma esatta). Ogni altro abbinamento, compresi i parziali, lo fa l'addetto.

### Classifica WSJF con i dati attuali

| # | Opportunità ed epica | WSJF |
|---|---|---|
| 1 | Verifica del beneficiario (PAYDISC-1 / DEMO-2) | 4,3 |
| 2 | Onboarding KYB (PAYDISC-3 / DEMO-3) | 2,0 |
| 3 | Incasso ricorrente SDD (PAYDISC-5) | 2,0 |
| 4 | Riconciliazione fatture (PAYDISC-2 / DEMO-9) | 1,2 |

Non calcolabili: PAYDISC-4 (nessun valore) e DEMO-4 (nessuna opportunità di origine). La classifica dimostra che il meccanismo funziona, non che l'ordine sia giusto: i job size sono inventati.

---

## 9. Aggiungere un tool

La procedura completa è in `adapters/README.md`. In sintesi:

1. Collega il connettore e chiedi a Claude l'elenco esatto dei tool, divisi in lettura e scrittura. Senza connettore: esporta un CSV in `backlog/import/<tool>_<progetto>.csv`.
2. Leggi 2-3 item reali per scoprire nomi dei campi, id dei campi custom e valori degli stati.
3. Scrivi `adapters/<tool>.md` copiando la struttura di `notion.md`. In un progetto nuovo, per Jira e Notion parti dai modelli del plugin, `plugin/templates/adapters/jira.md` e `notion.md`.
4. Aggiungi i tool di lettura a backlog-sync, quelli di scrittura in "ask" in `settings.json`, il tool in `CLAUDE.md`.
5. Lancia backlog-sync e controlla lo snapshot, poi backlog-health.

Gli agenti di analisi non si toccano.

Per un **progetto nuovo**, non solo un tool nuovo, si installa il plugin e si lancia la skill `nuovo-progetto`: vedi la sezione 15 e i dieci passi di `plugin/templates/CHECKLIST-nuovo-progetto.md`.

---

## 10. Cosa è verificato e cosa no

**Verificato su dati reali**

- Lettura via connettore di Notion (5 item) e Jira (16 item), sul Mac, con snapshot valido rispetto allo schema.
- Ripiego su CSV quando il connettore Jira non era raggiungibile.
- Tutti e sei gli agenti, ognuno con il suo frontmatter.
- Mappa dei campi Jira: story point, sprint, flag, parent, link tra issue, date.
- Scrittura con OK e rilettura: creazione di issue, modifica di descrizioni e stime, cambi di stato, sprint, link tra issue, commenti, aggiornamento di proprietà Notion.
- backlog-health ha trovato tutti i difetti piantati e, in più, contraddizioni tra criteri introdotte durante le correzioni.
- Agenti generici: i cinque test di non regressione di `TEST-generici.md` sono superati su copie della cartella di lavoro, compreso il caso di un valore mancante in `CLAUDE.md`.

**Non verificato**

- Il calcolo della velocity: non esiste nessuno sprint chiuso.
- Il passaggio di una story completa attraverso la Definition of Done.
- La lettura del corpo delle pagine Notion e dei commenti Jira: backlog-sync non lo fa.
- La scrittura su un tool in modalità CSV: per definizione non c'è.
- Il plugin su un secondo progetto: finora gli agenti generici sono stati provati solo su Conto Business. I test del plugin sono in `TEST-plugin.md`.

---

## 11. Limiti noti

- Le modifiche a `CLAUDE.md` e agli agenti valgono dalla sessione successiva: in quella in corso gli agenti ricevono la copia letta all'avvio.
- La velocity si calcola solo con almeno 3 sprint chiusi.
- La priorità Jira esiste ma non compare nella schermata di creazione: ogni issue nasce "Medium" e il valore non dice nulla.
- Gli snapshot fino al 13 novembre 2026 hanno il vecchio nome senza ora e `generato_il` convenzionale (23:59:59Z). Quello del 13 è la versione del pomeriggio: i report del mattino non sono riproducibili.
- Il collegamento Notion-Jira è un campo di testo ("Epica Jira"): niente garantisce che la chiave esista. backlog-health lo controlla.
- "Aggiornato il" su Notion cambia a ogni modifica, anche minima: un item può sembrare vivo per un refuso corretto.
- DEMO-1 è incluso nello snapshot come item di prova, già completato.
- Durante il test 2 di `TEST-generici.md` un agente ha creato un file vuoto nella radice della cartella di test. Il vincolo di scrivere solo in `output/` sta nelle istruzioni degli agenti, non nei permessi, quindi niente lo blocca.

---

## 12. Problemi incontrati e come si risolvono

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
| Lo snapshot del 13 novembre era stato sovrascritto nel pomeriggio: i report del mattino non si potevano più rifare sugli stessi dati. Lo ha scoperto il test di non regressione | Il nome conteneva solo la data, e backlog-sync sovrascriveva il file dello stesso giorno | Ora reale nel nome (`AAAA-MM-GG_HHMM_snapshot.json`) e in `generato_il`, e nessuna sovrascrittura: se il file esiste, backlog-sync si ferma |

---

## 13. Storia del progetto

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
- Resi generici backlog-health, story-refiner, prioritizer e sprint-prep: regole e valori passano in `CLAUDE.md`, nelle nuove sezioni Parametri del backlog, Collegamento discovery-delivery, Agent di dominio e Prioritizzazione.
- Creata la cartella `template/`, con checklist, `CLAUDE.md` da compilare, permessi e agenti.
- Test di non regressione su copie della cartella di lavoro: cinque test superati. Il test ha scoperto lo snapshot del 13 sovrascritto, e il nome dello snapshot ora contiene l'ora.

---

## 14. Prossimi passi

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
- Confronto tra due snapshot, per vedere cosa è cambiato tra un refinement e l'altro. Con un file per sync e niente sovrascritture, ora i dati per farlo ci sono.
- Una colonna "snapshot" in `proposte.md`, con il file da cui nasce ogni proposta: così la sessione principale rifiuta da sola le proposte nate da `backlog/sample/`.
- Provare il plugin su un secondo progetto, con tool e regole diversi, per capire cosa manca davvero in `CLAUDE.template.md`.

---

## 15. Il plugin per altri progetti

Gli agenti generici, la skill che prepara un progetto e i template sono anche un plugin di Claude Code, `po-backlog-agents`. Il repository è il suo marketplace, `surfab`. La demo Conto Business non usa il plugin: ha le sue copie degli agenti in `.claude/agents/`, domain-review compreso, che senza snapshot ripiegano su `backlog/sample/`.

### Installazione

Una volta per computer, da terminale:

```bash
claude plugin marketplace add Surfab/po-backlog-agents
claude plugin install po-backlog-agents@surfab
```

Senza opzioni il plugin vale per tutti i tuoi progetti (scope `user`). Con `--scope project` vale solo per il progetto ed entra in `.claude/settings.json`; con `--scope local` vale solo per te e solo per quel progetto. Dopo l'installazione apri una sessione nuova: plugin e agenti si leggono all'avvio.

**Dall'app desktop (tab Code).** Il marketplace si aggiunge comunque con il primo comando qui sopra, una volta per computer: la documentazione ufficiale non descrive come farlo dall'app. Poi, in una sessione locale, clicca **+** accanto al riquadro del messaggio e scegli **Add plugins**; se il menu mostra **Plugins**, scegli **Plugins** > **Browse plugins** (se manca, **Manage plugins**). Seleziona `po-backlog-agents` e lo scope: il tuo account, il progetto o solo locale. Per disattivarlo o disinstallarlo: **+** > **Plugins** > **Manage plugins**, oppure **Customize** o **Plugins** nella barra laterale. Il plugin non è disponibile nelle sessioni cloud. Fonti: [Install plugins nell'app desktop](https://code.claude.com/docs/en/desktop#install-plugins) e [Install a plugin](https://code.claude.com/docs/en/discover-plugins#install-a-plugin).

Gli agenti del plugin hanno il nome del plugin davanti: `po-backlog-agents:backlog-health`, `po-backlog-agents:domain-review` e così via. "Lancia backlog-health" di solito basta. Attenzione: se il progetto ha in `.claude/agents/` un agente con lo stesso nome, vince quello del progetto.

**Aggiornamenti.** `plugin.json` dichiara una versione (oggi 0.2.1). Chi ha installato il plugin riceve una modifica solo quando la versione cambia: a ogni modifica del plugin va alzata, e va scritta nel CHANGELOG. Per aggiornare: `claude plugin update po-backlog-agents@surfab`. Per l'aggiornamento la documentazione dell'app desktop non indica un percorso: si usa il comando da terminale.

### La skill nuovo-progetto

In una sessione locale sulla cartella del progetto scrivi `/po-backlog-agents:nuovo-progetto`. La skill fa al massimo sei domande:

1. prodotto, lingua dei report, dati reali o fittizi;
2. tool di backlog, connettore o CSV, e fonte di verità;
3. team e sprint;
4. scala di stima;
5. framework di priorità;
6. dominio, oppure nessuno.

Poi crea `CLAUDE.md`, `.claude/settings.json`, `.claude/agents/backlog-sync.md`, `schema/`, `adapters/README.md`, `adapters/csv.md`, i modelli `adapters/jira.md` e `adapters/notion.md` se hai indicato quei tool e, se hai indicato un dominio, `domain/<dominio>.md`. Compila solo quello che hai detto: il resto resta come segnaposto. Se un file esiste già non lo sovrascrive: si ferma e chiede. Alla fine elenca i segnaposto rimasti e i passi successivi della checklist.

### L'agente di dominio intercambiabile

domain-review è un solo agente per qualsiasi settore. Le regole non stanno nell'agente ma in un file del progetto, indicato in `CLAUDE.md`:

```markdown
## Agent di dominio

File di regole: `domain/fintech.md`
```

Con `File di regole: Nessuno` domain-review non analizza niente e lo dice in una riga.

Il file di regole ha sette sezioni fisse: Ruolo, Ambito (quali item), Cosa controllare, Gravità, Glossario, Con chi verificare, Cosa non fare. Il modello vuoto è `plugin/templates/domain.template.md`. L'esempio completo, `plugin/templates/domain/fintech.md`, contiene tutti i controlli di fintech-compliance.

Alcune regole valgono qualunque sia il file. domain-review indica il rischio e con chi verificarlo. Non cita articoli di legge a memoria e non dà pareri legali. Le regole di "Cosa non fare" si aggiungono a queste, non le sostituiscono. Gli agenti generici rimandano a domain-review quando un item rientra nell'Ambito del file.

### Cosa resta da fare in ogni progetto, e perché

- **`CLAUDE.md` e le regole di dominio.** Sono le regole del team: soglie, Definition of Ready e of Done, priorità, controlli di settore. Gli agenti non hanno valori di default: quello che manca finisce tra i dati mancanti del report.
- **backlog-sync.** La riga `tools:` contiene i nomi reali dei tool di sola lettura dei connettori, che cambiano da un computer all'altro. Per questo non sta nel plugin: la skill lo crea dal template, con i segnaposto.
- **`.claude/settings.json`.** I permessi (allow, ask, deny) servono al progetto perché gli agenti dei plugin non supportano `hooks`, `mcpServers` e `permissionMode`. La skill lo crea dal template, con i segnaposto dei connettori.
- **Gli adapter dei tool.** Uno per tool. Per Jira e Notion la skill copia un modello già scritto: restano da compilare prefisso del connettore, sito e chiave dello spazio, id dei campi custom, proprietà e stati del database, che cambiano in ogni progetto. Per gli altri tool si segue `adapters/README.md`.

I passi completi sono in `plugin/templates/CHECKLIST-nuovo-progetto.md`.
