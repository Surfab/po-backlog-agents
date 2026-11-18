# Checklist: nuovo progetto, dalla cartella vuota al primo report

Dieci passi. I primi cinque li fa la skill `nuovo-progetto` del plugin po-backlog-agents. Gli altri restano a te, perché dipendono dal team e dai connettori.

1. **Installa il plugin** (una volta per computer) e **apri una sessione locale** sulla cartella del progetto. Se la cartella è su iCloud Drive: clic destro, "Mantieni scaricato". Le cartelle che iniziano col punto nel Finder si vedono con Cmd+Maiusc+punto.

2. **Lancia la skill**: `/po-backlog-agents:nuovo-progetto`. Fa al massimo 6 domande: prodotto, tool di backlog, team e sprint, scala di stima, framework di priorità, dominio. Se un file esiste già, si ferma e chiede.

3. **Controlla i file creati**: `CLAUDE.md`, `.claude/settings.json`, `.claude/agents/backlog-sync.md`, `schema/backlog-item.schema.json`, `adapters/README.md`, `adapters/csv.md`, i modelli `adapters/jira.md` e `adapters/notion.md` se hai indicato quei tool e, se hai indicato un dominio, `domain/<dominio>.md`. Le cartelle `backlog/import/` e `output/` sono pronte.

4. **Completa `CLAUDE.md`.** La skill compila solo quello che hai detto. Restano da compilare almeno Collegamento discovery-delivery, Definition of Ready, Definition of Done, Formato story e Glossario. Non cambiare i nomi delle sezioni né quelli della tabella "Parametri del backlog": gli agent li cercano per nome.

5. **Compila le regole di dominio**, se hai un dominio: ruolo, ambito, cosa controllare, gravità, glossario, con chi verificare, cosa non fare. Le scrive il team, non un agent. Un esempio completo è `domain/fintech.md` nei template del plugin.

6. **Collega i connettori e trova i nomi esatti dei tool.** Nell'app collega i connettori (Impostazioni, Connettori), apri una sessione locale sulla cartella e chiedi a Claude l'elenco dei tool che vede, divisi in lettura, scrittura ed eliminazione. Metti i tool di lettura nella riga `tools:` di `.claude/agents/backlog-sync.md` e in "allow", quelli di scrittura in "ask", quelli che eliminano in "deny". Senza connettore: esporta un CSV in `backlog/import/<tool>_<progetto>.csv`.

7. **Completa o scrivi un adapter per ogni tool**, e aggiungi il tool alla tabella "Tool attivi" di `CLAUDE.md`. Per Jira e Notion la skill ha già copiato un modello: resta da compilare le parti tra `< >` (prefisso del connettore, sito e chiave dello spazio, id dei campi custom, proprietà e stati del database) leggendo 2-3 item reali. Per gli altri tool segui i 5 passi di `adapters/README.md`.

8. **Controlla che non restino segnaposto e riapri la sessione.** Chiedi a Claude: "Cerca il testo `<` seguito da maiuscole o da `ID-` in CLAUDE.md, .claude/, adapters/ e domain/". Ogni risultato è un segnaposto da compilare. Poi chiudi e riapri la sessione: agent e `CLAUDE.md` si leggono all'avvio.

9. **Primo sync.** Scrivi "Lancia backlog-sync". Nasce `backlog/AAAA-MM-GG_HHMM_snapshot.json` con l'ora reale anche in `generato_il`. Se il file esiste già, l'agent si ferma e chiede. Nello snapshot controlla tre cose: ogni tool compare in `fonti`, `campi_non_mappati` è vuoto o spiegato, due item a caso corrispondono all'originale.

10. **Primo report.** Scrivi "Lancia backlog-health". Il report arriva in `output/AAAA-MM-GG_backlog-health.md`. Leggi per prima la parte "Dati mancanti": ogni valore che manca in `CLAUDE.md` è elencato lì. Completa `CLAUDE.md`, riapri la sessione e rilancia finché quella parte non contiene più valori di configurazione.

## Perché tre cose restano per progetto

- **`CLAUDE.md` e regole di dominio**: sono le regole del team (soglie, DoR, DoD, priorità, controlli di settore). Il plugin non ha valori di default.
- **backlog-sync**: la riga `tools:` contiene i nomi reali dei tool di sola lettura dei connettori, diversi per ogni computer e ogni connettore.
- **`.claude/settings.json`**: i permessi (allow, ask, deny) stanno lì perché gli agent dei plugin non supportano `hooks`, `mcpServers` e `permissionMode`.
