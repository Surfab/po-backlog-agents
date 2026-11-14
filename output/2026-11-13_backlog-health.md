# Backlog health, 2026-11-13 (terzo passaggio)

Fonte: `./backlog/2026-11-13_snapshot.json`, rigenerato in serata (generato_il 2026-11-13T23:59:59Z, ora convenzionale; aggiornamento più recente letto: 2026-11-13T19:07:24+01:00, jira:DEMO-16). 21 item: 5 Notion, 16 Jira, tutti letti via connettore. Dati fittizi (demo). Nessuna scrittura su Notion o Jira. CLAUDE.md riletto dal disco prima di iniziare.

Versioni precedenti, non toccate: `2026-11-13_backlog-health_mattina.md`, `2026-11-13_backlog-health_pomeriggio.md`.

Composizione contata: 5 opportunity, 4 epic, 9 story, 1 bug, 2 task. Stati: 10 todo, 3 in_progress, 5 blocked, 3 done. In "PAY Sprint 1" (futuro, 16-27 novembre 2026): 5 item (DEMO-5, 6, 7, 8, 10), 15 punti su 4 item, 1 item senza stima (DEMO-6).

## 1. Sintesi

- Contraddizioni tra DEMO-8, 9, 14, 15, 16: nessuna. L'unico conflitto testuale (DEMO-8 criterio 3 contro DEMO-16 criterio 1) è chiuso dalla dichiarazione di sostituzione in DEMO-16 più il link "Blocks". Restano però 3 ambiguità con una lettura che contraddice (2 toccano DEMO-8, in sprint) e 7 lacune.
- Le modifiche dichiarate ci sono tutte e i testi di DEMO-14, 15, 16 ora applicano le regole di DEMO-9 senza eccezioni. Il punto più debole è DEMO-8 criterio 7 ("la fattura torna da incassare"): è l'unico criterio di storno rimasto con la formulazione che il PO ha già corretto in DEMO-14 e DEMO-16.
- Controlli standard invariati: critici 2 (DEMO-7, DEMO-6), 7 story senza stima, 1 epica vuota (DEMO-4), 2 bloccati senza causa (DEMO-7, PAYDISC-5). Ho accodato 5 righe nuove in `2026-11-13_proposte.md`, tutte "in attesa".

### Esito dei 9 controlli

| # | Controllo | Trovati | Item | Rispetto al pomeriggio |
|---|---|---|---|---|
| 1 | Duplicati | 0 | nessuno | uguale |
| 2 | Item fermi oltre 28 giorni | 0 | aggiornamento più vecchio: 2026-11-12T20:47:00Z, notion:PAYDISC-4 | uguale |
| 3 | Story incomplete | 7 senza stima, 0 senza criteri | DEMO-6, 11, 12, 13, 14, 15, 16 | uguale |
| 4 | Stima fuori scala | 0 | stime presenti: 5, 3, 5, 2 | uguale |
| 5 | Dipendenze o parent inesistenti | 0 | 15 voci di dipendenza (3 `realizza`, 5 `bloccato_da`, 5 `blocca`, 2 `correlato`) e 11 parent, tutti presenti | da 13 a 15 voci |
| 6 | Epiche vuote | 1 | DEMO-4 | uguale |
| 7 | Item senza epica | 0 | DEMO-1 ha `parent` null ma è l'item di prova completato: escluso | uguale |
| 8 | Opportunità validate senza epica | 0 | PAYDISC-1 → DEMO-2, PAYDISC-2 → DEMO-9 | uguale |
| 9 | Bloccati senza dipendenza | 2 su 5 bloccati | DEMO-7, PAYDISC-5 (DEMO-12, 13, 16 hanno il link che spiega il blocco) | uguale |

Coppie guardate per il controllo 1 e scartate: PAYDISC-1 / DEMO-2, PAYDISC-2 / DEMO-9, PAYDISC-3 / DEMO-3 (opportunità ed epica collegate da `realizza`); PAYDISC-4 / DEMO-4 (stessa label `carte`, bisogni diversi: limiti di spesa contro carte monouso); DEMO-8, 14, 15, 16 (casi distinti dello stesso split).

### Verifica delle modifiche dichiarate (sui dati)

