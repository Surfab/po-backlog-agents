# Test del plugin po-backlog-agents

Verifica che il plugin si validi, si installi da questo repository e funzioni in un progetto che non è Conto Business. Si esegue in sessioni nuove, senza bisogno di altre istruzioni.

**Le proposte prodotte durante i test non si applicano mai.** I test lavorano su copie, fuori dal repository.

## Preparazione

1. **Il clone è sul branch del plugin.** Nel Terminale:

   ```bash
   cd ~/Developer/po-backlog-agents && git switch feat/plugin && git status -sb
   ```

   Il marketplace si aggiunge da questa cartella e il plugin viene letto da qui, così com'è sul disco: se sei su un altro branch, provi un'altra versione.

2. **Serve la CLI di Claude Code.** Nel Terminale:

   ```bash
   claude --version
   ```

   Se il comando non esiste, installa Claude Code dal sito ufficiale e riprova. Senza CLI i test 1, 2 e 6 non si possono fare: fermati.

3. **Due cartelle di prova vuote, fuori dal repository.** La A serve per la skill, la B per gli agenti con i dati di Conto Business. Separarle evita che il `CLAUDE.md` creato dalla skill, con dati fittizi, cambi i risultati di backlog-health.

   ```bash
   mkdir ~/Developer/po-plugin-test-A ~/Developer/po-plugin-test-B
   ```

   Se `mkdir` dice che una cartella esiste già, fermati: scegli un altro nome e usalo in tutto il test.

4. **Nelle cartelle di prova non ci sono agenti di progetto.** Un agente in `.claude/agents/` con lo stesso nome di uno del plugin ha la precedenza, e il test non proverebbe niente. Non copiare mai `.claude/` dal repository nelle cartelle di prova.

## Test 1: validazione

```bash
claude plugin validate ~/Developer/po-backlog-agents
```

```bash
claude plugin validate ~/Developer/po-backlog-agents/plugin
```

Il primo controlla il marketplace (e il plugin, sulle versioni recenti della CLI), il secondo il plugin.

Esito: **superato** se entrambi finiscono con "Validation passed" o "Validation passed with warnings". Scrivi gli eventuali avvisi nelle note. "Validation failed": fermati.

## Test 2: installazione con lo scope più ristretto

Lo scope `local` vale solo per te e solo per la cartella in cui lanci il comando. Scrive in `.claude/settings.local.json` di quella cartella.

Nella cartella A:

```bash
cd ~/Developer/po-plugin-test-A && claude plugin marketplace add ~/Developer/po-backlog-agents --scope local && claude plugin install po-backlog-agents@surfab --scope local
```

Nella cartella B:

```bash
cd ~/Developer/po-plugin-test-B && claude plugin marketplace add ~/Developer/po-backlog-agents --scope local && claude plugin install po-backlog-agents@surfab --scope local
```

Controlla in ognuna delle due cartelle:

```bash
claude plugin list
```

```bash
cat .claude/settings.local.json
```

Atteso: `po-backlog-agents@surfab` installato e attivo, e in `.claude/settings.local.json` la voce `"po-backlog-agents@surfab": true` sotto `enabledPlugins`.

Nota: Claude Code registra il marketplace anche nella sua cartella, `~/.claude/plugins/`, fuori dalle cartelle di prova. Non si può evitare. Il test 6 lo toglie.

Esito: **superato** se il plugin risulta attivo in entrambe le cartelle.

## Test 3: la skill nuovo-progetto

1. Apri una **sessione nuova e locale** sulla cartella A.
2. Scrivi: "Elenca gli agent disponibili, con il nome completo". Devono comparire `po-backlog-agents:backlog-health`, `po-backlog-agents:story-refiner`, `po-backlog-agents:prioritizer`, `po-backlog-agents:sprint-prep` e `po-backlog-agents:domain-review`, e nessun agente di progetto.
3. Scrivi `/po-backlog-agents:nuovo-progetto` e rispondi con questi dati fittizi:

| # | Domanda | Risposta |
|---|---|---|
| 1 | Prodotto | "Ricette Facili", app di gestione ricette e magazzino per piccoli ristoranti. Report in italiano. Dati fittizi. |
| 2 | Tool di backlog | Jira via CSV, con epiche, story e bug. Jira è la fonte di verità per epic, story, bug, task, spike. |
| 3 | Team e sprint | 4 sviluppatori, 1 QA, sprint di 2 settimane, velocity sugli ultimi 3 sprint chiusi, item fermi dopo 21 giorni. |
| 4 | Scala di stima | Story point Fibonacci: 1, 2, 3, 5, 8, 13. Oltre 13 si divide. |
| 5 | Framework di priorità | "Lo compilo dopo." |
| 6 | Dominio | sicurezza alimentare |

