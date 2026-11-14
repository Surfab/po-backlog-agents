# Backlog health, 2026-11-13 (secondo passaggio)

Fonte: `./backlog/2026-11-13_snapshot.json`, rigenerato nel pomeriggio (generato_il 2026-11-13T23:59:59Z, ora convenzionale; aggiornamento più recente letto: 2026-11-13T16:43:36+01:00, jira:DEMO-14). 21 item: 5 Notion, 16 Jira, tutti letti via connettore. Dati fittizi (demo). Nessuna scrittura su Notion o Jira. CLAUDE.md riletto dal disco: la sezione WSJF ora dice che il punteggio si calcola su opportunità ed epiche e che le story dentro un'epica le ordina a mano il PO.

Il report di stamattina è in `2026-11-13_backlog-health_mattina.md` (non toccato).

Composizione contata: 5 opportunity, 4 epic, 9 story, 1 bug, 2 task. Stati: 10 todo, 3 in_progress, 5 blocked, 3 done. In "PAY Sprint 1" (futuro, 16-27 novembre 2026): 5 item (DEMO-5, 6, 7, 8, 10), 15 punti stimati su 4 item, 1 item senza stima (DEMO-6).

## 1. Sintesi

- I 5 link "Blocks" ci sono e sono coerenti nei due versi: i bloccati passano da 2 a 5, ma 3 (jira:DEMO-12, 13, 16) sono blocchi di sequenza con causa collegata e fuori sprint. Restano senza causa gli stessi 2 di stamattina: jira:DEMO-7 (critico, in sprint) e notion:PAYDISC-5.
- Tra jira:DEMO-14 e jira:DEMO-15 resta 1 contradizione (annullamento di un abbinamento quando la fattura ha più incassi: criterio 5 di DEMO-14 contro il modello a residuo di DEMO-15) e 1 punto aperto che può diventarlo (eccedenza: rifiutata in DEMO-14, "da definire" in DEMO-15). I due criteri nuovi di DEMO-14 hanno risolto il caso "importo inferiore". In più: 1 contraddizione letterale tra DEMO-8 criterio 3 e DEMO-16 criterio 1, e DEMO-14 cita DEMO-15 senza link.
- Critici 2 (DEMO-7, DEMO-6), invariati. Ho accodato 5 righe nuove in `2026-11-13_proposte.md`, tutte su DEMO-14, 15, 16 e tutte "in attesa".

### Esito dei 9 controlli

| # | Controllo | Trovati | Item | Rispetto a stamattina |
|---|---|---|---|---|
| 1 | Duplicati | 0 | nessuno | uguale |
| 2 | Item fermi oltre 28 giorni | 0 | nessuno (aggiornamento più vecchio: 2026-11-12T20:47:00Z, notion:PAYDISC-4) | uguale |
| 3 | Story incomplete | 7 senza stima, 0 senza criteri | DEMO-6, 11, 12, 13, 14, 15, 16 | uguale |
| 4 | Stima fuori scala | 0 | stime presenti: 5, 3, 5, 2 | uguale |
| 5 | Dipendenze o parent inesistenti | 0 | 13 voci di dipendenza (3 `realizza`, 5 `bloccato_da`, 5 `blocca`) e 11 parent controllati, tutti presenti | da 3 a 13 voci |
| 6 | Epiche vuote | 1 | DEMO-4 | uguale |
| 7 | Item senza epica | 0 | (DEMO-1 escluso: item di prova completato) | uguale |
| 8 | Opportunità validate senza epica | 0 | PAYDISC-1 → DEMO-2, PAYDISC-2 → DEMO-9 | uguale |
| 9 | Bloccati senza dipendenza | 2 su 5 bloccati | DEMO-7, PAYDISC-5 | uguale (i 3 nuovi bloccati hanno la causa collegata) |

### Verifica delle modifiche dichiarate

