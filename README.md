# Yupoo Extractor System
## Zero-Weight CDN Bridge Architecture

---

## Come funziona (la strategia)

Le immagini Yupoo vivono già su un **CDN veloce** (photo.yupoo.com).
Non ha senso scaricarle e ricaricarle su Firebase — basta usare quegli URL direttamente.

```
[Yupoo CDN] ──────────────────────────────────────────────► [Browser utente]
                         ▲
[catalog.json ~15KB] ───┘
     (Firebase Hosting)
```

Firebase ospita **solo un file JSON di ~15KB** con la lista degli URL.
Il browser carica le immagini direttamente da Yupoo. **Zero storage Firebase.**

---

## I file del sistema

| File | Scopo | Dove va |
|------|-------|---------|
| `extractor.html` | Estrae URL da Yupoo, esporta `catalog.json` | GitHub Pages |
| `catalog-viewer.html` | Webapp catalogo che legge il JSON | Firebase Hosting |
| `catalog.json` | Lista URL generata dall'estrattore | Firebase Hosting (stessa cartella) |

---

## Workflow completo

### 1. Estrai il catalogo
1. Apri `extractor.html` (su GitHub Pages o in locale)
2. Incolla il link dell'album Yupoo
3. Clicca **"Estrai →"**
4. Clicca **"📄 Esporta catalog.json"**

### 2. Deploy su Firebase
```
il-tuo-progetto-firebase/
├── catalog-viewer.html   ← la webapp
├── catalog.json          ← il file generato dall'estrattore
└── (altri file del tuo progetto)
```

```bash
firebase deploy
```

### 3. Aggiorna il catalogo
Quando hai un nuovo album Yupoo:
1. Esegui di nuovo l'estrattore
2. Scarica il nuovo `catalog.json`
3. Sostituisci il vecchio file su Firebase
4. `firebase deploy`

Tempo totale: **2 minuti**, zero costi.

---

## Formato catalog.json

```json
{
  "_meta": {
    "album": "nome-album",
    "total": 143,
    "generated": "2026-03-04T..."
  },
  "images": [
    { "id": 1, "url": "https://photo.yupoo.com/...", "filename": "image_0001.jpg" },
    { "id": 2, "url": "https://photo.yupoo.com/...", "filename": "image_0002.jpg" }
  ]
}
```

La webapp `catalog-viewer.html` accetta anche questi formati semplificati:
```json
["https://photo.yupoo.com/...", "https://..."]
```
```json
{ "urls": ["https://...", "https://..."] }
```

---

## Costi Firebase

| Risorsa | Uso | Costo |
|---------|-----|-------|
| Hosting (catalog.json) | ~15KB per catalogo | **Gratis** (10GB free) |
| Storage immagini | **0 byte** (le immagini restano su Yupoo) | **€0** |
| Bandwidth immagini | **0** (Yupoo paga il CDN) | **€0** |

---

## Limitazioni

- ⚠️ Se l'album Yupoo diventa privato, le immagini non saranno più visibili
- ⚠️ Se il venditore Yupoo cancella l'album, le URL non funzionano più
- ✅ Per uso temporaneo o cataloghi che si aggiornano spesso: soluzione ideale
- ✅ Per archivio permanente: usa il tasto "📦 Scarica ZIP" e carica su Firebase Storage
