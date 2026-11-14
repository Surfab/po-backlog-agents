# Change-log 2026-11-13

Scritture eseguite dalla sessione principale. Dati fittizi (demo).

Per questa demo il PO ha delegato alla sessione principale le decisioni di prodotto elencate qui sotto, che restano da confermare con il team.

## Decisioni prese al posto del PO

| Decisione | Scelta |
|---|---|
| Job size di opportunità ed epiche | valore esplicito su Notion, campo "Job Size". Niente somma dei figli |
| PAYDISC-3 e DEMO-3 | collegate |
| Split di DEMO-6 e DEMO-8 | applicati come proposto da story-refiner |
| DEMO-8, tipo di fatture | emesse |
| DEMO-8, regola di abbinamento | numero della fattura nella causale dell'incasso |
| DEMO-5 in attesa del parere | sviluppo dietro feature flag |

## Valori inventati per la demo

Non vengono da un team né da un PO reale.

| Dove | Campo | Valore |
|---|---|---|
| notion:PAYDISC-1 | Job Size | 8 |
| notion:PAYDISC-2 | Job Size | 13 |
| notion:PAYDISC-3 | Job Size, Risk Reduction | 13, 8 |
| notion:PAYDISC-5 | Job Size | 5 |
| jira:DEMO-7 | story point | 3 |
| jira:DEMO-8 | story point | 5 |
| jira:DEMO-10 | story point | 2 |

## Notion

| Id | Campo | Prima | Dopo | Strumento |
|---|---|---|---|---|
| database Discovery | schema | senza "Job Size" | colonna numerica "Job Size" | notion-update-data-source |
| notion:PAYDISC-1 | Job Size | vuoto | 8 | notion-update-page |
| notion:PAYDISC-2 | Job Size | vuoto | 13 | notion-update-page |
| notion:PAYDISC-3 | Job Size, Risk Reduction, Epica Jira | vuoti | 13, 8, DEMO-3 | notion-update-page |
| notion:PAYDISC-5 | Job Size | vuoto | 5 | notion-update-page |

## Jira

| Id | Campo | Prima | Dopo | Strumento |
|---|---|---|---|---|
| jira:DEMO-6 | titolo, descrizione, criteri, label, stima | "Gestione completa onboarding KYB", 21 punti, nessun criterio | "Caricare online i documenti societari", 7 criteri, label compliance, stima vuota | editJiraIssue |
| jira:DEMO-8 | titolo, descrizione, criteri | "Abbinamento automatico tra fatture elettroniche e incassi", nessun criterio | caso esatto, 9 criteri | editJiraIssue |
| jira:DEMO-11 | nuova story | | "Dati dell'impresa dalla visura", parent DEMO-3 | createJiraIssue |
| jira:DEMO-12 | nuova story | | "Dichiarare il titolare effettivo", parent DEMO-3 | createJiraIssue |
| jira:DEMO-13 | nuova story | | "Invio della pratica KYB, verifica ed esito", parent DEMO-3 | createJiraIssue |
| jira:DEMO-14 | nuova story | | "Abbinare a mano gli incassi non abbinati", parent DEMO-9 | createJiraIssue |
| jira:DEMO-15 | nuova story | | "Registrare gli incassi parziali", parent DEMO-9 | createJiraIssue |
| jira:DEMO-16 | nuova story | | "Abbinare un incasso a più fatture", parent DEMO-9 | createJiraIssue |
| sprint | nuovo sprint | nessuno | "PAY Sprint 1", 16-27 novembre 2026, futuro | manageJiraSprint (create) |
| DEMO-5, 6, 7, 8, 10 | sprint | nessuno | PAY Sprint 1 | manageJiraSprint (addIssues) |
| DEMO-7, 8, 10 | story point | vuoti | 3, 5, 2 | manageJiraSprint (addIssues) |
| jira:DEMO-5 | commento | | decisione sul feature flag | addOrEditJiraIssueComment |

I criteri delle story sono stati condensati su una riga per scenario rispetto al report di story-refiner. Le parti da definire sono tra parentesi quadre.

## File di progetto (su richiesta del PO)

