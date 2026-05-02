# Changelog

Tutti i cambiamenti significativi a `aude-extensions` sono documentati qui.

Formato basato su [Keep a Changelog](https://keepachangelog.com/it/1.1.0/).
Versioning secondo [Semantic Versioning](https://semver.org/lang/it/).

## [Unreleased]

### Da fare (riferimento `aude-platform/roadmap.md`)
- `/promote <file> [destinazione]` (Tier 1) — sposta da `_pending/` a `wiki/<dest>/` con creazione JIT della cartella
- `/save-pending` (Tier 1) — variante di `/save` che fila in `_pending/`
- `/ingest-pending`, `/autoresearch-pending` (Tier 2) — varianti pending dei comandi corrispondenti
- `userConfig` plugin (Tier 2) — opzioni opt-out e soglie configurabili
- `/deep-research`, `/diary`, `/weekly-review` (Tier 1-3)

## [0.2.0] - 2026-05-02

### Aggiunto
- Hook `SessionStart` in `hooks/hooks.json` — conta file in `wiki/_pending/` all'avvio sessione e mostra avviso se ≥ soglia 20 (configurabile in futuro)

### Note di rilascio
Prima implementazione del workflow "pending + JIT folders" deciso in `aude-platform/adr/0011`. Solo la parte hook (review queue counter); i comandi `/save-pending`, `/promote`, ecc. saranno implementati in una iterazione successiva (vedi roadmap.md sezione Tier 1).

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
