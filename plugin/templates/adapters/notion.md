# Adapter: notion

<!-- Modello del plugin po-backlog-agents, ricavato dall'adapter di Notion del progetto di riferimento Conto Business. Le parti uguali in ogni workspace (nomi dei tool del connettore Notion, query, regole di conversione) sono già scritte. Le parti tra < > dipendono dal database: compilale leggendo lo schema con notion-fetch e 2-3 righe reali, come spiegato in adapters/README.md. Quando hai finito, togli questo commento e le righe in corsivo. -->

Stato: **<da verificare | attivo via connettore dal AAAA-MM-GG>**. Mappa costruita leggendo <numero> item reali.

## Connettore

- Nome: Notion (connettore dell'app)
- Prefisso dei tool: `mcp__<ID-CONNETTORE-NOTION>__`
- Se in un'altra sessione il prefisso è diverso (es. `mcp__claude_ai_Notion__`), aggiorna questo file, `.claude/agents/backlog-sync.md` e `.claude/settings.json`.

## Dove sono i dati

- Pagina: "<NOME-PAGINA>", `<URL-PAGINA>`
- Database: "<NOME-DATABASE>", `<URL-DATABASE>`
- Data source: `collection://<ID-DATA-SOURCE>`. Si legge con `notion-fetch` sull'url del database.
- Fonte di verità per: <opportunità, ricerche, ipotesi, PRD>.

## Tool di lettura (gli unici concessi a backlog-sync)

- `mcp__<ID-CONNETTORE-NOTION>__notion-query-data-sources`: legge le righe del database
- `mcp__<ID-CONNETTORE-NOTION>__notion-fetch`: legge lo schema del database e il corpo di una pagina
- `mcp__<ID-CONNETTORE-NOTION>__notion-search`: ritrova pagina o database se l'id cambia

## Tool di scrittura (solo sessione principale)

In "ask":

- `mcp__<ID-CONNETTORE-NOTION>__notion-update-page`: modifica proprietà o contenuto di un item
- `mcp__<ID-CONNETTORE-NOTION>__notion-create-pages`: crea un item
- `mcp__<ID-CONNETTORE-NOTION>__notion-create-comment`: commenta un item
- `mcp__<ID-CONNETTORE-NOTION>__notion-move-pages`, `mcp__<ID-CONNETTORE-NOTION>__notion-duplicate-page`
- `mcp__<ID-CONNETTORE-NOTION>__notion-update-data-source`, `mcp__<ID-CONNETTORE-NOTION>__notion-create-database`: cambiano lo schema, da usare solo su richiesta esplicita

In "deny": ogni tool che elimina pagine o database (per esempio `notion-delete-pages`, `notion-delete-databases`, se il connettore li espone). Non si elimina, si archivia o si sposta.

*I nomi dei tool del connettore Notion sono gli stessi in ogni workspace: cambia solo il prefisso. Controlla l'elenco reale al primo collegamento, perché il connettore può aggiungerne o rinominarne.*

## Proprietà consigliate del database

Se il database è nuovo, crealo con queste proprietà: la mappa sotto è già pronta. Se esiste già, scrivi nella mappa i nomi reali.

| Proprietà | Tipo Notion | Valori |
|---|---|---|
| Nome | titolo | |
| ID | ID univoco, con prefisso `<PREFISSO>` | |
| Tipo | select | Opportunità, Ipotesi, Ricerca, PRD |
| Stato | status | Da esplorare, In ricerca, In attesa, Validata, Scartata |
| Priorità | select | Alta, Media, Bassa |
| Area | multi-select | aree del prodotto |
| Problema | testo | |
| Evidenze | testo | |
| Epica Jira | testo | chiave dell'epica che realizza l'opportunità |
| Business Value, Time Criticality, Risk Reduction, Job Size | numero | solo se il framework di priorità è WSJF e i valori stanno su Notion |
| Aggiornato il | ultima modifica | |

## Query standard

Tutti gli item (una sola chiamata a `notion-query-data-sources`, modalità sql):

```sql
SELECT url, createdTime, "<ID>", "<Nome>", "<Tipo>", "<Stato>", "<Priorità>", "<Area>",
       "<Problema>", "<Evidenze>", "<Epica Jira>",
       "<Business Value>", "<Time Criticality>", "<Risk Reduction>", "<Job Size>", "<Aggiornato il>"
FROM "collection://<ID-DATA-SOURCE>"
ORDER BY "<ID>"
```

*Togli dalla query le proprietà che il database non ha: una colonna inesistente fa fallire tutta la chiamata.*

Solo le opportunità validate: aggiungi `WHERE "<Stato>" = '<Validata>' AND "<Tipo>" = '<Opportunità>'`.

Il corpo della pagina (ipotesi, metrica, note) si legge con `notion-fetch` sull'url dell'item. Fallo solo se serve: una chiamata per item.

## Mappa dei campi

| Modello comune | Notion | Note |
|---|---|---|
| id | `notion:<PREFISSO>-<numero>` | dalla proprietà "<ID>". *Se è un numero semplice, il prefisso lo aggiunge backlog-sync: scrivilo qui* |
| tool | `notion` | |
| url | `url` | |
| tipo | "<Tipo>" | vedi tabella sotto |
| tipo_originale | "<Tipo>" | |
| titolo | "<Nome>" | |
| descrizione | "<Problema>" | se serve, aggiungi il corpo pagina |
| acceptance_criteria | nessun campo | sempre `[]`. Normale per la discovery, non è un difetto |
| stato, stato_originale | "<Stato>" | vedi tabella sotto |
| priorita | "<Priorità>" | Alta → alta, Media → media, Bassa → bassa |
| stima | nessun campo | sempre `null` |
| wsjf.business_value | "<Business Value>" | numero o `null`. *Togli le quattro righe wsjf se il framework non è WSJF o i valori non stanno su Notion* |
| wsjf.time_criticality | "<Time Criticality>" | numero o `null` |
| wsjf.risk_reduction | "<Risk Reduction>" | numero o `null` |
| wsjf.job_size | "<Job Size>" | numero o `null` |
| sprint | nessun campo | `null` |
| parent | nessun campo | `null` |
| dipendenze | "<Epica Jira>" | se valorizzato: `{"id": "jira:<chiave>", "relazione": "realizza"}` |
| label | "<Area>" | può arrivare come stringa JSON, es. `["Bonifici","Compliance"]`: va convertita in array, in minuscolo |
| assegnatario | <proprietà persona, se esiste> | altrimenti sempre `null` |
| creato_il | `createdTime` | convertire in ISO 8601 |
| aggiornato_il | "<Aggiornato il>" | formato tipo `2026-11-12 20:47:00Z`, convertire in ISO 8601 |
| extra | "<ID>", "<Evidenze>", "<Epica Jira>", "<Area>" | con il nome originale |

Tipo:

| Notion | Modello comune |
|---|---|
| <Opportunità> | opportunity |
| <Ipotesi> | opportunity (in `extra.tipo_discovery` = "Ipotesi") |
| <Ricerca> | spike |
| <PRD> | opportunity (in `extra.tipo_discovery` = "PRD") |

## Mappa degli stati

| Notion | Gruppo Notion | Normalizzato |
|---|---|---|
| <Da esplorare> | to_do | todo |
| <In ricerca> | in_progress | in_progress |
| <In attesa> | in_progress | blocked |
| <Validata> | complete | done |
| <Scartata> | complete | done (in `extra.esito` = "scartata") |

*Uno stato del database che non compare qui va in `campi_non_mappati`. Il gruppo (to_do, in_progress, complete) si legge nello schema con `notion-fetch`.*

"Validata" vuol dire discovery chiusa, non funzionalità rilasciata. Gli agent devono guardare `stato_originale` quando conta la differenza.

## Limiti noti

- "Aggiornato il" cambia a ogni modifica, anche minima. Un item può sembrare vivo solo perché qualcuno ha corretto un refuso.
- Il collegamento con Jira è un campo di testo libero ("Epica Jira"). Nessuno garantisce che la chiave esista.
- backlog-sync non legge il corpo delle pagine né i commenti, salvo richiesta esplicita.
- <altri limiti scoperti leggendo le righe reali>
