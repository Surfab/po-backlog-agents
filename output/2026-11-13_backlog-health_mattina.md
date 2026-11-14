# Backlog health, 2026-11-13

Fonte: `./backlog/2026-11-13_snapshot.json` (generato_il 2026-11-13T23:59:59Z, ora convenzionale). 21 item: 5 Notion, 16 Jira, tutti letti via connettore. Dati fittizi (demo). Nessuna scrittura su Notion o Jira.

Composizione contata: 5 opportunity, 4 epic, 9 story, 1 bug, 2 task. Stati: 13 todo, 3 in_progress, 2 blocked, 3 done. In "PAY Sprint 1" (futuro, 16-27 novembre 2026): 5 item (DEMO-5, 6, 7, 8, 10), 15 punti stimati su 4 item, 1 item senza stima.

## 1. Sintesi

- Sui 9 controlli: 0 duplicati, 0 item fermi, 0 stime fuori scala, 0 riferimenti rotti, 0 opportunità validate senza epica. Restano 2 problemi critici, entrambi dentro PAY Sprint 1 che parte il 16 novembre: jira:DEMO-7 bloccato senza causa collegata e jira:DEMO-6 senza stima.
- Problemi medi: 6 story fuori sprint senza stima (DEMO-11, 12, 13, 14, 15, 16), 1 opportunità bloccata senza spiegazione (notion:PAYDISC-5), 2 story con dipendenze scritte nei criteri ma non collegate (DEMO-12, DEMO-13), 1 opportunità ancora "In ricerca" con lavoro già in sprint (notion:PAYDISC-3).
- Ho aggiunto 4 righe in fondo a `2026-11-13_proposte.md`. Le altre azioni rimandano a 6 righe già "in attesa" (4 del 12 novembre su DEMO-7, 2 del 13 novembre su DEMO-6 e DEMO-7) che non ho duplicato.

### Esito dei 9 controlli

| # | Controllo | Trovati | Item |
|---|---|---|---|
| 1 | Duplicati | 0 | nessuno |
| 2 | Item fermi oltre 28 giorni | 0 | nessuno (aggiornamento più vecchio: 2026-11-12T20:47:00Z, notion:PAYDISC-4) |
| 3 | Story incomplete | 7 senza stima, 0 senza criteri | DEMO-6, 11, 12, 13, 14, 15, 16 |
| 4 | Stima fuori scala | 0 | stime presenti: 5, 3, 5, 2 |
| 5 | Dipendenze o parent inesistenti | 0 | 3 dipendenze e 11 parent controllati, tutti presenti |
| 6 | Epiche vuote | 1 | DEMO-4 |
| 7 | Item senza epica | 0 | (DEMO-1 escluso, vedi nota) |
| 8 | Opportunità validate senza epica | 0 | PAYDISC-1 → DEMO-2, PAYDISC-2 → DEMO-9 |
| 9 | Bloccati senza dipendenza | 2 | DEMO-7, PAYDISC-5 |

Coppie guardate per il controllo 1 e scartate:
- notion:PAYDISC-1 / jira:DEMO-2, notion:PAYDISC-2 / jira:DEMO-9, notion:PAYDISC-3 / jira:DEMO-3: opportunità ed epica che la realizza, collegate da `dipendenze` (`realizza`) e da `extra["Epica Jira"]`. Non sono duplicati. PAYDISC-2 e DEMO-9 hanno lo stesso titolo: è atteso.
- notion:PAYDISC-4 ("Limiti di spesa per singola carta aziendale") / jira:DEMO-4 ("Carte virtuali usa e getta"): stessa label `carte`, bisogni diversi (controllo della spesa per carta, carte monouso per acquisti online). Non duplicati.
- jira:DEMO-8, 14, 15, 16: stessa epica e stessa label, ma casi distinti dello split (caso esatto, abbinamento manuale, incassi parziali, un incasso su più fatture). Non duplicati.

