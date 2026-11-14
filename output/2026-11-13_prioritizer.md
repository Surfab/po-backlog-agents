# Prioritizer WSJF, 2026-11-13

Fonte: `./backlog/2026-11-13_snapshot.json` (21 item: 5 Notion, 16 Jira). Dati fittizi (demo).
Perimetro: 5 opportunità Notion (PAYDISC-1 … PAYDISC-5) e 4 epiche Jira aperte (DEMO-2, DEMO-3, DEMO-4, DEMO-9).
Regola applicata: quella di oggi in `CLAUDE.md` e `.claude/agents/prioritizer.md`. Job size = `wsjf.job_size` (campo "Job Size" di Notion); l'epica collegata con `realizza` eredita tutti e quattro i valori; la somma dei figli non si usa.

## 1. Sintesi

- Calcolabili 4 righe su 6 (7 item su 9): prima PAYDISC-1 / DEMO-2 (Verification of Payee) con 4,3, poi PAYDISC-3 / DEMO-3 (KYB) e PAYDISC-5 (SDD) a 2,0, ultima PAYDISC-2 / DEMO-9 (riconciliazione) con 1,2.
- Non calcolabili PAYDISC-4 (mancano tutti e quattro i valori) e DEMO-4 (nessuna opportunità che la origina, quindi nulla da ereditare).
- L'ordine regge solo in parte alla prova dei blocchi: la prima in classifica dipende da un parere di compliance non ancora raccolto e da un bug con impedimento, la terza è "In attesa" senza motivo registrato. Nessuna modifica con valore proposto dall'agent.

## 2. Classifica WSJF

| # | Item | BV | TC | RR | Job size | Calcolo | WSJF |
|---|---|---|---|---|---|---|---|
| 1 | notion:PAYDISC-1 / jira:DEMO-2 | 13 | 13 | 8 | 8 | (13 + 13 + 8) / 8 = 34 / 8 = 4,25 | 4,3 |
| 2 | notion:PAYDISC-3 / jira:DEMO-3 | 13 | 5 | 8 | 13 | (13 + 5 + 8) / 13 = 26 / 13 | 2,0 |
| 3 | notion:PAYDISC-5 | 5 | 3 | 2 | 5 | (5 + 3 + 2) / 5 = 10 / 5 | 2,0 |
| 4 | notion:PAYDISC-2 / jira:DEMO-9 | 8 | 5 | 3 | 13 | (8 + 5 + 3) / 13 = 16 / 13 = 1,23 | 1,2 |

Parità a 2,0 tra la riga 2 e la riga 3: prima PAYDISC-3 / DEMO-3 perché ha time criticality 5 contro 3.

Razionale:

1. PAYDISC-1 / DEMO-2: DEMO-2 eredita i quattro valori da PAYDISC-1 (relazione `realizza`). Il bonifico istantaneo non si richiama; evidenze: 12 interviste su 15 citano la paura dell'errore di IBAN, 38 ticket nel trimestre. Costo del ritardo più alto dello snapshot su un job size medio.
2. PAYDISC-3 / DEMO-3: DEMO-3 eredita i quattro valori da PAYDISC-3 (relazione `realizza`). Il 41% delle PMI abbandona al caricamento dei documenti societari (funnel a 90 giorni); valore alto, ma job size al massimo della scala.
3. PAYDISC-5: nessuna epica collegata, valori propri. Costo del ritardo basso (8 richieste dal commerciale) compensato da un job size piccolo.
4. PAYDISC-2 / DEMO-9: DEMO-9 eredita i quattro valori da PAYDISC-2 (relazione `realizza`). 6 ore al mese di riconciliazione manuale, 64% dei clienti su foglio di calcolo; valore discreto diviso per il job size massimo.

### Non calcolabili

| id | BV | TC | RR | Job size | Cosa manca | Dove va inserito |
|---|---|---|---|---|---|---|
| notion:PAYDISC-4 | manca | manca | manca | manca | tutti e quattro i valori; nessuna evidenza | Notion, database "Discovery": "Business Value", "Time Criticality", "Risk Reduction", "Job Size" |
| jira:DEMO-4 | manca | manca | manca | manca | tutti e quattro i valori: nessuna opportunità Notion ha una relazione `realizza` verso DEMO-4, quindi non c'è nulla da ereditare | Notion: un'opportunità che la origini, con "Epica Jira" = DEMO-4 e i quattro valori |

## 3. Problemi

### Critico

- jira:DEMO-2 (riga 1, WSJF 4,3): la prima in classifica non può chiudersi finché restano aperti due suoi figli. DEMO-10 (parere di compliance, DPO e legal, "Da completare") condiziona il completamento di DEMO-5, già "In corso"; DEMO-7 ha il flag "Impediment". Il punteggio dice di farla per prima, i blocchi dicono che la data di chiusura non dipende solo dal team.

### Medio

