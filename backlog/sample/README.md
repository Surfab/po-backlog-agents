# Dati di esempio

10 item fittizi: 7 da Jira (progetto PAY) e 3 da Notion (database Discovery). Servono a provare gli agent senza toccare i tool.

## Difetti piantati apposta

Se backlog-health non li trova tutti, qualcosa non va.

| Id | Difetto |
|---|---|
| jira:PAY-131 e notion:PAYDISC-2 | duplicato tra tool: stessa funzione (riconciliazione fatture SDI) |
| notion:PAYDISC-2 | opportunità validata senza epica su Jira |
| jira:PAY-131 | story senza epica, senza stima, senza acceptance criteria |
| jira:PAY-118 | story da 21 punti (oltre 13), senza acceptance criteria, non nel formato story |
| jira:PAY-125 | fermo da più di 4 settimane, bloccato, dipende da jira:PAY-140 che non esiste nello snapshot |
| jira:PAY-109 | epica vuota |
| notion:PAYDISC-3 | manca risk reduction: WSJF non calcolabile |

## Item sani, di controllo

- jira:PAY-112: story completa, 5 punti, in sprint.
- notion:PAYDISC-1 e jira:PAY-101: opportunità ed epica collegate. Non sono un duplicato.

## Caso non piantato

Il campione contiene anche un difetto che non è stato messo apposta: **notion:PAYDISC-3 e jira:PAY-102** rispondono allo stesso bisogno, ma non c'è nessun collegamento tra i due. Non c'è una dipendenza `realizza` e non c'è la chiave dell'epica nel campo "Epica Jira". Se backlog-health lo segnala, come duplicato o come mancato collegamento tra discovery e delivery, il risultato è corretto e non è un falso positivo. Non entra nel conteggio dei 7 difetti del test 1.

## Cosa manca di proposito

Nessuno sprint chiuso: sprint-prep deve dire che la velocity non si può calcolare.
