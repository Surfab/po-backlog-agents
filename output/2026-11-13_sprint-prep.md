# Sprint prep, PAY Sprint 1 (16-27 novembre 2026), 2026-11-13

Snapshot letto: `./backlog/2026-11-13_snapshot.json` (21 item: 5 Notion, 16 Jira). Candidati: i 5 item con `sprint` = "PAY Sprint 1" (jira:DEMO-5, DEMO-6, DEMO-7, DEMO-8, DEMO-10). Report considerati: `2026-11-12_fintech-compliance.md`, `2026-11-12_story-refiner.md`, `2026-11-13_prioritizer.md`, `2026-11-13_change-log.md`. Team: 6 sviluppatori, 1 QA. Dati fittizi (demo).

## 1. Sintesi

- Nessuno dei 5 candidati supera oggi la Definition of Ready. I più vicini sono jira:DEMO-8 e jira:DEMO-10, che possono chiudere i punti aperti entro il planning; jira:DEMO-5, DEMO-6 e DEMO-7 vanno in refinement.
- La capacità non si può valutare: non esiste nessuno sprint chiuso, quindi nessuna velocity. I punti candidati sono 15 su 4 item, DEMO-6 è senza stima, e 3 stime su 4 non vengono dal team.
- La bozza di goal in Jira dipende solo da jira:DEMO-5, che ha rischi di compliance critici e attende il parere di jira:DEMO-10, pianificato nello stesso sprint. Aggiunte 6 righe "in attesa" in `2026-11-13_proposte.md`.

## 2. Bozza di sprint goal

Bozza per il team, non una decisione. Bozza oggi in Jira: "il titolare di una PMI vede chi sta pagando prima di confermare un bonifico istantaneo". Copre solo DEMO-5: DEMO-6 (KYB) e DEMO-8 (riconciliazione) appartengono ad altre due epiche e restano fuori dal goal.

- **Bozza coerente con la DoR** (vale se DEMO-8 e DEMO-10 chiudono i punti aperti al planning): "L'addetto all'amministrazione trova già abbinati alla fattura gli incassi che ne citano il numero in causale per l'importo esatto, e sappiamo per iscritto che cosa possiamo mostrare al cliente nella verifica del beneficiario."
- **Alternativa 1** (solo se il PO tiene DEMO-5 nello sprint fuori dalla DoR, in modo esplicito): "Il titolare di una PMI vede in ambiente di prova, dietro feature flag, l'esito della verifica del beneficiario prima di confermare un bonifico istantaneo. Nessun rilascio ai clienti prima del parere."
- **Alternativa 2** (sprint di sblocco): "A fine sprint la verifica del beneficiario e il primo passo dell'onboarding KYB sono pronti per essere sviluppati: parere ricevuto, parametri definiti, story stimate dal team."

## 3. Verifica Definition of Ready

La DoR è scritta per le story. Per il bug (DEMO-7) e il task (DEMO-10) i punti che non si applicano al tipo sono segnati "n.a.". Per i punti 7 e 8 lo snapshot non contiene nessun dato: sono "no" perché non verificabili, non perché risulti un problema.

| id | 1 formato | 2 criteri | 3 stima | 4 epica | 5 dipendenze | 6 compliance | 7 dati di test | 8 mockup/API | Esito |
|---|---|---|---|---|---|---|---|---|---|
| jira:DEMO-5 | ok | no | ok | ok | no | no | no | no | refinement |
| jira:DEMO-6 | ok | no | no | ok | no | no | no | no | refinement |
| jira:DEMO-7 | n.a. | no | no | ok | no | no | no | n.a. | refinement |
| jira:DEMO-8 | ok | no | no | ok | no | no | no | no | entra solo se chiude i punti al planning |
| jira:DEMO-10 | n.a. | no | no | ok | no | n.a. | n.a. | n.a. | entra solo se chiude i punti al planning |

Motivi dei "no":

