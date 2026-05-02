---
type: project-level memory
project: aude-extensions
purpose: plugin Claude Code di Davide per estensioni del sistema Aude
---

# CLAUDE.md — aude-extensions

> **Sei in `~/Developer/aude-extensions/`. Questo è il PLUGIN PERSONALE di Davide.** Qui vivono comandi, skill, agenti, hook che estendono il sistema Aude.

## Regola fondamentale

**Le funzioni nuove di Davide vivono qui, NON nel fork claude-obsidian.** Vedi `~/Developer/aude-platform/adr/0003-strategia-fork-leggero.md`.

Vantaggio: questo plugin **non ha upstream**, quindi zero conflitti per sempre.

## Struttura

```
aude-extensions/
├── .claude-plugin/
│   ├── plugin.json          ← descrizione del plugin (versione, autore)
│   └── marketplace.json     ← descrizione del marketplace
├── commands/                ← slash commands (es. /saluta-aude)
├── skills/                  ← skill complete (definite via SKILL.md con frontmatter)
├── hooks/                   ← hook automatici (PreToolUse, SessionStart, ecc.)
├── agents/                  ← subagent specializzati
├── README.md
├── LICENSE
├── CHANGELOG.md
└── CLAUDE.md                ← questo file
```

## Come aggiungere un comando nuovo

1. Crea `commands/<nome>.md` con frontmatter `description:` + corpo del prompt
2. Aggiorna `CHANGELOG.md` (sezione `[Unreleased] → Aggiunto`)
3. Bump versione in `.claude-plugin/plugin.json` (semver: nuova feature = `0.x.0 → 0.x+1.0`)
4. `git add . && git commit && git push`
5. In Claude Code: `claude plugin update aude-extensions` per scaricare la nuova versione
6. Riavvio Claude Code → il comando è disponibile come `/aude-extensions:<nome>`

## Come aggiungere una skill

1. Crea cartella `skills/<nome>/`
2. Crea `skills/<nome>/SKILL.md` con frontmatter `name:`, `description:`, `allowed-tools:` + corpo
3. Idem per CHANGELOG, versione, push, plugin update

## Pattern raccomandati per ogni tipo di modifica

### "Voglio modificare un comando esistente di claude-obsidian"

→ **Adapter pattern**: crea un comando nuovo in `aude-extensions/commands/` che chiama internamente il comando originale.

Esempio: invece di modificare `claude-obsidian/commands/autoresearch.md`, crea `aude-extensions/commands/deep-research.md` che:
1. Mappa il dominio (codice tuo)
2. Per ogni topic, chiama `/autoresearch` (codice originale, intatto)
3. Sintetizza i risultati (codice tuo)

Originale `/autoresearch` resta funzionante. Tu usi `/deep-research`. Conflitti zero.

### "Voglio tradurre un comando in italiano"

→ **File parallelo**: crea `commands/<nome>-it.md` qui in aude-extensions. Il file originale di claude-obsidian non viene toccato.

### "Voglio aggiungere una funzione completamente nuova"

→ Plugin separato (qui): `commands/<nuova-funzione>.md`.

## Convenzioni di naming

- **Comandi italiani**: suffisso `-it` (es. `wiki-it`, `save-it`)
- **Comandi nuovi**: nome semantico in italiano o inglese, neutrale (es. `deep-research`, `morning-review`)
- **Skill**: cartelle con kebab-case (es. `mia-skill`, `gestione-task`)

## Versioning (Semantic Versioning)

- `0.x.0 → 0.(x+1).0`: nuova feature (comando/skill aggiunto)
- `0.x.y → 0.x.(y+1)`: bugfix o miglioramento minore
- `0.x.y → 1.0.0`: prima release stabile (quando il plugin è "pronto")

## Test prima di pushare

1. Lancia il comando in Claude Code (su una sessione di test)
2. Verifica che faccia quello che ci si aspetta
3. Solo dopo: commit + push + plugin update

## Vedi anche

- [README.md](README.md)
- [CHANGELOG.md](CHANGELOG.md)
- `~/Developer/aude-platform/03-pattern-professionali.md` — pattern in dettaglio
- `~/Developer/aude-platform/adr/0003-strategia-fork-leggero.md` — perché vivere qui
