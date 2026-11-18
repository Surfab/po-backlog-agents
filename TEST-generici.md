# Test di non regressione degli agent generici

Verifica che i quattro agent generici (backlog-health, story-refiner, prioritizer, sprint-prep) diano su Conto Business gli stessi risultati della versione precedente. Dove trovare la versione precedente: `versioni-precedenti/agents-v1/` nella cartella di lavoro; nel repository pubblico, [`.claude/agents/` al commit fccd388](https://github.com/Surfab/po-backlog-agents/tree/fccd388eb661cd99c0a9e91aa2e3deac5cd973aa/.claude/agents).

## Preparazione

1. **Lavora su una copia, mai qui.** Duplica la cartella `conto-business` e chiama la copia `conto-business-test`, nella stessa cartella padre. Nel Finder: clic destro, "Duplica", poi rinomina. Controlla che nella copia ci sia la cartella nascosta `.claude/` (Cmd+Maiusc+punto). Così `output/` e `proposte.md` dell'originale restano puliti e alla fine non serve spostare niente.
2. Apri una **sessione nuova e locale** su `conto-business-test`. Una sessione già aperta usa ancora gli agent vecchi.
3. Controlla che la sessione veda gli agent: "Elenca gli agent di progetto disponibili". Devono essere 6.
4. **Le proposte prodotte durante i test non si applicano mai**, nemmeno quando gli id somigliano a item reali. Il campione copia alla lettera i titoli di DEMO-2, 3, 4, 5, 7 e 9 con chiavi diverse (PAY-…). Le opportunità PAYDISC-1, 2 e 3 hanno lo stesso id e lo stesso titolo che su Notion.

Per ogni test scrivi l'esito nella tabella in fondo. **Se il test 3 (prioritizer) dà un risultato diverso, fermati e avvisa il PO**: decide lui se tornare alla versione precedente: `versioni-precedenti/agents-v1/` nella cartella di lavoro; nel repository pubblico, [`.claude/agents/` al commit fccd388](https://github.com/Surfab/po-backlog-agents/tree/fccd388eb661cd99c0a9e91aa2e3deac5cd973aa/.claude/agents).

## Test 1: backlog-health sui dati di prova

Richiesta: "Lancia backlog-health su `backlog/sample/2026-11-12_snapshot.json`".

Report atteso: `output/AAAA-MM-GG_HHMM_backlog-health.md` (fino al 18/11 il nome aveva solo la data).

La sintesi deve dichiarare che lo snapshot letto è `backlog/sample/2026-11-12_snapshot.json`.

Deve trovare tutti i 7 difetti di `backlog/sample/README.md`:

| # | Item | Difetto | Controllo dell'agent |
|---|---|---|---|
| 1 | jira:PAY-131 e notion:PAYDISC-2 | duplicato tra tool (riconciliazione fatture SDI) | 1, Duplicati |
| 2 | notion:PAYDISC-2 | opportunità validata senza epica | 8, Item validati senza collegamento |
| 3 | jira:PAY-131 | senza epica, senza stima, senza acceptance criteria | 3 e 7 |
| 4 | jira:PAY-118 | 21 punti (oltre 13), senza criteri | 4 e 3 |
| 5 | jira:PAY-125 | fermo da oltre 28 giorni, bloccato da jira:PAY-140 che non esiste | 2 e 5 |
| 6 | jira:PAY-109 | epica vuota | 6 |
| 7 | notion:PAYDISC-3 | manca risk reduction, WSJF non calcolabile | 10, Valori di priorità mancanti |

Non devono comparire come problemi:

- jira:PAY-112: story completa.
- notion:PAYDISC-1 e jira:PAY-101: opportunità ed epica collegate, non un duplicato.

Comportamento atteso in più, che non è una regressione: il controllo 10 segnala anche il Job Size mancante su PAYDISC-1 e PAYDISC-2. Nel campione è davvero vuoto.

Esito: **superato** se trova 7 su 7 e non segnala come problema i due casi sani.

## Test 2: story-refiner, stesso formato

Richiesta: "Lancia story-refiner su `jira:PAY-118` e `jira:PAY-131` usando `backlog/sample/2026-11-12_snapshot.json`".

Confronta con `output/2026-11-12_story-refiner.md`. Il report deve avere:

- Le sezioni Sintesi (3 righe), Problemi (Critico, Medio, Basso), Azioni proposte, Dati mancanti, con un dettaglio per ogni story come nel report del 12 novembre.
- Ogni story riscritta nel formato "Come <utente> voglio <azione> per ottenere <beneficio>", con un utente del dominio preso da `CLAUDE.md` (titolare di PMI, addetto all'amministrazione, dipendente con carta, commercialista).
- Una riga per ognuno dei sei criteri INVEST.
- Acceptance criteria in blocchi Gherkin: uno `Scenario` per caso, con `Dato`, `Quando`, `Allora` su righe separate, come nel report del 12 novembre. **La prosa numerata non basta**, anche se usa le parole Dato, Quando, Allora: in quel caso il test non è superato. Almeno un caso di errore e i casi limite di `CLAUDE.md` dove pertinenti (importi, timeout, stati del pagamento).
- Uno split obbligatorio per PAY-118 (21 punti), senza stime sulle nuove story.
- Il rimando "da passare a domain-review" se la story rientra nell'Ambito di `domain/fintech.md` (pagamenti, identità, dati personali). Fino al 17/11 l'agente di dominio era fintech-compliance: gli esiti qui sotto portano ancora quel nome.
- Righe aggiunte a `output/AAAA-MM-GG_proposte.md`, con il nome del report all'inizio del motivo.

Esito: **superato** se tutti i punti ci sono.

## Test 3: prioritizer, stessa classifica

Richiesta: "Lancia prioritizer su `backlog/2026-11-13_snapshot.json`".

Deve riprodurre `output/2026-11-13_prioritizer.md`:

| # | Item | BV | TC | RR | Job size | WSJF |
|---|---|---|---|---|---|---|
| 1 | notion:PAYDISC-1 / jira:DEMO-2 | 13 | 13 | 8 | 8 | 4,3 |
| 2 | notion:PAYDISC-3 / jira:DEMO-3 | 13 | 5 | 8 | 13 | 2,0 |
| 3 | notion:PAYDISC-5 | 5 | 3 | 2 | 5 | 2,0 |
| 4 | notion:PAYDISC-2 / jira:DEMO-9 | 8 | 5 | 3 | 13 | 1,2 |

Deve inoltre:

- mettere PAYDISC-3 prima di PAYDISC-5 dichiarando la regola di parità (time criticality 5 contro 3);
- mostrare opportunità ed epica collegate su una riga sola, dichiarando l'eredità;
- elencare tra i non calcolabili notion:PAYDISC-4 (mancano tutti e quattro i valori) e jira:DEMO-4 (nessuna opportunità di origine);
- non calcolare WSJF sulle story.

Esito: **superato** solo se classifica, valori e non calcolabili coincidono. **Se qualcosa è diverso, fermati e avvisa il PO.**

## Test 4: sprint-prep, stesso formato

Richiesta: "Lancia sprint-prep su PAY Sprint 1 usando `backlog/2026-11-13_snapshot.json`".

Confronta con `output/2026-11-13_sprint-prep.md`. Il report deve avere:

- Sintesi, bozza di sprint goal con 1-2 alternative, verifica della Definition of Ready, capacità, rischi, scalette di review e retrospettiva, azioni proposte, dati mancanti.
- I 5 candidati (DEMO-5, 6, 7, 8, 10), nessuno pronto sulla Definition of Ready.
- Velocity dichiarata non calcolabile: servono 3 sprint chiusi (valore letto da "Parametri del backlog").
- Team e durata dello sprint presi da `CLAUDE.md` (6 sviluppatori, 1 QA, 2 settimane).
- Tra i rischi, gli item che rientrano nell'Ambito di `domain/fintech.md` senza un report di domain-review (fino al 17/11: il rimando a fintech-compliance).

Esito: **superato** se tutti i punti ci sono.

## Test 5: valore mancante in CLAUDE.md

Verifica che un agent generico non usi un default.

1. Nella copia `conto-business-test`, in `CLAUDE.md`, cancella la riga "Item fermi" dalla tabella "Parametri del backlog".
2. Apri una sessione nuova sulla copia.
3. Rilancia il test 1.

Atteso: il controllo 2 (item fermi) non viene eseguito, PAY-125 non è segnalato come fermo, e tra i dati mancanti compare "Item fermi" con l'indicazione della sezione "Parametri del backlog". Il report non deve usare 28 giorni né un altro numero.

Esito: **superato** se l'agent non usa nessuna soglia e segnala il valore mancante.

## Esiti

| Test | Data | Esito | Note |
|---|---|---|---|
| 1 backlog-health | 2026-11-14 | superato | 7 difetti su 7, casi sani non segnalati. In più: sospetto duplicato notion:PAYDISC-3 / jira:PAY-102 (Critico), WSJF non calcolabile su PAY-102 e PAY-109, label con maiuscole diverse tra i tool. |
| 2 story-refiner | 2026-11-14 | superato | Sezioni Sintesi (3 righe), Problemi (Critico/Medio/Basso), dettaglio per story, Azioni, Dati mancanti presenti. Formato story con titolare di PMI e addetto all'amministrazione. INVEST con 6 righe per story. 50 `Scenario` Gherkin con Dato/Quando/Allora/E su righe separate, casi di errore, importi (1.219,99 contro 1.220,00), timeout, stati. PAY-118 divisa in S1-S4 senza stime, PAY-131 in caso esatto + S5-S7. Rimando a fintech-compliance per entrambe. 12 righe nuove nelle proposte. Fuori test: l'agent ha creato per errore un file vuoto `scratchpad_unused.txt` nella radice. |
| 3 prioritizer | 2026-11-14 | superato | Classifica, valori, parità e non calcolabili identici al 13/11. In più usa i link "blocca" presenti nello snapshot, che il report del 13/11 dava assenti, e propone una riga condizionata (DEMO-10 blocca DEMO-5). La differenza sui link non dipende dagli agent: lo snapshot del 13 era stato rigenerato nel pomeriggio, dopo la creazione dei link, sopra la versione letta dai report del mattino (vedi [Problemi incontrati](docs/guida.md#12-problemi-incontrati-e-come-si-risolvono) nella guida). |
| 4 sprint-prep | 2026-11-14 | superato | Tutti i punti presenti. Differenze: DoR punto 5 di DEMO-6 ora ok grazie ai link "blocca"; ordine delle sezioni diverso; nessuna riga nuova in proposte. La differenza sui link non dipende dagli agent: lo snapshot del 13 era stato rigenerato nel pomeriggio, dopo la creazione dei link, sopra la versione letta dai report del mattino (vedi [Problemi incontrati](docs/guida.md#12-problemi-incontrati-e-come-si-risolvono) nella guida). |
| 5 valore mancante | 2026-11-14 | superato | Controllo 2 non eseguito, PAY-125 non segnalato come fermo, "Item fermi" tra i dati mancanti con rimando a Parametri del backlog, nessuna soglia in giorni. Eseguito in una sessione nuova. La riga "Item fermi" è ancora tolta da `CLAUDE.md` della copia. |

Alla fine la cartella `conto-business-test` si può archiviare o tenere per i test successivi. Non contiene niente che serva all'originale.