- **5 link "Blocks"**: presenti in `extra.issuelinks` con id 10000-10004, 10 voci su 6 issue, ogni link visibile su entrambe le issue e con verso coerente. 10000: DEMO-11 blocca DEMO-12. 10001: DEMO-6 blocca DEMO-13. 10002: DEMO-11 blocca DEMO-13. 10003: DEMO-12 blocca DEMO-13. 10004: DEMO-8 blocca DEMO-16. Nessun ciclo. `dipendenze` riporta le stesse 10 voci.
- **Stato `blocked` da link**: DEMO-12, DEMO-13, DEMO-16, con `extra.motivo_blocked` valorizzato. Lo `stato_originale` su Jira resta "Da completare" per tutte e tre: il blocco è calcolato dal sync, non è uno stato Jira.
- **DEMO-14**: 6 criteri (erano 5). Il terzo e il quarto sono i due nuovi (pagamento parziale secondo DEMO-15; rifiuto con indicazione dell'eccedenza). `aggiornato_il` 2026-11-13T16:43:36+01:00.
- **DEMO-15**: non modificata da stamattina (`aggiornato_il` 2026-11-13T08:39:58+01:00). Il criterio 3 ha ancora il segnaposto sull'eccedenza.
- Le righe di stamattina su DEMO-13 e DEMO-12 corrispondono esattamente ai link presenti. Il link DEMO-8 → DEMO-16 non ha una riga nei due file proposte che ho letto (stamattina non lo avevo proposto perché dal testo non era certo); non ho letto il change-log.

### Blocchi: sequenza o senza causa

| Item | In sprint | Bloccato da | Stato dei bloccanti | Tipo di blocco |
|---|---|---|---|---|
| jira:DEMO-12 | no | DEMO-11 | todo, fuori sprint, senza stima | sequenza (dipendenza nota e collegata) |
| jira:DEMO-13 | no | DEMO-6, DEMO-11, DEMO-12 | DEMO-6 todo in PAY Sprint 1; DEMO-11 todo fuori sprint; DEMO-12 a sua volta bloccata da DEMO-11 | sequenza |
| jira:DEMO-16 | no | DEMO-8 | todo, in PAY Sprint 1 | sequenza |
| jira:DEMO-7 | sì | nessuna dipendenza | flag "Impediment", `issuelinks` vuoto | senza causa |
| notion:PAYDISC-5 | no | nessuna dipendenza | stato "In attesa" | senza causa |

I 3 blocchi di sequenza non sono impedimenti: dicono solo in che ordine si lavora. Nessuno dei 3 item è in sprint e nessun item in sprint è bloccato da un link. La catena KYB è DEMO-11 → DEMO-12 → DEMO-13, con DEMO-6 in parallelo: DEMO-13 non può partire prima che siano chiuse tre story, di cui due (DEMO-11, DEMO-12) non sono né stimate né pianificate.

Coppie guardate per il controllo 1 e scartate (come stamattina): PAYDISC-1 / DEMO-2, PAYDISC-2 / DEMO-9, PAYDISC-3 / DEMO-3 (opportunità ed epica che la realizza, collegate); PAYDISC-4 / DEMO-4 (stessa label `carte`, bisogni diversi); DEMO-8, 14, 15, 16 (casi distinti dello stesso split: non sono duplicati, ma i loro criteri si toccano, vedi sezione 2).

## 2. DEMO-14 e DEMO-15: ci sono ancora contraddizioni?

**Risposta: sì, ne resta 1 certa e 1 punto aperto che può diventarlo. Il caso "importo inferiore", che i due criteri nuovi dovevano sistemare, è ora coerente.**

Numerazione dei criteri usata qui sotto.

DEMO-14 ("Abbinare a mano gli incassi non abbinati"):
- 14.1 "Dato incassi da abbinare, quando apro la riconciliazione, allora li vedo in elenco con data, importo, ordinante e causale"
- 14.2 "Dato un incasso da abbinare e una fattura da incassare dello stesso importo, quando li abbino a mano, allora l'incasso risulta abbinato, la fattura incassata e l'abbinamento riporta chi l'ha fatto e quando"
- 14.3 "Dato un incasso da abbinare inferiore al residuo di una fattura da incassare o incassata in parte, quando li abbino a mano, allora l'incasso è registrato come pagamento parziale secondo DEMO-15"
- 14.4 "Dato un incasso da abbinare superiore al residuo di una fattura, quando provo ad abbinarli a mano, allora l'abbinamento è rifiutato con l'indicazione dell'eccedenza"
- 14.5 "Dato un abbinamento esistente, quando lo annullo, allora incasso e fattura tornano da abbinare e da incassare e l'annullamento è nell'audit trail"
- 14.6 "Dato nessun incasso da abbinare, quando apro la riconciliazione, allora vedo che non c'è nulla da abbinare"

DEMO-15 ("Registrare gli incassi parziali"):
- 15.1 "Dato una fattura da incassare di 1.000,00 EUR, quando le viene abbinato un incasso di 400,00 EUR, allora passa a incassata in parte con residuo 600,00 EUR"
- 15.2 "Dato una fattura incassata in parte con residuo 600,00 EUR, quando le viene abbinato un incasso di 600,00 EUR, allora passa a incassata con residuo 0,00 EUR"
- 15.3 "Dato una fattura con residuo 600,00 EUR, quando arriva un incasso di 700,00 EUR riferito a quella fattura, allora [comportamento sull'eccedenza da definire]"
- 15.4 "Dato una fattura incassata in parte con due incassi abbinati, quando uno viene stornato, allora il residuo aumenta dell'importo stornato e lo stato si aggiorna"

### Confronto caso per caso

| Caso | DEMO-14 | DEMO-15 | Esito |
|---|---|---|---|
| Incasso inferiore al residuo, fattura da incassare | 14.3: pagamento parziale secondo DEMO-15 | 15.1: incassata in parte, residuo 600 | Coerente. Risolto dal criterio nuovo |
| Incasso inferiore al residuo, fattura incassata in parte | 14.3: pagamento parziale secondo DEMO-15 | nessun criterio esplicito (15.1 parte da "da incassare", 15.2 dall'importo uguale al residuo) | Nessuna contraddizione. Lacuna: 14.3 rimanda a un comportamento che DEMO-15 non scrive (si deduce da 15.1: resta incassata in parte, residuo ridotto) |
| Incasso uguale all'importo, fattura da incassare | 14.2: fattura incassata | non trattato (non è un parziale) | Coerente |
| Incasso uguale al residuo, fattura incassata in parte | nessun criterio: 14.2 vale solo per "fattura da incassare dello stesso importo", 14.3 per "inferiore", 14.4 per "superiore" | 15.2: incassata, residuo 0 | Nessuna contraddizione. Lacuna in DEMO-14: l'abbinamento a mano del saldo di una fattura incassata in parte non ha un criterio |
| Incasso superiore al residuo | 14.4: abbinamento a mano rifiutato, con indicazione dell'eccedenza | 15.3: "[comportamento sull'eccedenza da definire]" | **Punto aperto, non ancora contraddizione** (vedi B) |
| Annullamento o storno con più incassi sulla stessa fattura | 14.5: la fattura torna "da incassare" | 15.4: il residuo aumenta dell'importo stornato e lo stato si aggiorna | **Contraddizione** (vedi A) |
| Elenco e caso vuoto | 14.1, 14.6 | non trattati | Nessun contatto |

### A. Contraddizione che resta: 14.5 contro 15.1 e 15.4

14.5 dice che annullando "un abbinamento esistente" la fattura torna "da incassare", senza condizioni. Ma 14.3 ora permette di abbinare a mano più incassi parziali alla stessa fattura. Esempio con i numeri di DEMO-15: fattura da 1.000,00 EUR con due incassi abbinati da 400,00 e 300,00 EUR (incassata in parte, residuo 300,00). Annullo l'abbinamento da 300,00:
- secondo 14.5 la fattura torna "da incassare";
- secondo il modello di DEMO-15 (15.1 e, per analogia, 15.4) una fattura con un incasso da 400,00 ancora abbinato è "incassata in parte" con residuo 600,00.

I due esiti non possono essere veri insieme. 14.5 è corretto solo se l'abbinamento annullato è l'unico della fattura. È un effetto collaterale della modifica di oggi: prima del criterio 14.3 il caso con più incassi non esisteva in DEMO-14. Nota: 15.4 parla di storno dell'incasso, 14.5 di annullamento dell'abbinamento da parte dell'utente. Sono eventi diversi, ma lo stato della fattura deve seguire la stessa regola del residuo.

### B. Punto aperto: 14.4 contro 15.3 (eccedenza)

Non è una contraddizione letterale, perché 15.3 non afferma ancora nulla. Ma:
- 14.4 ha già deciso per il canale manuale (rifiuto), mentre 15.3 lascia aperto lo stesso caso. 15.3 dice "arriva un incasso ... riferito a quella fattura", senza dire se l'abbinamento è automatico o manuale. Se il PO definisce 15.3 con un esito diverso dal rifiuto (per esempio abbinare fino al residuo e tenere l'eccedenza da parte) e non lo limita all'automatico, nasce la contraddizione con 14.4.
- Se invece l'esito è il rifiuto in entrambi i casi, un incasso in eccesso non si può chiudere in nessun modo: l'automatismo non lo abbina, l'utente non può abbinarlo. Questo contraddice il beneficio dichiarato di DEMO-14 ("chiudere la riconciliazione anche quando l'automatismo non decide"). Stesso vicolo cieco per il doppio pagamento di DEMO-8 criterio 6 (fattura già incassata, residuo zero: a mano ricade in 14.4).

