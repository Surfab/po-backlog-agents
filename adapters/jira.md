# Adapter: jira

Stato: **attivo via connettore dal 2026-11-12**. Mappa verificata su 3 issue reali (DEMO-1, DEMO-2, DEMO-3). Story point verificato su issue reali il 2026-11-13. Flag (DEMO-7 = Impediment) e parent verificati su issue reali. Sprint creato il 2026-11-13 ("PAY Sprint 1", id 2, board id 1): campo verificato su 5 issue (DEMO-5, 6, 7, 8, 10) il 2026-11-13. Link tra issue non ancora osservati.

Se il connettore non risponde, backlog-sync ripiega su `./backlog/import/jira_*.csv` seguendo `adapters/csv.md`.

## Connettore

- Nome: Atlassian MCP
- Prefisso dei tool: `mcp__<ID-CONNETTORE-ATLASSIAN>__`
- Sito (cloudId): `TUO-SITO.atlassian.net`
- Spazio: nome "PAY", **chiave `DEMO`**, id 10000, tipo team-managed. Le issue hanno chiave `DEMO-<n>`. Se la chiave viene rinominata in PAY, aggiorna questa riga e le query: Jira rinumera da solo le issue.
- Fonte di verità per: epiche, story, bug, task, spike.

## Tool di lettura (gli unici concessi a backlog-sync)

- `mcp__<ID-CONNETTORE-ATLASSIAN>__searchJiraIssuesUsingJql`: elenco delle issue, con il parametro `fields` indicato sotto.
- `mcp__<ID-CONNETTORE-ATLASSIAN>__getJiraIssue`: una issue, con `view: "evidence"` restituisce anche i link.

Altri tool di lettura, solo per la sessione principale: `executeRead` (con operazioni come `listJiraProjects`, `listJiraStatuses`, `getJiraIssueTypeMetaWithFields`, `listJiraIssueLinkTypes`, `listJiraIssueTransitions`), `discover`, `listJiraIssueComments`.

## Tool di scrittura (solo sessione principale, sempre in "ask")

- `mcp__<ID-CONNETTORE-ATLASSIAN>__createJiraIssue`
- `mcp__<ID-CONNETTORE-ATLASSIAN>__editJiraIssue`
- `mcp__<ID-CONNETTORE-ATLASSIAN>__transitionJiraIssue`: cambio di stato e spostamento in sprint
- `mcp__<ID-CONNETTORE-ATLASSIAN>__addOrEditJiraIssueComment`
- `mcp__<ID-CONNETTORE-ATLASSIAN>__addGraphContext`: link tra issue (blocca, collegato)
- `mcp__<ID-CONNETTORE-ATLASSIAN>__executeWrite`: ogni altra scrittura
- `mcp__<ID-CONNETTORE-ATLASSIAN>__executeDestructive`: solo per l'operazione `manageJiraSprint` (creare uno sprint, aggiungere issue, avviarlo). Il connettore la classifica così perché chiudere uno sprint è irreversibile. Per tutto il resto non usarlo: non si elimina.

## Query standard (JQL)

Passare sempre `cloudId: "TUO-SITO.atlassian.net"` e l'elenco esplicito dei campi (più leggero della vista `evidence`):

```json
["summary","description","issuetype","status","priority","labels","parent","issuelinks","assignee","created","updated","customfield_10016","customfield_10020","customfield_10021","customfield_10001","customfield_10015","duedate"]
```

I campi custom tornano sotto `fields.customFields`, con il nome come chiave: `customFields["Story point estimate"].value`, `customFields["Flagged"].value[].value`, `customFields["Sprint"].value`. Se il campo è vuoto manca l'intera chiave, non solo `value`. Le date arrivano con offset `+0100`: riscriverlo come `+01:00`.

Tutto lo spazio:

```
project = DEMO ORDER BY created ASC
```

Backlog aperto:

```
project = DEMO AND statusCategory != Done ORDER BY Rank ASC
```

Chiusi negli sprint chiusi (per la velocity):

```
project = DEMO AND sprint in closedSprints() AND statusCategory = Done
```

Fermi da più di 4 settimane:

