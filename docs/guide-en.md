# Full guide

[README](../README.md) · [Versione italiana](guida.md)

> All names of people and all data are fictional.
>
> The system does not provide legal advice.

**In short**

- A set of Claude Code subagents that helps a Scrum Product Owner keep a backlog healthy.
- It reads Notion and Jira through connectors, normalizes every item into one JSON snapshot, and runs six specialized agents on it.
- The agents cover sync, backlog health, story refinement, WSJF prioritization, fintech compliance impacts and sprint preparation.
- Agents only read and propose: every write to a tool needs an explicit OK from the Product Owner and is recorded in a change-log.
- Demo project: product, people, numbers and tickets are fictional.

**Language note.** The agents, `CLAUDE.md`, the adapters and all agent output are written in Italian. So are the demo data: Notion properties, Jira statuses and issue titles. In the tables below, titles are translated; on Notion and Jira they are in Italian. File and field names are left as they are, for example `proposte.md` (proposals) and `campi_non_mappati` (unmapped fields).

An agent system for Claude Code that helps a Scrum Product Owner keep the backlog in order. It reads data from several tools (Notion and Jira today), brings them into a single format and has six specialized agents analyze them. The agents read and propose. The Product Owner decides. No write to any tool happens without an explicit OK.

It is a demo: product, people, numbers and tickets are fictional.

**At a glance**

- Six agents: sync, backlog health, story refinement, WSJF prioritization, fintech compliance impacts, sprint preparation.
- Three-layer architecture: adding a tool means writing an adapter, without touching the agents.
- Tested on real tools: a Notion database and a Jira Cloud space, first in a cloud session and then on the Mac.
- Every proposed change goes into a file. The main session applies it after your OK and records it in a change-log.
- Four agents are generic: they read rules and values from `CLAUDE.md`. A new project starts from `template/`.

---

## Setup

This public copy contains no real identifiers: placeholders take their place. Before using the system, replace them with the values from your own account.

| Placeholder | What it is | Where to change it |
|---|---|---|
| `TUO-SITO.atlassian.net` | your Jira Cloud site (used as `cloudId` and in issue URLs) | `adapters/jira.md` |
| `mcp__<ID-CONNETTORE-NOTION>__` | tool prefix of the Notion connector in your session | `.claude/agents/backlog-sync.md` (`tools:` line), `.claude/settings.json`, `adapters/notion.md` |
| `mcp__<ID-CONNETTORE-ATLASSIAN>__` | tool prefix of the Atlassian connector in your session | `.claude/agents/backlog-sync.md` (`tools:` line), `.claude/settings.json`, `adapters/jira.md` |
| `<URL-PAGINA-NOTION>` | URL of the Notion page that holds the database | `adapters/notion.md` |
| `<URL-DATABASE-NOTION>` | URL of the "Discovery" database | `adapters/notion.md` |
| `collection://<ID-DATA-SOURCE-NOTION>` | identifier of the database's data source | `adapters/notion.md` ("Dove sono i dati" section and standard query) |
| `<URL-NOTION-PAYDISC-1>` … `<URL-NOTION-PAYDISC-5>` | URLs of the single opportunity pages | nothing to change by hand: they live in the snapshots in `backlog/`, which backlog-sync regenerates |

Notes:

- Until the connector prefixes are replaced, the permissions in `.claude/settings.json` and backlog-sync's tools match no real tool. Section 6 explains how to find the right prefix.
- `TUO-SITO.atlassian.net` also appears in the snapshots in `backlog/` and in a change-log in `output/`: they are sample data, not configuration.
- The Jira space key (`DEMO`) and the custom field ids (`customfield_…`) in `adapters/jira.md` apply to the demo site: check them on your own site.
- The snapshots in `backlog/` and the reports in `output/` are the results of the demo sessions and serve as examples.

---

## Contents