### C. DEMO-8 e DEMO-16 sugli stessi casi

- **DEMO-8 criterio 2 contro DEMO-15, ambiguità**: DEMO-8 dice "Dato una fattura da incassare di 1.220,00 EUR e un incasso di 1.219,99 EUR con il suo numero in causale, quando l'incasso è contabilizzato, allora non nasce nessun abbinamento automatico, l'incasso resta da abbinare e la fattura da incassare". DEMO-15 usa il passivo "le viene abbinato" e nella descrizione "voglio che un incasso inferiore al totale della fattura sia registrato come pagamento parziale", senza dire chi abbina. Se DEMO-15 intende un abbinamento automatico degli incassi inferiori, contraddice DEMO-8 criterio 2. Se intende solo la registrazione dopo l'abbinamento a mano di DEMO-14 (catena: DEMO-8 lascia da abbinare → 14.3 abbina a mano → DEMO-15 registra il parziale), è coerente. Il testo non permette di scegliere: lo decide il PO.
- **DEMO-8 criterio 3 contro DEMO-16 criterio 1, contraddizione letterale**: DEMO-8 dice "Dato due fatture da incassare citate nella causale dello stesso incasso, quando l'incasso è contabilizzato, allora non nasce nessun abbinamento automatico e l'incasso resta da abbinare". DEMO-16 dice "Dato due fatture da incassare di 500,00 EUR e 720,00 EUR dello stesso cliente e un incasso di 1.220,00 EUR che le cita entrambe in causale, quando l'incasso è contabilizzato, allora risulta abbinato a entrambe ed entrambe passano a incassata". Stessa premessa, esito opposto quando la somma coincide. È l'evoluzione voluta (DEMO-16 è bloccata da DEMO-8 e ne estende il comportamento), ma non è scritto da nessuna parte: quando DEMO-16 viene consegnata, il criterio 3 di DEMO-8 diventa falso e il suo test fallisce.
- **DEMO-8 criterio 7 e DEMO-16 criterio 4 (storno)**: entrambi dicono che la fattura "torna da incassare". Dentro il loro perimetro (un solo incasso per fattura) è corretto. Con i parziali di DEMO-15 lo stesso limite di 14.5 vale anche qui: se la fattura ha altri incassi abbinati, deve restare incassata in parte. Formulazione da restringere quando DEMO-15 entra in gioco, non una contraddizione oggi.
- **DEMO-16 criteri 2 e 3 contro DEMO-14**: quando la somma non torna o le combinazioni sono più di una, l'incasso "resta da abbinare", cioè passa a DEMO-14. Ma DEMO-14 abbina a mano a "una fattura" sola e 14.4 rifiuta un incasso superiore al residuo di quella fattura. Un incasso che paga più fatture e che l'automatismo non risolve non si può chiudere nemmeno a mano. Lacuna, non contraddizione.
- **DEMO-8 criterio 1 e "importo" contro "residuo"**: DEMO-8 confronta l'incasso con l'importo della fattura "da incassare", DEMO-14 dal criterio 3 in poi ragiona sul residuo. Con una fattura incassata in parte e un incasso uguale al residuo, DEMO-8 non abbina (importo diverso) e DEMO-14 non ha un criterio (vedi tabella). Il caso oggi non è coperto da nessuna delle due.

