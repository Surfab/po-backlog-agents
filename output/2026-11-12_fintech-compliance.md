# Fintech compliance, 2026-11-12

Snapshot letto: `./backlog/2026-11-12_snapshot.json`. Item valutati: jira:DEMO-5, jira:DEMO-7. Contesto: jira:DEMO-2, notion:PAYDISC-1.
Primo filtro di prodotto, non un parere legale. Nessuna norma è citata per articolo: i punti vanno confermati da compliance, legal, DPO, sicurezza o antifrode. Dati fittizi (demo).

## 1. Sintesi

- jira:DEMO-5 e jira:DEMO-7 hanno entrambe impatti critici: la prima mostra a un cliente dati personali di un terzo e condiziona una disposizione irrevocabile, la seconda lascia senza esito certo un movimento di denaro.
- jira:DEMO-5 è già "In corso" senza che risulti il passaggio di compliance (punto 6 della Definition of Ready) e non copre l'esito "non corrisponde"; jira:DEMO-7 non ha acceptance criteria, stima né causa del blocco.
- Proposti 7 righe in `2026-11-12_proposte.md` (acceptance criteria aggiuntivi, label, due task di parere). Nessuna è un requisito certo: decide il PO dopo il confronto con compliance.

## 2. Problemi

### Critico

