# Adapter: notion

Stato: **attivo via connettore**. Mappa costruita leggendo 5 item reali il 2026-11-12. Campo "Job Size" aggiunto il 2026-11-13.

## Connettore

- Nome: Notion
- Prefisso dei tool in questa sessione: `mcp__<ID-CONNETTORE-NOTION>__`
- Se in un'altra sessione il prefisso è diverso (es. `mcp__claude_ai_Notion__`), aggiorna questo file, `backlog-sync.md` e `.claude/settings.json`.

## Dove sono i dati

- Pagina: "PAY Product", `<URL-PAGINA-NOTION>`
- Database: "Discovery", `<URL-DATABASE-NOTION>`
- Data source: `collection://<ID-DATA-SOURCE-NOTION>`
- Fonte di verità per: opportunità, ricerche, ipotesi, PRD.

## Tool di lettura (gli unici concessi a backlog-sync)

- `mcp__<ID-CONNETTORE-NOTION>__notion-query-data-sources`: legge le righe del database
- `mcp__<ID-CONNETTORE-NOTION>__notion-fetch`: legge schema del database e corpo di una pagina
- `mcp__<ID-CONNETTORE-NOTION>__notion-search`: ritrova pagina o database se l'ID cambia

## Tool di scrittura (solo sessione principale, sempre in "ask")

- `mcp__<ID-CONNETTORE-NOTION>__notion-update-page`: modifica proprietà o contenuto di un item
- `mcp__<ID-CONNETTORE-NOTION>__notion-create-pages`: crea un item
- `mcp__<ID-CONNETTORE-NOTION>__notion-create-comment`: commenta un item
- `mcp__<ID-CONNETTORE-NOTION>__notion-move-pages`, `mcp__<ID-CONNETTORE-NOTION>__notion-duplicate-page`
- `mcp__<ID-CONNETTORE-NOTION>__notion-update-data-source`, `mcp__<ID-CONNETTORE-NOTION>__notion-create-database`: cambiano lo schema, da usare solo su richiesta esplicita
- `mcp__<ID-CONNETTORE-NOTION>__notion-delete-pages`, `mcp__<ID-CONNETTORE-NOTION>__notion-delete-databases`: non usarli. Regola del progetto: non si elimina, si archivia o si sposta.

L'elenco completo dei tool di scrittura Notion è in `.claude/settings.json`, sezione "ask".

## Query standard

Tutti gli item (una sola chiamata a `notion-query-data-sources`, modalità sql):

```sql
SELECT url, createdTime, "Opp ID", "Nome", "Tipo", "Stato", "Priorità", "Area",
       "Problema", "Evidenze", "Epica Jira",
       "Business Value", "Time Criticality", "Risk Reduction", "Job Size", "Aggiornato il"
FROM "collection://<ID-DATA-SOURCE-NOTION>"
ORDER BY "Opp ID"
```

Solo le opportunità validate: aggiungi `WHERE "Stato" = 'Validata' AND "Tipo" = 'Opportunità'`.

Il corpo della pagina (ipotesi, metrica, note) si legge con `notion-fetch` sull'url dell'item. Fallo solo se serve: una chiamata per item.

## Mappa dei campi

| Modello comune | Notion | Note |
|---|---|---|
| id | `notion:PAYDISC-<Opp ID>` | "Opp ID" è un numero, il prefisso PAYDISC è fisso |
| tool | `notion` | |
| url | `url` | |
| tipo | "Tipo" | vedi tabella sotto |
| tipo_originale | "Tipo" | |
| titolo | "Nome" | |
| descrizione | "Problema" | se serve, aggiungi il corpo pagina |
| acceptance_criteria | nessun campo | sempre `[]`. Normale per la discovery, non è un difetto |
| stato, stato_originale | "Stato" | vedi tabella sotto |
| priorita | "Priorità" | Alta → alta, Media → media, Bassa → bassa |
| stima | nessun campo | sempre `null` |
| wsjf.business_value | "Business Value" | numero o null |
| wsjf.time_criticality | "Time Criticality" | numero o null |
| wsjf.risk_reduction | "Risk Reduction" | numero o null |
| wsjf.job_size | "Job Size" | numero o null. Aggiunto il 2026-11-13 |
| sprint | nessun campo | `null` |
| parent | nessun campo | `null` |
| dipendenze | "Epica Jira" | se valorizzato: `{"id": "jira:<chiave>", "relazione": "realizza"}` |
| label | "Area" | arriva come stringa JSON, es. `["Bonifici","Compliance"]`: va convertita in array, in minuscolo |
| assegnatario | nessun campo | sempre `null` |
| creato_il | `createdTime` | stesso formato di "Aggiornato il", convertire in ISO 8601 |
| aggiornato_il | "Aggiornato il" | formato `2026-11-12 20:47:00Z`, convertire in ISO 8601 |
| extra | "Opp ID", "Evidenze", "Epica Jira", "Area" | con il nome originale |

Tipo:

| Notion | Modello comune |
|---|---|
| Opportunità | opportunity |
| Ipotesi | opportunity (in `extra.tipo_discovery` = "Ipotesi") |
| Ricerca | spike |
| PRD | opportunity (in `extra.tipo_discovery` = "PRD") |

## Mappa degli stati

| Notion | Gruppo Notion | Normalizzato |
|---|---|---|
| Da esplorare | to_do | todo |
| In ricerca | in_progress | in_progress |
| In attesa | in_progress | blocked |
| Validata | complete | done |
| Scartata | complete | done (in `extra.esito` = "scartata") |

"Validata" vuol dire discovery chiusa, non funzionalità rilasciata. Gli agent devono guardare `stato_originale` quando conta la differenza.

## Limiti noti

- "Aggiornato il" cambia a ogni modifica, anche minima. Un item può sembrare vivo solo perché qualcuno ha corretto un refuso.
- Il collegamento con Jira è un campo di testo libero ("Epica Jira"). Nessuno garantisce che la chiave esista.
