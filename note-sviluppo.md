
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

---
## Sessione 17 — 2026-04-13

### Bot Telegram — PDF upload
- Rimossa pulizia AI → salvataggio diretto testo raw (molto più veloce)
- Fix `pdf-parse` crash DOMMatrix su Vercel: aggiunta opzione `max: 0`
- OCR Claude Vision in background non-blocking: estrae testo da immagini/box colorati → aggiorna documento DB con `--- TESTO DA IMMAGINI/BOX ---`
- `maxDuration 300s` sul webhook
- Messaggi progresso: ⏳ Estrazione → ✅ Testo estratto (Xk) → 🔍 Analisi immagini → aggiornamento silenzioso
- Multi-upload: avviso "Invia un documento alla volta" per media_group_id (Telegram non supporta album sul bot)

### TelegramStatsSnapshot
- Nuova tabella `TelegramStatsSnapshot`: salva snapshot statistiche (totalSessions, totalMessages, avgMinutes, topQuestions[]) prima di ogni reset sessioni
- `telegram-reset` route: crea snapshot automaticamente prima di `deleteMany`
- `social-stats` route: legge da snapshot se sessioni vuote (preserva top domande dopo reset)
- Migration: `20260413_z_telegram_stats_snapshot`

### Blog autogen — foto
- Pexels: page casuale 1-4 + hash titolo per varietà (evita stessa foto ripetuta)
- Unsplash fallback dinamico con query destinazione (invece di foto statica hardcoded)
- Env var opzionale `UNSPLASH_ACCESS_KEY` per fallback API

### Badge operatore in Preventivi AI
- Campo `createdByName String?` aggiunto al modello `Preventivo`
- Salvato al POST con `useSession()` → `session.user.name.toLowerCase().split(' ')[0]`
- Mostrato nel frontend con `<CreatedByBadge>` (già presente, ora popolato)
- Migration: `20260413_z_preventivo_createdby`

### Proposta Viaggio — miglioramenti UI cliente
- **Voli senza orari**: bottone "Seleziona questa opzione" invece di tabella vuota → `preferredDate: 'confirmed'`
- **Hotel singolo**: auto-selezione al caricamento (nessun banner "Completa sistemazione")
- **Banner sticky generico**: "Completa la scelta della sistemazione" / "Completa la selezione dei trasporti"
- **Modifica email inline**: campo email editabile nel riepilogo sticky (pubblico) + click inline sull'email in admin
- **Action `update-email`**: salva email via POST `action: update-email`

### Proposta Viaggio — Extras dinamici (assicurazione, trasferimenti)
- `proposal.extras[]`: array `{label, price, included, negative}` letto dalla proposta
- Auto-selezione extras al caricamento (setSelectedExtras)
- Sezione visiva "Servizi aggiuntivi" nel body pagina con toggle per ogni extra
- Riepilogo sticky: mostra extras selezionati con prezzo
- Assicurazione generica 6% nascosta se esiste extra assicurativo in `proposal.extras`
- Totale: `baseTotal + transportTotal + extrasTotal`
- Submit: passa `extras_param` alla route `proposta-scegli`
- Route `proposta-scegli`: calcola `extrasTotal` dagli extras selezionati

### Proposta Viaggio — Parser PDF voli aerei
- Prompt `parseTravelDocument` aggiornato per gestire:
  - Voli aerei (Amadeus/Galileo format): compagnia, orari, scali, prezzi per opzione
  - 2 opzioni volo = 4 transport (andata/ritorno per ciascuna)
  - `prices: {economy: TOTALE}` per voli (un unico prezzo vs 3 classi treno)
  - Extras: assicurazioni, trasferimenti → array `extras`
- Action `update-proposal`: aggiorna legs/transports/extras/dates via POST

### Performance /contatti
- Immagini WebP: cont-01 242KB→125KB, cont-02 580KB→249KB, cont-03 111KB→34KB
- Script analytics GA4 + Meta Pixel: `afterInteractive` → `lazyOnload` (riduce TBT reale da 680ms)
- `<link rel="preload">` prima immagine slideshow per LCP
- Fix Next.js 15.5.14: rimosso `ssr: false` da `dynamic()` in Server Components (ora non permesso)
- Lighthouse /contatti (incognito): Performance 61, TBT reale 440ms (era 2750ms con estensioni Chrome)

### Ultimi commit sessione 17
- `4093c18` rimuovi pulizia AI PDF bot
- `fb297d9` OCR Vision background
- `e540b4b` messaggi progresso PDF
- `574c462` Vision OCR non-blocking + maxDuration 300s
- `5418da6` TelegramStatsSnapshot
- `54ca621` foto blog autogen varietà
- `aa6402d` voli senza orari → messaggio contatto
- `1d20dd7` date picker voli (poi sostituito con bottone)
- `bc2d378` bottone Seleziona questa opzione voli
- `cb7bcf0` auto-selezione hotel singolo
- `bd960d3` banner sticky generico
- `31797a4` voli→trasporti nel banner
- `e2a60f1` modifica email inline admin + pagina pubblica
- `99e5cd2` badge operatore preventivi AI
- `ea15555` immagini contatti WebP
- `243f8ec` analytics lazyOnload + preload LCP
- `dbb8150` fix ssr:false Next.js 15.5.14
- `92e301e` parser voli aerei + action update-proposal
- `34dcd57` extras dinamici proposta viaggio

### Pending sessione 17
- Aggiornare TP-2026-SX7VM con dati corretti da PDF (voli+extras) via console:
  `fetch('/api/admin/travel-proposal', {method:'POST', ...action:'update-proposal'...})`
- Rigenerare Gamma per TP-2026-SX7VM dopo aggiornamento dati
- Verificare Lighthouse /contatti dopo deploy lazyOnload (attesa 24-48h dati Speed Insights reali)
- Considerare riscrittura BgSlideshow: prima immagine come `<img fetchPriority="high">` per LCP reale
