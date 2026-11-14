# Change-log 2026-11-14

Scritture eseguite dalla sessione principale. Dati fittizi (demo). Prosegue `2026-11-13_change-log.md`: le scritture sono state fatte poco dopo la mezzanotte, a chiusura del giro sulla riconciliazione.

## Jira

Decisioni del PO, chiamate mostrate prima dell'esecuzione, eseguite con il suo OK dato in anticipo per queste due. `editJiraIssue` riscrive il campo `description` per intero in markdown. Entrambe rilette da Jira dopo la scrittura.

| Id | Campo | Prima | Dopo | Strumento | Esito |
|---|---|---|---|---|---|
| jira:DEMO-9 | descrizione, sezione "Regole di riconciliazione" | 6 punti | 8 punti: aggiunti, prima di "Le story di questa epica applicano queste regole", "Fattura candidata: fattura da incassare o incassata in parte il cui numero compare nella causale dell'incasso." e "Abbinamento automatico: solo per un incasso di importo uguale al residuo di una sola fattura candidata (DEMO-8) e, dopo il rilascio di DEMO-16, per un incasso pari alla somma dei residui di più fatture candidate dello stesso cliente. Ogni altro abbinamento, compresi i pagamenti parziali, lo fa l'addetto." | editJiraIssue | eseguita, verificata |
| jira:DEMO-8 | criterio 7 | "…allora l'abbinamento è rimosso, la fattura torna da incassare e la rimozione è nell'audit trail" | "…allora l'abbinamento è rimosso, il residuo della fattura aumenta dell'importo stornato e il suo stato si ricalcola secondo le regole di DEMO-9 e la rimozione è nell'audit trail" | editJiraIssue | eseguita, verificata: descrizione e altri 8 criteri invariati |

## Proposte

In `2026-11-13_proposte.md`, sezione "terzo passaggio": 3 righe segnate come applicate (DEMO-8 criterio 7; DEMO-9 regole 2 e 3; DEMO-16 criteri 2 e 3, queste ultime due in altra forma, con le nuove regole di DEMO-9). Restano "in attesa" DEMO-8 (descrizione) e DEMO-9 (regola 5 e storno).

## Non fatto, per decisione del PO

- backlog-sync e backlog-health non sono stati rilanciati dopo queste due scritture: lo snapshot `backlog/2026-11-13_snapshot.json` (19:11) non le contiene.
- Le 7 lacune e le altre proposte "in attesa" restano per il refinement con il team.