- notion:PAYDISC-5 (riga 3, WSJF 2,0): è "In attesa" (bloccata) senza motivo né dipendenza registrati, e sta sopra PAYDISC-2 / DEMO-9 che non è bloccata. Finché il blocco non è chiarito, la posizione 3 non è utilizzabile per il planning.
- notion:PAYDISC-3 / jira:DEMO-3 (riga 2): l'opportunità è ancora "In ricerca", ma la story DEMO-6 dell'epica è già in PAY Sprint 1 e non è stimata. Il job size 13 è stato assegnato a discovery non conclusa.
- jira:DEMO-3: confronto tra job size dichiarato e figli non verificabile. I quattro figli (DEMO-6, DEMO-11, DEMO-12, DEMO-13) non hanno stima; la descrizione di DEMO-6 ricorda che il flusso intero valeva 21 punti prima della divisione, contro un job size dichiarato di 13. Da ricontrollare quando il team stima.
- jira:DEMO-9 (riga 4): ultima in classifica, ma DEMO-8 è in PAY Sprint 1, il cui obiettivo in bozza riguarda la verifica del beneficiario. Scelta legittima del PO, da fare in modo consapevole.
- jira:DEMO-4: epica aperta senza opportunità di origine, senza story e senza evidenze. Resta fuori dalla classifica.

### Basso

- jira:DEMO-2: i figli stimati sommano 10 (DEMO-5 = 5, DEMO-7 = 3, DEMO-10 = 2) contro un job size dichiarato di 8. Scarto contenuto, non "di molto": solo da tenere d'occhio.
- jira:DEMO-9: figli stimati 5 (solo DEMO-8) su 13 dichiarati, con tre story non stimate (DEMO-14, DEMO-15, DEMO-16). Confronto non ancora possibile.
- notion:PAYDISC-4: ipotesi "Da esplorare" senza evidenze e senza valori. Normale in questa fase.
- jira:DEMO-5, DEMO-7, DEMO-10: i legami di blocco sono solo nel testo di DEMO-10; `issuelinks` è vuoto su tutte le issue. I contrasti tra ordine WSJF e dipendenze si ricavano a mano, non dallo snapshot.
- notion:PAYDISC-1 / jira:DEMO-2: 4,25 arrotondato a 4,3. L'arrotondamento non cambia l'ordine.

## 4. Azioni proposte

1. jira:DEMO-10: raccogliere il parere di compliance, DPO e legal prima del planning, o decidere se DEMO-5 prosegue dietro feature flag. È la condizione per chiudere la prima riga della classifica.
2. jira:DEMO-7: rimuovere l'impedimento o registrarne la causa; è nella stessa epica e nello stesso sprint.
3. notion:PAYDISC-5: registrare il motivo di "In attesa". Domande: che cosa la blocca e chi lo può sbloccare? Le 8 richieste del commerciale hanno una scadenza o un contratto legato? Se il blocco dura, la time criticality 3 è ancora corretta?
4. notion:PAYDISC-3: confermare il job size 13 con il team dopo la stima di DEMO-6, DEMO-11, DEMO-12, DEMO-13. Domanda: il 13 copre tutte e quattro le story o solo il primo rilascio?
5. jira:DEMO-6: farla stimare dal team prima che entri in sprint (punto 3 della Definition of Ready).
6. jira:DEMO-8: il PO conferma o rivede la presenza in PAY Sprint 1, visto che l'epica DEMO-9 è ultima per WSJF.
7. jira:DEMO-4: il PO chiarisce da quale opportunità nasce. Domande: quale problema dei clienti risolve e con quali evidenze? Va collegata a PAYDISC-4 (carte) o è un bisogno diverso? Senza opportunità, l'epica è prematura?
8. notion:PAYDISC-4: prima dei valori serve discovery. Domande: quanti clienti hanno chiesto limiti per carta? Che cosa succede se arriva tra sei mesi invece che tra uno? Riduce un rischio di spesa non autorizzata misurabile? Quanto è grande rispetto a PAYDISC-5 (job size 5)?
9. jira:DEMO-10, jira:DEMO-7: registrare su Jira come link i legami con DEMO-5, così il prossimo snapshot li riporta tra le dipendenze.

### Modifiche proposte

| id | tool | campo | valore attuale | valore proposto | motivo |
|---|---|---|---|---|---|
| (nessuna) | | | | | I valori mancanti li assegnano PO e team: l'agent non propone valori. `./output/2026-11-13_proposte.md` non è stato toccato. |

## 5. Dati mancanti

| Cosa manca | Tool | A cosa serve |
|---|---|---|
| Business Value, Time Criticality, Risk Reduction, Job Size di PAYDISC-4 | Notion | calcolare il WSJF |
| Opportunità di origine di DEMO-4 (campo "Epica Jira" su un'opportunità) | Notion | ereditare i quattro valori |
| Motivo dello stato "In attesa" di PAYDISC-5 | Notion | capire se il blocco contrasta con la posizione 3 |
| Stime di DEMO-6, DEMO-11, DEMO-12, DEMO-13 | Jira | confrontare i figli con il job size 13 di DEMO-3 |
| Stime di DEMO-14, DEMO-15, DEMO-16 | Jira | confrontare i figli con il job size 13 di DEMO-9 |
| Link di dipendenza tra DEMO-5, DEMO-7, DEMO-10 (`issuelinks` vuoto) | Jira | rilevare i contrasti tra ordine WSJF e blocchi |
| Causa dell'impedimento di DEMO-7 | Jira | valutare quanto pesa sulla chiusura di DEMO-2 |
| Team (customfield_10001 non restituito dal connettore) | Jira | sapere chi è impegnato su cosa |
| Corpo delle pagine Notion (non letto dal sync) | Notion | razionale più solido su ipotesi e metriche |