Controlla:

- La skill ha fatto al massimo 6 domande.
- Esistono `CLAUDE.md`, `.claude/settings.json`, `.claude/agents/backlog-sync.md`, `schema/backlog-item.schema.json`, `adapters/README.md`, `adapters/csv.md`, `adapters/jira.md` (Jira è indicato via CSV), `domain/sicurezza-alimentare.md` e le cartelle `backlog/import/` e `output/`.
- In `CLAUDE.md` ci sono le risposte (21 giorni, 4 sviluppatori, 1 QA, 2 settimane, 3 sprint, scala 1-13) e la riga ``File di regole: `domain/sicurezza-alimentare.md` ``. La sezione Prioritizzazione ha ancora i segnaposto: nessun framework inventato. Definition of Ready e of Done hanno ancora i segnaposto.
- I titoli delle sezioni di `CLAUDE.md` sono quelli del template.
- `domain/sicurezza-alimentare.md` ha le sette sezioni del modello (Ruolo, Ambito, Cosa controllare, Gravità, Glossario, Con chi verificare, Cosa non fare), con i segnaposto: la skill non scrive regole al posto del team.
- Nel Terminale, i file copiati sono identici ai template (nessun output = identici):

  ```bash
  cd ~/Developer/po-plugin-test-A && T=~/Developer/po-backlog-agents/plugin/templates && diff .claude/settings.json $T/settings.template.json; diff .claude/agents/backlog-sync.md $T/backlog-sync.template.md; diff schema/backlog-item.schema.json $T/schema/backlog-item.schema.json; diff adapters/README.md $T/adapters/README.md; diff adapters/csv.md $T/adapters/csv.md; diff adapters/jira.md $T/adapters/jira.md; test -e adapters/notion.md && echo "adapters/notion.md non doveva esserci"
  ```

- Alla fine la skill ha elencato i segnaposto rimasti e i passi successivi.

Poi verifica che non sovrascriva:

1. Nel Terminale salva le impronte dei file: `cd ~/Developer/po-plugin-test-A && find . -type f -not -path './.claude/settings.local.json' -not -name '.DS_Store' | sort | xargs shasum > ../impronte-A.txt`
2. Nella sessione rilancia `/po-backlog-agents:nuovo-progetto`. Atteso: si ferma prima delle domande, elenca i file che esistono già e chiede. Rispondi "interrompi".
3. Nel Terminale: `cd ~/Developer/po-plugin-test-A && find . -type f -not -path './.claude/settings.local.json' -not -name '.DS_Store' | sort | xargs shasum | diff - ../impronte-A.txt`. Nessun output = nessun file cambiato.

I file `.DS_Store` sono esclusi perché li scrive il Finder, non la skill, e possono cambiare in qualsiasi momento.

Esito: **superato** se tutti i punti ci sono e nessun file è cambiato al secondo lancio.

## Test 4: backlog-health trova 7 difetti su 7

Prepara la cartella B dal Terminale. Copia il `CLAUDE.md` di Conto Business, i dati e il file di regole fintech; **non** copiare `.claude/`.

```bash
cd ~/Developer/po-plugin-test-B && R=~/Developer/po-backlog-agents && mkdir -p backlog domain output && cp $R/CLAUDE.md . && cp -R $R/backlog/sample backlog/ && cp $R/backlog/2026-11-12_snapshot.json backlog/ && cp $R/plugin/templates/domain/fintech.md domain/
```

Dal 17 novembre 2026 il `CLAUDE.md` di Conto Business è già nella forma del plugin. Controlla nella copia B che la sezione "Agent di dominio" contenga la riga ``File di regole: `domain/fintech.md` `` e che la tabella "Agent" e la Definition of Ready (punto 6) citino domain-review. Il 17/11 il test è stato eseguito con la versione precedente, modificata a mano in questi tre punti.

Poi apri una **sessione nuova e locale** sulla cartella B e scrivi: "Elenca gli agent disponibili, con il nome completo". Devono comparire solo i cinque del plugin.

Richiesta: "Lancia backlog-health su `backlog/sample/2026-11-12_snapshot.json`".

La sintesi deve dichiarare che lo snapshot letto è `backlog/sample/2026-11-12_snapshot.json`. Deve trovare tutti i 7 difetti di `backlog/sample/README.md`, come in `TEST-generici.md`:

