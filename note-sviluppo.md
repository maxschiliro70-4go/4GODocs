
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

---

## Sessione 18 — 2026-04-24 (post-lancio, giorno 2)

### Stato sito
- 🟢 LIVE su fourgo.it dal 2026-04-23
- 193 pagine rilevate da Google, CrUX vuoto (serve 28gg traffico reale)
- Commit finale sessione: `0849a02`

### Newsletter — fix multipli
- Logo: base64 inline → `https://fourgo.it/logo-4go.png` (base64 bloccato da Gmail/iOS Mail)
- Social icons: SVG → blocchi testo colorati (f, in, TT, WA, Th, Pi) compatibili tutti i client email
- Invio destinatari selezionati: usa `sendTest` con `emailTo` (Campaign API non supporta `emailTo` su `sendNow`)
- Bounce/disiscritti: separati — `emailBlacklisted` (bounce, eliminabili) vs `emailUnsubscribed` (da tenere)
- Entrambi nascosti dalla lista contatti UI, esclusi da "Tutti attivi"
- Badge `⚠️ N esclusi` visibile nell'header Destinatari
- Bottone elimina compare solo se `bounced > 0`
- `brevo-stats` GET ora restituisce `bounced`, `unsubscribed`, `blacklisted` (totale) separati

### Hero carousel — fix LCP
- **Problema**: shuffle `Math.random()` in `useEffect` causava secondo render completo → TBT spike
- **Soluzione**: shuffle server-side in `page.tsx`, passa 5 immagini già ordinate a HeroSection
- HeroSection: `heroImages` ora è costante (no useState, no shuffle client)
- Preload `/_next/image?url=img[0]` ora allineato all'immagine LCP reale
- `revalidate: 10s` → nuove 5 immagini ogni ~10s (utenti diversi vedono set diversi)
- Immagini già su Vercel Blob da sempre — nessuna migrazione necessaria
- **Risultati PSI prima/dopo**: Desktop TBT 735ms→264ms, Chi-siamo 13.6s→1.7s LCP

### Blog dedup
- Normalizzatore: 3 parole → 2 parole (cattura più cluster)
- Soglia: 3 articoli → 2 articoli
- Eseguito `dry=false` → 13 articoli messi in DRAFT, rimossi dalla sitemap
- Cluster rimossi: Salonicco (2), New York (3), Singapore-Langkawi (1), Kuala Lumpur (1), Gili Trawangan (1), Maldive (1), Tanzania (1), Nuova Zelanda (1), Lang Tengah (1), San Antonio Ibiza (1)

### SEO / Search Console
- `/coming-soon` → redirect 301 a `/` (pagina esisteva fisicamente, indicizzata da Google)
- 3 pagine con redirect in Search Console: http://fourgo.it/ (link esterno http), sofia-bulgaria-weekend (transitorio lancio), sicilia-maggio-giugno-2026 (pre-lancio) — tutti storici, nessuna azione codice
- **Da fare**: cambiare link http:// in https:// su GBP e Pagine Gialle

### Pending
- Aruba email forwarding (in attesa)
- Test email conferma prenotazione 1€ con operatori
- TOTP reset QR admin
- Bing Places verificato (7-12gg) → aggiungere URL a `sameAs` in `layout.tsx`
- Cambiare link http:// → https:// su GBP e Pagine Gialle
- Aggiornare cron-job.org URL da vercel.app → fourgo.it (password-expiry, blog-check, appointment-reminders)

---

## Sessione 19 — 2026-04-27

### Landing locali — contenuto unico AI
- Problema: 30 pagine `/agenzia-viaggi/[citta]` con 2 frasi template → thin content → Google le escludeva dall'indice
- Soluzione: endpoint `/api/admin/generate-city-content?secret=4go2026` → Haiku genera 3 blocchi per ogni città: paragrafo unico, trasporti specifici, FAQ locale
- Salvataggio in `SiteSettings.cityContent` (DB Neon, non filesystem — Vercel è read-only)
- Pagina legge dal DB con `getCityContent(slug)`, mostra i blocchi solo se presenti
- `robots: index: true` automatico quando content presente, `noindex` se manca
- `export const revalidate = 3600` + `revalidatePath` dopo generazione → pagine aggiornate subito
- 31 città generate senza errori, noindex rimosso automaticamente
- Schema migration: `ALTER TABLE "SiteSettings" ADD COLUMN IF NOT EXISTS "cityContent" JSONB`

### Performance /pacchetti
- FilterBar (261 righe) → `dynamic(() => import('./FilterBar'), { ssr: false })` → esce dal bundle principale
- Obiettivo: ridurre TBT da 233ms

### SEO pacchetti
- `aggregateRating` con fallback recensioni globali se nessuna specifica del pacchetto
- `hasMerchantReturnPolicy` + `shippingDetails` aggiunti a `offers` — fix Search Console warnings
- Rich Results Test: 12 elementi validi, zero errori

### Email AI — fix multipli
- AI classifier (Haiku) per email sistema/directory → MANAGED automatico senza lista domini
- Check Brevo lista 7 → nuovo cliente → risposta warm + `send-welcome` automatico
- Allegati/screenshot cliente → `sendPhoto` su Telegram a tutti gli operatori
- Prompt: mai "purtroppo non ho informazioni", sempre proattivo
- staff.aruba.it, italiaonline e pattern schede directory → MANAGED
- Email AI default su Potenziali invece di Tutti

### Telegram bot — fix
- Direzioni dall'hotel: usa nome hotel dai documenti → link Google Maps diretto
- Negozi/brand → link Maps search invece di "non ho info"
- Ristoranti senza posizione → chiedi posizione GPS
- Trasporti/JR Pass → risponde con conoscenza propria senza rimandare alla reception
- MAI dire "purtroppo non ho informazioni"

### Fix vari
- Facebook ID: 61575551933426 → 61579686167154 in 6 file
- Email operatori: massimo/alessia/inga@agenziaviaggi-fourtravel.it → @fourgo.it
- /coming-soon → redirect 301 a homepage
- Blog-dedup: normalizzatore 2 parole, soglia 2 → 13 DRAFT
- Hero shuffle server-side, revalidate 10s

### Backlink acquisiti
- MilanoToday ✅ (form contribuisci)
- L'Agenzia di Viaggi Magazine ✅ (redazione@lagenziadiviaggimag.it)
- GBP link http→https ✅

### Pending
- FIAVET + Confcommercio Senago (telefonate Massimiliano)
- Test email conferma prenotazione 1€
- TOTP reset QR admin
- Bing Places verificato → sameAs layout.tsx
