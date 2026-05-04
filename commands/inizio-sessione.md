---
description: "Apri una sessione di lavoro su aude-docs: refresh roadmap snapshot + riepilogo Cose da fare + 3 sezioni RFC + handoff."
---

# /inizio-sessione

## Come usarlo

**Cosa fa**: apre una sessione di lavoro su `aude-docs/` mostrando in chat un riepilogo strutturato dello stato corrente del progetto. Quattro pezzi: cose tattiche da fare (dalla roadmap manuale), tre tabelle del backlog RFC (popolate dallo snapshot generato da `/aggiorna-roadmap`), e i task in sospeso dalla sessione precedente (da `handoff.md`).

**Perché esiste**: senza questo comando, le regole di "lettura iniziale" vivono nel `CLAUDE.md` root come istruzioni passive — caricate in contesto ma non si trasformano in azione. Con un trigger esplicito, l'apertura di sessione diventa azione concreta e prevedibile: tu lanci `/inizio-sessione`, Claude ti dice esattamente cosa c'è in pipeline.

**Quando lanciarlo**:
- **All'inizio di ogni sessione di lavoro su `aude-docs/`** — è il primo comando da invocare quando apri Claude Code
- Anche **a metà giornata** se vuoi rivedere lo stato corrente (richiama il refresh dello snapshot, quindi le 3 tabelle RFC sono sempre fresche)

**Output atteso**: messaggio strutturato con 4 sezioni — *Cose da fare*, *In corso ora*, *Importanti non ora*, *Da tenere d'occhio*, *Handoff*. Niente proposte di azione, niente check operativi (git status, ecc.) — solo il riepilogo dello stato. Sta a te decidere cosa fare dopo aver letto.

**Cosa NON fa** (intenzionalmente):
- Non fa `git status` né altre verifiche operative — fuori scope di questa iterazione
- Non propone un piano per la sessione — sta a te decidere
- Non agisce mai automaticamente — è solo lettura + display

**Specifica completa, alternative considerate, edge case**: vedi [[../../proposals/0002-comando-inizio-sessione|RFC 0002]] (quando archiviata: `[[archive/proposals/0002-comando-inizio-sessione]]`).

---

## Istruzioni per Claude (prompt operativo)

Sei in `aude-docs/` (o lavoro su di esso). Esegui i 5 step in ordine.

### Step 1 — Refresh implicito dello snapshot

Esegui via **Bash**:

```bash
python3 "$CLAUDE_PROJECT_DIR/.claude/hooks/regenerate-roadmap.py"
```

(Se `$CLAUDE_PROJECT_DIR` non è settato, usa il path assoluto `~/Developer/aude-docs/.claude/hooks/regenerate-roadmap.py`.)

Lo script rigenera `aude-docs/.cache/roadmap-snapshot.md` leggendo i frontmatter delle RFC attive in `proposals/`. Questo step copre il caso "modifica manuale in Obsidian" (il hook automatico copre solo le modifiche di Claude).

Output di questo step in chat: niente (è preparazione interna). Se lo script produce errori (exit ≠ 0 o stderr), riporta brevemente la prima riga di errore prima di proseguire.

### Step 2 — Carica i file rilevanti

Usa **Read** sui 3 file (path relativi a `aude-docs/`):

- `roadmap.md` — per la sezione manuale "Cose da fare"
- `handoff.md` — per i task in sospeso della sessione precedente
- `.cache/roadmap-snapshot.md` — per le 3 tabelle RFC (appena rigenerato dallo Step 1)

### Step 3 — Dichiara la lettura

Apri il messaggio in chat con esattamente questa riga:

```
Ho letto roadmap.md e handoff.md.
```

### Step 4 — Riepilogo a 4 sezioni

Mostra in chat il seguente blocco, popolato con i dati letti:

```
📋 Roadmap

Cose da fare:
<elenco delle voci della sezione "## Cose da fare" di roadmap.md, copiate testualmente>

In corso ora:
<tabella della sezione "## In corso ora" di .cache/roadmap-snapshot.md, copiata testualmente>

Importanti, non ora:
<tabella della sezione "## Importanti, non ora" di .cache/roadmap-snapshot.md, copiata testualmente>

Da tenere d'occhio:
<tabella della sezione "## Da tenere d'occhio" di .cache/roadmap-snapshot.md, copiata testualmente>

📌 Handoff (sessione precedente):
<contenuto integrale di handoff.md (corpo, escluso frontmatter)>
```

### Step 5 — Stop, attendi l'utente

**Non proporre piani**. **Non chiedere "cosa vuoi fare?"**. **Non agire**. Termina lì il messaggio. L'utente leggerà e dirà cosa vuole fare.

## Edge cases

- **`.cache/roadmap-snapshot.md` non esiste** → lo Step 1 (`/aggiorna-roadmap`) lo crea. Procedi normalmente.
- **`handoff.md` vuoto o "semanticamente vuoto"** → al posto del contenuto scrivi: *"Nessun task in sospeso. Sessione pulita."*. Considera "semanticamente vuoto" il file che:
  - Ha solo frontmatter (niente body), **oppure**
  - Contiene nel body la frase marker **"nessun task in sospeso"** (case-insensitive, anche dentro grassetto come `**Stato attuale: nessun task in sospeso.**`). Questa è la convenzione standard di `handoff.md` quando non ci sono task spezzati: il body resta con questa nota fissa fino a quando Claude scrive un vero handoff strutturato.
- **`roadmap.md` "Cose da fare" vuota** → al posto della lista scrivi: *"Nessuna voce manuale."*
- **3 sezioni RFC tutte vuote** nello snapshot → ognuna mostra "(nessuna)" come definito da `/aggiorna-roadmap`. Va bene così.
- **`roadmap.md` o `handoff.md` non esistono** → segnala l'anomalia e procedi con quello che hai.

## Cosa NON fare

- ❌ Non fare `git status` o altri controlli operativi
- ❌ Non proporre azioni o piani per la sessione
- ❌ Non modificare nessun file (eccetto `.cache/roadmap-snapshot.md` via Step 1)
- ❌ Non commentare il contenuto di handoff/roadmap (mostralo così com'è)