| # | Item | Difetto | Controllo dell'agent |
|---|---|---|---|
| 1 | jira:PAY-131 e notion:PAYDISC-2 | duplicato tra tool (riconciliazione fatture SDI) | 1, Duplicati |
| 2 | notion:PAYDISC-2 | opportunità validata senza epica | 8, Item validati senza collegamento |
| 3 | jira:PAY-131 | senza epica, senza stima, senza acceptance criteria | 3 e 7 |
| 4 | jira:PAY-118 | 21 punti (oltre 13), senza criteri | 4 e 3 |
| 5 | jira:PAY-125 | fermo da oltre 28 giorni, bloccato da jira:PAY-140 che non esiste | 2 e 5 |
| 6 | jira:PAY-109 | epica vuota | 6 |
| 7 | notion:PAYDISC-3 | manca risk reduction, WSJF non calcolabile | 10, Valori di priorità mancanti |

Non devono comparire come problemi jira:PAY-112 (story completa) né la coppia notion:PAYDISC-1 e jira:PAY-101 (opportunità ed epica collegate, non un duplicato). Il Job Size mancante su PAYDISC-1 e PAYDISC-2 è atteso: nel campione è davvero vuoto.

Esito: **superato** se trova 7 su 7 e non segnala come problema i due casi sani.

## Test 5: domain-review ritrova gli impatti di fintech-compliance

Nella stessa sessione sulla cartella B.

Richiesta: "Lancia domain-review su `jira:DEMO-5` e `jira:DEMO-7` usando `backlog/2026-11-12_snapshot.json`".

Report atteso: `output/AAAA-MM-GG_domain-review.md`. Confrontalo con `output/2026-11-12_fintech-compliance.md` nel repository, che ha valutato gli stessi item sullo stesso snapshot. Conta il contenuto dell'impatto, non le parole. Segna ogni riga:

| # | Item | Impatto atteso | Gravità del 12/11 | Trovato | Gravità ora |
|---|---|---|---|---|---|
| C1 | DEMO-5 | nome del titolare mostrato con esito "simile" (dati personali di un terzo) | Critico | | |
| C2 | DEMO-5 | esito "non corrisponde" assente dai criteri | Critico | | |
| C3 | DEMO-5 | uso del servizio per ricavare nomi da IBAN (abuso, limiti di frequenza) | Critico | | |
| C4 | DEMO-5 | "verifica non disponibile": si procede su un bonifico irrevocabile; origine della soglia di 5 secondi | Critico | | |
| C5 | DEMO-5 | ordine tra verifica e SCA; nuova verifica dopo una modifica | Critico | | |
| C6 | DEMO-5 | story già "In corso" senza valutazione di compliance (DoR punto 6) | Critico | | |
| C7 | DEMO-7 | esito ignoto di un bonifico irrevocabile dopo il timeout (doppio pagamento) | Critico | | |
| C8 | DEMO-7 | possibile incidente operativo da valutare (DORA) | Critico | | |
| M1 | DEMO-5 | audit trail della verifica, dati fuori dai log applicativi | Medio | | |
| M2 | DEMO-5 | fornitore del servizio di verifica (trattamento dati, fornitore ICT) | Medio | | |
| M3 | DEMO-5 | beneficiari che sono imprese (denominazione, nomi commerciali) | Medio | | |
| M4 | DEMO-5 e DEMO-2 | perimetro dei canali (PISP, API, bonifici in blocco) | Medio | | |
| M5 | DEMO-5 | controlli sulle sanzioni e antifrode non citati | Medio | | |
| M6 | DEMO-7 | tracciabilità dei cambi di stato e degli interventi manuali | Medio | | |
| M7 | DEMO-7 | flag "Impediment" senza causa né dipendenza | Medio | | |
| M8 | DEMO-2 | lato "risposta" alle verifiche richieste da altri prestatori | Medio | | |
| M9 | DEMO-2 e PAYDISC-1 | scadenza normativa non indicata, da verificare con compliance | Medio | | |
| B1 | DEMO-5 | testi degli avvisi da far approvare | Basso | | |
| B2 | DEMO-5 | dati di test per i quattro esiti, nessun nome reale | Basso | | |
| B3 | DEMO-7 | messaggio al cliente che non spinga a ripetere il bonifico | Basso | | |
| B4 | DEMO-7 | collegamento all'epica DEMO-2 da confermare | Basso | | |

Controlla anche:

- La sintesi cita lo snapshot `backlog/2026-11-12_snapshot.json` (ora non nota) e il file di regole `domain/fintech.md`.
- Nessun articolo, comma, numero di regolamento o data di entrata in vigore. Le norme sono nominate solo per area.
- Ogni "con chi verificare" è uno dei ruoli di `domain/fintech.md`: compliance, legal, DPO, sicurezza, antifrode, operations.
- Acceptance criteria e task sono proposte, non requisiti certi, e ci sono righe nuove in `output/AAAA-MM-GG_proposte.md`.

