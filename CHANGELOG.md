# Changelog

## 2026-11-14

- README accorciato a una schermata per lingua. La prima parte (risultati della demo, esempi di report, prova in 2 minuti) resta uguale.
- Nuove sezioni nel README, in italiano e in inglese: "Perché non bastano gli agenti di Jira" e "Come lo proverei in un team vero".
- La guida completa passa in `docs/guida.md` e `docs/guide-en.md`, con tutti i contenuti del README precedente. Corretto l'albero delle cartelle: nome del repository, `docs/` e questo file.
- Permessi: `notion-delete-pages` e `notion-delete-databases` passano da "ask" a "deny" in `.claude/settings.json`, coerenti con la regola "non si elimina niente". `executeDestructive` resta in "ask": un permesso non distingue le operazioni, e il limite a `manageJiraSprint` sta nelle istruzioni. Allineati la guida, `CLAUDE.md` e `adapters/notion.md`.
