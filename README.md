# PO Backlog Agents

Sei agenti per Claude Code che controllano il backlog di un Product Owner su Notion e Jira. Leggono e propongono, il PO decide.

Six Claude Code agents that review a Product Owner's backlog on Notion and Jira. They read and propose, the PO decides.

**Come funziona, in due minuti:** [pagina interattiva](https://surfab.github.io/po-backlog-agents/) · **How it works, in two minutes:** [interactive page](https://surfab.github.io/po-backlog-agents/#en)

**Cosa ha dimostrato la demo**

- Ha trovato errori che nessuno aveva previsto: contraddizioni tra criteri di accettazione introdotte dalle stesse correzioni, e una soglia di 5 secondi nei criteri che nessuno aveva giustificato.
- sprint-prep ha bocciato 5 candidati su 5 sulla Definition of Ready, invece di riempire lo sprint.
- backlog-health ha trovato tutti i 7 difetti piantati nei dati di prova, compreso un duplicato tra Notion e Jira.

Esempi di report reali: [salute del backlog](output/2026-11-13_backlog-health.md) · [preparazione dello sprint](output/2026-11-13_sprint-prep.md) · [impatti di compliance](output/2026-11-12_fintech-compliance.md)

**Provalo in 2 minuti, senza connettori.** Apri la cartella in Claude Code e scrivi: "Lancia backlog-health su `backlog/sample/2026-11-12_snapshot.json`". Poi confronta il report con l'elenco dei difetti in `backlog/sample/README.md`.

Progettato da un Product Owner e costruito con Claude Code in tre giorni.

[🇮🇹 Italiano](#italiano) · [🇬🇧 English](#english) · [Guida completa](docs/guida.md) · [Full guide](docs/guide-en.md)

---

## Italiano

> I nomi di persona e i dati sono inventati. Il sistema non fornisce consulenza legale.

Un sistema di agenti per Claude Code che aiuta un Product Owner Scrum a tenere in ordine il backlog. Legge i dati da più tool (oggi Notion e Jira), li porta in un formato unico e li fa analizzare da sei agenti specializzati. Gli agenti leggono e propongono. Il Product Owner decide. Nessuna scrittura su nessun tool parte senza un OK esplicito.

### Perché non bastano gli agenti di Jira

Jira ha già agenti AI che controllano se un item è pronto. Se il backlog vive solo su Jira, partirei da quelli.

Questo sistema nasce per tre esigenze diverse:

- **Più tool insieme.** La discovery sta su Notion, la delivery su Jira. Gli agenti leggono entrambi e trovano i buchi tra i due: opportunità validate senza epica, duplicati tra tool.
- **Analisi ripetibili.** Ogni analisi lavora su uno snapshot datato. Si può rifare, confrontare tra un refinement e l'altro e conservare.
- **Traccia di ogni modifica.** Nessuna scrittura parte senza un OK, riga per riga, e ogni modifica finisce in un change-log. Dove serve un audit trail, conta.

Il limite: è uno strumento per il PO e gira sulla sua macchina. Il team vede le proposte solo quando diventano modifiche su Jira.

### Come lo proverei in un team vero

Due sprint, sola lettura, con l'ok di security sull'uso dell'AI con i dati di backlog.

1. **Prima di partire**, misuro gli ultimi 3 sprint: quota di story pronte al planning, story uscite dallo sprint non finite, ore di refinement.
2. **Per due sprint** lancio backlog-health e sprint-prep prima di ogni refinement e planning. Le proposte si discutono con il team, non si applicano in automatico.
3. **Misuro quattro cose:** story pronte al planning, story che tornano in refinement a sprint iniziato, quota di proposte che il team accetta, mio tempo di preparazione.
4. **Mi fermo** se dopo due sprint il team accetta meno di metà delle proposte o il tempo di preparazione non scende. Vorrebbe dire che gli agenti producono rumore.

### Come funziona

```
   Notion          Jira           CSV
     |               |              |
  adapter         adapter        adapter        Livello 1: come si legge ogni tool
     \               |             /
      \              |            /
       backlog-sync (unico agent con accesso ai tool, solo lettura)
                     |
     backlog/AAAA-MM-GG_HHMM_snapshot.json      Livello 2: formato unico
                     |
   backlog-health  story-refiner  prioritizer   Livello 3: analisi
   fintech-compliance  sprint-prep
                     |
        output/report e output/proposte
                     |
   Sessione principale + OK del PO  ------>  scritture su Notion o Jira
                     |
        output/change-log
```

**Livello 1, adapter.** Un file per tool in `adapters/`: come si legge, come si scrive, come si traducono campi e stati.

**Livello 2, modello comune.** Uno snapshot JSON uguale per tutti i tool. Quello che backlog-sync non sa tradurre viene dichiarato, non indovinato.

**Livello 3, agenti di analisi.** Leggono solo lo snapshot. Aggiungere un tool non li tocca.

I sei agenti: sincronizzazione, salute del backlog, rifinitura delle story, priorità WSJF, impatti di compliance fintech, preparazione dello sprint. Solo il primo legge i tool, e solo in lettura.

### Per usarlo

- Questa copia pubblica usa segnaposto al posto degli identificativi reali. Prima di usarla sostituiscili, come spiegato in [Configurazione](docs/guida.md#configurazione).
- Per un progetto nuovo si parte da [`template/`](template/) e dalla [checklist](template/CHECKLIST-nuovo-progetto.md).
- Tutto il resto è nella [guida completa](docs/guida.md): uso quotidiano, i sei agenti, scritture e permessi, dati demo, regole di prodotto, cosa è verificato, limiti, problemi incontrati, storia del progetto.

---

## English

> All names of people and all data are fictional. The system does not provide legal advice.

An agent system for Claude Code that helps a Scrum Product Owner keep the backlog in order. It reads data from several tools (Notion and Jira today), brings them into a single format and has six specialized agents analyze them. The agents read and propose. The Product Owner decides. No write to any tool happens without an explicit OK.

The agents, `CLAUDE.md`, the adapters and all agent output are in Italian, and so are the demo data.

### Why Jira's own agents are not enough

Jira already has AI agents that check whether a work item is ready. If the backlog lives only in Jira, I would start there.

This system answers three different needs:

- **Several tools at once.** Discovery lives in Notion, delivery in Jira. The agents read both and find the gaps between them: validated opportunities without an epic, duplicates across tools.
- **Repeatable analysis.** Every analysis runs on a dated snapshot. It can be rerun, compared from one refinement to the next, and kept.
- **A trail for every change.** No write happens without an OK, row by row, and every change goes into a change-log. Where an audit trail is required, this matters.

The limit: it is a tool for the PO and runs on their machine. The team sees the proposals only when they become changes in Jira.

### How I would test it with a real team

Two sprints, read-only, with security's approval for using AI on backlog data.

1. **Before starting**, I measure the last 3 sprints: share of stories ready at planning, stories leaving the sprint unfinished, hours spent in refinement.
2. **For two sprints** I run backlog-health and sprint-prep before every refinement and planning. Proposals are discussed with the team, never applied automatically.
3. **I track four things:** stories ready at planning, stories going back to refinement after the sprint starts, share of proposals the team accepts, my preparation time.
4. **I stop** if after two sprints the team accepts fewer than half of the proposals or my preparation time does not go down. It would mean the agents produce noise.

### How it works

Three layers. **Adapters** (one file per tool in `adapters/`) say how each tool is read and written and how fields and statuses are translated. A **common model** turns every item into the same JSON snapshot, and whatever backlog-sync cannot translate is declared, not guessed. The **analysis agents** read only the snapshot, so adding a tool does not touch them.

The six agents: sync, backlog health, story refinement, WSJF prioritization, fintech compliance impacts, sprint preparation. Only the first one reads the tools, and only to read.

### Using it

- This public copy uses placeholders instead of real identifiers. Replace them before use, as explained in [Setup](docs/guide-en.md#setup).
- For a new project, start from [`template/`](template/) and the [checklist](template/CHECKLIST-nuovo-progetto.md) (in Italian).
- Everything else is in the [full guide](docs/guide-en.md): daily use, the six agents, writes and permissions, demo data, product rules, what is verified, known limits, problems found, project history.
