# Checklist: nuovo progetto, dalla cartella vuota al primo report

Dieci passi. Ti servono questa cartella `template/` e, dal progetto di riferimento (Conto Business), `schema/` e `adapters/`.

1. **Crea la cartella del progetto** con dentro `.claude/agents/`, `adapters/`, `schema/`, `backlog/import/` e `output/`. Se la cartella è su iCloud Drive: clic destro, "Mantieni scaricato". Le cartelle che iniziano col punto nel Finder si vedono con Cmd+Maiusc+punto.

2. **Copia schema e adapter di base** dal progetto di riferimento: `schema/backlog-item.schema.json`, `adapters/README.md` e `adapters/csv.md`. Copia anche `adapters/notion.md` o `adapters/jira.md` se usi quei tool: ti fanno da modello.

3. **Copia gli agent in `.claude/agents/`**: i quattro file di `template/agents/` (`backlog-health.md`, `story-refiner.md`, `prioritizer.md`, `sprint-prep.md`) così come sono, e `template/agents/backlog-sync.template.md` rinominato in `backlog-sync.md`. Se il progetto ha un agent di dominio, copia anche quello.

4. **Crea `CLAUDE.md`** copiando `template/CLAUDE.template.md` nella cartella del progetto con il nome `CLAUDE.md`. Compila ogni sezione e cancella le righe in corsivo di spiegazione. Non cambiare i nomi delle sezioni né quelli della tabella "Parametri del backlog": gli agent li cercano per nome.

5. **Crea `.claude/settings.json`** copiando `template/settings.template.json`. Per ora lascia i segnaposto dei connettori: li sostituisci al passo 6.

6. **Collega i connettori e trova i nomi esatti dei tool.** Nell'app collega i connettori (Impostazioni, Connettori), apri una sessione locale sulla cartella e chiedi a Claude l'elenco dei tool che vede, divisi in lettura, scrittura ed eliminazione. Metti i tool di lettura nella riga `tools:` di `backlog-sync.md` e in "allow", quelli di scrittura in "ask", quelli che eliminano in "deny". Senza connettore: esporta un CSV in `backlog/import/<tool>_<progetto>.csv`.

7. **Scrivi un adapter per ogni tool** seguendo i 5 passi di `adapters/README.md`, e aggiungi il tool alla tabella "Tool attivi" di `CLAUDE.md`.

8. **Controlla che non restino segnaposto e riapri la sessione.** Chiedi a Claude: "Cerca il testo `<` seguito da maiuscole o da `ID-` in CLAUDE.md, .claude/ e adapters/". Ogni risultato è un segnaposto da compilare. Poi chiudi e riapri la sessione: agent e `CLAUDE.md` si leggono all'avvio.

9. **Primo sync.** Scrivi "Lancia backlog-sync". Nasce `backlog/AAAA-MM-GG_HHMM_snapshot.json` con l'ora reale anche in `generato_il`. Se il file esiste già, l'agent si ferma e chiede. Nello snapshot controlla tre cose: ogni tool compare in `fonti`, `campi_non_mappati` è vuoto o spiegato, due item a caso corrispondono all'originale.

10. **Primo report.** Scrivi "Lancia backlog-health". Il report arriva in `output/AAAA-MM-GG_backlog-health.md`. Leggi per prima la parte "Dati mancanti": ogni valore che manca in `CLAUDE.md` è elencato lì. Completa `CLAUDE.md`, riapri la sessione e rilancia finché quella parte non contiene più valori di configurazione.
