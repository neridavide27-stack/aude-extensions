---
description: "Chiudi la sessione di lavoro su aude-docs: aggiorna roadmap, scrive handoff (sempre 9 sezioni), aggiorna CHANGELOG, esegue git update."
---

# /fine-sessione

## Come usarlo

**Cosa fa**: chiude in modo strutturato una sessione di lavoro su `aude-docs/`. Una sola interazione (conferma cancellazione voci di *Cose da fare* completate), poi 5 azioni automatiche in sequenza: aggiorna `roadmap.md`, scrive `handoff.md` (sempre con template 9 sezioni), aggiorna `CHANGELOG.md`, check igiene, git update finale (add + commit + push).

**Perché esiste**: oggi le regole di chiusura sessione (CHANGELOG entry, handoff, roadmap update, push) vivono come istruzioni passive nel `CLAUDE.md` root — caricate in contesto ma non si trasformano in azione attiva. Con un trigger esplicito, la chiusura diventa azione concreta e ripetibile.

**Quando lanciarlo**:
- Quando stai chiudendo la sessione di lavoro: *"chiudiamo qui"*, *"stop per oggi"*, *"fine sessione"* — invocare `/fine-sessione`
- Anche a metà giornata se vuoi "consolidare" lo stato corrente prima di una pausa lunga

**Output atteso**: una domanda (cancellazione voci roadmap completate) + report finale di chiusura con riassunto delle 5 azioni eseguite.

**Cosa NON fa**:
- Non chiede conferma per ogni singola azione automatica (è chiusura batch, non interactive)
- Non scrive `handoff.md` come "vuoto" — è **sempre** popolato col template a 9 sezioni
- Non gestisce il repo `plugin/` separato (solo `aude-docs/`)

**Specifica completa, alternative considerate, edge case**: vedi RFC `/fine-sessione` (da aprire come `0003-comando-fine-sessione`).

---

## Istruzioni per Claude (prompt operativo)

