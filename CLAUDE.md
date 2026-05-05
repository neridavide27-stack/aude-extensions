# CLAUDE.md — `plugin/` (codice del Plugin Claude Code)

> **Sei in `~/Developer/Aude/plugin/`.** Sotto-cartella del monorepo Aude che ospita il **codice** del plugin Claude Code: skill, comandi, hook, agenti.

---

## Posizione nel monorepo Aude

Dal 2026-05-05 ([[../decisions/0007-monorepo-aude|ADR 0007]]) il sistema Aude vive in `~/Developer/Aude/` come monorepo unico. Le 4 aree sono:

| Cartella | Ruolo | Cosa contiene |
|---|---|---|
| `~/Developer/Aude/wiki/` | **Knowledge base personale** | Note Obsidian, fonti grezze |
| `~/Developer/Aude/plugin/` (qui) | **Codice plugin** | Skill, comandi, hook, agenti |
| `~/Developer/Aude/` (root) | **Engineering handbook** | ADR, RFC, design, runbook, sources-tracking |
| `~/Developer/Aude/sources-tracking/forks/` | **Repo fonti (vendoring)** | Repo open source intatte |

`plugin/` è un **repo Git autonomo** ignorato dal `.gitignore` del monorepo (vedi [[../decisions/0005-aude-plugin-dentro-aude-docs|ADR 0005]]).

### Cosa significa per Claude (AI agent) che lavora qui

Sei in `plugin/`, il **codice** del sistema. Qui:
- ✅ Modifichi/crei skill, comandi, hook, agenti
- ✅ Esegui vendoring (copi da `../sources-tracking/forks/<fonte>/` qui)
- ✅ Aggiorni `plugin.json`, `marketplace.json`, `CHANGELOG.md`
- ❌ **Non scrivere documentazione di progetto** — quella vive in `../decisions/`, `../proposals/`, `../runbook/`
- ❌ **Non scrivere ADR/RFC** — quelli vivono in `../decisions/` e `../proposals/`
- ❌ **Non modificare le repo fonti** in `../sources-tracking/forks/`
- ❌ **Non scrivere note personali** — quelle vivono in `../wiki/`

---

## Stato attuale

- **Versione plugin:** v0.4.0 (2026-05-05)
- **Vendoring iniziale completato**: 11 skill + 6 comandi (autoresearch, canvas, save, wiki, inizio-sessione, fine-sessione) + 2 agenti
- **Plugin Claude Code installato**: solo `aude-plugin@aude-plugin-marketplace v0.4.0`

### Prossime modifiche pianificate (RFC attive)

Vedi [[../proposals/index]] per la lista corrente. Le RFC P1 storiche (skill `/save` con pending nativo, comandi `/promote`, `/discard`, `/review-queue`, ecc.) erano in roadmap precedente — oggi vivono come voci della roadmap "Cose da fare" o come RFC formalizzate quando arriva il momento di implementarle.

---

## Vantaggio di questa repo

**Non ha upstream**: zero conflitti per sempre. Tutto è sotto il tuo controllo.

Le repo fonti (`../sources-tracking/forks/`) hanno upstream e vengono sincronizzate mensilmente. Tu peschi codice da loro e lo modifichi qui senza vincoli.

---

## Struttura della repo

```
plugin/
├── .claude-plugin/
│   ├── plugin.json          ← descrizione del plugin (versione, autore)
│   └── marketplace.json     ← descrizione del marketplace
├── commands/                ← slash commands (autoresearch, canvas, save, wiki, inizio-sessione, fine-sessione)
├── skills/                  ← skill complete (definite via SKILL.md con frontmatter)
│   └── (autoresearch, canvas, defuddle, obsidian-bases, obsidian-markdown, save, wiki, wiki-fold, wiki-ingest, wiki-lint, wiki-query)
├── hooks/
│   └── hooks.json           ← SessionStart pending counter + 4 hook funzionali
├── agents/                  ← subagent specializzati (wiki-ingest, wiki-lint)
├── _templates/              ← template per i tipi di pagina del vault
├── bin/                     ← script bash di setup
├── scripts/                 ← script Python utility
├── README.md
├── LICENSE
├── CHANGELOG.md
└── CLAUDE.md                ← questo file
```

---

## Workflow per modifiche al codice

### Caso A — Modifico una skill esistente in `plugin/`

Skill già vendorata e presente in `skills/<nome>/SKILL.md`. Modifica diretta del file.

**Procedura completa**: [[../runbook/modificare-skill]]

### Caso B — Voglio modificare una skill che esiste solo in una fonte (non ancora copiata)

1. Vendora dalla fonte a plugin:
   ```bash
   cp -r ~/Developer/Aude/sources-tracking/forks/<fonte>/skills/<nome> ~/Developer/Aude/plugin/skills/
   ```
2. Aggiorna voce in `~/Developer/Aude/sources-tracking/<fonte>.md`
3. Modifica liberamente la copia in plugin
4. Commit + push + plugin update

### Caso C — Voglio una skill totalmente nuova (no fonte)

