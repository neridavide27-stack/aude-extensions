# CLAUDE.md — aude-plugin (Plugin Claude Code di Davide)

> **Sei in `~/Developer/aude-plugin/`.** Questa è la **REPO PRINCIPALE di Davide** — il plugin Claude Code che esegue tutte le skill, comandi, hook, agenti del sistema Aude.

---

## I 4 piani del progetto Aude (mai confonderli)

Architettura formalizzata in [decisioni/0013](https://github.com/neridavide27-stack/aude-docs/blob/main/decisioni/0013-architettura-vendor-custom-plugin.md):

| Cartella | Ruolo | Cosa contiene |
|---|---|---|
| `~/Desktop/Aude/` | **Conoscenza personale** | Vault Obsidian: note, fonti grezze, no codice |
| `~/Developer/aude-plugin/` (qui) | **Sviluppo concreto del sistema** | Plugin Claude Code installato: skill, comandi, hook, agenti |
| `~/Developer/aude-docs/` | **Engineering handbook** | ADR, RFC, design docs, runbook, tracking, memoria |
| `~/Developer/_forks/` | **Repo fonti (vendoring)** | Repo open source intatte (oggi `claude-obsidian/`) |

### Cosa significa per Claude (AI agent) che lavora qui

Sei in **aude-plugin**, il **codice** del sistema. Qui:
- ✅ Modifichi/crei skill, comandi, hook, agenti
- ✅ Esegui vendoring (copi da `_forks/<fonte>/` qui)
- ✅ Aggiorni `plugin.json`, `marketplace.json`, `CHANGELOG.md`
- ❌ **Non scrivere documentazione di progetto** — quella vive in `~/Developer/aude-docs/`
- ❌ **Non scrivere ADR/RFC** — quelli vivono in `~/Developer/aude-docs/`
- ❌ **Non modificare le repo fonti** in `~/Developer/_forks/`
- ❌ **Non scrivere note personali** — quelle vivono nel vault `~/Desktop/Aude/wiki/`

---

## Stato attuale

- **Versione:** v0.3.0 (2026-05-03)
- **Status Fase B di ADR 0013:** completata
  - Vendoring iniziale fatto: 11 skill + 4 comandi + 2 agenti + `_templates/`, `bin/`, `scripts/` copiati da `_forks/claude-obsidian/`
  - Hook `hooks/hooks.json` mergiato: pending counter (mio v0.2.0) + 4 hook funzionali del fork (SessionStart hot.md restore, PostCompact, PostToolUse auto-commit, Stop wiki-changed)
  - Plugin Claude Code: solo `aude-plugin@aude-plugin-marketplace` installato (claude-obsidian disinstallato)

### Prossime modifiche pianificate (RFC P1 in `aude-docs/proposte/`)

Vedi [aude-docs/proposte/README.md](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/README.md). Le 6 RFC P1 prossime da implementare:

- [RFC 0001](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/0001-skill-save-pending-nativo.md) — Skill `/save` con pending nativo
- [RFC 0002](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/0002-comando-promote.md) — Comando `/promote` (nuovo)
- [RFC 0003](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/0003-skill-wiki-ingest-pending-nativo.md) — Skill `/wiki-ingest` con pending nativo
- [RFC 0004](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/0004-skill-autoresearch-pending-nativo.md) — Skill `/autoresearch` con pending nativo
- [RFC 0005](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/0005-comando-discard.md) — Comando `/discard` (nuovo)
- [RFC 0006](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/0006-comando-review-queue.md) — Comando `/review-queue` (dashboard)

---

## Vantaggio di questa repo

**Non ha upstream**: zero conflitti per sempre. Tutto è sotto il tuo controllo.

Le repo fonti (`~/Developer/_forks/`) hanno upstream e vengono sincronizzate mensilmente. Tu pesi codice da loro e lo modifichi qui senza vincoli.

---

## Struttura della repo

```
aude-plugin/
├── .claude-plugin/
│   ├── plugin.json          ← descrizione del plugin (versione, autore)
│   └── marketplace.json     ← descrizione del marketplace
├── commands/                ← slash commands (es. /autoresearch, /save, /wiki, /canvas)
├── skills/                  ← skill complete (definite via SKILL.md con frontmatter)
│   ├── autoresearch/, canvas/, defuddle/, save/, wiki/, ...   (11 skill vendorate)
├── hooks/
│   └── hooks.json           ← SessionStart pending counter + 4 hook funzionali
├── agents/                  ← subagent specializzati (wiki-ingest, wiki-lint)
├── _templates/              ← template per i tipi di pagina del vault
├── bin/                     ← script bash di setup (setup-vault.sh, setup-dragonscale.sh)
├── scripts/                 ← script Python (boundary-score, tiling-check, allocate-address)
├── README.md
├── LICENSE
├── CHANGELOG.md
└── CLAUDE.md                ← questo file
```

---

## Workflow per modifiche al codice

### Caso A — Modifico una skill esistente in aude-plugin

Skill già vendorata e presente in `skills/<nome>/SKILL.md`. Modifica diretta del file.

**Procedura completa**: [aude-docs/runbook/modificare-skill.md](https://github.com/neridavide27-stack/aude-docs/blob/main/runbook/modificare-skill.md)

### Caso B — Voglio modificare una skill che esiste solo in una fonte (non ancora copiata)

1. Vendora dalla fonte in aude-plugin:
   ```bash
   cp -r ~/Developer/_forks/<fonte>/skills/<nome> ~/Developer/aude-plugin/skills/
   ```
2. Aggiungi voce in `~/Developer/aude-docs/tracking-fonti/<fonte>.md`
3. Modifica liberamente la copia in aude-plugin
4. Commit + push + plugin update

### Caso C — Voglio una skill totalmente nuova (no fonte)

Crea direttamente `skills/<nome>/SKILL.md` o `commands/<nome>.md`.

**Procedura completa**: [aude-docs/runbook/creare-skill-nuova.md](https://github.com/neridavide27-stack/aude-docs/blob/main/runbook/creare-skill-nuova.md)

### Caso D — Aggiungo una nuova fonte

Procedura in `aude-docs/runbook/aggiungere-fonte.md`. In sintesi:
1. `git clone <repo>` in `~/Developer/_forks/<nome-fonte>/`
2. Studia cosa fa
3. Vendora skill specifiche in aude-plugin (Caso B)
4. Crea file di tracking in `~/Developer/aude-docs/tracking-fonti/<nome-fonte>.md`

---

## Workflow per ogni commit

1. Modifichi/crei file in `commands/`, `skills/`, `hooks/`, `agents/`
2. Aggiorni `CHANGELOG.md` (sezione `[Unreleased] → Aggiunto/Modificato`)
3. Bump versione in `.claude-plugin/plugin.json` (semver: nuova feature `0.x.0 → 0.(x+1).0`, bugfix `0.x.y → 0.x.(y+1)`)
4. `git add . && git commit -m "..." && git push origin main`
5. In Claude Code: `claude plugin update aude-plugin` per scaricare la nuova versione
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

Versione attuale: **0.3.0** (vendoring iniziale completato).

---

## Test prima di pushare

1. Lancia il comando in Claude Code (su una sessione di test, idealmente nel vault `~/Desktop/Aude/`)
2. Verifica che faccia quello che ci si aspetta
3. Solo dopo: commit + push + `claude plugin update aude-plugin`

---

## REGOLE ARCHITETTONICHE — leggi prima di proporre modifiche

> **Tutte le modifiche al codice del sistema vivono qui (aude-plugin).** La documentazione di progetto vive in `~/Developer/aude-docs/`. Le repo fonti (`~/Developer/_forks/`) restano intatte.

### Decision tree per Davide

| Davide chiede | Cartella di lavoro | Note |
|---|---|---|
| Modifica/aggiunta di una skill, comando, hook, agente | **Qui** (`~/Developer/aude-plugin/`) | Vedi i Casi A-C sopra |
| Vendoring di una skill da una fonte | **Qui** (vendoring), poi **`aude-docs/tracking-fonti/`** (annotare) | Vedi runbook `modificare-skill.md` |
| Aggiunta nuova fonte | **`~/Developer/_forks/`** (clone) + **`aude-docs/tracking-fonti/`** (tracking) | Vedi runbook `aggiungere-fonte.md` |
| Una nuova decisione architettonica | **`~/Developer/aude-docs/decisioni/`** (ADR) | Numerazione progressiva |
| Una proposta di feature concreta | **`~/Developer/aude-docs/proposte/`** (RFC) | Numerazione progressiva |
| Aggiornare nota/conoscenza personale | **`~/Desktop/Aude/wiki/`** (vault) | Workflow pending |

### Prima di proporre modifiche, leggi

1. `~/Developer/aude-docs/README.md` — entry point del handbook
2. `~/Developer/aude-docs/CLAUDE.md` — regole prescrittive per agenti
3. `~/Developer/aude-docs/decisioni/0013-architettura-vendor-custom-plugin.md` — architettura corrente
4. `~/.claude/CLAUDE.md` — regole globali di Davide

---

## Vedi anche

- [README.md](README.md)
- [CHANGELOG.md](CHANGELOG.md)
- [aude-docs/decisioni/0013](https://github.com/neridavide27-stack/aude-docs/blob/main/decisioni/0013-architettura-vendor-custom-plugin.md) — architettura
- [aude-docs/runbook/modificare-skill.md](https://github.com/neridavide27-stack/aude-docs/blob/main/runbook/modificare-skill.md) — workflow vendor + custom
- [aude-docs/tracking-fonti/claude-obsidian.md](https://github.com/neridavide27-stack/aude-docs/blob/main/tracking-fonti/claude-obsidian.md) — tracking della fonte principale
- [aude-docs/proposte/README.md](https://github.com/neridavide27-stack/aude-docs/blob/main/proposte/README.md) — RFC delle prossime feature
