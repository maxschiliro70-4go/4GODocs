# Sessione 4GO-19 — 12 maggio 2026

## Commit range: `ac9043e` → `9a3a1b9`

---

## Fix completati

### Blog
- **E-E-A-T+SEO** compare nel filtro tipi (basato su `packageSlug.startsWith('keyword-')`)
- **Dedup blog** endpoint `/api/admin/blog-dedup?secret=4go2026&action=delete` — eliminati 2 duplicati (Bhutan, Follonica)
- **Blog reimage** endpoint `/api/admin/blog-reimage?secret=4go2026&dry=false&limit=20` — 17 foto duplicate sostituite
- **Foto Pexels** — query variata per tipo articolo (guide/faq/budget/itinerar/ecc.) + shuffle risultati → foto diverse garantite per stessa destinazione
- **Blog-autogen** — aggiunto `publishedAt: new Date().toISOString()` alla creazione post
- **Pacchetti admin** — rimosso A→Z, default Più recenti (già fatto sessione precedente, riconfermato)

### Indicizzazione Google
- **index-google cron** — include post con `publishedAt null`, landing città, pagine statiche principali (privacy, termini, quando-viaggiare, faq, cookie, ecc.); max 200 URL/giorno; notifica Telegram Emi con report dettagliato + errori critici
- **index-all endpoint** `/api/admin/index-all?secret=4go2026` — indicizzazione massiva; eseguito 112/200 URL (quota); 88 rimanenti affidate al cron
- **robots.txt** — blocca `/_next/static/`, `/_next/image`, `/pacchetti-viaggio-su-misura`
- **publishedAt** aggiornato in bulk via `index-all` su tutti i post PUBLISHED con valore null

### Geocoding mappe
- **regeocode-packages** — aggiunto GET handler `?slug=` per chiamate dal browser
- **Coordinate hardcoded** per 50+ città esotiche: Polinesia Francese, Seychelles, Oman, NZ, Australia, Fiji, Asia (Pechino/Seoul/Tokyo), Rep. Dominicana, isole del Pacifico
- **Città da ignorare**: `italia`, `italy`, `milano` → `null` (escluse dalla mappa)
- **Rigeocodificati** 10 pacchetti problematici: Oman/Zanzibar, NY+Rep.Dom, Cina/Giappone/Corea, Seychelles, Sudafrica+Zanzibar, NZ+Isole Cook, Australia+Polinesia, Australia+Fiji, Australia Grand Tour, Polinesia Francese

### Sicurezza / Auth
- **requireAdminAPI** — `authOptions` esportato da `[...nextauth]/route.ts` e passato a `getServerSession` → risolve 401 su prenotazioni e altre route admin
- **gdpr-cancellazione** — sostituito `@/lib/email` inesistente con nodemailer diretto

### Prenotazioni
- **Rinvia conferma** — bottone 📧 Rinvia su ogni prenotazione, apre prompt con email precompilata (modificabile), reinvia conferma a qualsiasi indirizzo
- **ParticipantEmailSelector** — cliente principale incluso nelle checkbox "Invia email a" (non solo partecipanti aggiuntivi)

### Recensioni
- **Stelle gialle** `#F59E0B`
- **Nome** visualizzato come `Nome I.` (iniziale cognome)
- **Homepage** mostra solo `featured=true` via prop `featuredOnly`
- **Contatore** mostra totale reale dal DB (non solo quelle caricate)
- **Media** calcolata su tutte le recensioni approvate
- **API reviews-public** restituisce `{reviews, total, avgRating}` invece di array piatto
- **Health check** aggiornato per nuovo formato API

### Pacchetti / FilterBar
- **Combinati** — aggiunto tipo `combinato` a `TRIP_TYPES`, FilterBar (🔀), PacchettiClient
- **quando-viaggiare** — pacchetti con `tripType=combinato` esclusi dal calendario (BestTimeCalendar)

### HeyGen
- **Sezione impostazioni** aggiornata con info API key, piano Creator, costi
- **API key** `HEYGEN_API_KEY` configurata su Vercel

### Favicon
- Tornato trasparente (PNG puro senza sfondo) — Google inferisce navy da theme-color

---

## Pending da questa sessione

- **WhatsApp** +39 379 363 8232 ancora "In sospeso" su Meta (2-4 settimane)
- **Foto profilo WhatsApp** da caricare quando attivo: `/mnt/user-data/outputs/whatsapp-profile-v3.png`
- **HeyGen video demo chatbot** — script + audio ElevenLabs + integrazione API: DA FARE
- **Digital Twin Massimo** — registrazione video 2 min parlato naturale
- **Canada Est, Giappone, Tanzania** — duplicati blog da eliminare manualmente in `/admin/blog`
- **replace `4go-gamma.vercel.app` → `fourgo.it`** in 4 file (grep `logo-4go`)
- **Fluid Compute** Vercel (Settings → Functions) — non ancora abilitato
- **TOTP QR verification** — pendente
- **Bing Places** `sameAs` in `layout.tsx` dopo verifica
- **Google Ads** account 152-306-0635 → configurare monitoraggio conversioni

---

## Note tecniche

- `requireAdminAPI` usa `getServerSession(authOptions)` — authOptions deve essere esportato da `[...nextauth]/route.ts`
- `reviews-public` API ora restituisce oggetto `{reviews, total, avgRating}` — tutti i consumer aggiornati
- Geocoder: `COORDS_OVERRIDE[name] === null` → città ignorata (non inclusa in geoData)
- Blog-autogen: `publishedAt` settato alla creazione → index-google cron lo trova subito
- index-google cron include: blog (publishedAt null OR recenti), pacchetti, landing città, 11 pagine statiche; max 200/giorno
