# Changelog

## 2026-11-18, plugin 0.2.4: controlli espliciti, criterio di superamento per gravità

- `domain/fintech.md` e `plugin/templates/domain/fintech.md`, sezione Cosa controllare: voce nuova "Uso improprio della funzione" (dati su terzi restituiti o mostrati: limiti di frequenza e monitoraggio contro l'uso ripetuto, con Antifrode e DPO). Motivo: nel secondo test 5 pulito del 18/11 il lancio 3 non cita l'uso della verifica per ricavare i nomi dei titolari (C3), l'unico impatto del riferimento senza una voce né un criterio che lo faccia trovare.
- Stessi file, altre voci per gli impatti che si trovavano solo grazie agli esempi dei criteri di Gravità, scritte in modo valido anche fuori da Conto Business: voce nuova "Stati del pagamento" (C7), voce nuova "Testi per il cliente" (B1 e B3), ordine tra verifica e autorizzazione forte nella voce "PSD2 e SCA" (C5), item bloccato senza causa nella voce "Contesto dell'item" (M7). La frase iniziale non conta più i controlli aggiunti e rimanda al CHANGELOG. I due file restano identici.
- Tutti gli agent che scrivono proposte, in `plugin/agents/` e in `.claude/agents/`, e la sezione Scritture di `CLAUDE.md` e `plugin/templates/CLAUDE.template.md`: l'id è sempre quello dello snapshot, nel formato tool:chiave. Motivo: nelle proposte del lancio 1 gli id erano senza prefisso del tool.
- `TEST-plugin.md`, test 5: un lancio è superato se trova tutti gli impatti Critico (C1-C8) con gravità Critico e manca al massimo un impatto Medio o Basso. Il test è superato se lo sono i 3 lanci e nessun impatto Medio o Basso manca in due lanci; un impatto che manca in due lanci diventa una voce di "Cosa controllare". Registrato l'esito del secondo test pulito del 18/11 (non superato). Il test non è stato rieseguito con questa versione.
- Versione del plugin 0.2.4.

## 2026-11-18, plugin 0.2.3: regole dopo il test 5 pulito, proposte con testo esatto

- `domain/fintech.md` e `plugin/templates/domain/fintech.md`, sezione Cosa controllare: i controlli sulle sanzioni sono una voce a sé, fuori da quella sui bonifici istantanei. Voce nuova sui numeri senza fonte (soglie, tempi, limiti): chiedere da dove vengono e chi li ha decisi. Motivo: nel test 5 pulito del 18/11 il lancio 3 non cita i controlli sulle sanzioni (M5) e l'origine della soglia di 5 secondi (C4) esce solo nel lancio 1. I due file restano identici.
- Stessi file, G5: il fatto che domain-review venga lanciato prima dello sprint non rende un item candidato. Conta solo una richiesta del PO che lo dice in modo esplicito. Motivo: 2 lanci su 3 hanno applicato G5 a DEMO-7 con il ragionamento "la review si fa prima dello sprint, quindi è candidato".
- Tutti gli agent che scrivono proposte, in `plugin/agents/` e in `.claude/agents/`, e la sezione Scritture di `CLAUDE.md` e `plugin/templates/CLAUDE.template.md`: il valore proposto è il testo esatto da scrivere, mai un rimando al report. Una riga per ogni scenario Gherkin e una per ogni campo, con `<br>` tra le righe di uno scenario. Motivo: 2 lanci su 3 hanno messo 9-10 scenari in una sola riga, con un rimando a una sezione del report.
- `TEST-plugin.md`: registrato l'esito del test 5 pulito del 18/11 (non superato). Il test va rifatto con questa versione e non è stato rieseguito.
- Versione del plugin 0.2.3.

## 2026-11-18, plugin 0.2.2: report mai sovrascritti, G5 con sprint non noto

- Tutti gli agent di analisi, in `plugin/agents/` e in `.claude/agents/`: il report va in `./output/AAAA-MM-GG_HHMM_<agent>.md`, con data e ora reali che la sessione principale passa nel prompt, come per backlog-sync. Senza ora l'agent si ferma e la chiede. Prima di scrivere controlla con Glob: se il file esiste, non lo scrive e si ferma. Motivo: il 18/11 tre lanci di domain-review in parallelo hanno scritto tutti in `2026-11-18_domain-review.md` e due report sono andati persi. I report già esistenti non sono rinominati.
- `proposte.md` resta un file al giorno. Il motivo di ogni riga comincia con il nome del report da cui viene. L'agent scrive prima il report, poi le proposte.
- sprint-prep cerca i report di domain-review con e senza ora nel nome.
- `CLAUDE.md` e `plugin/templates/CLAUDE.template.md`: la tabella Agent dice che ogni agent riceve data e ora del lancio, un `date` per lancio. Architettura e Scritture citano il nuovo nome dei report e l'origine delle proposte.
- `domain/fintech.md` e `plugin/templates/domain/fintech.md`, G5: si applica solo se lo snapshot dice che l'item è in corso o in uno sprint, o se il PO lo indica come candidato. Altrimenti non si applica, nemmeno come caso dubbio, e il dato va in "Dati mancanti". Motivo: con il campo sprint vuoto, 2 lanci su 3 hanno messo DEMO-7 Critico (G5, caso dubbio). I due file restano identici.
- `TEST-plugin.md`, test 5: lanci uno alla volta; prima di ogni lancio report di domain-review e file di proposte, anche dei giorni precedenti, si spostano in `lanci/`. Registrati gli esiti del 18/11: 21 su 21 e C1-C8 Critico nei 3 lanci, da confermare perché tutti e 3 citano `2026-11-17_proposte.md`. Il test non è stato rieseguito con queste modifiche.
- Guide, `TEST-generici.md` e checklist: nuovo nome dei report.
- Versione del plugin 0.2.2.

## 2026-11-18, plugin 0.2.1: gravità decidibile in domain-review

- `domain/fintech.md` e `plugin/templates/domain/fintech.md`, sezione Gravità: nove criteri numerati (G1-G5 Critico, G6-G8 Medio, G9 Basso), ognuno con un esempio di Conto Business. Ogni voce del report cita il criterio. La probabilità e il feature flag non abbassano la gravità. Nei casi dubbi si sceglie il livello più alto e si scrive perché. I due file restano identici.
- `plugin/templates/domain.template.md`: la sezione Gravità chiede criteri numerati con un esempio e contiene la regola per i casi dubbi.
- `TEST-plugin.md`, test 5: 3 lanci in sessioni nuove, superato solo se li supera tutti. Un lancio fallisce se un impatto Critico del 12/11 (C1-C8) risulta Medio o Basso senza una motivazione che rimandi ai criteri. Motivo: il 17/11 C4, C5 e C8 erano scesi a Medio e C6 a Basso senza spiegazione. Il test non è stato rieseguito.
- `archivio/` rinominata in `versioni-precedenti/`, rimandi aggiornati in guide e `TEST-generici.md`.
- README e guide: percorso per installare e gestire il plugin dall'app desktop, preso dalla documentazione ufficiale. Il marketplace si aggiunge ancora da terminale: la documentazione non descrive come farlo dall'app.
- Versione del plugin 0.2.1.

## 2026-11-18, plugin 0.2.0: modelli degli adapter di Jira e Notion

- Nuovi `plugin/templates/adapters/jira.md` e `plugin/templates/adapters/notion.md`, ricavati dagli adapter della demo. Le parti uguali in ogni sito sono già scritte: nomi dei tool dei connettori, query, mappa degli stati per categoria, convenzione degli acceptance criteria, regole di conversione. Le parti che cambiano per progetto sono segnaposto `< >`: prefisso del connettore, sito e chiave dello spazio, id dei campi custom, proprietà e stati del database Notion. Il modello Notion propone anche le proprietà consigliate per un database nuovo.
- La skill `nuovo-progetto` copia i due modelli solo se il PO indica Jira o Notion tra i tool, anche via CSV. Checklist, `adapters/README.md` del plugin e guide aggiornati.
- `TEST-plugin.md`, test 3: tra i file attesi anche `adapters/jira.md` (Jira è indicato via CSV), identico al modello, e nessun `adapters/notion.md`. Il test non è stato rieseguito.
- Versione del plugin 0.2.0, perché chi l'ha installato riceva l'aggiornamento.

## 2026-11-17, la demo passa a domain-review

- La demo Conto Business usa domain-review con le regole di `domain/fintech.md`, copiato dal plugin. fintech-compliance è spostato in `archivio/agents/`: i suoi controlli sono tutti nel file di regole.
- `.claude/agents/`: nuova copia di domain-review, e story-refiner e sprint-prep allineati alla versione del plugin. Come le altre copie della demo, senza snapshot ripiegano su `backlog/sample/`.
- `CLAUDE.md`: tabella Agent, sezione Agent di dominio nella forma del plugin (`File di regole: domain/fintech.md`), Definition of Ready al punto 6.
- README, guide e pagina interattiva aggiornati. Le attese di `TEST-generici.md` e il test 4 di `TEST-plugin.md` seguono il nuovo `CLAUDE.md`; gli esiti già registrati restano com'erano. I report vecchi in `output/` portano ancora il nome fintech-compliance.

## 2026-11-17, plugin po-backlog-agents 0.1.0

- Il repository diventa un marketplace di Claude Code, `surfab` (`.claude-plugin/marketplace.json`), con il plugin `po-backlog-agents` nella cartella `plugin/`.
- Il plugin contiene i quattro agenti generici, il nuovo agente di dominio domain-review e la skill `nuovo-progetto`, che prepara un progetto con al massimo sei domande e non sovrascrive file esistenti.
- `template/` spostata in `plugin/`: gli agenti in `plugin/agents/`, template e checklist in `plugin/templates/`. Nei template anche schema e adapter di base (README e CSV), perché un plugin installato non vede i file fuori dalla sua cartella.
- domain-review legge le regole dal file indicato nella sezione Agent di dominio di `CLAUDE.md`, oppure non analizza niente se la sezione dice "Nessuno". Modello vuoto in `plugin/templates/domain.template.md`, esempio completo in `plugin/templates/domain/fintech.md`, con tutti i controlli di fintech-compliance.
- story-refiner e sprint-prep rimandano a domain-review. Il template di `CLAUDE.md` ha la nuova forma della sezione Agent di dominio.
- Nuovo `TEST-plugin.md`: validazione, installazione in una cartella di prova, skill, backlog-health e domain-review.
- README e guide: installazione, skill, agente di dominio e cosa resta da fare in ogni progetto. La demo Conto Business usa ancora fintech-compliance: passa a domain-review dopo i test.
- `TEST-plugin.md` eseguito il 2026-11-17: sei test su sei superati. Per il test 5 `fintech.md` ha tre controlli in più (beneficiari e perimetro della verifica, scadenze, contesto dell'item): senza, domain-review ritrovava 15 impatti su 21, e fintech-compliance rilanciato lo stesso giorno 17. Corrette nel test le istruzioni su impronte, `CLAUDE.md` della cartella B, nuovo lancio di domain-review e disinstallazione.

## 2026-11-14, screenshot nel README

- README: screenshot del flusso con il pannello di backlog-sync aperto, in versione chiara e scura (GitHub sceglie in base al tema), cliccabile verso la pagina interattiva.
- Immagini in `docs/img/flusso-chiaro.png` e `docs/img/flusso-scuro.png`, circa 80 KB l'una.
- Se cambia la sezione "Il flusso" di `docs/index.html`, lo screenshot va rifatto.

## 2026-11-14, pagina interattiva

- Nuova pagina `docs/index.html`, in italiano e in inglese, che spiega in due minuti come funzionano gli agenti. Ogni numero e ogni citazione rimanda al file del repository da cui viene. Pubblicata con GitHub Pages su `surfab.github.io/po-backlog-agents`.
- `docs/.nojekyll`: GitHub Pages serve i file così come sono, senza elaborarli.
- README: link alla pagina, in italiano e in inglese.

## 2026-11-14, agenti generici e template

- backlog-health, story-refiner, prioritizer e sprint-prep diventano generici: regole e valori li leggono da `CLAUDE.md` e, se un valore manca, lo segnalano invece di usare un default. fintech-compliance resta l'agente di dominio.
- `CLAUDE.md`: nuove sezioni Parametri del backlog, Collegamento discovery-delivery, Agent di dominio e Prioritizzazione (al posto di WSJF). Formato story con i casi limite e i criteri Gherkin a `Scenario`.
- Snapshot con l'ora nel nome, `AAAA-MM-GG_HHMM_snapshot.json`, e `generato_il` con l'ora reale. backlog-sync non sovrascrive più un file esistente. Gli agenti di analisi citano il file esatto che hanno letto. Gli snapshot vecchi restano con il nome senza ora.
- Nuova cartella `template/` per partire con un progetto nuovo, con la checklist in dieci passi.
- Nuovo `TEST-generici.md`: test di non regressione degli agenti generici, cinque su cinque superati.
- Guida aggiornata in italiano e in inglese. Spiegata la differenza voluta quando manca lo snapshot: gli agenti del template si fermano e chiedono backlog-sync, quelli di Conto Business ripiegano su `backlog/sample/` perché è una demo.

## 2026-11-14

- README accorciato a una schermata per lingua. La prima parte (risultati della demo, esempi di report, prova in 2 minuti) resta uguale.
- Nuove sezioni nel README, in italiano e in inglese: "Perché non bastano gli agenti di Jira" e "Come lo proverei in un team vero".
- La guida completa passa in `docs/guida.md` e `docs/guide-en.md`, con tutti i contenuti del README precedente. Corretto l'albero delle cartelle: nome del repository, `docs/` e questo file.
- Permessi: `notion-delete-pages` e `notion-delete-databases` passano da "ask" a "deny" in `.claude/settings.json`, coerenti con la regola "non si elimina niente". `executeDestructive` resta in "ask": un permesso non distingue le operazioni, e il limite a `manageJiraSprint` sta nelle istruzioni. Allineati la guida, `CLAUDE.md` e `adapters/notion.md`.