- **jira:DEMO-9**: la `descrizione` contiene la sezione "## Regole di riconciliazione" con 6 punti elenco (5 regole più "Le story di questa epica applicano queste regole"). `acceptance_criteria` resta vuoto: corretto, è un'epica. `aggiornato_il` 19:06:39.
- **jira:DEMO-14**: criterio 2 ora parte da "importo uguale al residuo di una fattura da incassare o incassata in parte"; criterio 5 ora dice "il residuo della fattura aumenta dell'importo annullato, lo stato della fattura si ricalcola secondo le regole di DEMO-9". `aggiornato_il` 19:07:12.
- **jira:DEMO-15**: descrizione ora "inferiore al residuo della fattura"; criterio 3 senza segnaposto ("non viene abbinato, resta da abbinare e l'eccedenza di 100,00 EUR è indicata"). `aggiornato_il` 19:07:12.
- **jira:DEMO-16**: in descrizione "Al rilascio, questa story sostituisce il criterio 3 di DEMO-8 (incasso che cita più fatture)."; criterio 4 ora "il residuo di ciascuna fattura collegata aumenta della quota stornata e il suo stato si ricalcola secondo le regole di DEMO-9". `aggiornato_il` 19:07:24.
- **Link "relates to"**: id 10005, presente su entrambe le issue (`correlato` in DEMO-14 e in DEMO-15), senza effetto sullo stato: entrambe restano `todo`.
- **jira:DEMO-8**: non modificata in serata (`aggiornato_il` 16:42:17). I 9 criteri sono quelli del pomeriggio.

## 2. Restano contraddizioni tra DEMO-8, 9, 14, 15, 16?

**Risposta: no. Zero contraddizioni aperte. 1 conflitto testuale chiuso per dichiarazione (8.3 / 16.1). 3 ambiguità. 7 lacune.**

Definizioni usate: contraddizione = due testi con esiti incompatibili a parità di premessa; ambiguità = un testo si legge in due modi e uno dei due contraddice un altro testo; lacuna = caso che nessun testo copre. Notazione: R1-R5 sono le regole di DEMO-9 nell'ordine in cui sono scritte, 8.2 è il criterio 2 di DEMO-8 e così via.

### 2.1 Le regole di DEMO-9

- R1: "Una fattura ha un totale e un residuo. Stati: da incassare (residuo = totale), incassata in parte (0 < residuo < totale), incassata (residuo = 0)."
- R2: "Incasso uguale al residuo: la fattura passa a incassata."
- R3: "Incasso inferiore al residuo: la fattura passa a incassata in parte e il residuo diminuisce."
- R4: "Incasso superiore al residuo: nessun abbinamento, né automatico né manuale. L'incasso resta da abbinare e l'eccedenza è indicata."
- R5: "Annullamento di un abbinamento: il residuo aumenta dell'importo annullato, lo stato della fattura si ricalcola, l'incasso torna da abbinare."

### 2.2 Ogni regola contro ogni story