| File | Punto | Prima | Dopo | Strumento |
|---|---|---|---|---|
| CLAUDE.md | sezione WSJF, in fondo | assente | "- Il WSJF si calcola su opportunità ed epiche. Le story dentro un'epica le ordina a mano il PO." | Edit |
| adapters/jira.md | riga di stato | "Story point (DEMO-6 = 21)"; sprint "va ricontrollato al prossimo sync" | "Story point verificato su issue reali il 2026-11-13"; sprint verificato su 5 issue (DEMO-5, 6, 7, 8, 10) | Edit |
| adapters/jira.md | elenco `fields` della query standard | senza `customfield_10015` e `duedate` | con `customfield_10015` e `duedate` | Edit |
| adapters/jira.md | mappa dei campi, `descrizione` e `acceptance_criteria` | nessuna regola sugli escape | escape `\[` `\]` rimossi; marcatore di punto elenco tolto dai criteri | Edit |
| adapters/jira.md | limiti noti, DEMO-1 | "non fa parte del backlog demo" | aggiunto: resta nella query ed è nello snapshot come item di prova, già completato | Edit |
| adapters/notion.md | mappa dei campi, riga `extra` | "Evidenze", "Epica Jira", "Area" | aggiunto "Opp ID" | Edit |

## Scritture richieste e non eseguite

Richieste dal PO il 2026-11-13, ferme perché il connettore Atlassian ha smesso di rispondere (errori del server, poi "This connector can't be used with the current credential"). Nessuna chiamata di scrittura è partita.