### D. Riferimenti non collegati e sovrapposizioni

- **jira:DEMO-14 → jira:DEMO-15**: il criterio 14.3 cita DEMO-15 nel testo, ma `dipendenze` e `extra.issuelinks` sono vuoti su entrambe. DEMO-14 e DEMO-15 sono le uniche due story dell'epica DEMO-9 senza alcun link. Il criterio 14.3 non è verificabile senza DEMO-15.
- **jira:DEMO-14 e jira:DEMO-15 → jira:DEMO-8**: nessun link. DEMO-14 lavora sugli incassi che DEMO-8 lascia "da abbinare": la dipendenza è plausibile ma dal testo non è certa. La conferma il team.
- **jira:DEMO-5 → jira:DEMO-10**: ancora solo nel testo di DEMO-10, `dipendenze` vuoto su entrambe. Riga già "in attesa" (sprint-prep), non duplicata.
- **Sovrapposizione 14.3 / 15.1**: lo stesso comportamento (incasso inferiore → incassata in parte) è scritto in due story. Con il rimando "secondo DEMO-15" la fonte è una sola, ed è corretto; va tenuto così, senza ricopiare i numeri in DEMO-14.
- **Link ridondante**: DEMO-13 è bloccata da DEMO-11 sia direttamente sia tramite DEMO-12. Non è un errore, solo una voce in più.