Esito: **superato** se tutte le 21 righe sono trovate. Una gravità diversa da quella del 12/11 va scritta nelle note ma non fa fallire il test. **Se manca anche un solo impatto, fermati e avvisa il PO**: la demo non passa a domain-review finché il file di regole non è corretto.

Il report del 12/11 viene da un lancio molto approfondito: il 17/11 fintech-compliance, rilanciato sugli stessi item, ne ha ritrovati 17 su 21. Lo stesso agente con le stesse regole può dare report diversi. Per questo un impatto che deve uscire sempre va scritto come controllo in "Cosa controllare", e non lasciato al giudizio del modello.

Per ripetere il test nello stesso giorno, prima sposta il report precedente, per esempio in `output/AAAA-MM-GG_domain-review_lancio1.md`, e apri una sessione nuova. Se il report del giorno esiste già, l'agente può decidere di non riscriverlo.

Poi verifica il caso "Nessuno":

1. In `CLAUDE.md` della copia B cambia la riga in `File di regole: Nessuno`.
2. Apri una sessione nuova sulla cartella B e scrivi: "Lancia domain-review su `jira:DEMO-5` usando `backlog/2026-11-12_snapshot.json`".

Atteso: una riga che dice che nessun dominio è configurato, nessun report nuovo in `output/` e nessuna riga nuova in `proposte.md`.

## Test 6: disinstallazione

Il marketplace è registrato una sola volta per tutto il computer: si toglie con un solo comando, che disinstalla il plugin anche dalle altre cartelle. Il `.claude/settings.local.json` delle altre cartelle però non viene pulito. Per questo prima si disinstalla il plugin in ogni cartella, poi si toglie il marketplace una volta sola.

Nel Terminale, un comando alla volta:

```bash
cd ~/Developer/po-plugin-test-A && claude plugin uninstall po-backlog-agents@surfab --scope local
```

```bash
cd ~/Developer/po-plugin-test-B && claude plugin uninstall po-backlog-agents@surfab --scope local && claude plugin marketplace remove surfab --scope local
```

Poi controlla `.claude/settings.local.json` in tutte e due le cartelle: `enabledPlugins` e `extraKnownMarketplaces` devono essere vuoti. Se in una cartella restano voci di `surfab` (di solito in A, dove il marketplace non è stato tolto), toglile a mano con un editor. Rilanciare lì `claude plugin marketplace remove` non serve: risponde che `surfab` non esiste.

Controlla con `claude plugin list` che il plugin non compaia più. Le cartelle di prova restano come sono, con i file creati dai test. Si possono archiviare o tenere per i test successivi: non contengono niente che serva al repository.

## Esiti

| Test | Data | Esito | Note |
|---|---|---|---|
| 1 validazione | 2026-11-17 | superato | CLI 2.1.326. Unico avviso: ICON_MISSING, che serve solo per l'elenco nella directory dei plugin. |
| 2 installazione | 2026-11-17 | superato | Plugin attivo in A e in B, letto dal clone. `claude plugin list` mostra due righe local, una per cartella. |
| 3 nuovo-progetto | 2026-11-17 | superato | 6 domande, 7 file e 2 cartelle, copie identiche ai template, nessun valore inventato. Al secondo lancio si ferma e chiede per tutti i file insieme, non file per file. Nessun file cambiato. |
| 4 backlog-health | 2026-11-17 | superato | 7 su 7, PAYDISC-1 / PAY-101 non segnalati. PAY-112 segnalata come osservazione fuori dai controlli, per i criteri in prosa e non in Gherkin, coerente con il Formato story. Sospetto duplicato PAYDISC-3 / PAY-102 come il 14/11. Report datato con la data dello snapshot. |
| 5 domain-review | 2026-11-17 | superato dopo una correzione | Primo lancio 15 su 21 (mancavano M3, M4, M8, M9, B2, B4); fintech-compliance rilanciato lo stesso giorno 17 su 21. Aggiunti a `fintech.md` tre controlli espliciti: secondo lancio 21 su 21. Gravità diverse dal 12/11: C4, C5, C8 e B2 Medio, C6 e M7 Basso. Caso "Nessuno": nessuna analisi, nessun file scritto. |
| 6 disinstallazione | 2026-11-17 | superato | Il marketplace tolto nella cartella A ha disinstallato anche la copia della B, ma ne ha lasciato le dichiarazioni in `settings.local.json`, tolte a mano. Istruzioni del test corrette. |