Nota: jira:DEMO-1 è l'item di prova, già completato. Non ha parent, descrizione né label, ma non è conteggiato in nessun controllo.

## 2. Problemi

### Critico

- **jira:DEMO-7** (bug, "Bonifico istantaneo resta in stato pending dopo il timeout"): `stato` blocked (flag "Impediment"), in PAY Sprint 1, `dipendenze` vuote. Nello snapshot non c'è nulla che spieghi il blocco: `issuelinks` è vuoto su tutte le 16 issue e la descrizione non cita la causa. Inoltre `acceptance_criteria` è vuoto. Sono ancora "in attesa" 4 righe del 12 novembre (descrizione con causa del flag, criteri, label, task sull'incidente operativo) e 1 riga del 13 novembre (uscita dallo sprint).
- **jira:DEMO-6** (story, "Caricare online i documenti societari"): `stima` null, in PAY Sprint 1. Non supera il punto 3 della Definition of Ready. La scala di gravità mette "story senza stima" in Medio: l'ho alzata a Critico perché la story è in uno sprint che parte fra 3 giorni. Con lettura letterale è Medio. È già "in attesa" la riga di sprint-prep che la toglie dallo sprint.

### Medio

- **jira:DEMO-11, DEMO-12, DEMO-13** (epica DEMO-3) e **jira:DEMO-14, DEMO-15, DEMO-16** (epica DEMO-9): 6 story con `stima` null, fuori sprint. Tutte hanno criteri (5, 6, 7, 5, 4, 4). La stima spetta al team.
- **notion:PAYDISC-5** ("Incasso ricorrente con SDD per abbonamenti B2B"): `stato` blocked ("In attesa"), `dipendenze` vuote, `extra["Epica Jira"]` null. Nessuna spiegazione dell'attesa nello snapshot.
- **jira:DEMO-13**: il primo criterio richiede "documenti caricati, dati dell'impresa confermati e titolare effettivo dichiarato", cioè il risultato di DEMO-6, DEMO-11 e DEMO-12. `dipendenze` è vuoto (punto 5 della DoR).
- **jira:DEMO-12**: il primo criterio parte da "dati dell'impresa confermati", cioè il risultato di DEMO-11. `dipendenze` è vuoto.
- **jira:DEMO-5**: il legame con DEMO-10 (parere di compliance) è solo nel testo di DEMO-10. Riga già "in attesa" da sprint-prep, non duplicata.
- **notion:PAYDISC-3** ("Onboarding KYB in meno di 10 minuti"): `stato_originale` "In ricerca", ma l'epica che la realizza (DEMO-3) ha già 4 story e una, DEMO-6, è in PAY Sprint 1. Si rischia di costruire prima che la discovery sia chiusa. Non rientra nel controllo 8, che riguarda solo le "Validata".
- **Criteri con parti da definire** (fuori dai 9 controlli, ma tocca il punto 2 della DoR): 15 criteri su 50 contengono segnaposto tra parentesi quadre, in 6 story su 9. DEMO-6: 5 su 7. DEMO-11: 4 su 5. DEMO-13: 3 su 7. DEMO-8: 1 su 9. DEMO-12: 1 su 6. DEMO-15: 1 su 4. Senza segnaposto: DEMO-5, DEMO-14, DEMO-16. DEMO-6 e DEMO-8 sono in sprint.

### Basso

- **jira:DEMO-4** (epica "Carte virtuali usa e getta"): nessun item la indica come `parent` e nessuna opportunità Notion la realizza.
- **jira:DEMO-5** e **jira:DEMO-2**: `stato` in_progress mentre lo sprint è "future" (inizio 2026-11-16). DEMO-5 è in corso con `assegnatario` null.
- **Priorità Jira**: 16 item su 16 hanno priorità "Medium". Il campo non distingue nulla.
- **Epiche Jira DEMO-2, DEMO-3, DEMO-9**: `wsjf` null nello snapshot anche se le opportunità che le realizzano hanno i quattro valori. Secondo CLAUDE.md l'epica li eredita: è materia del prioritizer, qui solo segnalato.

