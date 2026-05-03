# aude-extensions

Estensioni personali di Davide per il sistema Aude (knowledge management basato sul pattern [LLM Wiki di Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)).

## Cosa è

Plugin Claude Code che si **affianca** al plugin [`AgriciDaniel/claude-obsidian`](https://github.com/AgriciDaniel/claude-obsidian) senza modificarlo. Implementa il [pattern Adapter](https://alistair.cockburn.us/hexagonal-architecture) per estendere le funzionalità del sistema Aude tramite comandi e skill nuove.

## Filosofia

> *"Don't fork what you can extend. Don't extend what you can configure."*

Le modifiche di Davide al sistema Aude vivono qui, non nel fork di claude-obsidian. Questo plugin **non ha upstream**, vita autonoma, zero conflitti per sempre. Vedi documentazione completa in [`aude-docs`](https://github.com/neridavide27-stack/aude-docs).

## Comandi disponibili

| Comando | Descrizione | Status |
|---|---|---|
| `/saluta-aude` | Saluto + stato sintetico del vault Aude | ✓ funzionante (giocattolo, primo test) |

## Skill disponibili

(nessuna ancora, sezione segnaposto)

## Installazione

```bash
# 1. Aggiungi il marketplace di aude-extensions
claude plugin marketplace add neridavide27-stack/aude-extensions

# 2. Installa il plugin
claude plugin install aude-extensions@aude-extensions-marketplace
```

## Struttura

```
aude-extensions/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── commands/
│   └── saluta-aude.md
├── skills/
│   └── (vuota — aggiunte future)
├── hooks/
│   └── (vuota — aggiunte future)
├── agents/
│   └── (vuota — aggiunte future)
└── README.md
```

## Sviluppo

Per ogni nuovo comando/skill, vedi la guida completa in [`aude-docs`](https://github.com/neridavide27-stack/aude-docs).

Il flusso è:
1. Crea il file in `commands/<nome>.md` o `skills/<nome>/SKILL.md`
2. Commit + push su `main`
3. Aggiorna il plugin nel tuo Claude Code: `claude plugin update aude-extensions`

## License

MIT