1. [How to use it](#1-how-to-use-it)
2. [How it works](#2-how-it-works)
3. [Folder structure](#3-folder-structure)
4. [The six agents](#4-the-six-agents)
5. [Writes: proposals, OK, change-log](#5-writes-proposals-ok-change-log)
6. [Connectors, tool names and permissions](#6-connectors-tool-names-and-permissions)
7. [Demo data](#7-demo-data)
8. [Product rules](#8-product-rules)
9. [Adding a tool](#9-adding-a-tool)
10. [What is verified and what is not](#10-what-is-verified-and-what-is-not)
11. [Known limits](#11-known-limits)
12. [Problems found and how to fix them](#12-problems-found-and-how-to-fix-them)
13. [Project history](#13-project-history)
14. [Next steps](#14-next-steps)

---

## 1. How to use it

### First time

1. Open the project folder in Claude Desktop. It also works on iCloud Drive. It must contain `CLAUDE.md`, `README.md`, `adapters/`, `backlog/`, `output/`, `schema/` and the hidden `.claude/` folder. In Finder, hidden folders show up with **Cmd+Shift+period**.
2. On iCloud: right-click the folder, "Keep Downloaded". If iCloud frees up space and leaves the files online only, Claude finds them empty.
3. In Claude Desktop, **Code** tab, open a **local** session on that folder. Not a cloud session: the folder cannot be reached from the cloud.
4. Check that the Notion and Atlassian connectors are connected (Settings, Connectors).

### Every working session

Write your requests in plain language. The agents expect Italian, for example:

| When | What you write | Meaning |
|---|---|---|
| Start of session | "Lancia backlog-sync" | Run backlog-sync |
| Right after | "Lancia backlog-health sullo snapshot di oggi" | Run backlog-health on today's snapshot |
| Before refinement | "Lancia story-refiner su `DEMO-14` e `DEMO-15`" | Run story-refiner on `DEMO-14` and `DEMO-15` |
| Before a story enters a sprint | "Lancia fintech-compliance su `DEMO-5`" | Run fintech-compliance on `DEMO-5` |
| Before planning | "Lancia prioritizer", then "Lancia sprint-prep su `PAY Sprint 1`" | Run prioritizer, then sprint-prep on `PAY Sprint 1` |
| To apply proposals | "Mostrami le proposte in attesa", then "OK per la riga 3" | Show pending proposals, then OK for row 3 |

**The examples use placeholders.** `DEMO-14`, `DEMO-15` and `DEMO-5` stand for Jira issue keys, `PAY Sprint 1` for the sprint name. Replace them with the ones from your own project, for example `CHECKOUT-142` and `Sprint 12`. In the demo data they match real issues and a real sprint in the test Jira space.

The recommended order is always: **sync, then analysis, then decisions**. The analysis agents read only the snapshot: if you skip the sync, they work on old data.

### What to read after each agent

Each agent writes a report to `output/AAAA-MM-GG_<agent>.md` (AAAA-MM-GG = YYYY-MM-DD), always in the same format:

1. Summary in 3 lines.
2. Problems grouped as Critical, Medium, Low, each with the item id.
3. Proposed actions.
4. Missing data.

The concrete changes it proposes go into `output/AAAA-MM-GG_proposte.md`.

---

## 2. How it works

The system has three layers.

```
   Notion          Jira           CSV
     |               |              |
  adapter         adapter        adapter        Layer 1: how each tool is read
     \               |             /
      \              |            /
       backlog-sync (the only agent with tool access, read-only)
                     |
     backlog/AAAA-MM-GG_HHMM_snapshot.json      Layer 2: single format
                     |
   backlog-health  story-refiner  prioritizer   Layer 3: analysis
   fintech-compliance  sprint-prep
                     |
        output/reports and output/proposals
                     |
   Main session + PO's OK  ------>  writes to Notion or Jira
                     |
        output/change-log
```

**Layer 1, adapters.** One file per tool in `adapters/`. It says which connector to use, which tools read and which write, which queries to run and how to translate fields and statuses into the common format.

**Layer 2, common model.** `schema/backlog-item.schema.json` defines the snapshot. Every item has the same fields whatever its source tool: id in the `tool:key` format, type, title, description, acceptance criteria, normalized status (todo, in_progress, blocked, done) and original status, priority, estimate, WSJF values, sprint, parent, dependencies, labels, dates. Anything extra the tool has goes into `extra`, so nothing is lost. Whatever backlog-sync cannot translate goes into `campi_non_mappati`, instead of being guessed.

**Layer 3, analysis agents.** They read only the snapshot. They do not know which tool the data come from, so they do not need to change when you change or add a tool.

**One snapshot per sync.** The name holds the real date and time, for example `2026-11-14_0935_snapshot.json`, and an existing file is never overwritten. Every report quotes the exact file it read, so an analysis can be rerun on the same data.

**Source of truth.** Discovery (opportunities, research, hypotheses, PRDs) lives on Notion. Delivery (epics, stories, bugs, tasks) lives on Jira. If the same content exists in two tools, the source of truth wins and the other item is linked or closed, never kept in sync by hand. The bridge between the two worlds is the "Epica Jira" (Jira epic) field on the Notion opportunity.

---

## 3. Folder structure

```
po-backlog-agents/
├── README.md                      short overview
├── CHANGELOG.md                   changes to the repository
├── docs/
│   ├── guida.md                   the guide in Italian
│   └── guide-en.md                this guide
├── CLAUDE.md                      project instructions, read by Claude at every start
├── TEST-generici.md               regression tests of the generic agents, with results
├── .claude/
│   ├── settings.json              permissions: what is allowed, what asks first, what is denied
│   └── agents/
│       ├── backlog-sync.md
│       ├── backlog-health.md
│       ├── story-refiner.md
│       ├── prioritizer.md
│       ├── fintech-compliance.md
│       └── sprint-prep.md
├── template/                      starting point for a new project
│   ├── CHECKLIST-nuovo-progetto.md   ten steps, from an empty folder to the first report
│   ├── CLAUDE.template.md         CLAUDE.md with the sections to fill in
│   ├── settings.template.json     permissions with connector placeholders
│   └── agents/                    the four generic agents and backlog-sync.template.md
├── adapters/
│   ├── README.md                  how to add a tool in 5 steps
│   ├── notion.md
│   ├── jira.md
│   └── csv.md
├── schema/
│   └── backlog-item.schema.json   snapshot format
├── backlog/
│   ├── AAAA-MM-GG_HHMM_snapshot.json   normalized snapshots, one per sync, never overwritten
│   ├── 2026-11-12_snapshot.json   old snapshots, named without a time
│   ├── 2026-11-13_snapshot.json
│   ├── import/
│   │   └── jira_PAY.csv           sample export, used only as a fallback
│   └── sample/
│       ├── 2026-11-12_snapshot.json   10 fictional items with planted defects
│       └── README.md                  list of the defects, to test the agents
└── output/
    ├── AAAA-MM-GG_<agent>.md      agent reports
    ├── AAAA-MM-GG_proposte.md     proposed changes, with a "stato" (status) column
    └── AAAA-MM-GG_change-log.md   applied changes
```

Claude does not create git repositories or worktrees: `settings.json` denies `git init` and `git worktree`. The original working folder does not use git; this GitHub repository is a copy made for publishing. If you clone it, commits and pushes stay your decision.

---

## 4. The six agents

All of them have `model: inherit`, so they use the session's model.

| Agent | When | What it does | Tools |
|---|---|---|---|
| **backlog-sync** | At the start of a session | Reads every active tool read-only, normalizes and writes the snapshot with the real date and time in its name. Stops if the file already exists. If a connector does not answer, falls back to CSV. Declares the fields it cannot map | Read, Glob, Write, 3 Notion read tools, 2 Jira read tools |
| **backlog-health** | After every sync | Duplicates, also across tools; items idle beyond the threshold in `CLAUDE.md`; stories without criteria or estimate; estimates off the scale; broken dependencies; empty epics; items without an epic; validated items not linked to delivery; items blocked without a reason; missing priority values; contradictions between criteria | Read, Grep, Glob, Write |
| **story-refiner** | Before refinement, on given ids | Rewrites the story in the format set in `CLAUDE.md` ("As a… I want… so that…"), checks INVEST, writes the criteria as Gherkin blocks with one `Scenario` per case, proposes a split if it exceeds the top of the scale or is not testable | Read, Grep, Glob, Write |
| **prioritizer** | Before planning | Applies the framework in the Prioritization section of `CLAUDE.md` (here WSJF on opportunities and epics), shows the calculation and the rationale, refuses to compute if a value is missing | Read, Grep, Glob, Write |
| **fintech-compliance** | Before an item enters a sprint | Flags impacts on PSD2 and SCA, Verification of Payee, AML, KYC and KYB, GDPR, DORA, cards, SDD, SDI, audit trail. Does not quote articles of law: it states the risk and who to check it with | Read, Grep, Glob, Write |
| **sprint-prep** | 1-2 days before planning | Draft sprint goal, Definition of Ready check on the candidates, capacity against the velocity of the last closed sprints (how many is set in `CLAUDE.md`), review and retrospective agendas | Read, Grep, Glob, Write |

Only backlog-sync touches the connectors, and only to read. The other five work on files.

### Generic agents and domain agents

backlog-health, story-refiner, prioritizer and sprint-prep are **generic**: they hold no Conto Business rules. They read story format, estimation scale, priority framework, thresholds, team and sprint length from `CLAUDE.md`. If a value is missing they do not use a default: they skip the check that depends on it and list it under missing data, with the `CLAUDE.md` section to complete. The regression test in `TEST-generici.md` confirmed that on Conto Business they give the same results as the previous version.

fintech-compliance is a **domain agent**: it only makes sense for a payments product. The generic agents hand items over to it when they fall under the Agent di dominio (domain agents) section of `CLAUDE.md`. Another project can have different domain agents, or none.

backlog-sync stays project-specific, because its `tools:` line holds the connector prefixes. `template/agents/` has it as `backlog-sync.template.md`, with placeholders.

### Which snapshot the analysis agents read

Unless the PO names one, they read the most recent one in `backlog/`, sorted by date and time. Old files without a time in the name are compared by date only. If an old file and a new one share the same date, they ask the PO which one to read.

If `backlog/` holds no snapshot at all, the two versions behave differently, on purpose:

- **Template agents** (`template/agents/`): they stop and ask the PO to run backlog-sync. In a real project, analyzing sample data instead of the real backlog would produce wrong reports without anyone noticing.
- **Conto Business agents** (`.claude/agents/`): they fall back to `backlog/sample/`. Conto Business is a demo, and whoever tries it without connectors must be able to run the agents straight away on the sample data.

Either way, the report summary quotes the exact path of the file read and its `generato_il` (generated at).

backlog-sync gets the real date and time from the main session (`date "+%Y-%m-%dT%H:%M:%S%z"`). If the prompt has no time, it does not write the snapshot and asks for it.

The agents write only to `backlog/` (backlog-sync) and to `output/` (all the others). An agent's frontmatter cannot restrict writing to a folder: the constraint lives in each agent's instructions and in the permissions.

---

## 5. Writes: proposals, OK, change-log

The life of a change:

1. An agent writes a row to `output/AAAA-MM-GG_proposte.md`:
   `| id | tool | field | current value | proposed value | reason | status |`
   with status "in attesa" (pending).
2. You ask the main session to apply it.
3. The main session checks the source of truth, translates field and value with the adapter, **shows you the exact call** and waits for your OK.
4. It runs the call, reads the item back from the tool to verify it, logs the outcome in `output/AAAA-MM-GG_change-log.md` and marks the row as applied.

Rules:

- An OK covers one row, not the following ones. You can give a combined OK, but it must be explicit ("OK for rows 2, 3 and 4").
- For a tool in CSV mode there is no write: the proposal stays in the file and you apply it by hand.
- Nothing is deleted, neither files nor items. Things are archived, closed or moved.
- When `editJiraIssue` changes a description, it rewrites it in full. The main session must read the current description from Jira before preparing the call, not use the snapshot text, which is normalized.

---

## 6. Connectors, tool names and permissions

### The tool prefix changes with the environment

The same tool has a different name depending on where the session runs:

| Environment | Notion | Atlassian |
|---|---|---|
| Claude Desktop, local session (the one in use) | `mcp__<ID-CONNETTORE-NOTION>__` | `mcp__<ID-CONNETTORE-ATLASSIAN>__` |
| Cloud session | `mcp__Notion__` | `mcp__Atlassian_MCP__` |

The code in the desktop prefix is the identifier of the connector installed on your account. It stays stable until you disconnect and reconnect the connector. **If you reconnect a connector, check the prefixes right away** and update them in these four files:

- `.claude/agents/backlog-sync.md` (`tools:` line)
- `.claude/settings.json`
- `adapters/notion.md`
- `adapters/jira.md`

### Tools used

| Connector | Read (backlog-sync only) | Write (main session only, always with an OK) |
|---|---|---|
| Notion | `notion-query-data-sources`, `notion-fetch`, `notion-search` | `notion-update-page`, `notion-create-pages`, `notion-update-data-source` and the others listed in `settings.json` |
| Atlassian | `searchJiraIssuesUsingJql`, `getJiraIssue` | `editJiraIssue`, `createJiraIssue`, `transitionJiraIssue`, `addOrEditJiraIssueComment`, `executeWrite` (for example `createJiraIssueLink`) |

`executeDestructive` stays in "ask", not in "deny". A permission in `settings.json` applies to the whole tool and cannot tell one operation from another, so the limit lives in the instructions (`CLAUDE.md` and `adapters/jira.md`): it is allowed only for the `manageJiraSprint` operation, to create a sprint or add issues to it. The connector classifies it as destructive because the same operation also closes sprints, which cannot be undone. Every call still asks for confirmation.

### Permissions in `.claude/settings.json`

- **allow:** reading files and the 5 connector read tools.
- **ask:** the Notion (36) and Atlassian (9) write tools. Claude asks for confirmation before each one.
- **deny:** the two tools that delete Notion pages and databases, `git init` and `git worktree`.

---

## 7. Demo data

### Scenario

A payments fintech for Italian SMEs. The "Conto Business" product: online account opening, company cards, instant transfers, invoice collection and reconciliation. A team of 6 developers and 1 QA, 2-week sprints.

### Notion

Page **PAY Product**, database **Discovery**. Properties (in Italian): Nome (name), Tipo (type), Stato (status), Priorità (priority), Area, Problema (problem), Evidenze (evidence), Business Value, Time Criticality, Risk Reduction, Job Size, Epica Jira (Jira epic), Opp ID, Aggiornato il (updated on).

| Id | Opportunity | Status | BV | TC | RR | Job Size | Jira epic |
|---|---|---|---|---|---|---|---|
| PAYDISC-1 | Payee verification before an instant transfer | Validated | 13 | 13 | 8 | 8 | DEMO-2 |
| PAYDISC-2 | Automatic reconciliation of SDI invoices with incoming payments | Validated | 8 | 5 | 3 | 13 | DEMO-9 |
| PAYDISC-3 | KYB onboarding in under 10 minutes | In research | 13 | 5 | 8 | 13 | DEMO-3 |
| PAYDISC-4 | Spending limits per company card | To explore | | | | | |
| PAYDISC-5 | Recurring collection via SDD for B2B subscriptions | On hold | 5 | 3 | 2 | 5 | |

### Jira

Site `TUO-SITO.atlassian.net`. Space named **PAY**, but with **key DEMO**: issues are called DEMO-n. Board "DEMO board" (id 1). Team-managed space, statuses in Italian: Da completare (to do), In corso (in progress), In revisione (in review), Completata (done).

| Key | Type | Title | Epic | Notes |
|---|---|---|---|---|
| DEMO-1 | Task | Demo | | Test item, done |
| DEMO-2 | Epic | Verification of Payee on instant transfers | | In progress |
| DEMO-3 | Epic | Digital KYB onboarding | | |
| DEMO-4 | Epic | Disposable virtual cards | | Empty, no source opportunity |
| DEMO-5 | Story | Show the IBAN holder's name before confirmation | DEMO-2 | In progress, 5 points, in sprint. Built behind a feature flag while awaiting the opinion |
| DEMO-6 | Story | Upload company documents online | DEMO-3 | In sprint, no estimate |
| DEMO-7 | Bug | Instant transfer stays pending after the timeout | DEMO-2 | 3 points, in sprint, Impediment flag with no cause |
| DEMO-8 | Story | Automatically match a payment to its invoice (exact case) | DEMO-9 | 5 points, in sprint |
| DEMO-9 | Epic | Automatic reconciliation of SDI invoices with incoming payments | | Holds the reconciliation rules |
| DEMO-10 | Task | Compliance, DPO and legal opinion on payee verification | DEMO-2 | 2 points, in sprint |
| DEMO-11 | Story | Company data from the business register extract | DEMO-3 | |
| DEMO-12 | Story | Declare the beneficial owner | DEMO-3 | Blocked by DEMO-11 |
| DEMO-13 | Story | KYB application submission, check and outcome | DEMO-3 | Blocked by DEMO-6, 11, 12 |
| DEMO-14 | Story | Manually match unmatched payments | DEMO-9 | Linked to DEMO-15 |
| DEMO-15 | Story | Record partial payments | DEMO-9 | |
| DEMO-16 | Story | Match one payment to several invoices | DEMO-9 | Blocked by DEMO-8. On release it replaces criterion 3 of DEMO-8 |

Sprint **PAY Sprint 1**, from 16 to 27 November 2026, not started yet: DEMO-5, 6, 7, 8, 10.

### Mapped Jira fields

| Field | Id on this site |
|---|---|
| Story point estimate | `customfield_10016` |
| Sprint | `customfield_10020` |
| Flagged (Impediment) | `customfield_10021` |
| Team | `customfield_10001` |
| Start date | `customfield_10015` |

Acceptance criteria have no field of their own: they sit in the description, under the heading `## Acceptance criteria`. Criteria still awaiting an opinion sit under `## Criteri proposti, in attesa del parere di compliance` (proposed criteria, awaiting compliance opinion), and the snapshot keeps them separate.

### Made-up values

Some numbers do not come from a real team or PO. I entered them to make the demo run, and they are declared in `output/2026-11-13_change-log.md`:

- Job Size of PAYDISC-1, 2, 3, 5 and Risk Reduction of PAYDISC-3.
- Story points of DEMO-7, 8, 10.

The agents know this and flag it as a breach of the "the team estimates" rule.

### Offline test data

`backlog/sample/` holds 10 fictional items with deliberately planted defects (a duplicate across tools, a 21-point story, a validated opportunity without an epic, an item idle for 6 weeks, an empty epic). They let you try the agents without connectors. The list of defects is in `backlog/sample/README.md`.

---

## 8. Product rules

They live in `CLAUDE.md` and apply to every agent. The generic agents look them up by section name, so section names must not change.

- **Backlog parameters:** items idle after 28 days without updates, a team of 6 developers and 1 QA, 2-week sprints, velocity over the last 3 closed sprints.
- **Discovery-delivery link:** a Notion opportunity is validated when its status is "Validata", and from then on it must have a linked Jira epic (`realizza` dependency or "Epica Jira" field). A linked opportunity and epic are not duplicates.
- **Domain agents:** fintech-compliance, for items touching payments, customer identity, personal data, cards or third-party integrations.
- **Story format:** "As a <user> I want <action> so that <benefit>", with a domain user (SME owner, accounting clerk, employee with a company card, accountant). INVEST criteria. Acceptance criteria as Gherkin blocks, one `Scenario` per case with `Dato`, `Quando`, `Allora` (Given, When, Then) on separate lines: a numbered list in prose is not enough. Edge cases to cover: amounts, timeouts, payment statuses.
- **Estimates:** Fibonacci story points 1, 2, 3, 5, 8, 13. Above 13 the story is split. The team estimates, not the PO and not the agents.
- **Prioritization:** WSJF framework, (business value + time criticality + risk reduction) / job size, all on the 1-13 scale. Computed on opportunities and epics. Job size is the explicit "Job Size" value of the opportunity on Notion, not the sum of the children. The linked epic inherits all four values. Stories inside an epic are ordered by hand by the PO. If a value is missing, the score is not computed. On a tie, the item with higher time criticality comes first.
- **Definition of Ready** and **Definition of Done:** 8 points each, with attention to compliance, audit trail and personal data kept out of logs.
- **Glossary:** KYC, KYB, AML, SCA, PSD2 (AISP and PISP), SEPA instant transfer, Verification of Payee, SDD, chargeback, virtual IBAN, reconciliation, SDI.

### Reconciliation rules (in DEMO-9)

Written once in the epic, and the stories refer to them:

- An invoice has a total and an outstanding balance. Statuses: to be collected, partly collected, collected.
- Payment equal to the balance: invoice collected. Lower: partly collected. Higher: no match, the excess is shown.
- Cancellation or reversal: the balance goes back up and the status is recalculated.
- Candidate invoice: an open invoice whose number appears in the payment reference.
- Automatic matching only for the exact case (and, after DEMO-16, for several invoices with an exact sum). Every other match, partial ones included, is done by the clerk.

### WSJF ranking with the current data

| # | Opportunity and epic | WSJF |
|---|---|---|
| 1 | Payee verification (PAYDISC-1 / DEMO-2) | 4.3 |
| 2 | KYB onboarding (PAYDISC-3 / DEMO-3) | 2.0 |
| 3 | Recurring SDD collection (PAYDISC-5) | 2.0 |
| 4 | Invoice reconciliation (PAYDISC-2 / DEMO-9) | 1.2 |

Not computable: PAYDISC-4 (no values) and DEMO-4 (no source opportunity). The ranking shows that the mechanism works, not that the order is right: the job sizes are made up.

---

## 9. Adding a tool

The full procedure is in `adapters/README.md`. In short:

1. Connect the connector and ask Claude for the exact list of tools, split into read and write. Without a connector: export a CSV to `backlog/import/<tool>_<project>.csv`.
2. Read 2-3 real items to find field names, custom field ids and status values.
3. Write `adapters/<tool>.md`, copying the structure of `notion.md`.
4. Add the read tools to backlog-sync, the write tools to "ask" in `settings.json`, and the tool to `CLAUDE.md`.
5. Run backlog-sync and check the snapshot, then backlog-health.

The analysis agents stay untouched.

For a **new project**, not just a new tool, start from `template/` and follow the ten steps in `template/CHECKLIST-nuovo-progetto.md`: fill in `CLAUDE.template.md`, copy the generic agents as they are, and write adapters and permissions for your own connectors.

---

## 10. What is verified and what is not

**Verified on real tools**

- Reading Notion (5 items) and Jira (16 items) through connectors, on the Mac, with a snapshot valid against the schema.
- Fallback to CSV when the Jira connector could not be reached.
- All six agents, each with its own frontmatter.
- Jira field map: story points, sprint, flag, parent, issue links, dates.
- Writes with an OK and read-back: issue creation, changes to descriptions and estimates, status changes, sprints, issue links, comments, Notion property updates.
- backlog-health found every planted defect and, on top of that, contradictions between criteria introduced while fixing them.
- Generic agents: the five regression tests in `TEST-generici.md` passed on copies of the working folder, including the case of a value missing from `CLAUDE.md`.

**Not verified**

- The velocity calculation: no sprint has been closed.
- A full story going through the Definition of Done.
- Reading the body of Notion pages and Jira comments: backlog-sync does not do it.
- Writing to a tool in CSV mode: by definition there is none.
- The template on a second project: so far it has only been tried on Conto Business.

---

## 11. Known limits

- Changes to `CLAUDE.md` and to the agents take effect from the next session: in the current one the agents get the copy read at startup.
- Velocity is computed only with at least 3 closed sprints.
- Jira priority exists but is not on the create screen: every issue starts as "Medium" and the value means nothing.
- Snapshots up to 13 November 2026 have the old name without a time and a conventional `generato_il` (generated at) of 23:59:59Z. The one for the 13th is the afternoon version: the morning reports cannot be reproduced.
- The Notion-Jira link is a text field ("Epica Jira"): nothing guarantees the key exists. backlog-health checks it.
- "Aggiornato il" (updated on) on Notion changes with every edit, however small: an item can look alive because of a fixed typo.
- DEMO-1 is included in the snapshot as a test item, already done.

---

## 12. Problems found and how to fix them

| Symptom | Cause | Fix |
|---|---|---|
| backlog-sync cannot find the connector tools | Different prefix between cloud and desktop | Update the prefixes in the 4 files listed in section 6 |
| The agents do not show up among the available ones | Session opened on the wrong folder, for example a `conto-business` inside another one | Open the session on the folder that contains `CLAUDE.md` |
| Agents and permissions missing after copying the files | The `.claude` folder is hidden and was left behind | Cmd+Shift+period in Finder, then copy it |
| A cloud session cannot see the folder on the Mac | iCloud Drive folders (under `~/Library`) do not attach to a cloud session | Use a local session |
| All connectors return 503 or time out at once | Temporary outage of the service that routes the calls | Wait and retry. Do not disconnect the connector: the prefix would change |
| "This connector can't be used with the current credential" | Seen during the same outage | Reopen the session once the service is back |
| New rules in `CLAUDE.md` are not applied | The agents read the copy from the start of the session | Reopen the session, or ask the agent to read the file again from disk |
| Fixing one criterion breaks another | Rules scattered across several stories | Write the rule once in the epic and have the stories refer to it |
| The 13 November snapshot had been overwritten in the afternoon: the morning reports could no longer be rerun on the same data. The regression test found it | The name held only the date, and backlog-sync overwrote the file for the same day | Real time in the name (`AAAA-MM-GG_HHMM_snapshot.json`) and in `generato_il`, and no overwriting: if the file exists, backlog-sync stops |

---

## 13. Project history

**12 November 2026, cloud session**

- Checked the connectors: Notion reachable, Atlassian not connected.
- Created the PAY Product page and the Discovery database with 5 opportunities on Notion.
- Wrote the schema, the adapters, the six agents, `CLAUDE.md`, `settings.json` and the 10 sample items.
- First sync: Notion through the connector, Jira through CSV.
- Connected Atlassian to a free Jira Cloud site. Rewrote the Jira adapter on the real fields.
- Created 7 issues on Jira, then estimates, statuses, flags. backlog-health found every planted defect.
- Created epic DEMO-9 for reconciliation and resolved the duplicate between Notion and Jira.
- story-refiner on DEMO-6 and DEMO-8, fintech-compliance on DEMO-5 and DEMO-7.

**13 November 2026**

- Compliance criteria on DEMO-5 and opinion task DEMO-10.
- New WSJF rule with an explicit Job Size on Notion.
- Splits applied: DEMO-6 and DEMO-8 narrowed, DEMO-11 to DEMO-16 created.
- Created PAY Sprint 1. Prioritizer produced the first ranking, sprint-prep rejected every candidate on the Definition of Ready.
- Moved the system to the Mac. Fixed the tool prefixes, first local sync succeeded.
- Created 5 dependency links between issues. Three rounds of backlog-health on reconciliation, down to zero contradictions.

**14 November 2026**

- Reconciliation rules completed in DEMO-9 (candidate invoice, automatic matching) and reversal criterion fixed in DEMO-8.
- Made backlog-health, story-refiner, prioritizer and sprint-prep generic: rules and values move to `CLAUDE.md`, in the new sections Parametri del backlog (backlog parameters), Collegamento discovery-delivery, Agent di dominio (domain agents) and Prioritizzazione (prioritization).
- Created the `template/` folder, with a checklist, a `CLAUDE.md` to fill in, permissions and agents.
- Regression test on copies of the working folder: five tests passed. The test found the overwritten snapshot of the 13th, and the snapshot name now includes the time.

---

## 14. Next steps

Left open on purpose, for planning:

- DEMO-6 in sprint without an estimate.
- DEMO-7 in sprint, blocked with no cause.
- 10 pending proposals and 7 gaps in the criteria, for refinement with the team.

To use it for real, it needs:

1. Estimates made by the team, instead of the made-up ones.
2. At least three closed sprints, for velocity.
3. The compliance opinion on DEMO-5 and DEMO-10.

Useful improvements to the system:

- Automatic validation of the snapshot after every sync.
- A `/applica-proposte` (apply proposals) skill for the proposal, OK, write, change-log cycle, today described only in `CLAUDE.md`.
- A comparison between two snapshots, to see what changed from one refinement to the next. With one file per sync and no overwriting, the data for it now exist.
- Trying the template on a second project, with different tools and rules, to find out what `CLAUDE.template.md` really lacks.
