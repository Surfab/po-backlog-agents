# Changelog

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
