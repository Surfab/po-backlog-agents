---
name: nuovo-progetto
description: "Prepara la cartella di un progetto per gli agent del Product Owner: fa al massimo 6 domande e crea CLAUDE.md, .claude/settings.json, .claude/agents/backlog-sync.md, schema, adapter di base e, se serve, il file di regole di dominio, partendo dai template del plugin. Non sovrascrive mai un file esistente."
disable-model-invocation: true
---

# Nuovo progetto

Prepari la cartella della sessione (la cartella del progetto) perché gli agent del plugin po-backlog-agents possano lavorarci. Lavori solo in questa cartella. Non sovrascrivi mai un file che esiste già.

I template stanno in `${CLAUDE_PLUGIN_ROOT}/templates/`:

| Template | File da creare nel progetto |
|---|---|
| `CLAUDE.template.md` | `CLAUDE.md` |
| `settings.template.json` | `.claude/settings.json` |
| `backlog-sync.template.md` | `.claude/agents/backlog-sync.md` |
| `schema/backlog-item.schema.json` | `schema/backlog-item.schema.json` |
| `adapters/README.md` | `adapters/README.md` |
| `adapters/csv.md` | `adapters/csv.md` |
| `domain.template.md` oppure `domain/fintech.md` | `domain/<dominio>.md`, solo se il progetto ha un dominio |
| `CHECKLIST-nuovo-progetto.md` | nessuno: lo leggi per i passi finali |

## Passo 1: controlla la cartella

1. Con Glob controlla quali file della tabella esistono già nella cartella del progetto. Controlla anche `domain/`.
2. Se uno o più file esistono: **fermati**. Elencali e chiedi al PO, file per file, se saltarlo (resta com'è) o interrompere tutto. Non sovrascrivere, non rinominare, non spostare niente. Un file saltato resta com'è anche se è diverso dal template.
3. Controlla `.claude/agents/`: se contiene file con lo stesso nome degli agent del plugin (`backlog-health.md`, `story-refiner.md`, `prioritizer.md`, `sprint-prep.md`, `domain-review.md`), avvisa il PO. Un agent di progetto con lo stesso nome ha la precedenza su quello del plugin. Non toccarli: decide il PO.

## Passo 2: le domande

Fai **al massimo 6 domande**, una alla volta, in quest'ordine. Non aggiungerne altre. Se il PO non sa o non vuole rispondere, lascia il segnaposto del template e vai avanti.

1. **Prodotto.** Nome del prodotto, una o due frasi su utenti e settore, lingua dei report, dati reali o fittizi.
2. **Tool di backlog.** Quali tool usa il team. Per ognuno: connettore dell'app o CSV, cosa contiene (per esempio epiche, story, bug oppure opportunità di discovery) e per quali tipi di item è la fonte di verità.
3. **Team e sprint.** Numero di sviluppatori e di QA, durata dello sprint in settimane, quanti sprint chiusi usare per la velocity, dopo quanti giorni senza aggiornamenti un item è fermo.
4. **Scala di stima.** Unità (per esempio story point Fibonacci), valori ammessi, valore massimo oltre il quale la story va divisa.
5. **Framework di priorità.** Nome e formula (per esempio WSJF), oppure "lo compilo dopo".
6. **Dominio.** "Nessuno", oppure il nome del dominio (per esempio fintech, sanità, accessibilità). Se il nome è "fintech", chiedi nella stessa domanda se partire dall'esempio completo dei pagamenti o dal modello vuoto.

## Passo 3: crea i file

Leggi ogni template con Read dal percorso `${CLAUDE_PLUGIN_ROOT}/templates/...` e scrivi il file nel progetto. Salta i file che il PO ha scelto di non toccare al passo 1.

- **`CLAUDE.md`**: dal template, sostituisci i segnaposto con le risposte. Usa solo quello che il PO ha detto: **non inventare valori e non usare default**. Dove non c'è una risposta, lascia il segnaposto `<...>` com'è. Togli le righe in corsivo di spiegazione solo dalle sezioni compilate per intero.
  - Sezione "Agent di dominio": con dominio "Nessuno" scrivi `File di regole: Nessuno` e togli la riga di domain-review dalla tabella "Agent". Altrimenti scrivi `File di regole: domain/<dominio>.md`.
  - Non cambiare i titoli delle sezioni né i nomi nella tabella "Parametri del backlog": gli agent li cercano per nome.
- **`.claude/settings.json`**: copia il template così com'è. I segnaposto dei connettori restano: si compilano quando i connettori sono collegati.
- **`.claude/agents/backlog-sync.md`**: copia il template così com'è, con i segnaposto nella riga `tools:`.
- **`schema/backlog-item.schema.json`**, **`adapters/README.md`**, **`adapters/csv.md`**: copia così come sono.
- **`domain/<dominio>.md`**, solo se il dominio non è "Nessuno". Il nome del file è il nome del dominio in minuscolo, con trattini al posto degli spazi e senza accenti (per esempio `domain/sanita.md`). Dall'esempio `domain/fintech.md` se il PO l'ha scelto, altrimenti da `domain.template.md` con il nome del dominio nel titolo. Non compilare tu le altre sezioni del modello: le regole di dominio le scrive il team.
- Crea le cartelle `backlog/import/` e `output/` se non esistono.

## Passo 4: chiudi

Scrivi al PO, in breve:

1. I file creati e quelli saltati.
2. I segnaposto rimasti: cerca il testo `<` seguito da maiuscole, da minuscole o da `ID-` nei file creati e riporta file e riga.
3. Cosa resta da fare in questo progetto, dai passi 6-10 di `${CLAUDE_PLUGIN_ROOT}/templates/CHECKLIST-nuovo-progetto.md`: collegare i connettori, mettere i nomi esatti dei tool di lettura in `backlog-sync.md` e nei permessi, scrivere un adapter per ogni tool, compilare il file di dominio, riaprire la sessione, primo sync e primo report.
4. Perché tre cose restano per progetto: `CLAUDE.md` e le regole di dominio sono del team; `backlog-sync.md` contiene i nomi reali dei tool di sola lettura dei connettori; `.claude/settings.json` serve perché gli agent dei plugin non possono dichiarare hooks, server MCP né permessi.

## Regole

- Scrivi solo i file elencati in questa skill, solo nella cartella del progetto.
- Non sovrascrivere, non eliminare, non spostare file esistenti.
- Non creare repository git né worktree.