- **jira:DEMO-5**. 2: manca l'esito "non corrisponde"; i criteri P1-P6 sono proposti, con parti tra parentesi quadre, in attesa del parere. 3: 5 punti, ma da rivedere se entrano P1-P6. 5: il legame con DEMO-10 è solo nel testo di DEMO-10, `dipendenze` è vuoto, ed è bloccante. 6: passata da fintech-compliance con esito critico, parere non ancora arrivato. 8: fornitore e contratto API della verifica non indicati.
- **jira:DEMO-6**. 2: 5 criteri su 7 hanno parametri non definiti (formati ammessi, dimensione massima, timeout, validità della bozza); manca l'elenco dei documenti richiesti. 3: stima vuota. 5: nessuna dipendenza registrata; l'opportunità notion:PAYDISC-3 è ancora "In ricerca". 6: non è passata da fintech-compliance (KYB, AML, dati personali). Utente "titolare di PMI" ancora ipotesi (domanda D1 di story-refiner).
- **jira:DEMO-7**. 2: nessun criterio. 3: i 3 punti sono un valore inserito per la demo, non del team (`2026-11-13_change-log.md`). 5: stato "blocked" con flag Impediment, causa non registrata, nessun link. 6: esito critico, nessun parere, task di valutazione come incidente operativo non creato.
- **jira:DEMO-8**. 2: 8 criteri su 9 verificabili; resta [tempo massimo di abbinamento]. 3: i 5 punti non sono del team. 5: nel backlog non c'è l'item che porta le fatture SDI nel prodotto; dipendenza non chiarita. 6: non è passata da fintech-compliance (dati di pagamento, fiscali, degli ordinanti).
- **jira:DEMO-10**. 2: nessun criterio di uscita (ad esempio: parere scritto sulle 10 domande, allegato all'item). 3: i 2 punti non sono del team. 5: dipende da compliance, DPO e legal; disponibilità e tempi di risposta non noti.

**Proposta di sprint**: oggi vuota. Entrano DEMO-8 e DEMO-10 solo se i punti sopra si chiudono prima o durante il planning.

## 4. Capacità

- Punti candidati: 15 su 4 item (DEMO-5 5, DEMO-8 5, DEMO-7 3, DEMO-10 2). DEMO-6 non è stimata: il totale dei 5 candidati non si calcola.
- Velocity media degli ultimi 3 sprint chiusi: **dato mancante**. Nello snapshot non c'è nessun item `done` con `sprint` valorizzato (l'unico item Jira `done`, DEMO-1, non ha sprint) e PAY Sprint 1 è il primo sprint, in stato "future". Nessun numero proposto.
- Senza storico l'impegno lo decide il team al planning. Le stime di DEMO-7, DEMO-8 e DEMO-10 vanno rifatte dal team prima di sommarle.
- Domanda al PO: assenze, ferie, festività locali e altri impegni del team tra il 16 e il 27 novembre. Lo snapshot non li contiene.
- Un solo QA su tre epiche diverse: da discutere al planning come vincolo, lo snapshot non permette di misurarlo.

## 5. Problemi

### Critico

