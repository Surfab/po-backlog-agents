# Changelog

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
