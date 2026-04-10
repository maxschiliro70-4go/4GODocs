
---
## Sessione 16 — 2026-04-10

### Multi Preventivo — implementazione completa
- **Inbox IMAP** `preventivi@fourgo.it` — cron ogni 5min, markSeen:true, msgIndex per UID corretto
- **Browserless.io** `/export` via `https.request` Node (fetch non disponibile in callback IMAP) — scarica PDF da link protetti Alpitour (403 con fetch diretto). `BROWSERLESS_API_KEY` su Vercel + DB siteSettings. Visibile in Sistema+Impostazioni
- **Drag & drop** inbox → drop zone → Genera MP
- **Pagina pubblica** `/selezione-viaggio?code=MP-xxx` — card con foto, badge stagione, includes, Mi interessa, Vedi brochure Gamma
- **Gamma MP** — generazione sequenziale 1 per destinazione, 10 slides, background server-side (`/api/admin/multi-proposal/gamma-bg`, maxDuration 300s), polling 10s client, indicatore X/N Gamma persistente al rientro pagina
- **Migrations**: MultiProposal, PreventiviInbox, browserlessApiKey

### Fix vari
- `jsonrepair` import statico ESM (era require, non funzionava in Next.js)
- `max_tokens` call meta: 8000, call itinerario: 32000 (fix Vietnam PDF)
- `maxDuration 300s` su route Gamma (fix timeout)
- Mappa: banner "Percorso locale" per destinazioni con coordinate entro 50km (Malaysia, isole)
- Ultima slide Gamma: logo piccolo (80px) + foto sfondo destinazione
- Padding top 100px su `/selezione-viaggio` (fix navbar overlay)