## 3. Azioni proposte

Decide il Product Owner. Le righe nuove sono in `2026-11-13_proposte.md`, sezione "backlog-health, 2026-11-13".

1. **jira:DEMO-7**: prima del planning, decidere le 5 righe già "in attesa" (4 in `2026-11-12_proposte.md`, 1 in `2026-11-13_proposte.md`). Serve soprattutto la causa del flag "Impediment", collegata come dipendenza se è un altro item. Nessuna riga nuova: la causa non è nello snapshot e non la invento.
2. **jira:DEMO-6**: farla stimare dal team oppure applicare la riga di sprint-prep già "in attesa" che la toglie da PAY Sprint 1. Nessuna riga nuova.
3. **jira:DEMO-11, 12, 13, 14, 15, 16**: portarle al prossimo refinement per la stima del team. Nessuna riga: gli agent non stimano.
4. **jira:DEMO-13**: collegare come "bloccata da" DEMO-6, DEMO-11, DEMO-12 (riga nuova 1).
5. **jira:DEMO-12**: collegare come "bloccata da" DEMO-11 (riga nuova 2).
6. **notion:PAYDISC-5**: indicare su Notion che cosa tiene in attesa l'opportunità (riga nuova 3). Il valore lo conosce solo il PO.
7. **jira:DEMO-4**: decidere se l'epica ha una discovery alle spalle. Se no, archiviarla senza eliminarla (riga nuova 4, condizionata). Se sì, creare l'opportunità su Notion e collegarla.
8. **notion:PAYDISC-3**: decidere se la ricerca è chiusa (stato "Validata") oppure se il lavoro su DEMO-3 va tenuto fuori dallo sprint finché non lo è. Nessuna riga: lo stato di discovery lo conosce solo il PO, e la riga su DEMO-6 esiste già.
9. **jira:DEMO-5**: verificare con il team se è davvero in corso prima dell'inizio dello sprint e chi la segue. Nessuna riga.
10. **jira:DEMO-14, 15, 16**: chiedere al team se dipendono da DEMO-8. Dai criteri non è certo, quindi non propongo link.

## 4. Dati mancanti

| Cosa manca | Tool | A cosa servirebbe |
|---|---|---|
| Causa del blocco di DEMO-7 (nessun link, descrizione senza causa; i commenti non sono letti dal sync) | Jira | controllo 9, decidere se resta nello sprint |
| Motivo dell'attesa di PAYDISC-5 (il corpo della pagina non è letto dal sync) | Notion | controllo 9 |
| `assegnatario` null su 21 item su 21; Team (customfield_10001) non restituito per nessuna issue | Jira, Notion | sapere chi segue DEMO-5 in corso e gli item in sprint |
| Chi ha assegnato le stime: lo snapshot non lo dice. Da `2026-11-13_change-log.md` risulta che i punti di DEMO-7, DEMO-8 e DEMO-10 sono valori inseriti per la demo, non del team | Jira | punto 3 della DoR; il controllo 4 guarda solo la scala |
| Esito del passaggio da fintech-compliance per story: nessun campo nello snapshot | Jira | punto 6 della DoR |
| Opportunità a monte dell'epica DEMO-4 | Notion | controllo 6, decidere se tenerla |
| Valori WSJF e evidenze di PAYDISC-4 (tutti null; è un'ipotesi "Da esplorare") | Notion | prioritizzazione |
| Link tra issue: `issuelinks` vuoto su 16 issue su 16 | Jira | controllo 5 e punto 5 della DoR; oggi le dipendenze si leggono solo dal testo |
| Ora reale di generazione dello snapshot (usata 23:59:59Z per convenzione) | snapshot | controllo 2; oggi ininfluente, tutti gli item hanno meno di 2 giorni |