```
project = DEMO AND statusCategory != Done AND updated <= -28d
```

## Mappa dei campi

| Modello comune | Jira | Note |
|---|---|---|
| id | `jira:<key>` | es. `jira:DEMO-4` |
| url | `https://TUO-SITO.atlassian.net/browse/<key>` | |
| tipo | `issuetype.name` | Epic → epic, Story → story, Bug → bug, Task → task, Subtask → task. "Feature" non ha corrispondenza: va in `campi_non_mappati` |
| tipo_originale | `issuetype.name` | |
| titolo | `summary` | |
| descrizione | `description` | la parte prima del titolo "Acceptance criteria". Gli escape markdown delle parentesi quadre (`\[` e `\]`) vanno rimossi |
| acceptance_criteria | dentro `description` | Non esiste un campo. Convenzione: in fondo alla descrizione, sotto il titolo `## Acceptance criteria`, un criterio per punto elenco. Da ogni criterio si toglie il marcatore di punto elenco iniziale e si rimuovono gli escape markdown `\[` e `\]`. Se il titolo manca: `[]`. La sezione finisce al titolo successivo: i punti sotto `## Criteri proposti, in attesa del parere di compliance` non sono acceptance criteria e vanno in `extra.criteri_proposti` |
| stato, stato_originale | `status.name` e categoria | vedi sotto |
| priorita | `priority.name` | Highest → critica, High → alta, Medium → media, Low e Lowest → bassa. Il campo non compare nella schermata di creazione e ogni issue nasce "Medium": finché nessuno lo cambia, il valore non dice nulla |
| stima | `customfield_10016` ("Story point estimate") | unità `story_point`. Vuoto = `null` |
| sprint | `customfield_10020` ("Sprint") | arriva come array di oggetti in `customFields["Sprint"].value`, ognuno con `id`, `name`, `state`, `goal`, `startDate`, `endDate`. Si prende il `name` dello sprint attivo, altrimenti di quello futuro, altrimenti dell'ultimo chiuso per `endDate`. L'array intero va in `extra.customfield_10020`. Se la issue non è in nessuno sprint la chiave manca del tutto: `null` |
| parent | `parent.key` | `jira:<key>` |
| dipendenze | `issuelinks` | "is blocked by" → bloccato_da, "blocks" → blocca, "relates to" → correlato, "duplicates" → duplica |
| label | `labels` | in minuscolo |
| assegnatario | `assignee.displayName` | |
| creato_il | `created` | |
| aggiornato_il | `updated` | |
| wsjf | nessun campo | sempre `null`. I valori WSJF stanno su Notion |
| extra | `customfield_10001` (Team), `customfield_10021` (Flagged), `customfield_10015` (Data di inizio), `duedate` | con il nome originale |

Spike non esiste come tipo: una story o un task con label `spike` diventa tipo `spike`.

## Mappa degli stati

Gli stati sono in italiano. Si mappa sulla categoria, che non dipende dalla lingua.

| Stato Jira | Categoria | Normalizzato |
|---|---|---|
| Da completare | new | todo |
| In corso | indeterminate | in_progress |
| In revisione | indeterminate | in_progress |
| Completata | done | done |

Non esiste uno stato "Blocked". Un item è `blocked` se:

- `customfield_10021` (Flagged) contiene "Impediment", oppure
- ha un link "is blocked by" verso un item non completato.

Lo stato Jira resta in `stato_originale`. Uno stato con categoria sconosciuta va in `campi_non_mappati`.

## Limiti noti

- Priorità: presente nei dati ma non modificabile dalla schermata dello spazio. Tutte le issue sono "Medium" di default.
- La vista `evidence` è pesante (molti dati di contorno per issue): leggere a pagine di 20.
- DEMO-1 ("Demo", Task) è un item di prova creato a mano: non fa parte del backlog demo. Resta nella query "Tutto lo spazio" ed è incluso nello snapshot come item di prova, già completato.
- Acceptance criteria in descrizione: dipende dal rispetto della convenzione.
- Nessuno sprint chiuso: la velocity non si può calcolare. Esiste solo "PAY Sprint 1", futuro (16-27 novembre 2026).
