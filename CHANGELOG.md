# Changelog

Tutti i cambiamenti significativi a `aude-extensions` sono documentati qui.

Formato basato su [Keep a Changelog](https://keepachangelog.com/it/1.1.0/).
Versioning secondo [Semantic Versioning](https://semver.org/lang/it/).

## [Unreleased]

### Da fare
- Comando `/deep-research` (ambizione 2): mappa dominio + autoresearch ricorsivo
- Skill personalizzate (ambizione 3)
- File paralleli per traduzione comandi claude-obsidian (ambizione 1)
- Adapter per integrazioni multi-repo (visione 6)

## [0.1.0] - 2026-05-02

### Aggiunto
- Setup iniziale del plugin: `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`
- Primo comando di prova `/saluta-aude` (verifica del flusso completo: comando → plugin → vault)
- Struttura cartelle: `commands/`, `skills/`, `hooks/`, `agents/`
- File `LICENSE` (MIT)
- File `CHANGELOG.md` (questo)

### Note di rilascio
Prima versione "giocattolo" del plugin di estensioni personali per il sistema Aude. Nessuna funzione operativa di valore in produzione, solo verifica che la pipeline plugin-marketplace funzioni end-to-end.
