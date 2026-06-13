# SESSIONE 4GO-30 — 13 Giugno 2026

## LAVORO COMPLETATO

### Crociere
- Importazione PDF MSC: fix `volo_incluso` hardcodato false → estratto dall'AI
- Campo `voli` strutturato: [{tipo, numero, compagnia, da, a, data_partenza, ora_partenza, data_arrivo, ora_arrivo, classe, status}]
- UI dettaglio voli nella card admin con logo compagnia (Brandfetch CDN)
- Pagina cliente `/preventivo-crociera/[id]`: aggiunge assicurazione/transfer nella griglia, sezione Dettaglio Voli con NEOS AIR
- Migrate: colonne `voli` JSONB su `CruiseQuote`

### Logo Partner Multi-Preventivo
- Loghi da `/public/partners/` locali (turisanda.svg, francorosso.svg, bravoclub.png, alpitour.png, veratour.svg, going.png, costa.svg, msc.svg)
- `getPartnerLogo()` in `multi-preventivo/page.tsx` e `getPartnerLogoSV()` in `selezione-viaggio/page.tsx`
- Brandfetch per airline logos (NEOS AIR = neosair.it) con fallback firstWord.com
- Bravo Villaggi: logo aggiornato con sfondo trasparente (numpy PIL), height 55px per Bravo vs 40px per gli altri
- Matitina inline per modificare nome hotel nel multi-preventivo (salva via PATCH /api/admin/multi-proposal)
- Fix closure `i not defined`: il .map destinations ora dichiara `(d: any, i: number)`

### Prenotazioni
- Tab **Attive / Archiviate** (filtro su `bookingStatus === 'ARCHIVIATA'`)
- Filtro brand **4GO FourTravel / 4Travel** (campo `bookingType`)
- Filtro codice **4GO / PRV** (prefix bookingCode)
- Campi **Diritti agenzia** (`agencyFee`) e **50/50** (`fiftyFifty`) nel form
- Stats: Revenue lordo / Revenue netto / 50/50 totale (calcolati sui filtered)
- API `/api/admin/manual-bookings`: aggiunge agencyFee, fiftyFifty, bookingType nel SELECT e PATCH
- Migrate: `ALTER TABLE "ManualBooking" ADD COLUMN IF NOT EXISTS "agencyFee"/"fiftyFifty" DECIMAL(10,2)`
- Bottone "✓ Confermato" nascosto in multi-hotel quando stato è già CONFIRMED/CONVERTED/PAID

### Fix Telegram
- `itinerary is not defined` riga 3672: dichiarata `itineraryForAudio` locale
- Strip email dal nome passeggero: `replace(/\s*<[^>]*>?$/, '').replace(/@.*$/, '')`
- Cleanup DB `participantName` con REGEXP_REPLACE via migrate

### Dashboard
- Blog count: usa `?limit=all` invece di default 20; gestisce risposta `{ posts, total }`
- Email AI: usa `badges.emailAi` da `/api/admin/badges` invece di `stats.emailAi`

### GBP
- Fix path location completo `accounts/xxx/locations/xxx` nel callback OAuth
- Fix immagini `.webp` non supportate: esclude webp, usa Pexels come fallback
- Fix secondo punto costruzione post con webp
- Post pubblicato con successo: Marsa Matrouh con foto Pexels

### Blog Foto Duplicate (342/455 → 0/488)
- Root cause: Pexels pool esaurito per destinazioni frequenti, usedImages funzionava ma trovava solo foto già usate
- Fix strutturale: pagina Pexels random 1-12 + varianti query con tipo articolo e titolo
- Cascata: Pexels (3 varianti × 4 pagine random) → Unsplash API → Pixabay API
- `blog-reimage`: stesso fallback cascade, ran 3 volte per azzerare tutti i duplicati
- Notifica Telegram a Emi se >20 duplicati dopo ogni run autogen
- **Pixabay** aggiunto: `PIXABAY_API_KEY` su Vercel, visibile in admin/sistema e admin/impostazioni
- Strategia immagini aggiornata in admin/impostazioni

### Settings
- `browserlessApiKey` aggiunto alla lista `fields` del POST e masked nel GET

### SEO
- City page Senago: title e description ottimizzati per CTR (pos.1 con 0 clic)
- Redirect 301: `istanbul-capaddocia` → `istanbul-cappadocia`

### Email Escalation
- Notifica WhatsApp a Massimo per email escalate (OPERATOR_MASSIMO_WHATSAPP configurato)

### Varie
- Blog-settings: corretto da "4 sessioni" a "10 sessioni/giorno 06:00-06:45 UTC"
- GBP report lag: aggiornato da "3-4 giorni" a "7-10 giorni"
- Multi-hotel bottone Confermato: nascosto se stato già CONFIRMED/CONVERTED/PAID
- Migrate: cleanup `participantName` sporchi con email

## PENDENTE

- [ ] MIGRATE produzione: `https://fourgo.it/api/admin/migrate?secret=4go2026` (agencyFee, fiftyFifty, voli crociere, cleanup participantName)
- [ ] Twilio: bundle `[TWILIO_BUNDLE_SID — vedi Vercel env]` approvato — attendere assegnazione numero (form inviato)
- [ ] Telnyx: incident attivo dal 9 giugno — monitorare statuspage.incident.io/telnyx
- [ ] UIBM: registrazione marchio "Violetta" come assistente di viaggio (classi 39 + 42)
- [ ] EUIPO Voucher: rimborso 75% tasse deposito marchio UE
- [ ] RateHawk/ETG: mail inviata a start@ratehawk.com con Anna in CC
- [ ] Meta Business verification: WhatsApp blocked
- [ ] Pixabay: aggiungere `PIXABAY_API_KEY` su Vercel (già fatto dalla sessione)
- [ ] blog-reimage 11 rimasti: lanciare domani mattina con rate limit azzerati

## DATI TECNICI

- GitHub token: `[GITHUB_TOKEN — vedi Vercel env]`
- MIGRATE_SECRET: `4go2026`
- EMAIL_AI_SECRET: `4go-ai-2026`
- Twilio Account SID: `[TWILIO_ACCOUNT_SID — vedi Vercel env]`
- Twilio Bundle SID: `[TWILIO_BUNDLE_SID — vedi Vercel env]` (APPROVED)
- Telnyx Requirement Group: `93cf51b4` (incident attivo)
- GBP seed: `https://fourgo.it/api/admin/gbp-seed-recent?secret=4go2026`
