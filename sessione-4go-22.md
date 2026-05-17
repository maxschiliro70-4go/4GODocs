# Sessione 4GO-22 — 17 maggio 2026

## Commit range: `2440577` → `910f59f`

---

## Video Violetta (HeyGen)

- **Lip sync** risolto con `use_avatar_iv_model: true` (Avatar IV)
- **Screenshot Browserless** come background: fix `waitFor` → `gotoOptions.waitUntil: 'networkidle2'`, viewport 1080×1920, env var `BROWSERLESS_API_KEY`
- **Script** con `for go four travel` per TTS corretto
- **Voice** HeyGen Violetta: `bf04704b87d94e4cb4f2d8f27d8c6e3a` ✅
- **Video finale** generato manualmente in HeyGen web editor (Build a video scene by scene) con scena multi-clip + voce Violetta + Avatar IV
- Video caricato su Vercel Blob: `https://b7abijpmgnu3dkml.public.blob.vercel-storage.com/heygen/02e97caf06194c4dacbdb9f6acec1c8a.mp4`
- **HeyGenVideoGenerator** rimosso da `/admin/impostazioni` (video generato manualmente)

## Demo-bot

- **Video Violetta** integrato in `/demo-bot` con `<video>` tag nativo (no iframe)
- **Layout responsive**: video sopra su mobile, affiancato 50/50 su desktop (≥900px)
- **Pausa reciproca**: video in play → pulsante invio disabilitato; messaggio inviato → video in pausa automatica
- **CSP `media-src`** aggiornata per `*.public.blob.vercel-storage.com`
- **TODO futuro**: rimuovere `noindex,nofollow` da `/demo-bot/layout.tsx` quando il prodotto Violetta è commercializzato

## SEO / CTR (da GSC 2026-05-17)

### Blog ottimizzati (seoTitle + seoDescription)
12 post aggiornati via migration `20260517000001_seo_blog_meta_update`:
- vaccini-egitto, giappone-tour-guidato, route-66-noleggio-auto, spagna-quanto-costa
- crociera-costa-crociere, visto-belgio, bali-viaggio-nozze, seychelles-sicuro
- dublino-citta-letteratura, cina-giappone-corea-faq, australia-quanto-costa, vaccini-marocco

### Landing ottimizzate
- `/tour-operator-milano-nord` — title focus "17km Milano + 24h"
- `/agenzia-viaggi/garbagnate-milanese` — "5 minuti + destinazioni specifiche"
- `/preventivo` — "Preventivo Viaggio Gratuito in 24h | 4GO FourTravel Senago Milano"
- `/come-funziona` — "Preventivo Gratis, Consulente Dedicato"

### aggregateRating JSON-LD (4.9 / 47 recensioni)
Aggiunto a tutte le landing:
- `/agenzia-viaggi/[citta]` ✅
- `/tour-operator-milano-nord` ✅
- `/fly-and-drive` ✅
- `/pacchetti-viaggio-su-misura` ✅
- `/` (homepage) già dinamico da DB ✅

Campo `image: logo-4go.png` aggiunto al TravelAgency JSON-LD (warning Rich Results risolto).

### Rich Results Test
`fourgo.it/agenzia-viaggi/garbagnate-milanese` → **5 valid items** ✅
- Local businesses (2) + Organization (2) + Review snippets (1)

## Bug critici risolti

### robots.txt
`/_next/static/` era in Disallow → Googlebot non poteva eseguire JS/CSS → rimosso.
Anche `/pacchetti-viaggio-su-misura` rimosso da Disallow (era residuo del coming soon).
File: `src/app/robots.ts`

### blog-autogen
Vaccini esclusi per destinazioni europee (non solo italiane). Lista europea ampliata con: Inghilterra, UK, Irlanda, Scozia, Polonia, Praga, Budapest, Ungheria, Romania, Bulgaria, ecc.

## Stato noindex
- `/grazie` → noindex ✅ corretto
- `/prenotazione/[code]` → noindex ✅ corretto
- `/demo-bot` → noindex,nofollow — **da rimuovere** quando Violetta è commercializzato

## Pending
- FIAVET / Confcommercio registrazione
- WhatsApp Business SIM
- Meta Business verifica
- Telnyx KYC (2-5gg) → acquisto numero → test Vapi
- Fluid Compute Vercel (Settings → Functions)
- `REDIRECT_VERCEL_TO_FOURGO=true` su Vercel
- Gamma logo 4 file: sostituire `4go-gamma.vercel.app` → `fourgo.it`
- Cron-job.org: aggiungere inbox-organizer (lun 7:30) e gbp-post (lun 8:00)
- Demo-bot `noindex` da rimuovere quando Violetta è vendibile
