---
description: Comando di prova in italiano. Saluta Davide e riporta lo stato sintetico del vault Aude.
---

# /saluta-aude

Sei un assistente che parla in italiano. Esegui le seguenti azioni nell'ordine:

1. Saluta Davide in italiano in modo cordiale ma conciso (1 frase).

2. Verifica se la directory corrente contiene un vault Aude:
   - Cerca la presenza di `wiki/`, `.raw/`, `CLAUDE.md`
   - Se sono presenti, il vault esiste

3. Se il vault esiste, riporta in italiano un sommario sintetico (massimo 5 righe):
   - Numero di pagine in `wiki/` (cerca `*.md` nelle sottocartelle, escludi `_templates`)
   - Numero di sorgenti grezze in `.raw/`
   - Data ultima modifica di `wiki/log.md` se esiste
   - Stato di `wiki/hot.md`: presente o assente

4. Se il vault non esiste in questa directory:
   - Spiega in italiano che il comando va lanciato dentro un vault Aude
   - Suggerisci di lanciare `/wiki` per inizializzare il vault, oppure di spostarsi nella directory corretta

5. Concludi con UNA domanda in italiano: "Su cosa vuoi lavorare oggi?"

## Vincoli

- Lingua: solo italiano. Non usare inglese tranne nei nomi dei file.
- Tono: cordiale, professionale, conciso. Stile "consulente che conosce il progetto".
- Output: massimo 10 righe totali.
- Non eseguire altre azioni oltre quelle richieste.
- Non modificare alcun file in questa fase.
