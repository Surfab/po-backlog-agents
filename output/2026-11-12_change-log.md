# Change-log 2026-11-12

Scritture eseguite dalla sessione principale. Dati fittizi (demo).

## Notion

| Azione | Oggetto | OK del PO |
|---|---|---|
| creata pagina | PAY Product | sì ("crea tu i dati demo") |
| creato database | Discovery | sì |
| create 5 pagine | PAYDISC-1 … PAYDISC-5 | sì |

## Jira (TUO-SITO.atlassian.net, spazio PAY, chiave DEMO)

OK del PO: "OK per tutte e 7 le creazioni". Tool: `createJiraIssue`. Campi impostati: tipo, titolo, descrizione, etichette, parent.

| Chiave | Tipo | Titolo | Id nel CSV di esempio | Esito |
|---|---|---|---|---|
| DEMO-2 | Epic | Verification of Payee sui bonifici istantanei | PAY-101 | creata |
| DEMO-3 | Epic | Onboarding KYB digitale | PAY-102 | creata |
| DEMO-4 | Epic | Carte virtuali usa e getta | PAY-109 | creata |
| DEMO-5 | Story | Mostrare il nome del titolare dell'IBAN prima della conferma (parent DEMO-2) | PAY-112 | creata |
| DEMO-6 | Story | Gestione completa onboarding KYB (parent DEMO-3) | PAY-118 | creata |
| DEMO-7 | Bug | Bonifico istantaneo resta in stato pending dopo il timeout (parent DEMO-2) | PAY-125 | creata |
| DEMO-8 | Story | Abbinamento automatico tra fatture elettroniche e incassi (senza epica, di proposito) | PAY-131 | creata |

## Modifiche successive

OK del PO: "OK per tutte e 4".

| Id | Tool | Campo | Prima | Dopo | Strumento | Esito |
|---|---|---|---|---|---|---|
| jira:DEMO-5 | jira | Story point estimate | vuoto | 5 | editJiraIssue | fatto |
| jira:DEMO-6 | jira | Story point estimate | vuoto | 21 | editJiraIssue | fatto, riletto |
| jira:DEMO-7 | jira | Flagged | vuoto | Impediment | editJiraIssue | fatto, riletto |
| jira:DEMO-5 | jira | stato | Da completare | In corso | transitionJiraIssue | fatto |
| jira:DEMO-2 | jira | stato | Da completare | In corso | transitionJiraIssue | fatto |
| notion:PAYDISC-1 | notion | Epica Jira | PAY-101 | DEMO-2 | notion-update-page | fatto |

## Proposte di backlog-health applicate

OK del PO: "1) si" (righe 1, 2, 3 e 6 di `2026-11-12_proposte.md`).

| Id | Tool | Campo | Prima | Dopo | Strumento | Esito |
|---|---|---|---|---|---|---|
| jira:DEMO-9 | jira | nuovo item (epic) | non esiste | "Riconciliazione automatica fatture SDI con gli incassi" | createJiraIssue | creata |
| jira:DEMO-8 | jira | parent | vuoto | DEMO-9 | editJiraIssue | fatto, riletto |
| notion:PAYDISC-2 | notion | Epica Jira | vuoto | DEMO-9 | notion-update-page | fatto |
| jira:DEMO-1 | jira | stato | Da completare | Completata | transitionJiraIssue | fatto |

Non applicate: riga 4 (PAYDISC-3 verso DEMO-3, serve una decisione del PO) e riga 5 (divisione di DEMO-6, passa da story-refiner).

Nota: lo snapshot `backlog/2026-11-12_snapshot.json` è precedente a queste modifiche.

## 2026-11-13, proposte di fintech-compliance su DEMO-5

OK del PO: "concordo su tutto. procedi".

| Id | Tool | Campo | Prima | Dopo | Strumento | Esito |
|---|---|---|---|---|---|---|
| jira:DEMO-5 | jira | label | bonifici | bonifici, compliance | editJiraIssue | fatto |
| jira:DEMO-5 | jira | descrizione | 3 criteri | 3 criteri più sezione "Criteri proposti, in attesa del parere di compliance" (P1-P6) | editJiraIssue | fatto |
| jira:DEMO-10 | jira | nuovo item (task) | non esiste | "Parere di compliance, DPO e legal sulla verifica del beneficiario", parent DEMO-2 | createJiraIssue | creato |

Scelta della sessione principale: P1-P6 non sono entrati tra gli acceptance criteria veri, perché contengono parti da definire e la proposta stessa dice di applicarli dopo il parere.