## 3. Problemi

### Critico

- **jira:DEMO-7** (bug, "Bonifico istantaneo resta in stato pending dopo il timeout"): `stato` blocked per flag "Impediment", in PAY Sprint 1, `dipendenze` e `issuelinks` vuoti, `acceptance_criteria` vuoto. Blocco senza causa. Invariato da stamattina. Ancora "in attesa" 4 righe del 12 novembre e 1 del 13 novembre (uscita dallo sprint).
- **jira:DEMO-6** (story, "Caricare online i documenti societari"): `stima` null, in PAY Sprint 1, 5 criteri su 7 con segnaposto. Non supera i punti 2 e 3 della Definition of Ready. Come stamattina: la scala mette "story senza stima" in Medio; la tengo in Critico perché lo sprint parte il 16 novembre. Con lettura letterale è Medio. Ora blocca DEMO-13 tramite link.

### Medio

- **jira:DEMO-14**: il criterio 5 contraddice il modello a residuo di DEMO-15 quando la fattura ha più incassi abbinati (sezione 2, A). Manca il criterio per l'incasso uguale al residuo di una fattura incassata in parte. Cita DEMO-15 senza link.
- **jira:DEMO-15**: il criterio 3 è ancora "[comportamento sull'eccedenza da definire]" mentre DEMO-14 criterio 4 ha già deciso il rifiuto per il canale manuale (sezione 2, B). Non è scritto se l'abbinamento dei parziali è automatico o manuale (sezione 2, C, rispetto a DEMO-8 criterio 2).
- **jira:DEMO-16**: il criterio 1 ha esito opposto al criterio 3 di jira:DEMO-8 a parità di premessa, senza che sia dichiarato che lo sostituisce. DEMO-8 è in PAY Sprint 1: il suo criterio 3 resta valido finché DEMO-16 non viene consegnata.
- **jira:DEMO-11, DEMO-12, DEMO-13** (epica DEMO-3) e **jira:DEMO-14, DEMO-15, DEMO-16** (epica DEMO-9): 6 story con `stima` null, fuori sprint. Criteri: 5, 6, 7, 6, 4, 4. La stima spetta al team.
- **notion:PAYDISC-5**: `stato` blocked ("In attesa"), `dipendenze` vuote, `extra["Epica Jira"]` null. Blocco senza causa. Riga di stamattina ancora "in attesa".
- **jira:DEMO-5**: legame con DEMO-10 solo nel testo. Riga già "in attesa", non duplicata.
- **notion:PAYDISC-3**: `stato_originale` "In ricerca" con 4 story già scritte sotto l'epica DEMO-3 e una (DEMO-6) in sprint. Invariato.
- **Criteri con parti da definire** (fuori dai 9 controlli, punto 2 della DoR): 15 criteri su 51, in 6 story su 9. DEMO-6: 5 su 7. DEMO-11: 4 su 5. DEMO-13: 3 su 7. DEMO-8: 1 su 9. DEMO-12: 1 su 6. DEMO-15: 1 su 4. Senza segnaposto: DEMO-5, DEMO-14, DEMO-16. DEMO-6 e DEMO-8 sono in sprint.

