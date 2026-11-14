# Adapter: csv

Per i tool senza connettore, o quando un connettore non risponde.

## Dove sono i file

- Cartella: `./backlog/import/`
- Nome file: `<tool>_<progetto>.csv`, es. `jira_PAY.csv`. La parte prima del trattino basso diventa il campo `tool`.
- Codifica UTF-8, separatore virgola, prima riga con le intestazioni.
- Nessuno strumento di scrittura: un CSV si legge e basta. Le modifiche proposte per un tool in modalità CSV restano in `./output/` e le applichi tu a mano.

## Colonne attese

| Colonna | Obbligatoria | Modello comune | Note |
|---|---|---|---|
| Issue key | sì | id (`<tool>:<valore>`) | |
| Issue Type | sì | tipo, tipo_originale | Epic, Story, Bug, Task, Spike, Opportunity |
| Summary | sì | titolo | |
| Description | no | descrizione | |
| Acceptance Criteria | no | acceptance_criteria | più criteri separati da ` \|\| ` |
| Status | sì | stato_originale, stato | usa la mappa stati dell'adapter del tool (es. `adapters/jira.md`) |
| Priority | no | priorita | usa la mappa dell'adapter del tool |
| Story Points | no | stima | vuoto = `null` |
| Sprint | no | sprint | |
| Parent | no | parent | chiave del padre, diventa `<tool>:<chiave>` |
| Linked Issues | no | dipendenze | formato `relazione:CHIAVE`, più valori separati da `;`. Es. `is blocked by:PAY-140` |
| Labels | no | label | separatore `;` |
| Assignee | no | assegnatario | |
| Created | no | creato_il | ISO 8601 |
| Updated | sì | aggiornato_il | ISO 8601 |

Ogni altra colonna finisce in `extra` con il suo nome. Se una colonna obbligatoria manca, backlog-sync si ferma su quel file e lo dichiara in `campi_non_mappati`.

## Limiti

- Niente url: il campo resta `null`.
- Il CSV è una fotografia. La data dell'export non è nel file: backlog-sync scrive in `fonti[].note` il valore più recente della colonna Updated e dichiara che la data di export è ignota.
