# Changelog

Tutti i cambiamenti significativi a `aude-plugin` (ex `aude-extensions`) sono documentati qui.

Formato basato su [Keep a Changelog](https://keepachangelog.com/it/1.1.0/).
Versioning secondo [Semantic Versioning](https://semver.org/lang/it/).

## [0.3.0] - 2026-05-03

### Cambiato (BREAKING)

- **Rinominato il plugin da `aude-extensions` a `aude-plugin`**. Riflette il nuovo ruolo architetturale (ADR 0013): non più "estensioni del fork claude-obsidian" ma **repo principale del sistema Aude**.
- Aggiornato `plugin.json` (name, description, homepage, repository, keywords) e `marketplace.json` (name, description, plugin reference).
- Repo GitHub rinominata da `neridavide27-stack/aude-extensions` a `neridavide27-stack/aude-plugin`.

### Aggiunto

- **Vendoring iniziale dal fork claude-obsidian** (architettura "vendor + custom", ADR 0013):
  - `skills/`: 11 skill copiate (autoresearch, canvas, defuddle, obsidian-bases, obsidian-markdown, save, wiki, wiki-fold, wiki-ingest, wiki-lint, wiki-query)
  - `commands/`: 4 comandi copiati (autoresearch, canvas, save, wiki)
  - `agents/`: 2 agenti copiati (wiki-ingest, wiki-lint)
  - `_templates/`: template del plugin
  - `bin/`: script di setup
  - `scripts/`: script utility (boundary-score, tiling-check, allocate-address)
- **Hook merge** in `hooks/hooks.json`: combina il pending counter (mio, da v0.2.0) con i 4 hook funzionali del fork (SessionStart hot.md restore, PostCompact context restore, PostToolUse auto-commit, Stop wiki-changed alert).

### Rimosso

- Comando `/saluta-aude` (era comando di prova di v0.1.0, non più utile).

### Note di rilascio

Questa è la prima versione del plugin con contenuto sostanziale. Inizia il percorso di "refactoring incrementale" delle skill copiate dal fork: una alla volta, le skill verranno modificate in italiano + pending workflow nativo + criteri di Davide.

**Da disinstallare** in Claude Code dopo questa release: `aude-extensions`, `claude-obsidian`. **Da installare**: `aude-plugin` (l'unico plugin attivo nell'architettura ADR 0013).

## [0.2.0] - 2026-05-02

### Aggiunto

- Hook `SessionStart` in `hooks/hooks.json` — conta file in `wiki/_pending/` all'avvio sessione e mostra avviso se ≥ soglia 20 (configurabile in futuro)

### Note di rilascio

Prima implementazione del workflow "pending + JIT folders" deciso in `aude-docs/adr/0011`. Solo la parte hook (review queue counter); i comandi `/save-pending`, `/promote`, ecc. saranno implementati in una iterazione successiva (vedi roadmap.md sezione Tier 1).

Hook attivo per **tutti i progetti** dove aude-extensions è installato. Controlla la presenza di `wiki/_pending/` (cartella di staging del vault Aude) e mostra messaggio solo se esiste e contiene file. In altri progetti, silenzioso.

## [0.1.0] - 2026-05-02

### Aggiunto

- Setup iniziale del plugin: `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`
- Primo comando di prova `/saluta-aude` (verifica del flusso completo: comando → plugin → vault)
- Struttura cartelle: `commands/`, `skills/`, `hooks/`, `agents/`
- File `LICENSE` (MIT)
- File `CHANGELOG.md` (questo)

### Note di rilascio

Prima versione "giocattolo" del plugin di estensioni personali per il sistema Aude. Nessuna funzione operativa di valore in produzione, solo verifica che la pipeline plugin-marketplace funzioni end-to-end.
