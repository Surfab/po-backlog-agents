# Story refiner, 2026-11-12

Snapshot letto: `./backlog/2026-11-12_snapshot.json` (generato_il 2026-11-12T23:59:59Z). Story indicate: jira:DEMO-6, jira:DEMO-8. Contesto usato: notion:PAYDISC-3, notion:PAYDISC-2, jira:DEMO-3. Dati fittizi (demo).

Nota sullo snapshot: è precedente alla modifica che ha dato a DEMO-8 l'epica jira:DEMO-9 ("Riconciliazione automatica fatture SDI con gli incassi", realizza notion:PAYDISC-2). L'informazione viene dalla richiesta e da `./output/2026-11-12_change-log.md`; DEMO-9 non è nello snapshot e non è stata letta da Jira.

## 1. Sintesi

- DEMO-6 (21 punti, nessun acceptance criteria) non è una story ma una copia dell'epica DEMO-3: split obbligatorio in 4 story per passo del flusso KYB. Chi apre il conto e chi approva la pratica non sono scritti da nessuna parte: restano domande per il PO.
- DEMO-8 si riscrive con utente e beneficio presi da PAYDISC-2 (addetto all'amministrazione, 6 ore al mese di riconciliazione manuale), ma oggi non è testabile: manca la regola di abbinamento e la descrizione è ambigua ("fatture ricevute" contro "incassi"). Proposto il taglio caso esatto prima, casi limite dopo.
- Entrambe da passare a fintech-compliance prima dello sprint. 10 righe aggiunte in `./output/2026-11-12_proposte.md`. Nessuna stima assegnata: stima il team.

## 2. Problemi

### Critico

- **jira:DEMO-6**: stima 21, sopra il limite di 13, e perimetro uguale a quello dell'epica DEMO-3 (documenti, visura, titolare effettivo, verifica, approvazione). Non testabile come unità. Split obbligatorio.
- **jira:DEMO-6**: tocca identità, dati personali (titolare effettivo) e controlli KYB/AML. Da passare a fintech-compliance. Nessuna valutazione risulta nello snapshot (DoR punto 6).
- **jira:DEMO-6**: l'opportunità di riferimento PAYDISC-3 è ancora "In ricerca" e non è collegata a DEMO-3 (riga 4 di `proposte.md` non applicata). Si rischia di rifinire un flusso che la ricerca può ancora cambiare.
- **jira:DEMO-8**: non testabile. Non è definita la regola con cui un incasso si riconosce come pagamento di una fattura (causale, IBAN virtuale, ordinante, importo). Senza la regola il QA non può verificare nessun criterio.
- **jira:DEMO-8**: descrizione ambigua. "Fatture ricevute da SDI" indica di norma le fatture passive (da pagare), mentre gli "incassi" si abbinano alle fatture emesse. PAYDISC-2 parla solo di "incassi e fatture elettroniche". Il perimetro va chiarito prima di tutto il resto.
- **jira:DEMO-8**: tocca dati di pagamento, dati fiscali e dati degli ordinanti. Da passare a fintech-compliance.

### Medio

- **jira:DEMO-6**: descrizione non nel formato story. Utente e beneficio assenti. Il beneficio si può dedurre da PAYDISC-3 (non abbandonare l'apertura conto al caricamento dei documenti); l'utente no.
- **jira:DEMO-6**: il passo "verifica e approvazione" ha un attore interno (chi approva la pratica) che non è tra le persone di dominio di `CLAUDE.md`.
- **jira:DEMO-6**: "visura" non dice se il documento lo carica il cliente o se i dati si recuperano da un servizio esterno. Cambia la story, le dipendenze e la stima.
- **jira:DEMO-8**: nessuna stima e nessun acceptance criteria. Descrizione non nel formato story.
- **jira:DEMO-8**: dipendenza non nota. Nel backlog non c'è nessun item che porti le fatture SDI dentro Conto Business. Se quella funzione non esiste, DEMO-8 non è indipendente e non è pronta (DoR punto 5).
- **notion:PAYDISC-3**: risk reduction vuoto, quindi il WSJF dell'onboarding KYB non si calcola. Non blocca il refinement, blocca la priorità.

### Basso

- **jira:DEMO-6**: titolo "Gestione completa onboarding KYB" descrive un'epica, non un risultato per l'utente.
- **jira:DEMO-6**: label `onboarding` senza `compliance`, che invece è sull'epica e sull'opportunità.
- **jira:DEMO-8**: "in meno di 6 ore al mese" è il dato di partenza di PAYDISC-2, non un obiettivo misurabile. Manca il valore atteso dopo il rilascio.
- **jira:DEMO-8**: snapshot non aggiornato sul parent (null nello snapshot, DEMO-9 oggi).

## 3. Dettaglio per story

### 3.1 jira:DEMO-6, Gestione completa onboarding KYB

**Riscrittura**

Non proposta per la story intera: così com'è coincide con l'epica DEMO-3. Forma di riferimento, con i dati mancanti in evidenza:

> Come [chi apre il conto per la società: da confermare] voglio completare online la verifica della mia società per aprire il conto senza abbandonare al caricamento dei documenti societari.

- Utente: non ricavabile. PAYDISC-3 e DEMO-3 parlano di "PMI" e "società", non della persona. Ipotesi di lavoro usata nelle story dello split: titolare di PMI. Da confermare (domanda D1).
- Beneficio: ricavato da PAYDISC-3 (41% di abbandono al caricamento dei documenti, obiettivo "meno di 10 minuti"). L'opportunità è in ricerca: il beneficio può cambiare.

**INVEST**

| Criterio | Esito | Motivo |
|---|---|---|
| Independent | no | contiene cinque passi in sequenza, ognuno dipende dal precedente |
| Negotiable | dubbio | "implementare tutto il flusso" fissa il perimetro e non lascia spazio a un primo rilascio ridotto |
| Valuable | dubbio | il valore c'è (PAYDISC-3) ma non è scritto, e arriva solo a flusso completo |
| Estimable | no | 21 punti è già il segnale; visura e approvazione non sono definite |
| Small | no | 21 punti, oltre il limite di 13 |
| Testable | no | nessun acceptance criteria |

**Split proposto**

Criterio di taglio: per passo del flusso. Quattro story sotto l'epica DEMO-3. Proposta: DEMO-6 viene ristretta al primo passo (S1), così conserva storia e collegamento all'epica, e si creano tre nuovi item (S2, S3, S4). Alternativa a scelta del PO: quattro item nuovi e DEMO-6 chiusa come "sostituita da", senza eliminarla. La stima 21 non vale più per nessuna delle quattro: va rifatta dal team.

I valori tra parentesi angolari sono parametri da definire (vedi Dati mancanti). I nomi degli stati sono una proposta.

#### S1 (DEMO-6 ristretta): Caricare online i documenti societari

Come titolare di PMI voglio caricare online i documenti societari richiesti per l'apertura del conto, per completare la richiesta senza interromperla e senza mandare documenti per altri canali.

Valore proprio: è il punto in cui PAYDISC-3 misura l'abbandono.

```gherkin
Scenario: caricamento completo
  Dato una richiesta di apertura conto in stato "bozza" con l'elenco dei documenti richiesti
  Quando carico per ogni documento richiesto un file in un formato ammesso e di dimensione non superiore a <dimensione massima>
  Allora ogni documento risulta "caricato" con nome del file e data
  E posso passare al passo successivo

Scenario: formato non ammesso
  Dato una richiesta in stato "bozza"
  Quando carico un file in un formato non compreso in <formati ammessi>
  Allora il file viene rifiutato con un messaggio che elenca i formati ammessi
  E il documento resta "mancante"

Scenario: file oltre il limite di dimensione
  Dato una richiesta in stato "bozza"
  Quando carico un file più grande di <dimensione massima>
  Allora il file viene rifiutato con un messaggio che indica il limite
  E il documento resta "mancante"

Scenario: documento obbligatorio mancante
  Dato una richiesta in cui almeno un documento obbligatorio è "mancante"
  Quando provo a passare al passo successivo
  Allora non posso proseguire
  E vedo l'elenco dei documenti ancora mancanti

Scenario: caricamento interrotto
  Dato un caricamento in corso
  Quando la connessione cade o il caricamento non termina entro <timeout di caricamento>
  Allora vedo che il caricamento non è riuscito e posso riprovare
  E il documento non risulta né "caricato" né duplicato

Scenario: ripresa della richiesta
  Dato una richiesta in "bozza" con alcuni documenti già caricati
  Quando esco e rientro entro <validità della bozza>
  Allora ritrovo i documenti già caricati e riprendo dal punto in cui ero

Scenario: sostituzione di un documento
  Dato un documento "caricato" e una richiesta non ancora inviata
  Quando carico un nuovo file per lo stesso documento
  Allora il nuovo file sostituisce il precedente
  E la sostituzione è registrata nell'audit trail
```

#### S2 (nuovo item): Dati dell'impresa dalla visura

Come titolare di PMI voglio che i dati della mia società siano presi dalla visura e mi vengano mostrati per conferma, per non doverli inserire a mano.

Valore proprio: riduce il tempo di compilazione. Dipende dalla risposta a D3 (visura caricata dal cliente o recuperata da un servizio).

```gherkin
Scenario: dati letti dalla visura
  Dato una richiesta in "bozza" e una visura disponibile per la società
  Quando arrivo al passo dei dati dell'impresa
  Allora vedo precompilati <campi dell'impresa> letti dalla visura
  E posso confermarli

Scenario: visura non leggibile o servizio non disponibile
  Dato una richiesta in "bozza"
  Quando la visura non è leggibile oppure il servizio non risponde entro <timeout visura>
  Allora vedo che i dati non sono stati recuperati
  E posso riprovare oppure <alternativa prevista: da definire>

Scenario: visura troppo vecchia
  Dato una visura con data anteriore a <anzianità massima>
  Quando viene acquisita
  Allora viene rifiutata con un messaggio che indica l'anzianità massima ammessa

Scenario: dati non corrispondenti
  Dato dati dell'impresa letti dalla visura
  Quando la partita IVA della visura è diversa da quella indicata all'inizio della richiesta
  Allora non posso confermare
  E vedo quale dato non corrisponde

Scenario: impresa in uno stato non ammesso
  Dato una visura che riporta <stati dell'impresa non ammessi>
  Quando viene acquisita
  Allora la richiesta non prosegue in automatico
  E vedo <messaggio e passo successivo: da definire con compliance>
```

#### S3 (nuovo item): Dichiarare il titolare effettivo

Come titolare di PMI voglio dichiarare online chi è il titolare effettivo della società, per completare la verifica senza moduli cartacei.

Valore proprio: chiude la parte dichiarativa del KYB. La regola per individuare il titolare effettivo e i dati da raccogliere li indica compliance, non questa story.

```gherkin
Scenario: dichiarazione completa
  Dato una richiesta in "bozza" con i dati dell'impresa confermati
  Quando inserisco per ogni titolare effettivo <dati richiesti> e confermo
  Allora la dichiarazione risulta "completa"
  E vedo il riepilogo dei titolari effettivi dichiarati

Scenario: nessun titolare effettivo dichiarato
  Dato una richiesta senza titolari effettivi inseriti
  Quando provo a proseguire
  Allora non posso proseguire
  E vedo che serve almeno un titolare effettivo

Scenario: dato obbligatorio mancante o non valido
  Dato un titolare effettivo con un dato obbligatorio vuoto o in formato non valido
  Quando confermo
  Allora la conferma è rifiutata
  E il campo da correggere è indicato

Scenario: quote incoerenti
  Dato più titolari effettivi con quota di partecipazione
  Quando la somma delle quote supera il 100%
  Allora la conferma è rifiutata con un messaggio sull'incoerenza delle quote

Scenario: modifica prima dell'invio
  Dato una dichiarazione "completa" e una richiesta non ancora inviata
  Quando modifico o rimuovo un titolare effettivo
  Allora il riepilogo si aggiorna
  E la modifica è registrata nell'audit trail

Scenario: dati personali fuori dai log
  Dato una dichiarazione salvata
  Quando si esaminano i log applicativi dell'operazione
  Allora non contengono dati personali dei titolari effettivi
```

#### S4 (nuovo item): Invio della pratica, verifica ed esito

Come titolare di PMI voglio inviare la richiesta e conoscere l'esito della verifica, per sapere quando il conto è aperto o che cosa devo integrare.

Valore proprio: è il passo che porta all'apertura del conto. L'attore che approva non è definito (domanda D2). Se il team la stima oltre 13, il taglio successivo è per regola: verifica automatica prima, revisione manuale dopo.

```gherkin
Scenario: invio della pratica completa
  Dato una richiesta con documenti caricati, dati dell'impresa confermati e titolare effettivo dichiarato
  Quando la invio
  Allora passa in stato "in verifica"
  E non posso più modificarla

Scenario: invio di una pratica incompleta
  Dato una richiesta a cui manca almeno uno dei tre passi
  Quando provo a inviarla
  Allora l'invio è rifiutato
  E vedo quali passi mancano

Scenario: approvazione
  Dato una pratica "in verifica"
  Quando viene approvata
  Allora passa in stato "approvata"
  E ricevo la comunicazione dell'esito su <canale>
  E <effetto sul conto: da definire>

Scenario: richiesta di integrazione
  Dato una pratica "in verifica"
  Quando viene richiesta un'integrazione
  Allora passa in stato "integrazione richiesta"
  E vedo quali documenti o dati devo fornire
  E posso modificare solo quelli richiesti e inviare di nuovo

Scenario: rifiuto
  Dato una pratica "in verifica"
  Quando viene respinta
  Allora passa in stato "respinta"
  E ricevo <comunicazione prevista: testo e motivazione da definire con compliance>
  E non posso inviarla di nuovo

Scenario: verifica oltre il tempo previsto
  Dato una pratica "in verifica" da più di <tempo massimo di verifica>
  Quando apro la richiesta
  Allora vedo che la verifica è ancora in corso
  E <comunicazione prevista: da definire>

Scenario: traccia delle decisioni
  Dato una pratica che cambia stato
  Quando il cambio è registrato
  Allora l'audit trail riporta stato precedente, stato nuovo, autore e data
```

### 3.2 jira:DEMO-8, Abbinamento automatico tra fatture elettroniche e incassi

Epica oggi: jira:DEMO-9 (non nello snapshot). Opportunità: notion:PAYDISC-2, "Validata", business value 8, time criticality 5, risk reduction 3.

**Riscrittura**

> Come addetto all'amministrazione voglio che gli incassi sul conto siano abbinati in automatico alle fatture elettroniche corrispondenti, per non riconciliarli a mano (oggi in media 6 ore al mese).

- Utente: da PAYDISC-2 ("chi tiene l'amministrazione").
- Beneficio: da PAYDISC-2 (6 ore al mese; 64% riconcilia su foglio di calcolo).
- "Fatture elettroniche corrispondenti" lascia aperta di proposito la domanda D5 (emesse o ricevute).

**INVEST**

| Criterio | Esito | Motivo |
|---|---|---|
| Independent | dubbio | presuppone che le fatture SDI siano già disponibili nel prodotto; nel backlog non c'è un item che lo faccia |
| Negotiable | sì | descrive il risultato, non la soluzione |
| Valuable | sì | bisogno validato in PAYDISC-2 |
| Estimable | no | regola di abbinamento non definita, perimetro ambiguo, nessuna stima |
| Small | dubbio | senza stima non si sa; "riconciliare in automatico" comprende caso esatto, parziali, cumulativi, non abbinati, storni |
| Testable | no | nessun acceptance criteria e nessuna regola verificabile |

**Split proposto**

Obbligatorio perché la story non è testabile. Criterio di taglio: caso felice prima, casi limite dopo. DEMO-8 viene ristretta al caso esatto (un incasso, una fattura, stesso importo); tre nuovi item sotto DEMO-9.

#### DEMO-8 ristretta: Abbinamento automatico nel caso esatto

Come addetto all'amministrazione voglio che un incasso che corrisponde a una sola fattura aperta dello stesso importo sia abbinato in automatico, per non riconciliare a mano i casi più semplici.

`<regola di identificazione>` è il criterio con cui un incasso si riferisce a una fattura: da definire (domanda D4). Gli importi sono esempi.

```gherkin
Scenario: abbinamento esatto
  Dato una fattura "da incassare" di 1.220,00 EUR
  E un incasso contabilizzato di 1.220,00 EUR che soddisfa la <regola di identificazione> per quella sola fattura
  Quando l'incasso viene contabilizzato
  Allora l'incasso risulta "abbinato" alla fattura
  E la fattura passa a "incassata"
  E da ciascuno dei due vedo il riferimento all'altro

Scenario: importo diverso
  Dato una fattura "da incassare" di 1.220,00 EUR
  E un incasso contabilizzato di 1.219,99 EUR che soddisfa la <regola di identificazione>
  Quando l'incasso viene contabilizzato
  Allora nessun abbinamento automatico viene creato
  E l'incasso resta "da abbinare"
  E la fattura resta "da incassare"

Scenario: più fatture candidate
  Dato due fatture "da incassare" che soddisfano entrambe la <regola di identificazione> per lo stesso incasso
  Quando l'incasso viene contabilizzato
  Allora nessun abbinamento automatico viene creato
  E l'incasso resta "da abbinare"

Scenario: nessuna fattura candidata
  Dato un incasso contabilizzato per cui nessuna fattura soddisfa la <regola di identificazione>
  Quando l'incasso viene contabilizzato
  Allora l'incasso resta "da abbinare"

Scenario: incasso non ancora contabilizzato
  Dato un incasso in stato "in elaborazione"
  Quando il sistema valuta gli abbinamenti
  Allora l'incasso non viene abbinato finché non è contabilizzato

Scenario: fattura già incassata
  Dato una fattura "incassata" e già abbinata
  E un secondo incasso dello stesso importo che soddisfa la <regola di identificazione>
  Quando il secondo incasso viene contabilizzato
  Allora la fattura non cambia
  E il secondo incasso resta "da abbinare"

Scenario: incasso stornato dopo l'abbinamento
  Dato un incasso "abbinato" a una fattura "incassata"
  Quando l'incasso viene stornato
  Allora l'abbinamento viene rimosso
  E la fattura torna "da incassare"
  E la rimozione è registrata nell'audit trail

Scenario: tempo di abbinamento
  Dato un incasso che soddisfa le condizioni dell'abbinamento esatto
  Quando viene contabilizzato
  Allora l'abbinamento è visibile entro <tempo massimo di abbinamento>

Scenario: traccia dell'abbinamento
  Dato un abbinamento automatico creato
  Quando apro il dettaglio
  Allora vedo data, ora e regola applicata
  E l'operazione è nell'audit trail
```

#### S5 (nuovo item): Abbinare a mano gli incassi non abbinati

Come addetto all'amministrazione voglio vedere gli incassi non abbinati e collegarli a mano a una fattura, per chiudere la riconciliazione anche quando l'automatismo non decide.

```gherkin
Scenario: elenco degli incassi da abbinare
  Dato incassi in stato "da abbinare"
  Quando apro la riconciliazione
  Allora li vedo in elenco con data, importo, ordinante e causale

Scenario: abbinamento manuale
  Dato un incasso "da abbinare" e una fattura "da incassare" dello stesso importo
  Quando li abbino a mano
  Allora l'incasso risulta "abbinato" e la fattura "incassata"
  E l'abbinamento riporta chi l'ha fatto e quando

Scenario: importo diverso
  Dato un incasso "da abbinare" e una fattura di importo diverso
  Quando provo ad abbinarli
  Allora <comportamento: rifiuto, da confermare; i parziali sono in S6>

Scenario: annullamento
  Dato un abbinamento esistente
  Quando lo annullo
  Allora incasso e fattura tornano a "da abbinare" e "da incassare"
  E l'annullamento è registrato nell'audit trail

Scenario: nessun incasso da abbinare
  Dato nessun incasso "da abbinare"
  Quando apro la riconciliazione
  Allora vedo che non c'è nulla da abbinare
```

#### S6 (nuovo item): Incassi parziali

Come addetto all'amministrazione voglio che un incasso inferiore al totale della fattura sia registrato come pagamento parziale, per sapere quanto resta da incassare.

```gherkin
Scenario: primo pagamento parziale
  Dato una fattura "da incassare" di 1.000,00 EUR
  Quando le viene abbinato un incasso di 400,00 EUR
  Allora la fattura passa a "incassata in parte" con residuo 600,00 EUR

Scenario: saldo
  Dato una fattura "incassata in parte" con residuo 600,00 EUR
  Quando le viene abbinato un incasso di 600,00 EUR
  Allora la fattura passa a "incassata" con residuo 0,00 EUR

Scenario: incasso superiore al residuo
  Dato una fattura con residuo 600,00 EUR
  Quando arriva un incasso di 700,00 EUR riferito a quella fattura
  Allora <comportamento sull'eccedenza: da definire>

Scenario: storno di un parziale
  Dato una fattura "incassata in parte" con due incassi abbinati
  Quando uno dei due viene stornato
  Allora il residuo aumenta dell'importo stornato
  E lo stato della fattura si aggiorna di conseguenza
```

#### S7 (nuovo item): Un incasso per più fatture

Come addetto all'amministrazione voglio che un solo incasso che paga più fatture dello stesso cliente sia abbinato a tutte, per non dividerlo a mano.

```gherkin
Scenario: somma esatta
  Dato due fatture "da incassare" di 500,00 EUR e 720,00 EUR dello stesso cliente
  E un incasso di 1.220,00 EUR che soddisfa la <regola di identificazione> per entrambe
  Quando l'incasso viene contabilizzato
  Allora l'incasso risulta abbinato a entrambe
  E entrambe passano a "incassata"

Scenario: somma non corrispondente
  Dato fatture candidate la cui somma è diversa dall'importo dell'incasso
  Quando l'incasso viene contabilizzato
  Allora nessun abbinamento automatico viene creato
  E l'incasso resta "da abbinare"

Scenario: più combinazioni possibili
  Dato fatture candidate che danno la somma dell'incasso in più di una combinazione
  Quando l'incasso viene contabilizzato
  Allora nessun abbinamento automatico viene creato
  E l'incasso resta "da abbinare"

Scenario: storno
  Dato un incasso abbinato a più fatture
  Quando viene stornato
  Allora tutte le fatture collegate tornano "da incassare"
```

## 4. Azioni proposte

1. **jira:DEMO-6**: il PO risponde a D1, D2, D3 prima del refinement. Senza D1 il formato story resta su un'ipotesi.
2. **jira:DEMO-6**: restringere la story al primo passo, "Caricare online i documenti societari" (titolo, descrizione, acceptance criteria), e svuotare la stima 21 in attesa della nuova stima del team.
3. **jira:DEMO-6**: creare tre nuovi item sotto DEMO-3: S2 dati dell'impresa dalla visura, S3 titolare effettivo, S4 invio, verifica ed esito.
4. **jira:DEMO-6**: passare DEMO-6 e i tre nuovi item a fintech-compliance. Da verificare con compliance: regola del titolare effettivo, dati da raccogliere, motivazione del rifiuto comunicabile al cliente, stati dell'impresa non ammessi, conservazione dei documenti.
5. **jira:DEMO-6**: il PO decide se rifinire ora o attendere la chiusura della ricerca su PAYDISC-3, e se collegare PAYDISC-3 a DEMO-3 (riga 4 di `proposte.md`, ancora aperta).
6. **jira:DEMO-8**: il PO risponde a D4 e D5 prima del refinement. Senza la regola di identificazione gli acceptance criteria non sono eseguibili.
7. **jira:DEMO-8**: restringere la story al caso esatto (titolo, descrizione, acceptance criteria).
8. **jira:DEMO-8**: creare tre nuovi item sotto DEMO-9: S5 abbinamento manuale, S6 incassi parziali, S7 un incasso per più fatture.
9. **jira:DEMO-8**: verificare se esiste la funzione che porta le fatture SDI nel prodotto. Se non esiste, serve un item e un collegamento "bloccato da".
10. **jira:DEMO-8**: passare la story e i tre nuovi item a fintech-compliance (dati di pagamento, fiscali e degli ordinanti).
11. **jira:DEMO-6, jira:DEMO-8**: far stimare al team tutte le story dopo le risposte del PO.
12. **jira:DEMO-8**: rilanciare backlog-sync, così lo snapshot riporta DEMO-9 e il nuovo parent.

Domande per il PO:

- **D1 (DEMO-6)**: chi apre il conto per la società? Il titolare o legale rappresentante, oppure anche una persona delegata dell'amministrazione?
- **D2 (DEMO-6)**: chi verifica e approva la pratica? Un controllo automatico, un operatore interno, entrambi? Serve una story per lo strumento dell'operatore?
- **D3 (DEMO-6)**: la visura la carica il cliente o i dati si recuperano da un servizio esterno a partire dalla partita IVA?
- **D4 (DEMO-8)**: con quale regola un incasso si riconosce come pagamento di una fattura? Numero fattura in causale, IBAN virtuale per cliente o per fattura, dati dell'ordinante, combinazione di questi?
- **D5 (DEMO-8)**: la story riguarda le fatture emesse (da incassare) o davvero le fatture ricevute via SDI (da pagare)? Nel secondo caso si abbinano pagamenti in uscita, non incassi.
- **D6 (DEMO-8)**: qual è l'obiettivo dopo il rilascio? Ore al mese, o quota di incassi abbinati in automatico.
- **D7 (DEMO-6)**: "meno di 10 minuti" di PAYDISC-3 è un criterio di accettazione o un obiettivo di prodotto da misurare dopo?

## 5. Dati mancanti

| Cosa manca | Dove | A cosa serve |
|---|---|---|
| Persona che apre il conto (D1) | Jira DEMO-6, DEMO-3; Notion PAYDISC-3 | scrivere le story dello split nel formato story |
| Attore e modalità dell'approvazione (D2) | Jira DEMO-6 | perimetro e acceptance criteria di S4 |
| Origine della visura (D3) | Jira DEMO-6 | perimetro, dipendenze e stima di S2 |
| Elenco dei documenti richiesti, formati ammessi, dimensione massima, timeout di caricamento, validità della bozza | Jira DEMO-6 | rendere eseguibili i criteri di S1 |
| Campi dell'impresa, anzianità massima della visura, timeout, stati dell'impresa non ammessi | Jira DEMO-6; compliance | criteri di S2 |
| Dati richiesti per il titolare effettivo e regola per individuarlo | compliance | criteri di S3 |
| Stati della pratica, canale e testo delle comunicazioni, tempo massimo di verifica, effetto dell'approvazione sul conto | Jira DEMO-6 | criteri di S4 |
| Esito della ricerca e risk reduction | Notion PAYDISC-3 ("In ricerca", risk reduction vuoto) | confermare il beneficio e calcolare il WSJF |
| Regola di identificazione incasso-fattura (D4) | Jira DEMO-8; Notion PAYDISC-2 | tutti i criteri di DEMO-8 e di S7 |
| Fatture emesse o ricevute (D5) | Jira DEMO-8 | perimetro della story |
| Stati di fattura e incasso, tolleranza sull'importo, tempo massimo di abbinamento, comportamento sull'eccedenza | Jira DEMO-8 | criteri di DEMO-8, S5, S6 |
| Item che porta le fatture SDI nel prodotto | Jira (assente nello snapshot) | dipendenza di DEMO-8, DoR punto 5 |
| Stima del team | Jira DEMO-8 (vuota), DEMO-6 (21, da rifare) | DoR punto 3, job size del WSJF |
| Corpo delle pagine Notion | Notion PAYDISC-2, PAYDISC-3 (non letto dal sync) | potrebbe già contenere utente, regole o risposte alle domande |
| Item jira:DEMO-9 e parent aggiornato di DEMO-8 | snapshot (precedente alla modifica) | verificare titolo, label e stato dell'epica |
| Sprint | Jira (non restituito) | sapere se le due story sono già candidate a uno sprint |
| Mockup, contratto API, dati di test | non presenti nello snapshot | DoR punti 7 e 8 |