### Basso

- **jira:DEMO-12, DEMO-13, DEMO-16**: `stato` blocked per sequenza, con dipendenza collegata. Non sono impedimenti e non richiedono azione. Li segnalo perché il conteggio dei bloccati (5) va letto come 2 senza causa più 3 di sequenza.
- **jira:DEMO-4** (epica "Carte virtuali usa e getta"): nessun figlio e nessuna opportunità che la realizzi. Riga di stamattina ancora "in attesa".
- **jira:DEMO-5** e **jira:DEMO-2**: `stato` in_progress con lo sprint ancora "future". DEMO-5 è in corso con `assegnatario` null.
- **Priorità Jira**: 16 item su 16 con priorità "Medium".
- **jira:DEMO-2, DEMO-3, DEMO-9**: `wsjf` null nello snapshot. Secondo CLAUDE.md l'epica eredita i quattro valori dall'opportunità che realizza (PAYDISC-1, PAYDISC-3, PAYDISC-2, tutti e quattro i valori presenti). Materia del prioritizer.
- **jira:DEMO-8 criterio 7 e jira:DEMO-16 criterio 4**: "la fattura torna da incassare" allo storno vale solo se non ci sono altri incassi abbinati. Da restringere quando DEMO-15 viene rifinita.

## 4. Azioni proposte

Decide il Product Owner. Le 5 righe nuove sono in `2026-11-13_proposte.md`, sezione "backlog-health, 2026-11-13, secondo passaggio".