| Id | Campo | Richiesto | Esito |
|---|---|---|---|
| jira:DEMO-12 | link | bloccata da DEMO-11 | tentata con addGraphContext (`jira-work-item-blocks-jira-work-item`) dopo l'OK del PO: FAILED, il tool non risolve il tipo di link "blocks" su questo sito (nomi localizzati). Nessun link creato, verificato rileggendo DEMO-12. Da rifare con `createJiraIssueLink` |
| jira:DEMO-13 | link | bloccata da DEMO-6, DEMO-11, DEMO-12 | non eseguita in quel momento; fatta dopo, vedi sotto |
| jira:DEMO-16 | link | bloccata da DEMO-8 | non eseguita in quel momento; fatta dopo, vedi sotto |
| jira:DEMO-14 | terzo acceptance criterion | sostituito da due criteri (pagamento parziale secondo DEMO-15; rifiuto con indicazione dell'eccedenza) | non eseguita: chiamata mostrata al PO, in attesa dell'OK |

## Jira, link tra issue (pomeriggio, connettore di nuovo attivo)

Tutte con `executeWrite`, operazione `createJiraIssueLink`, `linkType: "Blocks"`, dopo l'OK del PO. Verso verificato rileggendo le issue bloccate: il link compare come "is blocked by".

| Id | Campo | Prima | Dopo | Chiamata (inwardIssue → outwardIssue) | Esito |
|---|---|---|---|---|---|
| jira:DEMO-12 | issuelinks | vuoto | is blocked by DEMO-11 | DEMO-11 → DEMO-12 | creato, verificato |
| jira:DEMO-13 | issuelinks | vuoto | is blocked by DEMO-6 | DEMO-6 → DEMO-13 | creato, verificato |
| jira:DEMO-13 | issuelinks | | is blocked by DEMO-11 | DEMO-11 → DEMO-13 | creato, verificato |
| jira:DEMO-13 | issuelinks | | is blocked by DEMO-12 | DEMO-12 → DEMO-13 | creato, verificato |
| jira:DEMO-16 | issuelinks | vuoto | is blocked by DEMO-8 | DEMO-8 → DEMO-16 | creato, verificato |

## Jira, DEMO-14 (pomeriggio)

Con `editJiraIssue`, campo `description` riscritto per intero in markdown, dopo l'OK del PO (con una sua correzione al nuovo terzo criterio).

| Id | Campo | Prima | Dopo | Esito |
|---|---|---|---|---|
| jira:DEMO-14 | acceptance criteria, terzo criterio | "Dato un incasso da abbinare e una fattura di importo diverso, quando provo ad abbinarli, allora l'abbinamento è rifiutato" | due criteri: "Dato un incasso da abbinare inferiore al residuo di una fattura da incassare o incassata in parte, quando li abbino a mano, allora l'incasso è registrato come pagamento parziale secondo DEMO-15" e "Dato un incasso da abbinare superiore al residuo di una fattura, quando provo ad abbinarli a mano, allora l'abbinamento è rifiutato con l'indicazione dell'eccedenza" | eseguita, verificata rileggendo DEMO-14: 6 criteri, gli altri 4 e la descrizione invariati |

## Jira, regole di riconciliazione e criteri (sera)

Decisioni del PO sulle 5 proposte del secondo passaggio di backlog-health. Tutte dopo il suo OK, ognuna riletta da Jira dopo la scrittura. `editJiraIssue` riscrive il campo `description` per intero in markdown.

| Id | Campo | Prima | Dopo | Strumento | Esito |
|---|---|---|---|---|---|
| jira:DEMO-9 | descrizione | solo la frase iniziale | aggiunta la sezione "## Regole di riconciliazione" con 6 punti (totale e residuo, stati, incasso uguale, inferiore, superiore al residuo, annullamento) | editJiraIssue | eseguita, verificata |
| jira:DEMO-14 | criterio 2 | "Dato un incasso da abbinare e una fattura da incassare dello stesso importo, quando li abbino a mano, allora l'incasso risulta abbinato, la fattura incassata e l'abbinamento riporta chi l'ha fatto e quando" | "Dato un incasso da abbinare di importo uguale al residuo di una fattura da incassare o incassata in parte, quando li abbino a mano, allora l'incasso risulta abbinato, la fattura passa a incassata e l'abbinamento riporta chi l'ha fatto e quando" | editJiraIssue (una sola chiamata con il criterio 5) | eseguita, verificata |
| jira:DEMO-14 | criterio 5 | "Dato un abbinamento esistente, quando lo annullo, allora incasso e fattura tornano da abbinare e da incassare e l'annullamento è nell'audit trail" | "Dato un abbinamento esistente, quando lo annullo, allora l'incasso torna da abbinare, il residuo della fattura aumenta dell'importo annullato, lo stato della fattura si ricalcola secondo le regole di DEMO-9 e l'annullamento è nell'audit trail" | editJiraIssue (stessa chiamata) | eseguita, verificata |
| jira:DEMO-15 | frase iniziale | "inferiore al totale della fattura" | "inferiore al residuo della fattura" | editJiraIssue (una sola chiamata per le tre modifiche a DEMO-15) | eseguita, verificata |
| jira:DEMO-15 | descrizione | riga "Le parti tra parentesi quadre sono da definire." | riga tolta | editJiraIssue (stessa chiamata) | eseguita, verificata |
| jira:DEMO-15 | criterio 3 | "…allora [comportamento sull'eccedenza da definire]" | "…allora non viene abbinato, resta da abbinare e l'eccedenza di 100,00 EUR è indicata" | editJiraIssue (stessa chiamata) | eseguita, verificata |
| jira:DEMO-14, DEMO-15 | issuelinks | nessun link tra le due | DEMO-14 "relates to" DEMO-15 (link id 10005) | executeWrite, createJiraIssueLink, linkType "Relates" | creato, verificato su entrambe |
| jira:DEMO-16 | descrizione | solo la frase iniziale | aggiunta, prima di "## Acceptance criteria", la frase "Al rilascio, questa story sostituisce il criterio 3 di DEMO-8 (incasso che cita più fatture)." | editJiraIssue (una sola chiamata con il criterio 4) | eseguita, verificata |
| jira:DEMO-16 | criterio 4 | "Dato un incasso abbinato a più fatture, quando viene stornato, allora tutte le fatture collegate tornano da incassare" | "Dato un incasso abbinato a più fatture, quando viene stornato, allora il residuo di ciascuna fattura collegata aumenta della quota stornata e il suo stato si ricalcola secondo le regole di DEMO-9" | editJiraIssue (stessa chiamata) | eseguita, verificata |

In `2026-11-13_proposte.md` sono segnate come applicate le righe su DEMO-12 e DEMO-13 (dipendenze) e le 5 del secondo passaggio; le richieste dirette del PO senza riga di un agent sono in una sezione a parte.