Sei in `aude-docs/`. Esegui **Fase 1** (1 sola domanda all'utente), poi **Fase 2** (5 step automatici in sequenza, senza ulteriori conferme).

## Fase 1 — Domanda all'utente

### Detect voci di "Cose da fare" completate

1. Leggi `aude-docs/roadmap.md` sezione `## Cose da fare`
2. Leggi i commit di oggi: `git log --since=midnight --pretty=format:"%h %s"` (in `aude-docs/`)
3. Leggi i file modificati di oggi: `git log --since=midnight --name-only --pretty=format:""` (deduplica)
4. **Heuristic detection**: una voce risulta "completata" se almeno una di queste condizioni:
   - Una keyword del testo della voce matcha un commit message di oggi
   - La voce dice *"creare X"* / *"aggiungere X"* e il file `X` è stato creato/modificato di recente
   - La voce dice *"decidere X"* e nei commit/conversazione c'è evidenza di decisione presa
5. Mostra in chat la lista numerata di tutte le voci, con marker `✓` per quelle rilevate completate:

```
📋 Cose da fare:
✓ 1. Revisione finale dei file aude-docs
  2. Architecture overview...
  3. Compilare informs: nei file di research/...
✓ 4. Decidere: filtro "In corso ora" Draft o Developing?

Risultano completate: 1, 4. Cancello? (sì / no)
```

6. **Casi limite**:
   - Lista *Cose da fare* vuota → salta Fase 1, vai direttamente a Fase 2
   - Nessuna voce rilevata come completata → mostra la lista senza ✓ e dichiara: *"Nessuna voce risulta completata oggi. `Cose da fare` invariata."*. Vai a Fase 2 senza domanda.

7. **Aspetta la risposta** (sì/no). Se "sì" → memorizza i numeri da cancellare (1, 4). Procedi a Fase 2.

## Fase 2 — Esecuzione automatica (5 step in sequenza, no conferme intermedie)

### Step 1 — Aggiorna `roadmap.md`

Apri `aude-docs/roadmap.md`:

- Cancella le voci confermate dalla risposta della Fase 1 (mantieni l'ordine delle altre)
- Aggiorna il campo `last-review:` nel frontmatter alla data odierna (`YYYY-MM-DD`)

**Niente accumulo di `[x]`** — le voci completate si cancellano fisicamente, non si marcano. Le sezioni Dataview di `roadmap.md` non si toccano (si auto-aggiornano).

### Step 2 — Scrive `handoff.md` (template 9 sezioni, **sempre**)

Sovrascrive completamente `aude-docs/handoff.md` col seguente template, **popolato sulla base di quello che è successo nella sessione** (conversazione + commit + file modificati). Il file è **sempre** in questo formato — non esiste più lo stato "vuoto/marker".

Template:

````markdown
---
genre: handoff
topic: "<topic kebab della sessione>"
created: YYYY-MM-DD
related-rfc: <NNNN se applicabile, oppure null>
predecessor: <filename del precedente handoff se la sessione è continuazione, oppure null>
---

# Handoff — <topic>

## 1. Stato corrente
Task principale della sessione, fase raggiunta, % progresso (anche "completato" è uno stato valido).

## 2. Cosa ho fatto in questa sessione
2-3 frasi concrete. Riassumi i commit principali e gli artefatti prodotti.

## 3. Decisioni prese
- Decisione X — motivazione (non solo outcome)
- Decisione Y — motivazione

## 4. Cose che NON hanno funzionato
- Approccio Z — perché ho rinunciato
(Salta se nulla di rilevante)

## 5. File modificati
- path/file.md — cosa ho cambiato e perché
- path/altro.md — ...

## 6. Domande aperte / blocker
*Formula con opzioni quando possibile, non solo "ho un dubbio".*
- "Devo X o Y?" → opzione A=..., B=..., mia opinione=...

(Se nessuna domanda aperta, scrivi: "Nessun blocker. Sessione chiusa cleanly.")

## 7. Prossimi passi
1. Concretamente: aprire X e fare Y
2. Poi: leggere Z

(Se la sessione è chiusa cleanly senza task spezzati, scrivi qui i prossimi passi suggeriti per la sessione successiva sulla base della roadmap "Cose da fare" rimanente.)

## 8. File da rileggere prima di riprendere
- ADR/RFC/runbook rilevanti per riprendere il contesto

## 9. Note aggiuntive
Vincoli, preferenze, contesto non già coperto sopra. Salta se vuoto.
````

**Importante**:
- Le 9 sezioni sono **sempre presenti** (anche con contenuto minimale)
- Niente "Stato attuale: nessun task in sospeso" come marker — quel pattern è stato sostituito dal post-sessione log strutturato
- Sii sintetico ma concreto: handoff serve a Davide-domani per ripartire senza dover ricostruire la sessione

### Step 3 — Aggiorna `CHANGELOG.md`

`CHANGELOG.md` = diario macro del repo, formato [Keep a Changelog 1.1.0](https://keepachangelog.com/it/1.1.0/).

**Granularità**: 1 entry per giornata `## [YYYY-MM-DD]` con sotto-sezioni `Added` / `Changed` / `Removed` / `Architecture decisions`. Bullet di 1 riga + link ad ADR/RFC. Il CHANGELOG **indicizza**, l'ADR **spiega**.

**Procedura**:
1. Leggi i commit di oggi (in `aude-docs/`)
2. Leggi gli eventi della sessione (voci roadmap cancellate dalla Fase 1, eventuali RFC archiviate, ADR creati)
3. Componi i bullet della giornata, classificandoli nelle 4 sotto-sezioni KaC. Solo le sotto-sezioni con voci appaiono.
4. **Caso A — data odierna NON in CHANGELOG**: crea nuova entry `## [YYYY-MM-DD]` in cima (subito dopo l'header introduttivo del file), con i bullet appena composti.
5. **Caso B — data odierna esiste già** (sessioni multiple stesso giorno): apri l'entry esistente, fai **merge** dei nuovi bullet nelle sotto-sezioni corrispondenti (preservando i bullet precedenti).
6. Mostra in chat il blocco dell'entry odierna (post-merge) come conferma visiva.

**Mai nel CHANGELOG**: motivazioni/tradeoff → ADR/RFC; procedure ripetibili → runbook; journal/note → vault; prosa lunga → ADR linkato.

### Step 4 — Check igiene (informativo, non blocca)

Esegue 2 verifiche di sanità senza modificare niente:

1. **Wikilink rotti**: scan rapido di `**.md` (esclude `sources-tracking/forks/`, `.git/`, `plugin/`, `archive/`) per `[[file]]` o `[[file|alias]]` che non corrispondono a un file esistente. Lista massimo 5 violazioni.
2. **RFC orfane**: RFC in `proposals/` con status `Draft` o `Developing` ma `created` > 30 giorni fa (probabili candidate a revisione).

Output: report inline nel messaggio finale (vedi sotto). Niente azione automatica — solo segnalazione.

### Step 5 — Git update finale

Esegui via Bash, **senza ulteriori conferme**:

```bash
cd "$CLAUDE_PROJECT_DIR" || cd ~/Developer/aude-docs

# Add file modificati durante /fine-sessione (CHANGELOG, handoff, roadmap)
git add CHANGELOG.md handoff.md roadmap.md

# Add eventuali altri file modificati significativi (es. RFC archiviata)
# (Se utente ha archiviato un'RFC: git mv proposals/NNNN-...md archive/proposals/NNNN-...md, già fatto in fase precedente)

# Commit con messaggio sintetico
git commit -m "fine-sessione $(date +%Y-%m-%d): chiusura sessione, update CHANGELOG/handoff/roadmap"

# Push su origin/main
git push origin main
```

Segui le **regole git documentate nel `CLAUDE.md` root**: niente force push, niente skip hooks, niente `git add -A` indiscriminato.

Se git push fallisce (rete, conflitti), riporta l'errore e proponi azione (ma NON tentare auto-recovery).

## Output finale

Dopo Fase 2, mostra in chat questo messaggio di chiusura:

```
✓ Sessione chiusa.

Roadmap: cancellate <N> voci, last-review aggiornato a <YYYY-MM-DD>
Handoff: aggiornato (topic: <topic>, sezioni popolate: 9/9)
CHANGELOG: entry [<YYYY-MM-DD>] aggiornata (<M> bullet)
Igiene:
  - Wikilink rotti: <0 / lista>
  - RFC orfane: <0 / lista>
Git: <K> commit pushati su origin/main

Buona pausa.
```

## Cosa NON fare

- ❌ Non chiedere conferme intermedie tra step della Fase 2
- ❌ Non lasciare `handoff.md` "vuoto" o col vecchio marker
- ❌ Non lavorare sul repo `plugin/` (solo aude-docs)
- ❌ Non eseguire `git push --force`, `git reset --hard`, o operazioni distruttive
- ❌ Non aggiungere voci a `Cose da fare` di roadmap (è territorio di `/inizio-sessione` o input utente diretto)