| Regola | DEMO-8 | DEMO-14 | DEMO-15 | DEMO-16 |
|---|---|---|---|---|
| R1 stati e residuo | Coerente. Usa "da incassare" e "incassata"; non usa "residuo" né "incassata in parte", che nel suo perimetro (un incasso pari al totale) non servono | Coerente. 14.2, 14.3, 14.4, 14.5 ragionano sul residuo | Coerente. 15.1: "passa a incassata in parte con residuo 600,00 EUR"; 15.2: "incassata con residuo 0,00 EUR" | Coerente. 16.4 usa residuo e ricalcolo |
| R2 uguale al residuo | Coerente. 8.1: fattura da incassare di 1.220,00 e incasso di 1.220,00 → "la fattura passa a incassata" (residuo = totale). Fattura incassata in parte con incasso pari al residuo: non coperta da 8.1, vedi A1 | Coerente. 14.2: "importo uguale al residuo di una fattura da incassare o incassata in parte ... la fattura passa a incassata" | Coerente. 15.2 | Coerente. 16.1: ogni fattura riceve il proprio residuo, "entrambe passano a incassata" |
| R3 inferiore al residuo | **Ambiguità A1.** 8.2: incasso di 1.219,99 su 1.220,00 → "non nasce nessun abbinamento automatico, l'incasso resta da abbinare e la fattura da incassare" | Coerente. 14.3: "registrato come pagamento parziale secondo DEMO-15" | Coerente. Descrizione e 15.1 | Coerente. 16.2 non abbina in automatico se la somma è diversa; R3 non obbliga ad abbinare (stessa riserva di A1) |
| R4 superiore al residuo | Coerente sull'esito. 8.6 (doppio pagamento, residuo zero): "la fattura non cambia e il secondo incasso resta da abbinare". Manca "l'eccedenza è indicata" e manca il caso 1.220,01: lacuna L1 | Coerente. 14.4: "l'abbinamento è rifiutato con l'indicazione dell'eccedenza" | Coerente. 15.3: "non viene abbinato, resta da abbinare e l'eccedenza di 100,00 EUR è indicata" | Coerente sull'esito. 16.2: "non nasce nessun abbinamento automatico e l'incasso resta da abbinare"; non dice che l'eccedenza è indicata (L1) |
| R5 annullamento | **Ambiguità A2.** 8.7: "l'abbinamento è rimosso, la fattura torna da incassare" | Coerente, quasi alla lettera. 14.5 | Coerente sul lato fattura. 15.4: "il residuo aumenta dell'importo stornato e lo stato si aggiorna". Stato dell'incasso stornato: lacuna L2 | Coerente sul lato fattura. 16.4. Stato dell'incasso stornato: lacuna L2 |

### 2.3 Le story a coppie