Crea direttamente `skills/<nome>/SKILL.md` o `commands/<nome>.md`.

**Procedura completa**: [[../runbook/creare-skill-nuova]]

### Caso D — Aggiungo una nuova fonte

Procedura in [[../runbook/aggiungere-fonte]]. In sintesi:
1. `git clone <repo>` in `~/Developer/Aude/sources-tracking/forks/<NNNN>-<nome-fonte>/`
2. Studia cosa fa
3. Vendora skill specifiche in plugin (Caso B)
4. Crea file di tracking in `~/Developer/Aude/sources-tracking/<NNNN>-<nome-fonte>.md`

---

## Workflow per ogni commit

1. Modifichi/crei file in `commands/`, `skills/`, `hooks/`, `agents/`
2. Aggiorni `CHANGELOG.md` (sezione `[Unreleased] → Aggiunto/Modificato`)
3. Bump versione in `.claude-plugin/plugin.json` E `.claude-plugin/marketplace.json` (semver: nuova feature `0.x.0 → 0.(x+1).0`, bugfix `0.x.y → 0.x.(y+1)`)
4. `git add . && git commit -m "..." && git push origin main`
5. In Claude Code: `claude plugin update aude-plugin@aude-plugin-marketplace` per scaricare la nuova versione
6. Riavvio Claude Code → modifica disponibile

---

## Convenzioni di naming

| Tipo | Convenzione | Esempio |
|---|---|---|
| Skill copiate da fonti | Stesso nome dell'originale | `skills/save/`, `skills/autoresearch/` |
| Skill nuove | Nome semantico in italiano | `skills/diary/`, `skills/weekly-review/` |
| Comandi (slash commands) | Kebab-case in italiano o inglese | `commands/diary.md`, `commands/promote.md` |

**Niente suffissi** (`-it`, `-aude`, ecc.) — la modifica è interna al file. La traduzione/personalizzazione è parte del contenuto del file, non del nome.

---

## Versioning (Semantic Versioning)

- `0.x.0 → 0.(x+1).0`: nuova feature (skill/comando aggiunto, modifica funzionale rilevante)
- `0.x.y → 0.x.(y+1)`: bugfix o miglioramento minore
- `0.x.y → 1.0.0`: prima release stabile (quando il plugin è "pronto")

Versione attuale: **0.4.0** (comandi `/inizio-sessione` e `/fine-sessione` aggiunti il 2026-05-05).

---

## Test prima di pushare

1. Lancia il comando in Claude Code (su una sessione di test, idealmente nel monorepo `~/Developer/Aude/`)
2. Verifica che faccia quello che ci si aspetta
3. Solo dopo: commit + push + `claude plugin update aude-plugin@aude-plugin-marketplace`

---

## REGOLE ARCHITETTONICHE — leggi prima di proporre modifiche

> **Tutte le modifiche al codice del sistema vivono qui (`plugin/`).** La documentazione di progetto vive in `../decisions/`, `../proposals/`, `../runbook/`. Le repo fonti (`../sources-tracking/forks/`) restano intatte.

### Decision tree per Davide

| Davide chiede | Cartella di lavoro | Note |
|---|---|---|
| Modifica/aggiunta di una skill, comando, hook, agente | **Qui** (`plugin/`) | Vedi i Casi A-C sopra |
| Vendoring di una skill da una fonte | **Qui** (vendoring), poi **`../sources-tracking/`** (annotare) | Vedi [[../runbook/modificare-skill]] |
| Aggiunta nuova fonte | **`../sources-tracking/forks/`** (clone) + **`../sources-tracking/`** (tracking) | Vedi [[../runbook/aggiungere-fonte]] |
| Una nuova decisione architettonica | **`../decisions/`** (ADR) | Numerazione progressiva |
| Una proposta di feature concreta | **`../proposals/`** (RFC) | Numerazione progressiva |
| Aggiornare nota/conoscenza personale | **`../wiki/`** (knowledge base) | Workflow pending |

### Prima di proporre modifiche, leggi

1. [[../README]] — entry point del monorepo
2. [[../CLAUDE]] — regole prescrittive del monorepo
3. [[../decisions/0001-architettura-vendor-custom-plugin|ADR 0001]] — architettura vendor + custom
4. [[../decisions/0007-monorepo-aude|ADR 0007]] — monorepo Aude
5. `~/.claude/CLAUDE.md` — regole globali di Davide

---

## Vedi anche

- [[README]] — README del plugin
- [[CHANGELOG]] — storia del plugin
- [[../decisions/0001-architettura-vendor-custom-plugin|ADR 0001]] — architettura
- [[../decisions/0005-aude-plugin-dentro-aude-docs|ADR 0005]] — plugin spostato dentro il monorepo
- [[../decisions/0007-monorepo-aude|ADR 0007]] — monorepo Aude
- [[../runbook/modificare-skill]] — workflow vendor + custom
- [[../runbook/creare-skill-nuova]] — workflow nuova skill
- [[../runbook/aggiungere-fonte]] — workflow aggiungere fonte
- [[../sources-tracking/CLAUDE]] — regole tracking fonti
