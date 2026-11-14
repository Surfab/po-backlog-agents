# Aggiungere un nuovo tool in 5 passi

Gli agent di analisi non si toccano. Si lavora solo qui, in `backlog-sync` e nei permessi.

1. **Collega e osserva.** Attiva il connettore nell'app, poi chiedi a Claude l'elenco dei tool che vede, con il nome esatto, divisi in lettura e scrittura. Senza connettore: esporta un CSV in `./backlog/import/<tool>_<progetto>.csv` e salta al passo 3.

2. **Leggi 2-3 item reali.** In sola lettura. Servono per scoprire i nomi veri dei campi, gli ID dei campi custom e i valori degli stati.

3. **Scrivi `./adapters/<tool>.md`.** Copia la struttura di `notion.md`: connettore, tool di lettura, tool di scrittura, query standard, mappa dei campi, mappa degli stati, limiti noti. Ogni campo senza corrispondenza nel modello comune va in `extra`.

4. **Aggiorna permessi e sync.** Aggiungi i tool di lettura a `tools:` in `.claude/agents/backlog-sync.md`. Aggiungi i tool di scrittura alla lista "ask" di `.claude/settings.json`. Aggiungi il tool alla tabella "Tool attivi" e alla fonte di verità in `CLAUDE.md`.

5. **Prova.** Lancia backlog-sync e controlla tre cose nello snapshot: il tool compare in `fonti`, `campi_non_mappati` è vuoto o spiegato, 2 item a caso corrispondono all'originale. Poi lancia backlog-health: se trova duplicati con gli altri tool, sta funzionando.

## Prefisso dei tool

Il prefisso dei tool dipende dall'ambiente. In Claude Desktop è `mcp__<id del connettore>__` e resta stabile finché il connettore non viene ricollegato. In una sessione cloud è `mcp__<nome del connettore>__`. Al primo uso in un ambiente nuovo, controlla i prefissi prima di lanciare backlog-sync.