| Coppia | Esito | Dettaglio |
|---|---|---|
| 8-14 | Coerente, con A2 e 2 lacune | 8.2 e 8.6 lasciano l'incasso "da abbinare", 14.3 e 14.4 lo raccolgono: catena coerente. 8.7 (storno) e 14.5 (annullamento a mano) danno lo stesso esito solo se la fattura ha un solo incasso (A2). Il doppio pagamento di 8.6 a mano ricade in 14.4 e resta senza uscita (L3). 8.9 chiede l'audit trail dell'abbinamento, 14.2 no (L7) |
| 8-15 | Ambiguità A1 | 8.2 nega l'abbinamento automatico dell'incasso inferiore; DEMO-15 dice "voglio che un incasso inferiore al residuo della fattura sia registrato come pagamento parziale" e "quando le viene abbinato", senza dire chi abbina. 8.6 e 15.3 coerenti. 8.7 e 15.4 hanno premesse diverse (fattura incassata contro incassata in parte): nessun conflitto diretto, resta A2 |
| 8-16 | Conflitto chiuso per dichiarazione, più A3 | 8.3 contro 16.1: vedi 2.5. 8.1 contro 16.2 e 16.3: dipende da cosa sono le "fatture candidate" (A3). 8.5 e 16.1 coerenti (entrambe partono dall'incasso contabilizzato). 8.7 e 16.4: premesse diverse (una fattura contro più fatture), coerenti |
| 14-15 | Coerente | 14.3 rimanda a DEMO-15 e 15.1 dà l'esito; 14.2 e 15.2 uguali; 14.4 e 15.3 uguali (rifiuto, eccedenza indicata); 14.5 e 15.4 stessa regola del residuo. Le due contraddizioni del pomeriggio (14.5 contro 15.4; 14.4 contro 15.3 aperto) sono chiuse |
| 14-16 | Coerente, 2 lacune | Nessun esito incompatibile. L'incasso che 16.2 o 16.3 lasciano "da abbinare" non si chiude a mano (L4). L'annullamento a mano di un abbinamento su più fatture non ha un criterio (L5) |
| 15-16 | Coerente, 1 lacuna | 15.4 e 16.4 applicano la stessa regola. 16.1 parla solo di fatture "da incassare": fatture incassate in parte citate insieme non sono coperte (L6) |

### 2.4 I casi richiesti

| Caso | Testi | Classificazione |
|---|---|---|
| Incasso uguale al residuo, fattura da incassare | R2, 8.1 (automatico), 14.2 (manuale) | Coerente |
| Incasso uguale al residuo, fattura incassata in parte | R2, 14.2 (manuale), 15.2 (canale non detto); 8.1 parte solo da "fattura da incassare" | Coerente a mano. In automatico: ambiguità A1 |
| Incasso inferiore, fattura da incassare | R3, 8.2, 14.3, 15.1 | Coerente a mano. In automatico: ambiguità A1 |
| Incasso inferiore, fattura incassata in parte | R3, 14.3; DEMO-15 non ha un esempio | Coerente: lo copre R3 (la lacuna segnalata nel pomeriggio è chiusa dalla regola dell'epica) |
| Incasso superiore al residuo | R4, 14.4, 15.3; 8.6 per il residuo zero | Coerente. Lacune L1 (eccedenza in DEMO-8 e in 16.2) e L3 (nessuna uscita) |
| Doppio pagamento | 8.6, R4, 14.4 | Coerente. Lacuna L3 |
| Annullamento a mano, un incasso | R5, 14.5 | Coerente |
| Annullamento a mano, più incassi sulla fattura | R5, 14.5 | Coerente (chiuso oggi) |
| Annullamento a mano, incasso su più fatture | 14.5 parla di "il residuo della fattura", al singolare | Lacuna L5 |
| Storno, un incasso pari al totale | 8.7, R5 | Coerente |
| Storno, più incassi sulla fattura | 15.4, R5; 8.7 se letto alla lettera | Coerente tra 15.4 e R5. Ambiguità A2 su 8.7 |
| Storno, incasso su più fatture | 16.4, R5 | Coerente |
| Stato dell'incasso stornato | nessun testo; R5 dice "l'incasso torna da abbinare" ma non nomina lo storno | Lacuna L2 |
| Abbinamento automatico | 8.1-8.9, 16.1-16.3 | Coerente, con A1 e A3 |
| Abbinamento manuale | 14.1-14.6 | Coerente. Lacune L4, L5, L7 |
| Incasso che cita più fatture | 8.3, 16.1-16.3 | Conflitto chiuso per dichiarazione (2.5). Ambiguità A3. Lacune L4, L6 |

### 2.5 DEMO-8 criterio 3 contro DEMO-16 criterio 1: la dichiarazione basta?

Testi. 8.3: "Dato due fatture da incassare citate nella causale dello stesso incasso, quando l'incasso è contabilizzato, allora non nasce nessun abbinamento automatico e l'incasso resta da abbinare". 16.1: "Dato due fatture da incassare di 500,00 EUR e 720,00 EUR dello stesso cliente e un incasso di 1.220,00 EUR che le cita entrambe in causale, quando l'incasso è contabilizzato, allora risulta abbinato a entrambe ed entrambe passano a incassata". DEMO-16, descrizione: "Al rilascio, questa story sostituisce il criterio 3 di DEMO-8 (incasso che cita più fatture)."

**Sì, basta a chiudere la contraddizione.** I due criteri hanno esiti opposti a parità di premessa, ma non valgono mai nello stesso momento: il link "Blocks" (DEMO-8 blocca DEMO-16) fissa l'ordine e la frase dice quale dei due vale dopo il rilascio. Non la conto tra le contraddizioni aperte.

**Non basta su tre punti operativi**, nessuno dei quali è una contraddizione:

1. La dichiarazione è scritta solo in DEMO-16. Chi legge DEMO-8, che è in sprint, non sa che il criterio 3 è provvisorio. Per la Definition of Done (punti 1 e 3) 8.3 sarà verificato dal QA e coperto da un test automatico: al rilascio di DEMO-16 quel test va ritirato o riscritto, e nessun criterio di DEMO-16 lo dice.
2. DEMO-16 sostituisce 8.3 per intero, ma 16.1 copre solo fatture "dello stesso cliente" con somma uguale. Due fatture di clienti diversi citate nella stessa causale, dopo la sostituzione, non hanno più un criterio (lacuna L6).
3. Gli altri casi di 8.3 passano a 16.2 e 16.3, che parlano di "fatture candidate" senza definirle (ambiguità A3).

### 2.6 DEMO-8 è coerente con le regole di DEMO-9?

**Sì, dentro il suo perimetro** (descrizione: "un incasso che corrisponde a una sola fattura emessa aperta dello stesso importo"; tutti i criteri partono da una fattura "da incassare" o "incassata" con un solo incasso pari al totale). Due criteri sono però scritti più larghi del perimetro e, letti alla lettera, entrano in conflitto con le regole.

- **Importo diverso (8.2)**: coerente se R3 descrive l'effetto di un abbinamento già avvenuto. Ambiguo se R3 si legge come obbligo: vedi A1. Manca il caso dell'incasso superiore (L1).
- **Doppio pagamento (8.6)**: coerente con R4. Residuo zero, incasso di 1.220,00 superiore al residuo: nessun abbinamento, incasso da abbinare. Manca solo l'indicazione dell'eccedenza (L1).
- **Storno (8.7)**: coerente con R5 quando l'incasso stornato è l'unico della fattura (residuo torna uguale al totale, quindi "da incassare"). Ambiguo quando la fattura ha altri incassi: vedi A2.
- 8.1 coerente con R2. 8.3 sostituito al rilascio di DEMO-16 (2.5). 8.4, 8.5, 8.8, 8.9: nessuna regola li tocca.

### 2.7 Le 3 ambiguità

**A1. Chi abbina un incasso inferiore (o uguale) al residuo che cita la fattura: l'automatismo o solo l'addetto?** Testi: R3 "Incasso inferiore al residuo: la fattura passa a incassata in parte e il residuo diminuisce", senza dire il canale (R4 invece lo dice: "né automatico né manuale"); DEMO-15 "voglio che un incasso inferiore al residuo della fattura sia registrato come pagamento parziale" e "quando le viene abbinato"; 8.2 "non nasce nessun abbinamento automatico ... la fattura [resta] da incassare".
- Lettura 1: R2 e R3 dicono cosa succede alla fattura quando un abbinamento nasce; l'incasso inferiore lo abbina solo l'addetto (8.2 → 14.3 → DEMO-15). Tutto coerente.
- Lettura 2: R3 e DEMO-15 valgono anche per l'automatismo. Allora con fattura da 1.220,00 e incasso da 1.219,99 R3 dà "incassata in parte" e 8.2 dà "da incassare": contraddizione, e senza una dichiarazione di sostituzione come quella di DEMO-16.
- Stessa domanda per l'incasso uguale al residuo di una fattura incassata in parte: 8.1 parte solo da "fattura da incassare", la descrizione di DEMO-8 dice "fattura emessa aperta dello stesso importo" senza dire se "aperta" comprende "incassata in parte" e se "importo" è il totale o il residuo.
- Era aperta già nel pomeriggio (azione 5, senza riga). Tocca DEMO-8, in sprint.

**A2. 8.7: "un incasso abbinato a una fattura incassata" vale anche se la fattura ha altri incassi?** Testi: 8.7 "quando l'incasso viene stornato, allora l'abbinamento è rimosso, la fattura torna da incassare"; R5 "il residuo aumenta dell'importo annullato, lo stato della fattura si ricalcola"; 15.4 "il residuo aumenta dell'importo stornato e lo stato si aggiorna".
- Lettura 1: 8.7 vale solo per gli abbinamenti nati da DEMO-8 (un incasso pari al totale). Coerente: il ricalcolo dà proprio "da incassare".
- Lettura 2: 8.7 vale per ogni incasso abbinato a una fattura incassata. Fattura da 1.000,00 incassata con 400,00 e 600,00 (possibile dopo DEMO-14 e DEMO-15), storno dei 600,00: 8.7 dà "da incassare", R5 dà "incassata in parte" con residuo 600,00. Contraddizione.
- Nello sprint 1, con la sola DEMO-8, la lettura 2 non si può verificare: una fattura ha al massimo un incasso. Il conflitto nasce al rilascio di DEMO-14 o DEMO-15. È la stessa formulazione che il PO ha già riscritto oggi in 14.5 e in 16.4.

**A3. Che cosa sono le "fatture candidate" di 16.2 e 16.3?** Testi: 16.2 "Dato fatture candidate la cui somma è diversa dall'importo dell'incasso ... non nasce nessun abbinamento automatico"; 16.3 "Dato fatture candidate che danno la somma dell'incasso in più di una combinazione ... non nasce nessun abbinamento automatico"; 8.1 "un incasso contabilizzato di 1.220,00 EUR con il numero di quella sola fattura in causale ... l'incasso risulta abbinato".
- Lettura 1: candidate = le fatture citate in causale (come in 16.1). Coerente con 8.1, che ha una sola fattura citata. Ma allora 16.3 è difficile da verificare: con le fatture citate la combinazione è una sola, salvo ammettere sottoinsiemi.
- Lettura 2: candidate = tutte le fatture aperte del cliente. Incasso di 1.220,00 che cita solo la fattura A da 1.220,00, mentre il cliente ha aperte anche B da 500,00 e C da 720,00: per 16.3 le combinazioni sono due e non nasce abbinamento, per 8.1 l'incasso è abbinato ad A. Contraddizione, e la sostituzione dichiarata riguarda solo 8.3, non 8.1.
- Non è scritto nemmeno se la "somma" è dei totali o dei residui (vedi L6).

### 2.8 Le 7 lacune

- **L1. Eccedenza in DEMO-8 e in 16.2.** R4 chiede che "l'eccedenza è indicata". 8.6 non lo dice, DEMO-8 non ha un criterio per l'incasso superiore all'importo (per esempio 1.220,01), 16.2 non lo dice quando la somma è inferiore all'incasso. Nello sprint 1 l'indicazione dell'eccedenza non è in nessuna story pianificata (15.3 è fuori sprint).
- **L2. Stato dell'incasso stornato.** 8.7, 15.4 e 16.4 dicono cosa succede alla fattura, non all'incasso. R5 non nomina lo storno; applicata alla lettera ("l'incasso torna da abbinare") rimetterebbe nell'elenco di 14.1 un incasso che non c'è più.
- **L3. Incasso in eccesso o doppio senza uscita.** R4, 14.4, 15.3 e 8.6 concordano: non si abbina in nessun modo. Nessun testo dice come si chiude (restituzione, acconto, nota). Resta "da abbinare" per sempre. Coerente, ma contro il beneficio dichiarato di DEMO-14 ("chiudere la riconciliazione anche quando l'automatismo non decide").
- **L4. Incasso su più fatture non risolto dall'automatismo.** 16.2 e 16.3 lo lasciano "da abbinare"; DEMO-14 abbina a mano a "una fattura" e 14.4 rifiuta se l'incasso supera il residuo. Esempio: 1.000,00 a fronte di fatture da 500,00 e 720,00 non si abbina né in automatico né a mano.
- **L5. Annullamento a mano di un abbinamento su più fatture.** 14.5 parla di "il residuo della fattura", 16.4 copre solo lo storno.
- **L6. DEMO-16 dopo la sostituzione di 8.3.** Non coperti: fatture di clienti diversi citate nella stessa causale; fatture incassate in parte citate insieme (16.1 dice "da incassare"); "somma" dei totali o dei residui. DEMO-16 non ripete nemmeno i criteri di tempo massimo e di traccia di DEMO-8 (8.8, 8.9).
- **L7. Audit trail dell'abbinamento.** 8.9 lo chiede per l'automatico, 14.5 per l'annullamento. 14.2 dice solo "riporta chi l'ha fatto e quando"; DEMO-15 e 16.1 non ne parlano. Punto 4 della Definition of Done.

## 3. Problemi

### Critico

- **jira:DEMO-7** (bug, "Bonifico istantaneo resta in stato pending dopo il timeout"): `stato` blocked per flag "Impediment", in PAY Sprint 1, `dipendenze` e `issuelinks` vuoti, `acceptance_criteria` vuoto. Blocco senza causa. Invariato. Restano "in attesa" 4 righe del 12 novembre e 1 del 13 novembre (uscita dallo sprint).
- **jira:DEMO-6** (story, "Caricare online i documenti societari"): `stima` null, in PAY Sprint 1, 5 criteri su 7 con segnaposto. Non supera i punti 2 e 3 della Definition of Ready. Come nei passaggi precedenti: la scala mette "story senza stima" in Medio; la tengo in Critico perché lo sprint parte il 16 novembre. Con lettura letterale è Medio. Blocca DEMO-13.

### Medio

- **jira:DEMO-8** (in PAY Sprint 1): criterio 7 scritto più largo del perimetro, in conflitto con R5 di DEMO-9 se letto alla lettera (A2). Criterio 2 e descrizione dipendono dalla decisione su chi abbina i parziali (A1). Manca l'eccedenza chiesta da R4 (L1). Il criterio 3 è provvisorio ma DEMO-8 non lo dice (2.5). Criterio 8 con segnaposto "[tempo massimo di abbinamento]".
- **jira:DEMO-9** (epica): R2 e R3 non dicono se valgono per l'automatismo (A1). R5 non dice se copre lo storno né che cosa succede all'incasso stornato (L2). Nessuna regola per l'incasso su più fatture (L4, L5, L6).
- **jira:DEMO-16**: "fatture candidate" e "somma" non definite (A3, L6). Clienti diversi e fatture incassate in parte non coperti.
- **jira:DEMO-15**: non dice chi abbina l'incasso parziale (A1). Nessun criterio di audit trail (L7).
- **jira:DEMO-14**: a mano si abbina a una sola fattura (L4, L5); il link verso DEMO-15 è "relates to", quindi il criterio 3 resta non verificabile finché DEMO-15 non è consegnata e nulla impone l'ordine (scelta del PO, la segnalo e basta).
- **jira:DEMO-11, DEMO-12, DEMO-13** (epica DEMO-3) e **jira:DEMO-14, DEMO-15, DEMO-16** (epica DEMO-9): 6 story con `stima` null, fuori sprint. La stima spetta al team.
- **notion:PAYDISC-5**: `stato` blocked ("In attesa"), `dipendenze` vuote. Blocco senza causa. Riga ancora "in attesa".
- **jira:DEMO-5**: legame con DEMO-10 solo nel testo di DEMO-10. Riga ancora "in attesa", non duplicata.
- **notion:PAYDISC-3**: `stato_originale` "In ricerca" con 4 story sotto l'epica DEMO-3 e una (DEMO-6) in sprint. Invariato.
- **Criteri con parti da definire** (fuori dai 9 controlli, punto 2 della DoR): 14 criteri su 51, in 5 story su 9 (nel pomeriggio 15 su 51 in 6 story: DEMO-15 non ne ha più). DEMO-6: 5 su 7. DEMO-11: 4 su 5. DEMO-13: 3 su 7. DEMO-8: 1 su 9. DEMO-12: 1 su 6. DEMO-6 e DEMO-8 sono in sprint.

### Basso

- **jira:DEMO-12, DEMO-13, DEMO-16**: `stato` blocked per sequenza, con dipendenza collegata. Nessuna azione. Dei 5 bloccati, 2 sono senza causa e 3 di sequenza.
- **jira:DEMO-4** (epica "Carte virtuali usa e getta"): nessun figlio e nessuna opportunità che la realizzi. Riga ancora "in attesa".
- **jira:DEMO-1**: item di prova completato, `parent` null, `label` vuote, `descrizione` null. Nessuna azione.
- **jira:DEMO-5** e **jira:DEMO-2**: `stato` in_progress con lo sprint ancora "future". DEMO-5 in corso con `assegnatario` null.
- **Priorità Jira**: 16 item su 16 con priorità "Medium".
- **Goal di PAY Sprint 1** (bozza, sulla verifica del beneficiario): DEMO-6 (onboarding) e DEMO-8 (fatture) sono nello sprint ma fuori dal goal. Materia di sprint-prep.
- **jira:DEMO-2, DEMO-3, DEMO-9**: `wsjf` null nello snapshot; i valori si ereditano da PAYDISC-1, PAYDISC-3, PAYDISC-2. Materia del prioritizer.

## 4. Azioni proposte

Decide il Product Owner. Le 5 righe nuove sono in `2026-11-13_proposte.md`, sezione "backlog-health, 2026-11-13, terzo passaggio".

1. **jira:DEMO-8**: riscrivere il criterio 7 con la formula già usata in 14.5 e 16.4 (residuo che aumenta, stato ricalcolato secondo DEMO-9). Nel perimetro di DEMO-8 l'esito non cambia: chiude A2 senza allargare la story (riga nuova 1).
2. **jira:DEMO-8**: aggiungere in descrizione che il criterio 3 vale fino al rilascio di DEMO-16, così la sostituzione è scritta su entrambe le story (riga nuova 2).
3. **jira:DEMO-9**: decidere e scrivere nelle regole chi abbina un incasso inferiore o uguale al residuo che cita la fattura, automatismo o solo addetto. Il valore lo decide il PO. Da qui dipende se 8.2 resta com'è (riga nuova 3). Chiude A1.
4. **jira:DEMO-9**: scrivere nelle regole se R5 vale anche per lo storno e in quale stato finisce l'incasso stornato (riga nuova 4). Chiude L2.
5. **jira:DEMO-16**: definire "fatture candidate" e "somma" nei criteri 2 e 3 (riga nuova 5). Chiude A3 e parte di L6.
6. **jira:DEMO-16**: al refinement aggiungere un criterio o un task per ritirare il test del criterio 3 di DEMO-8 al rilascio, e decidere i casi "clienti diversi" e "fatture incassate in parte". Nessuna riga: prima serve l'azione 5.
7. **jira:DEMO-8, DEMO-15**: decidere in quale story si mostra l'eccedenza chiesta da R4 nello sprint 1 (L1). Nessuna riga: è una scelta di perimetro.
8. **jira:DEMO-9, DEMO-14**: decidere come si chiude un incasso in eccesso o doppio (L3) e se l'abbinamento e l'annullamento a mano su più fatture entrano in DEMO-14 o in una story nuova (L4, L5). Nessuna riga: decisioni di prodotto, già segnalate nel pomeriggio.
9. **jira:DEMO-14, DEMO-15, DEMO-16**: al refinement verificare l'audit trail della creazione dell'abbinamento (L7). Nessuna riga.
10. **jira:DEMO-7**: prima del planning decidere le 5 righe già "in attesa". Serve la causa del flag "Impediment". Nessuna riga nuova.
11. **jira:DEMO-6**: farla stimare dal team o applicare la riga di sprint-prep già "in attesa" che la toglie da PAY Sprint 1. Nessuna riga nuova.
12. **jira:DEMO-11, 12, 13, 14, 15, 16**: al prossimo refinement per la stima. Nessuna riga: gli agent non stimano.
13. **notion:PAYDISC-5**, **jira:DEMO-4**, **notion:PAYDISC-3**, **jira:DEMO-5**: azioni dei passaggi precedenti ancora aperte, righe già presenti. Nessuna riga nuova.

## 5. Dati mancanti

| Cosa manca | Tool | A cosa servirebbe |
|---|---|---|
| Chi abbina un incasso inferiore o uguale al residuo che cita la fattura (automatismo o addetto) | Jira, DEMO-9 e DEMO-15 | chiudere A1; sapere se 8.2 va dichiarato provvisorio |
| Definizione di "fatture candidate" e di "somma" (totali o residui) | Jira, DEMO-16 | chiudere A3 e L6 |
| Stato dell'incasso dopo lo storno | Jira, DEMO-9 | chiudere L2 |
| Uscita per l'incasso in eccesso o doppio | Jira, DEMO-9 | chiudere L3 |
| Causa del blocco di DEMO-7 (nessun link; i commenti non sono letti dal sync) | Jira | controllo 9, decidere se resta nello sprint |
| Motivo dell'attesa di PAYDISC-5 (il corpo della pagina non è letto dal sync) | Notion | controllo 9 |
| Valore di "[tempo massimo di abbinamento]" in DEMO-8 criterio 8 | Jira | punto 2 della DoR per una story in sprint |
| `assegnatario` null su 21 item su 21; Team (customfield_10001) null su 16 issue su 16 | Jira, Notion | sapere chi segue DEMO-5 in corso e gli item in sprint |
| Chi ha assegnato le stime: lo snapshot non lo dice | Jira | punto 3 della DoR |
| Esito del passaggio da fintech-compliance per story: nessun campo nello snapshot | Jira | punto 6 della DoR |
| Opportunità a monte dell'epica DEMO-4 | Notion | controllo 6 |
| Valori WSJF ed evidenze di PAYDISC-4 (tutti null) | Notion | prioritizzazione |
| Data di inizio e scadenza (customfield_10015, duedate): null su 16 issue su 16 | Jira | capire quando si sciolgono i blocchi di sequenza |
| Ora reale di generazione dello snapshot (23:59:59Z è convenzionale) | snapshot | controllo 2; oggi ininfluente, tutti gli item hanno meno di 2 giorni |
| Testi precedenti dei criteri modificati oggi: lo snapshot contiene solo la versione attuale | Jira | il confronto è fatto solo sui testi attuali; i valori precedenti li ho letti dalle righe di `2026-11-13_proposte.md` |