1. **jira:DEMO-14**: collegarla a DEMO-15 con un link (riga nuova 1). Il verso proposto è "bloccata da DEMO-15"; lo conferma il team.
2. **jira:DEMO-14**: riscrivere il criterio 5 perché l'annullamento segua la regola del residuo (riga nuova 2).
3. **jira:DEMO-14**: estendere il criterio 2 all'incasso uguale al residuo di una fattura incassata in parte (riga nuova 3).
4. **jira:DEMO-15**: definire il criterio 3 sull'eccedenza, dicendo se vale per l'abbinamento automatico, manuale o entrambi, e tenendolo coerente con DEMO-14 criterio 4 (riga nuova 4; il valore lo decide il PO, l'agent non lo conosce). Nella stessa occasione decidere come si chiude un incasso in eccesso o doppio, che oggi non ha uscita.
5. **jira:DEMO-15**: chiarire nella descrizione chi abbina l'incasso parziale (automatismo o addetto). Nessuna riga: è una decisione di prodotto, e da essa dipende se serve cambiare DEMO-8 criterio 2.
6. **jira:DEMO-16**: dichiarare nella descrizione che sostituisce il criterio 3 di DEMO-8 quando la somma coincide (riga nuova 5). DEMO-8, in sprint, non viene toccata.
7. **jira:DEMO-14, DEMO-16**: decidere con il team se l'abbinamento a mano di un incasso a più fatture è nel perimetro di DEMO-14 o serve una story a parte. Nessuna riga.
8. **jira:DEMO-7**: prima del planning decidere le 5 righe già "in attesa". Serve la causa del flag "Impediment". Nessuna riga nuova.
9. **jira:DEMO-6**: farla stimare dal team o applicare la riga di sprint-prep già "in attesa" che la toglie da PAY Sprint 1. Nessuna riga nuova.
10. **jira:DEMO-11, 12, 13, 14, 15, 16**: al prossimo refinement per la stima. Per la sequenza KYB l'ordine è DEMO-11, poi DEMO-12, poi DEMO-13. Nessuna riga: gli agent non stimano.
11. **notion:PAYDISC-5**, **jira:DEMO-4**, **notion:PAYDISC-3**, **jira:DEMO-5**: azioni di stamattina ancora aperte (righe già presenti per le prime due e per il link DEMO-5 → DEMO-10). Nessuna riga nuova.
12. **jira:DEMO-12, DEMO-13, DEMO-16**: nessuna azione. Il blocco si scioglie da solo quando le story a monte sono completate.

## 5. Dati mancanti

| Cosa manca | Tool | A cosa servirebbe |
|---|---|---|
| Causa del blocco di DEMO-7 (nessun link, descrizione senza causa; i commenti non sono letti dal sync) | Jira | controllo 9, decidere se resta nello sprint |
| Motivo dell'attesa di PAYDISC-5 (il corpo della pagina non è letto dal sync) | Notion | controllo 9 |
| Comportamento sull'eccedenza (DEMO-15 criterio 3) e chi abbina l'incasso parziale | Jira | chiudere il confronto DEMO-14 / DEMO-15 / DEMO-8 |
| Testo del criterio 3 di DEMO-14 prima della modifica di oggi: lo snapshot contiene solo la versione attuale | Jira | confermare quale contraddizione di stamattina è stata chiusa; il confronto qui sopra è fatto solo sui testi attuali |
| `assegnatario` null su 21 item su 21; Team (customfield_10001) null su 16 issue su 16 | Jira, Notion | sapere chi segue DEMO-5 in corso e gli item in sprint |
| Chi ha assegnato le stime: lo snapshot non lo dice | Jira | punto 3 della DoR; il controllo 4 guarda solo la scala |
| Esito del passaggio da fintech-compliance per story: nessun campo nello snapshot | Jira | punto 6 della DoR |
| Opportunità a monte dell'epica DEMO-4 | Notion | controllo 6 |
| Valori WSJF ed evidenze di PAYDISC-4 (tutti null) | Notion | prioritizzazione |
| Data di inizio e scadenza (customfield_10015, duedate): null su 16 issue su 16 | Jira | capire quando si sciolgono i blocchi di sequenza |
| Ora reale di generazione dello snapshot (23:59:59Z è convenzionale; il file risulta scritto alle 18:56) | snapshot | controllo 2; oggi ininfluente, tutti gli item hanno meno di 2 giorni |