- **jira:DEMO-5**: già "In corso" senza parere su 6 punti critici (nome di un terzo mostrato con esito "simile", esito "non corrisponde" assente, uso del servizio per ricavare nomi da IBAN, procedere con verifica non disponibile su un bonifico irrevocabile, ordine tra verifica e SCA, nessuna valutazione prima dell'avvio). Il parere (DEMO-10) è nello stesso sprint: se arriva tardi o cambia i criteri, il lavoro fatto va rifatto e il goal salta.
- **jira:DEMO-7**: bloccato senza causa né dipendenza, nessun criterio, esito ignoto di un bonifico irrevocabile con rischio di doppio pagamento, possibile incidente operativo da far valutare a sicurezza e compliance. Non pianificabile.
- **jira:DEMO-6**: senza stima, con 5 criteri su 7 non eseguibili e senza passaggio da fintech-compliance. Non pianificabile.
- **jira:DEMO-5, DEMO-6, DEMO-7, DEMO-8, DEMO-10**: nessun candidato supera la DoR a un giorno lavorativo dall'inizio dello sprint.
- **jira:DEMO-7, DEMO-8, DEMO-10**: le stime sono valori inseriti per la demo dalla sessione principale, non dal team. Anche la scelta "DEMO-5 dietro feature flag", la regola "numero fattura in causale" e il perimetro "fatture emesse" sono decisioni prese al posto del PO: vanno confermate dal PO reale prima del planning.

### Medio

- **jira:DEMO-8**: dipendenza non chiarita dalla disponibilità delle fatture SDI nel prodotto; non valutata da fintech-compliance.
- **jira:DEMO-10**: nessun criterio di uscita e nessun responsabile; dipende da persone esterne al team. Se resta "Da completare" a metà sprint, DEMO-5 non si chiude.
- **jira:DEMO-5**: la dipendenza da DEMO-10 non è un link: il prossimo snapshot non la mostrerà.
- **jira:DEMO-5, DEMO-6, DEMO-8**: tre epiche in uno sprint con un solo goal. Il goal attuale ne copre una.
- **jira:DEMO-5, DEMO-6, DEMO-7, DEMO-8, DEMO-10**: nessun assegnatario e campo Team vuoto.

### Basso

- **jira:DEMO-7**: collegamento all'epica DEMO-2 da confermare (riguarda l'esecuzione del bonifico, non la verifica del beneficiario).
- **jira:DEMO-5**: testi degli avvisi da far approvare a legal; servono IBAN di prova per i quattro esiti, senza nomi reali.
- **jira:DEMO-6**: label `compliance` presente ma nessuna valutazione collegata.

## 6. Rischi dello sprint

| Rischio | Item | Cosa osservare |
|---|---|---|
| Parere in ritardo o che cambia il perimetro | jira:DEMO-10, jira:DEMO-5 | data concordata per la risposta; chi la dà |
| Rilascio di dati di terzi senza base verificata | jira:DEMO-5 | feature flag spento in produzione fino al parere |
| Item bloccato in sprint | jira:DEMO-7 | causa del flag Impediment |
| Possibile incidente operativo non classificato | jira:DEMO-7 | task T2 ancora "in attesa" in `2026-11-12_proposte.md` |
| Dipendenza nascosta | jira:DEMO-8 | esiste o no la fonte delle fatture emesse nel prodotto |
| Stime non del team | jira:DEMO-7, DEMO-8, DEMO-10 | nuova stima al planning |
| Ricerca ancora aperta | jira:DEMO-6, notion:PAYDISC-3 | la ricerca può cambiare il flusso |

## 7. Scaletta review (27 novembre)

Vale per ciò che il team avrà davvero finito secondo la Definition of Done. Gli stakeholder sono indicati per ruolo: i nomi non sono nello snapshot.

1. Sprint goal e esito, 5 minuti. A tutti.
2. jira:DEMO-8, abbinamento nel caso esatto, se finita: incasso con numero fattura in causale e importo esatto, poi un caso che non si abbina (1.219,99 EUR), poi lo storno. A chi rappresenta gli addetti all'amministrazione, supporto, commerciale. Domande: la regola "numero in causale" copre i casi reali? Quali casi non abbinati pesano di più (manuale, parziali, più fatture: DEMO-14, 15, 16)?
3. jira:DEMO-10, parere ricevuto: che cosa cambia per la verifica del beneficiario. A compliance, DPO, legal, antifrode. Domande: i criteri P1-P6 riflettono il parere? Esiste una scadenza obbligatoria?
4. jira:DEMO-5, solo se il PO l'ha tenuta: dimostrazione in ambiente di prova, dietro feature flag, dei tre esiti coperti. Dire in modo esplicito che non è rilasciata. Domande: testi degli avvisi, che cosa mostrare per "simile".
5. Che cosa non è stato fatto e perché (DEMO-6, DEMO-7), 5 minuti.
6. Prossimi passi sul backlog, con il PO.

## 8. Scaletta retrospettiva

Primo sprint: non ci sono riporti né storico. Formato proposto: linea del tempo dello sprint (10 minuti), poi "Continuare, Smettere, Provare", una azione con un responsabile. 60 minuti.

