# CLAUDE.md — aude-extensions (in transizione a `aude-plugin`)

> **Sei in `~/Developer/aude-extensions/`. Questa è la REPO PRINCIPALE di Davide** — il plugin Claude-plugin Code dove vivono tutte le skill, comandi, hook, agenti del suo sistema.

> **Aggiornamento 2026-05-03 (ADR 0013):** la repo cambia scopo e nome.
> - **Vecchio scopo:** estensioni del fork claude-obsidian via Adapter pattern
> - **Nuovo scopo:** repo principale di Davide (vendor + custom). Inizialmente popolata copiando dal fork, poi modificata progressivamente.
> - **Rinomina prevista:** `aude-extensions` → `aude-plugin` (Fase B di ADR 0013)

## Regola fondamentale (aggiornata)

**Tutte le modifiche al sistema vivono qui.** Le repo fonti (oggi `claude-obsidian/`, in futuro N) restano intatte. Quando vuoi modificare una skill di una fonte, la copi qui e la modifichi qui.

Vedi `~/Developer/aude-platform/adr/0013-architettura-vendor-custom-plugin.md` per la decisione architetturale completa.

Vantaggio: questa repo **non ha upstream**, quindi zero conflitti per sempre. Tutto è sotto il tuo controllo.

## Versione attuale: v0.2.0 (2026-05-02)

- ✅ Hook SessionStart per pending count (vedi `hooks/hooks.json`)
- ✅ Comando giocattolo `/saluta-aude-plugin` (in `commands/`)

## Roadmap a breve (post-rinomina e setup ADR 0013)

Riferimento completo in `~/Developer/aude-platform/roadmap.md`.

**Tier 0 — Setup architettura ADR 0013:**
- Rinomina repo `aude-extensions` → `aude-plugin` su GitHub e localmente
- Copia contenuto del fork in aude-plugin (skills, commands, hooks, agents)
- Aggiornamento `plugin.json` (nome, descrizione)
- Disinstallazione plugin claude-obsidian, installazione plugin aude-plugin

**Tier 1 — Prime modifiche:**
- Skill `/save` modificata: italiano + pending workflow nativo
- Comando nuovo `/promote`: sposta file da `_pending/` a wiki destinazione

## Workflow del vault Aude (ADR 0011)

Il vault Aude usa il workflow "JIT folders + pending" (ADR 0011). Significato per le skill che modifichi qui:

- Le skill che producono pagine wiki (es. `/save`, `/wiki-ingest`, `/autoresearch`) devono filare in `wiki/_pending/`, non direttamente in `wiki/<folder>/`
- Pre-popolare frontmatter con 3 sezioni: standard + type-specific + estensione pending (`pending: true`, `proposed_target`, `proposed_filename`, `generated_by`)
- Status default: `seed` (vedi ADR 0012)
- Promozione tramite comando esplicito di Davide (`/promote`)

## Struttura

```
aude-extensions/   (→ rinominata in `aude-plugin`)
├── .claude-plugin/
│   ├── plugin.json          ← descrizione del plugin (versione, autore)
│   └── marketplace.json     ← descrizione del marketplace
├── commands/                ← slash commands (es. /saluta-aude-plugin)
├── skills/                  ← skill complete (definite via SKILL.md con frontmatter)
├── hooks/                   ← hook automatici (PreToolUse, SessionStart, ecc.)
├── agents/                  ← subagent specializzati
├── README.md
├── LICENSE
├── CHANGELOG.md
└── CLAUDE.md                ← questo file
```

## Workflow per modifiche

### Caso A — Modifico una skill esistente in aude-plugin

Modifica diretta del file `skills/<nome>/SKILL.md`. Standard.

### Caso B — Voglio modificare una skill che esiste solo in una fonte

1. Copia il file dalla fonte in aude-plugin:
   ```bash
   cp ~/Developer/claude-obsidian/skills/<nome>/SKILL.md ~/Developer/aude-plugin/skills/<nome>/SKILL.md
   ```
2. Aggiungi voce in `~/Developer/aude-platform/upstream-watch/claude-obsidian.md` (skill copiata, modifiche pianificate)
3. Modifica liberamente la copia in aude-plugin
4. Commit + push + plugin update

### Caso C — Voglio una skill totalmente nuova

Crea direttamente in `skills/<nome>/SKILL.md` o `commands/<nome>.md`. Standard.

### Caso D — Aggiungo una nuova fonte

Procedura:
1. `git clone <repo>` in `~/Developer/<nome-fonte>/`
2. Studia cosa fa la fonte
3. Copia le skill che ti interessano in aude-plugin (caso B)
4. Crea `~/Developer/aude-platform/upstream-watch/<nome-fonte>.md` per tracking

## Workflow per ogni commit

1. Modifichi/crei file in `commands/`, `skills/`, `hooks/`, `agents/`
2. Aggiorni `CHANGELOG.md` (sezione `[Unreleased] → Aggiunto/Modificato`)
3. Bump versione in `.claude-plugin/plugin.json` (semver: nuova feature = `0.x.0 → 0.x+1.0`, bugfix = `0.x.y → 0.x.(y+1)`)
4. `git add . && git commit && git push`
5. In Claude-plugin Code: `claude-plugin plugin update aude-plugin` per scaricare la nuova versione
6. Riavvio Claude-plugin Code → modifica disponibile

## Convenzioni di naming

- **Skill copiate da fonti**: stesso nome dell'originale (es. `skills/save/`, `skills/autoresearch/`). Niente suffissi `-it` o `-aude-plugin`. Modifica diretta nel file.
- **Skill nuove**: nome semantico in italiano (es. `skills/diary/`, `skills/weekly-review/`)
- **Comandi**: kebab-case in italiano (es. `comandi/diario`) o nome internazionale (es. `commands/diary`)

## Versioning (Semantic Versioning)

- `0.x.0 → 0.(x+1).0`: nuova feature (skill/comando aggiunto, modifica funzionale rilevante)
- `0.x.y → 0.x.(y+1)`: bugfix o miglioramento minore
- `0.x.y → 1.0.0`: prima release stabile (quando il plugin è "pronto")

Salto importante previsto: `0.2.0 → 0.3.0` quando avverrà la copia iniziale dal fork (Fase B di ADR 0013, prima espansione massiccia del plugin).

## Test prima di pushare

1. Lancia il comando in Claude-plugin Code (su una sessione di test)
2. Verifica che faccia quello che ci si aspetta
3. Solo dopo: commit + push + plugin update

## Vedi anche

- [README.md](README.md)
- [CHANGELOG.md](CHANGELOG.md)
- `~/Developer/aude-platform/adr/0013-architettura-vendor-custom-plugin.md` — architettura corrente
- `~/Developer/aude-platform/upstream-watch/claude-obsidian.md` — tracking della fonte principale
- `~/Developer/aude-platform/roadmap.md` — funzionalità future
