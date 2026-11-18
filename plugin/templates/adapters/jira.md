# Adapter: jira

<!-- Modello del plugin po-backlog-agents, ricavato dall'adapter di Jira del progetto di riferimento Conto Business. Le parti uguali in ogni sito (nomi dei tool del connettore Atlassian, categorie degli stati, convenzioni) sono già scritte. Le parti tra < > dipendono dal sito e dallo spazio: compilale leggendo 2-3 issue reali, come spiegato in adapters/README.md. Quando hai finito, togli questo commento e le righe in corsivo. -->

Stato: **<da verificare | attivo via connettore dal AAAA-MM-GG>**. Mappa verificata su <numero> issue reali (<CHIAVE>-<n>, ...).

Se il connettore non risponde, backlog-sync ripiega su `./backlog/import/jira_*.csv` seguendo `adapters/csv.md`.

## Connettore

- Nome: Atlassian (connettore dell'app)
- Prefisso dei tool: `mcp__<ID-CONNETTORE-JIRA>__`
- Sito (cloudId): `<SITO>.atlassian.net`. Il valore esatto si legge con `getAccessibleAtlassianResources`.
- Spazio: nome "<NOME-SPAZIO>", **chiave `<CHIAVE>`**, tipo <team-managed | company-managed>. Le issue hanno chiave `<CHIAVE>-<n>`. Se la chiave viene rinominata, aggiorna questa riga e le query: Jira rinumera da solo le issue.
- Fonte di verità per: <epiche, story, bug, task, spike>.

## Tool di lettura (gli unici concessi a backlog-sync)

- `mcp__<ID-CONNETTORE-JIRA>__searchJiraIssuesUsingJql`: elenco delle issue, con il parametro `fields` indicato sotto.
- `mcp__<ID-CONNETTORE-JIRA>__getJiraIssue`: una issue, con `view: "evidence"` restituisce anche i link.

Altri tool di lettura, solo per la sessione principale: `getAccessibleAtlassianResources`, `executeRead` (con operazioni come `listJiraProjects`, `listJiraStatuses`, `getJiraIssueTypeMetaWithFields`, `listJiraIssueLinkTypes`, `listJiraIssueTransitions`), `discover`, `listJiraIssueComments`.

## Tool di scrittura (solo sessione principale, sempre in "ask")

- `mcp__<ID-CONNETTORE-JIRA>__createJiraIssue`
- `mcp__<ID-CONNETTORE-JIRA>__editJiraIssue`
- `mcp__<ID-CONNETTORE-JIRA>__transitionJiraIssue`: cambio di stato
- `mcp__<ID-CONNETTORE-JIRA>__addOrEditJiraIssueComment`
- `mcp__<ID-CONNETTORE-JIRA>__addGraphContext`: link tra issue
- `mcp__<ID-CONNETTORE-JIRA>__executeWrite`: ogni altra scrittura, per esempio l'operazione `createJiraIssueLink`
- `mcp__<ID-CONNETTORE-JIRA>__executeDestructive`: in "ask" solo se il team gestisce gli sprint da qui (operazione `manageJiraSprint`: creare uno sprint, aggiungere issue, avviarlo). Il connettore la classifica distruttiva perché chiudere uno sprint è irreversibile. Per tutto il resto non usarlo: non si elimina. Se non serve, mettilo in "deny".

*I nomi dei tool del connettore Atlassian sono gli stessi in ogni sito: cambia solo il prefisso. Controlla l'elenco reale al primo collegamento, perché il connettore può aggiungerne o rinominarne.*

## Query standard (JQL)

Passare sempre `cloudId: "<SITO>.atlassian.net"` e l'elenco esplicito dei campi (più leggero della vista `evidence`):

```json
["summary","description","issuetype","status","priority","labels","parent","issuelinks","assignee","created","updated","<ID-CAMPO-STORY-POINT>","<ID-CAMPO-SPRINT>","<ID-CAMPO-FLAGGED>","duedate"]
```

I campi custom tornano sotto `fields.customFields`, con il nome come chiave (per esempio `customFields["Sprint"].value`). Se il campo è vuoto manca l'intera chiave, non solo `value`. Le date possono arrivare con offset senza due punti (`+0100`): riscriverlo come `+01:00`.

*Gli id dei campi custom (`customfield_<numero>`) cambiano da un sito all'altro. Per trovarli: `executeRead` con `getJiraIssueTypeMetaWithFields` sul tipo Story, oppure `getJiraIssue` con `view: "evidence"` su una issue che ha story point e sprint. Nei siti team-managed il campo si chiama di solito "Story point estimate", nei company-managed "Story Points".*

Tutto lo spazio:

```
project = <CHIAVE> ORDER BY created ASC
```

Backlog aperto:

```
project = <CHIAVE> AND statusCategory != Done ORDER BY Rank ASC
```

Chiusi negli sprint chiusi (per la velocity):

```
project = <CHIAVE> AND sprint in closedSprints() AND statusCategory = Done
```

Fermi (giorni dal parametro "Item fermi" di `CLAUDE.md`):

```
project = <CHIAVE> AND statusCategory != Done AND updated <= -<giorni>d
```

## Mappa dei campi

| Modello comune | Jira | Note |
|---|---|---|
| id | `jira:<key>` | es. `jira:<CHIAVE>-4` |
| url | `https://<SITO>.atlassian.net/browse/<key>` | |
| tipo | `issuetype.name` | Epic → epic, Story → story, Bug → bug, Task → task, Subtask → task. Un tipo che non ha corrispondenza (es. "Feature") va in `campi_non_mappati`. *Se lo spazio usa nomi in italiano (Epica, Storia, Attività), aggiungili qui* |
| tipo_originale | `issuetype.name` | |
| titolo | `summary` | |
| descrizione | `description` | la parte prima del titolo "Acceptance criteria". Gli escape markdown delle parentesi quadre (`\[` e `\]`) vanno rimossi |
| acceptance_criteria | dentro `description` | Jira non ha un campo standard. Convenzione: in fondo alla descrizione, sotto il titolo `## Acceptance criteria`, i criteri in Gherkin o un criterio per punto elenco. Si tolgono i marcatori di punto elenco e gli escape `\[` e `\]`. Se il titolo manca: `[]`. La sezione finisce al titolo successivo. *Se il team usa un campo custom per i criteri, scrivi qui il suo id e togli la convenzione* |
| stato, stato_originale | `status.name` e categoria | vedi sotto |
| priorita | `priority.name` | Highest → critica, High → alta, Medium → media, Low e Lowest → bassa. *Negli spazi team-managed il campo spesso non è in schermata e ogni issue nasce "Medium": se è così, scrivilo nei limiti noti* |
| stima | `<ID-CAMPO-STORY-POINT>` ("<nome del campo>") | unità `story_point`. Vuoto = `null` |
| sprint | `<ID-CAMPO-SPRINT>` ("Sprint") | arriva come array di oggetti, ognuno con `id`, `name`, `state`, `goal`, `startDate`, `endDate`. Si prende il `name` dello sprint attivo, altrimenti di quello futuro, altrimenti dell'ultimo chiuso per `endDate`. L'array intero va in `extra.<ID-CAMPO-SPRINT>`. Se la issue non è in nessuno sprint la chiave manca del tutto: `null` |
| parent | `parent.key` | `jira:<key>` |
| dipendenze | `issuelinks` | "is blocked by" → bloccato_da, "blocks" → blocca, "relates to" → correlato, "duplicates" → duplica. *Controlla i tipi di link del sito con `listJiraIssueLinkTypes`* |
| label | `labels` | in minuscolo |
| assegnatario | `assignee.displayName` | |
| creato_il | `created` | |
| aggiornato_il | `updated` | |
| wsjf | <nessun campo, oppure gli id dei campi> | *Se i valori di priorità stanno in un altro tool (per esempio Notion), scrivi "sempre `null`" e dove stanno. Se stanno su Jira, un campo per valore* |
| extra | `<ID-CAMPO-FLAGGED>` (Flagged), `duedate`, <altri campi custom utili> | con il nome originale |

Spike non esiste come tipo standard: una story o un task con label `spike` diventa tipo `spike`.

## Mappa degli stati

Si mappa sulla categoria, che non dipende dalla lingua né dai nomi scelti dal team. I nomi degli stati si leggono con `listJiraStatuses`.

| Stato Jira | Categoria | Normalizzato |
|---|---|---|
| <nome dello stato iniziale, es. "Da completare" o "To Do"> | new | todo |
| <nome, es. "In corso" o "In Progress"> | indeterminate | in_progress |
| <altri stati intermedi, es. "In revisione"> | indeterminate | in_progress |
| <nome, es. "Completata" o "Done"> | done | done |

Un item è `blocked` se:

- `<ID-CAMPO-FLAGGED>` (Flagged) contiene "Impediment", oppure
- ha un link "is blocked by" verso un item non completato, oppure
- <lo stato si chiama "Blocked" o simile, se il workflow lo prevede>.

Lo stato Jira resta in `stato_originale`. Uno stato con categoria sconosciuta va in `campi_non_mappati`.

## Limiti noti

- La vista `evidence` è pesante: leggere a pagine di 20.
- Acceptance criteria in descrizione: dipendono dal rispetto della convenzione.
- Senza sprint chiusi la velocity non si calcola.
- <altri limiti scoperti leggendo le issue reali>
