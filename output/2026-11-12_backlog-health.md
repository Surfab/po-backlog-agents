# Backlog health, 2026-11-12

Snapshot letto: `./backlog/2026-11-12_snapshot.json` (generato_il 2026-11-12T23:59:59Z, 13 item: 5 Notion, 8 Jira, entrambi via connettore). Dati fittizi (demo).

## 1. Sintesi

- Trovati 4 problemi critici: 3 sospetti duplicati o item non collegati tra Notion e Jira, e 1 opportunità validata senza epica (PAYDISC-2).
- Trovati 6 problemi medi (2 story incomplete, 1 stima a 21 punti, 2 item bloccati senza motivo, 1 story senza epica) e 3 bassi.
- Nessun item fermo e nessuna dipendenza rotta; il campo sprint è vuoto su tutti gli item, quindi la gravità "in sprint" non è verificabile.

## 2. Problemi

### Critico

- **notion:PAYDISC-2 / jira:DEMO-8**: sospetto duplicato tra tool. "Riconciliazione automatica fatture SDI con gli incassi" e "Abbinamento automatico tra fatture elettroniche e incassi" descrivono lo stesso bisogno (riconciliare fatture SDI e incassi senza lavoro manuale), stessa label `fatture`, nessun collegamento tra i due. Fonte di verità: Notion per l'opportunità, Jira per la story. Non va chiuso nessuno dei due: vanno collegati tramite un'epica Jira, che oggi non esiste.
- **notion:PAYDISC-2**: opportunità in stato "Validata" senza dipendenza `realizza` e con `Epica Jira` vuoto. Il lavoro validato non ha un'epica che lo porti in delivery.
- **notion:PAYDISC-3 / jira:DEMO-3**: sospetto duplicato tra tool. "Onboarding KYB in meno di 10 minuti" e l'epica "Onboarding KYB digitale" descrivono lo stesso bisogno (apertura conto per società senza abbandono al caricamento documenti), label comuni `onboarding` e `compliance`, ma `Epica Jira` è vuoto e non c'è dipendenza `realizza`. Fonte di verità: Notion per l'opportunità, Jira per l'epica. Nota: l'opportunità è ancora "In ricerca" mentre l'epica ha già una story da 21 punti.
- **jira:DEMO-6 / jira:DEMO-3**: sospetto duplicato dentro Jira. La story "Gestione completa onboarding KYB" copre l'intero perimetro della sua epica (documenti, visura, titolare effettivo, verifica, approvazione). Fonte di verità: Jira per entrambe. La story non è una story ma una copia dell'epica: va divisa, non chiusa.

### Medio

- **jira:DEMO-6**: stima 21 punti, sopra il limite di 13. Va divisa.
- **jira:DEMO-6**: story senza acceptance criteria. Descrizione non nel formato story (manca utente e beneficio).
- **jira:DEMO-8**: story senza acceptance criteria e senza stima. Descrizione non nel formato story.
- **jira:DEMO-8**: story senza epica (`parent` null).
- **jira:DEMO-7**: stato `blocked` (flag "Impediment") senza dipendenza che spieghi il blocco. È un bug sui bonifici istantanei sotto un'epica in corso: se è in sprint diventa critico, ma lo sprint non è leggibile.
- **notion:PAYDISC-5**: stato `blocked` ("In attesa") senza dipendenza né motivo del blocco.

### Basso

- **jira:DEMO-4**: epica vuota, nessun item la indica come `parent`.
- **jira:DEMO-1**: task senza epica, senza descrizione e senza label. Titolo "Demo": la nota di sync lo indica come item di prova fuori dal backlog.
- **jira:DEMO-2**: epica in corso con priorità "Medium" (valore di default) mentre l'opportunità che realizza, PAYDISC-1, è "Alta". Disallineamento da verificare, non un errore certo.

### Controlli senza rilievi

- Item fermi: nessuno. Tutti gli item hanno `aggiornato_il` del 2026-11-12.
- Stima non Fibonacci: nessuna (5 e 21 sono Fibonacci; 21 è segnalato per il limite di 13).
- Dipendenze non collegate: nessuna. `jira:DEMO-2` (da PAYDISC-1) e i parent `jira:DEMO-2`, `jira:DEMO-3` esistono nello snapshot.
- Coppie esaminate e non ritenute duplicate: PAYDISC-1 / DEMO-2 (collegate con `realizza`); PAYDISC-4 / DEMO-4 (stessa label `carte`, bisogni diversi: limiti di spesa contro carte monouso).

## 3. Azioni proposte

1. **notion:PAYDISC-2**: creare su Jira un'epica per la riconciliazione fatture SDI e incassi, poi valorizzare `Epica Jira` su Notion con la sua chiave.
2. **jira:DEMO-8**: collegarla come figlia della nuova epica (azione 1).
3. **notion:PAYDISC-3**: valorizzare `Epica Jira` con DEMO-3. Prima il PO decide se l'epica è stata aperta troppo presto rispetto a un'opportunità ancora in ricerca.
4. **jira:DEMO-6**: dividere la story lungo le fasi già elencate nella descrizione (caricamento documenti, visura, titolare effettivo, verifica e approvazione), passandola a story-refiner. La stima delle nuove story spetta al team.
5. **jira:DEMO-6, jira:DEMO-8**: passarle a story-refiner per formato story e acceptance criteria in Gherkin; stima dal team per DEMO-8.
6. **jira:DEMO-7**: il PO indica cosa blocca il bug e lo collega con `bloccato_da`, oppure toglie il flag se il blocco è superato.
7. **notion:PAYDISC-5**: il PO annota cosa si sta aspettando e collega l'item bloccante, se esiste.
8. **jira:DEMO-4**: il PO decide se aggiungere story all'epica o archiviarla. Non eliminarla.
9. **jira:DEMO-1**: chiudere o archiviare l'item di prova, senza eliminarlo.
10. **jira:DEMO-2**: verificare la priorità rispetto a PAYDISC-1.

Le azioni 1, 2, 3, 4 e 9 sono anche in `./output/2026-11-12_proposte.md`. Le altre non hanno un valore proponibile senza una decisione del PO o una stima del team.

## 4. Dati mancanti

- **Sprint (Jira, customfield_10020)**: non restituito per nessuna issue. Serve a decidere se DEMO-5, DEMO-6, DEMO-7 e DEMO-8 sono in sprint e quindi se i loro problemi sono critici.
- **Motivo del blocco (Jira DEMO-7, Notion PAYDISC-5)**: `issuelinks` vuoto su tutte le issue e nessuna dipendenza su Notion. Serve a capire se il blocco è reale e chi lo scioglie.
- **Priorità (Jira)**: "Medium" su tutte le issue, valore di default. Serve a distinguere le priorità reali da quelle mai impostate.
- **Assegnatario e Team (Jira)**: null ovunque, anche su DEMO-5 e DEMO-2 in corso. Serve a sapere a chi rivolgere le azioni.
- **Corpo delle pagine (Notion)**: non letto dal sync. Il confronto dei duplicati usa solo titolo, descrizione breve e label.
- **Storico delle date**: tutti gli item sono stati creati il 2026-11-12, quindi il controllo sugli item fermi oggi non può trovare nulla.
- **Modalità Jira**: `CLAUDE.md` indica Jira in modalità CSV sul progetto PAY, lo snapshot indica connettore sul progetto DEMO. Serve a sapere se le proposte Jira si applicano via connettore o a mano.