- **jira:DEMO-5, nome del terzo mostrato con esito "simile" (GDPR, regole VoP).** Il secondo criterio dice "vedo un avviso e il nome corretto": il cliente riceve il nome del titolare di un IBAN che non ha saputo indicare correttamente. Verificare con DPO e compliance quali dati si possono mostrare per ciascun esito, con quale base giuridica e se l'informativa privacy lo copre. Manca nella story: cosa si mostra esattamente per ogni esito (nome completo, parziale, nessuno).
- **jira:DEMO-5, esito "non corrisponde" assente.** I criteri coprono corrisponde, simile e non disponibile, non il caso più rischioso. Rischio: lo sviluppo decide da solo se mostrare il nome (da evitare finché non c'è un parere) e se lasciare proseguire. Verificare con compliance e legal: cosa mostrare, se il cliente può procedere, testo dell'avviso, ripartizione della responsabilità se procede. Manca: lo scenario intero.
- **jira:DEMO-5, uso del servizio per ricavare nomi da IBAN.** Chi inserisce IBAN a ripetizione senza mai confermare può raccogliere nomi di titolari. Verificare con antifrode e sicurezza: limiti di frequenza, monitoraggio delle verifiche non seguite da un bonifico, blocco. Manca: qualsiasi criterio su abuso.
- **jira:DEMO-5, "verifica non disponibile, scelgo se procedere".** Un bonifico istantaneo non si revoca: il cliente procede senza verifica. Verificare con compliance e legal se è ammesso procedere, con quale avviso e con quale conferma esplicita; verificare con compliance se la soglia di 5 secondi è coerente con le regole dello schema (nello snapshot non c'è la fonte del valore). Manca: testo dell'avviso, forma della conferma, origine dei 5 secondi.
- **jira:DEMO-5, ordine tra verifica e SCA.** Il terzo criterio fa partire il caso "quando provo a confermare": non è chiaro se la verifica avviene prima dell'autorizzazione del pagamento. Rischio: il cliente autorizza con SCA un beneficiario non ancora verificato, oppure modifica nome o IBAN dopo la verifica senza che venga ripetuta. Verificare con compliance e sicurezza (requisiti SCA della PSD2, collegamento dell'autorizzazione a importo e beneficiario). Manca: la sequenza dei passi e la regola di nuova verifica dopo una modifica.
- **jira:DEMO-5, story già "In corso" senza valutazione di compliance.** Nello snapshot non c'è traccia di un parere. La gravità è critica (espone dati personali a terzi e riguarda una disposizione di pagamento): per le regole del progetto non può andare in produzione senza parere. Decisione del PO se sospendere lo sviluppo o proseguirlo dietro feature flag in attesa del parere.
- **jira:DEMO-7, esito ignoto di un bonifico irrevocabile.** Dopo il timeout il cliente non sa se il denaro è partito: se riprova, rischia un doppio pagamento che non si può richiamare; se non riprova, può mancare una scadenza. Verificare con compliance e operations: regole dello schema sul timeout, tempo massimo per dare un esito definitivo, obblighi di informazione al cliente, gestione dei fondi (impegnati o liberati) nell'attesa. Verificare con sicurezza: protezione dai doppi invii (idempotenza) e riallineamento dello stato con il circuito. Manca: acceptance criteria, passi per riprodurre, frequenza, numero di clienti e importi coinvolti, comportamento atteso.
- **jira:DEMO-7, possibile incidente operativo (DORA).** Se il difetto è in produzione e ricorrente, può rientrare nella gestione e classificazione degli incidenti ICT. Verificare con sicurezza e compliance se va classificato e se scatta una notifica. Manca: ambiente in cui si verifica, data della prima occorrenza, volumi, se il timeout nasce dal circuito o da un fornitore.

### Medio

- **jira:DEMO-5, audit trail della verifica.** Per gestire contestazioni serve sapere nome inserito, esito, cosa è stato mostrato, scelta del cliente e momento. Questi dati sono personali: devono stare nell'audit trail e non nei log applicativi (punti 4 e 5 della Definition of Done). Verificare con DPO e compliance contenuto e tempi di conservazione. Manca: criterio di tracciamento.
- **jira:DEMO-5, fornitore del servizio di verifica.** La story non dice chi fornisce la verifica. Verificare con DPO il ruolo del fornitore nel trattamento e con sicurezza se è un fornitore ICT da censire e valutare per la continuità operativa (DORA). Manca: nome del fornitore, contratto API, livelli di servizio.
- **jira:DEMO-5, beneficiari che sono imprese.** Il prodotto serve PMI che pagano spesso altre società. Verificare con compliance se per le persone giuridiche la verifica è per denominazione o per identificativo e come trattare nomi commerciali diversi dalla ragione sociale. Manca: qualsiasi indicazione sul tipo di beneficiario.
- **jira:DEMO-5 e jira:DEMO-2, perimetro dei canali.** Non è detto se la verifica vale anche per bonifici disposti da terze parti (PISP), da API o in blocco. Verificare con compliance il perimetro obbligatorio. Manca: elenco dei canali nell'epica.
- **jira:DEMO-5, controlli sulle sanzioni e antifrode.** La story non li cita. Verificare con antifrode e compliance che il nuovo passo non sposti né salti i controlli esistenti e che una serie di esiti negativi alimenti il monitoraggio. Manca: posizione della verifica rispetto ai controlli attuali.
- **jira:DEMO-7, tracciabilità dei cambi di stato.** Ogni passaggio (inviato, timeout, in verifica, eseguito, non eseguito) e ogni intervento manuale del supporto deve lasciare traccia. Verificare con compliance cosa serve per reclami e rimborsi. Manca: elenco degli stati e chi può forzarli.
- **jira:DEMO-7, blocco senza motivo.** L'item ha il flag "Impediment" ma nessuna dipendenza collegata né spiegazione. Non si può valutare se il blocco dipende dal circuito o da un fornitore. Manca: causa del blocco.
- **jira:DEMO-2, lato "risposta" non coperto.** L'epica parla solo di mostrare l'esito al nostro cliente. Non è chiaro chi gestisce le richieste di verifica che arrivano da altri prestatori sui nomi dei nostri clienti. Verificare con compliance e DPO se è nel perimetro. Manca: story o nota di esclusione.
- **jira:DEMO-2 e notion:PAYDISC-1, scadenza normativa non indicata.** L'opportunità ha time criticality 13 e label `compliance`, l'epica ha priorità "Medium" (valore di default di Jira). Verificare con compliance se esiste una data obbligatoria: l'agent non la indica a memoria.

### Basso

- **jira:DEMO-5, testi degli avvisi.** I messaggi per "simile", "non corrisponde" e "non disponibile" vanno approvati da legal prima del rilascio: incidono sulla scelta del cliente.
- **jira:DEMO-5, dati di test.** Servono IBAN di prova per i quattro esiti e nessun nome reale negli ambienti di test (punto 7 della Definition of Ready).
- **jira:DEMO-7, messaggio al cliente.** "In elaborazione" senza indicazioni spinge a ripetere il bonifico: serve un testo che dica di non riprovare e quando arriverà l'esito.
- **jira:DEMO-7, epica.** Il bug riguarda l'esecuzione del bonifico, non la verifica del beneficiario: il collegamento a jira:DEMO-2 è da confermare dal PO.

## 3. Azioni proposte

Sono proposte, non requisiti. I valori tra parentesi angolari li definisce il PO con compliance.

1. **jira:DEMO-5**: chiedere il parere di compliance, DPO e legal sui punti critici prima del rilascio (task T1). Il PO decide se sospendere lo sviluppo o tenerlo dietro feature flag.
2. **jira:DEMO-5**: aggiungere gli acceptance criteria P1-P6 qui sotto. Il perimetro cresce: la stima di 5 punti va rivista dal team.
   - P1. Dato un IBAN valido, quando la verifica restituisce "non corrisponde", allora vedo un avviso, `<non vedo il nome del titolare>` e `<posso procedere solo con conferma esplicita | non posso procedere>`.
   - P2. Dato un esito "simile", "non corrisponde" o "non disponibile", quando scelgo di procedere, allora devo confermare in modo esplicito di aver letto l'avviso prima dell'autorizzazione del pagamento.
   - P3. Dato che ho ricevuto un esito, quando modifico nome del beneficiario o IBAN, allora la verifica viene ripetuta prima che io possa autorizzare.
   - P4. Dato un qualsiasi esito, quando la verifica si conclude, allora nome inserito, esito, dati mostrati, scelta del cliente, utente e momento sono registrati nell'audit trail e non compaiono nei log applicativi.
   - P5. Dato che lo stesso utente esegue più di `<N>` verifiche in `<intervallo>` senza disporre bonifici, quando ne chiede un'altra, allora la verifica è `<rifiutata | rallentata>` e l'evento è segnalato all'antifrode.
   - P6. Dato un esito "simile", quando vedo l'avviso, allora del titolare vedo solo `<i dati ammessi dal parere di DPO e compliance>`.
3. **jira:DEMO-5**: aggiungere la label `compliance` (oggi solo `bonifici`).
4. **jira:DEMO-7**: completare la descrizione con ambiente, passi per riprodurre, frequenza, clienti e importi coinvolti, causa del flag "Impediment".
5. **jira:DEMO-7**: aggiungere gli acceptance criteria B1-B4 qui sotto e la label `compliance`.
   - B1. Dato un bonifico istantaneo senza risposta del circuito entro `<timeout>`, quando scade il tempo, allora lo stato viene riallineato con il circuito e diventa "eseguito" o "non eseguito" entro `<tempo massimo>`.
   - B2. Dato un bonifico in attesa di esito, quando apro il dettaglio, allora leggo che l'esito è in verifica, che non devo ripetere il bonifico e ricevo una notifica all'esito definitivo.
   - B3. Dato un bonifico in attesa di esito, quando ne dispongo uno identico (stesso beneficiario, importo e causale), allora ricevo un avviso di possibile doppione prima dell'autorizzazione.
   - B4. Dato un bonifico passato per il timeout, quando lo stato cambia, allora ogni passaggio e ogni intervento manuale è registrato nell'audit trail con autore e momento.
6. **jira:DEMO-7**: far valutare a sicurezza e compliance se il difetto va trattato come incidente operativo (task T2).
7. **jira:DEMO-2**: chiedere a compliance perimetro dei canali, lato "risposta" e eventuale scadenza obbligatoria; poi il PO decide se servono altre story. Nessuna riga in proposte finché non c'è la risposta.

## 4. Dati mancanti

| Cosa manca | Tool | A cosa serve |
|---|---|---|
| Parere di compliance, DPO o legal su jira:DEMO-5 e jira:DEMO-2 | non presente in Jira né in Notion (corpo delle pagine Notion non letto dal sync) | punto 6 della Definition of Ready |
| Dati mostrati per ciascun esito e scenario "non corrisponde" | Jira, DEMO-5 | valutare l'esposizione di dati di terzi |
| Fonte della soglia di 5 secondi | Jira, DEMO-5 | confronto con le regole dello schema |
| Fornitore del servizio di verifica, contratto API, livelli di servizio | Jira, DEMO-5 o DEMO-2 | valutazione GDPR del fornitore e DORA |
| Sequenza verifica, SCA, controlli sanzioni e antifrode | Jira, DEMO-5 | valutare SCA e controlli esistenti |
| Canali coperti (app, API, PISP, bonifici in blocco) e lato "risposta" | Jira, DEMO-2 | perimetro dell'obbligo |
| Scadenza normativa, se esiste | Notion, PAYDISC-1; Jira, DEMO-2 | time criticality e priorità |
| Acceptance criteria, stima, passi per riprodurre, ambiente, frequenza, volumi | Jira, DEMO-7 | gravità reale e valutazione come incidente |
| Motivo del flag "Impediment" e dipendenza collegata | Jira, DEMO-7 | capire da chi dipende lo sblocco |
| Sprint e team (campi vuoti per tutte le issue) | Jira | sapere se gli item stanno per entrare in sprint |