Domande basate sullo snapshot:

1. jira:DEMO-5 è partita prima della valutazione di compliance. Che cosa ci serve per non iniziare una story che non ha superato il punto 6 della DoR?
2. jira:DEMO-7 ha il flag Impediment senza causa né item collegato. Come registriamo un blocco perché chi legge sappia da chi dipende?
3. Le story sono state divise, stimate e messe in sprint il giorno prima, con parametri ancora tra parentesi quadre e stime non del team. Quanto refinement ci serve prima del planning, e chi stima?

## 9. Azioni proposte

1. **jira:DEMO-6**: togliere dallo sprint e portare in refinement (riga in `proposte.md`). Servono: parametri dei criteri, stima del team, passaggio da fintech-compliance.
2. **jira:DEMO-7**: togliere dallo sprint finché non sono note causa del blocco, passi per riprodurre e criteri (riga in `proposte.md`). Le 4 righe del 2026-11-12 su DEMO-7 restano "in attesa": il PO le decide.
3. **jira:DEMO-5**: il PO decide tra togliere dallo sprint fino al parere (riga in `proposte.md`) e tenerla dietro feature flag come eccezione esplicita alla DoR, con goal dell'alternativa 1.
4. **jira:DEMO-5**: registrare il link "bloccato da DEMO-10" (riga in `proposte.md`).
5. **jira:DEMO-10**: al planning definire criterio di uscita, responsabile, data attesa della risposta; far stimare al team. Se non si chiude, esce dallo sprint (riga condizionata).
6. **jira:DEMO-8**: prima del planning passare da fintech-compliance, chiarire la fonte delle fatture emesse, definire [tempo massimo di abbinamento], far stimare al team. Se non si chiude, esce dallo sprint (riga condizionata).
7. **jira:DEMO-7, DEMO-8, DEMO-10**: far rifare le stime al team al planning.
8. **jira:DEMO-5, DEMO-8**: il PO conferma o cambia le decisioni prese al suo posto (feature flag, fatture emesse, numero fattura in causale).
9. **jira:DEMO-5, DEMO-6, DEMO-7, DEMO-8, DEMO-10**: il PO comunica assenze e festività del team dal 16 al 27 novembre.
10. **jira:DEMO-5, DEMO-8, DEMO-10**: il team sceglie il goal al planning in base a ciò che resta nello sprint. Nessuna riga in `proposte.md` sul goal: è una bozza.

## 10. Dati mancanti

| Cosa manca | Tool | A cosa serve |
|---|---|---|
| Sprint chiusi con item `done` (almeno 3) | Jira | velocity e confronto con i punti candidati |
| Assenze, ferie, festività del team 16-27 novembre | nessun tool nello snapshot, da chiedere al PO | capacità |
| Stima di DEMO-6; stime del team per DEMO-7, DEMO-8, DEMO-10 | Jira | DoR punto 3, totale dei punti candidati |
| Parere di compliance, DPO e legal | Jira, DEMO-10 | DoR punto 6 di DEMO-5 |
| Passaggio da fintech-compliance di DEMO-6 e DEMO-8 | `./output/` | DoR punto 6 |
| Causa del flag Impediment, passi per riprodurre, ambiente, volumi | Jira, DEMO-7 | sblocco e valutazione come incidente |
| Link tra item (issuelinks vuoto su tutte le issue) | Jira | DoR punto 5, rischi di dipendenza |
| Fonte delle fatture emesse nel prodotto | Jira (nessun item) | dipendenza di DEMO-8 |
| Dati di test, ambienti, mockup, contratti API | non presenti nello snapshot | DoR punti 7 e 8 |
| Assegnatari e Team (customfield_10001 non restituito) | Jira | carico per persona, carico sul QA |
| Commenti delle issue (decisione sul feature flag di DEMO-5) | Jira, non letti dal sync | verificare la decisione e chi l'ha presa |
| Stakeholder della review e data del planning | nessun tool nello snapshot | inviti e scaletta |
