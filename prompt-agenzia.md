# 4GO FourTravel — Prompt Agenzia (aggiornato 30 Giugno 2026 — 4GO-23)

## Stato branch
- **main** `98be316` — produzione
- **develop** `a8d9b1d5` — Violetta staging (noindex attivo)

## Stack
Next.js 15 + Prisma + Neon PostgreSQL + Vercel Pro  
Repo: `github.com/emilianomarchesi-droid/4-GO`  
Branch: `main` (prod) / `develop` (staging: `staging.fourgo.it`, DB: `ep-sweet-rain`)

## Persone
- **Massimo** (Schilirò) — titolare, autorizza PAGATO, escalation WA/TG
- **Alessia**, **Inga** — operatori, ricevono notifiche TG via `notifyAll()`
- **Emi** — sviluppatore, riceve alert tecnici

## Account chiave
- `MIGRATE_SECRET=4go2026` | Admin: `/admin` (NextAuth + MFA TOTP)
- Vapi: `maxschiliro70@gmail.com` | Assistant: `640e941e` | PAYG ~$0.11/min
- Twilio: `+390250020031` attivo (bypassato Telnyx KYC)
- GitHub token (giugno 2026): `[GITHUB_TOKEN — vedere Vercel env]`
- Staging bot: `@FourGoTraveltestBot` token `[TELEGRAM_BOT_TOKEN staging — vedere Vercel env]`

## Violetta™
- Trademark UIBM n.302026000109033, 15/06/2026, classi 39+42
- Piani: Explorer €29.90 / Traveller €39.90 / Concierge €49.90
- Pagina: `/violetta` (noindex fino a go-live)
- Voice ElevenLabs: `gfKKsLN1k0oYYN9n2dXX`

## Vapi ristorante
- Assistant sempre Flux `686274c0`
- Transcriber: Nova-3 monolingua per lingue supportate, `multi` per le altre
- `voice.speed=0.8` su dashboard Vapi
- `temperature` NON supportato in `assistantOverrides`
- 36 lingue con template completo + step3 colloquiale + step3_no_alternative
- LANG_TO_ISO: mai codice paese (en non GB)
- Lakera: skip durante flusso ristorante attivo
- Check 50km: se indirizzo >50km dalla destinazione → chiede conferma
- `triedRestaurants` tracciato, radius ×2 se lista esaurita

## Blog autogen
- Cron: 6:00-6:45 ogni 5 min + fix-images alle 7:00
- `normalizeImageUrl()`: confronta solo pathname (no query params Pexels/Pixabay/Unsplash)
- Check anti-duplicato coverImage nel POST /api/admin/blog
- COUNTRY_MAP aggrega destinazioni correlate
- Anno rimosso da titoli/slug nuovi e esistenti
- Actions: `fix-images`, `fix-years`, `fix-slugs`, `audit`, `fix-duplicates`
- Redirect 301 automatico slug-con-anno → slug-senza-anno
- Blog paginato: `?page=N` con skip/take Prisma, ~500 articoli navigabili

## AI Act (scadenza 2 agosto 2026) — COMPLETATO
- Disclosure AI: WA/TG/ChatWidget/social caption ✅
- Alfabetizzazione: Massimo/Alessia/Inga confermato 29/06/2026 ✅
- DPIA: completata, firmata, archiviata ✅
- FRIA: esclusione motivata, firmata, archiviata ✅
- admin/gdpr: 4 card tutte ✅

## Performance (lab PSI mobile — 30 Giugno 2026)
| Pagina | Score | LCP | TBT |
|--------|-------|-----|-----|
| Homepage | 92 | 2.1s | 240ms |
| Pacchetti | 89 | 2.7s | 313ms |
| Chi Siamo | 90 | 3.0s | 173ms |
| Contatti | 65 | 11.3s | 98ms |
| Blog | 97 | 2.3s | 148ms |
| Quando Viaggiare | 89 | 2.1s | 398ms |

Fix applicati in 4GO-23 (main):
- `/blog`: `priority` sulla featured image → 65/9.9s → 97/2.3s
- `/quando-viaggiare`: fetch packages server-side RSC, passa `initialPackages` → 57/29.8s → 89/2.1s
- `/contatti`: TBT 503ms→98ms (BgSlideshow già dynamic(), varianza PSI confermata)
- `ssr:false` NON permesso in RSC Next.js 15 — usare solo in Client Component
- Homepage 16.7s LCP era causato da `1cc86c5` (rimozione opacity:0): ora PSI misura hero image (Pexels via /_next/image) invece di h1. Risolto spontaneamente (92/2.1s) — monitorare field data CrUX.

## Cron attivi (tutti su fourgo.it)
- preventivi-poll (*/5), blog-autogen (4×/day 06:00–06:15 + fix-images 07:00)
- blog-check (1° del mese 8:00), password-expiry (09:00), appointment-reminders (ogni min)
- inbox-organizer (Lun 7:30 — su cron-job.org E Vercel Cron, maxDuration=60)
- gbp-post (daily)
- **Regola**: ogni cron va registrato SIA su cron-job.org SIA in vercel.json

## Regole assolute
1. Leggere file PRIMA di modificare — mai commit speculativi
2. webhook/route.ts, middleware, auth, pagamenti, schema DB → sempre su develop
3. Merge develop→main solo dopo staging + autorizzazione esplicita Emi
4. Nuove colonne DB nel migrate endpoint come `ALTER TABLE IF NOT EXISTS`
5. Prisma schema-external columns → `$queryRawUnsafe` / `$executeRawUnsafe`
6. Branch main: solo fix urgenti produzione — tutto il resto su develop

## Prossimi step
1. SIAE raccomandata A/R
2. Violetta go-live (post-SIAE): rimuovi noindex → GSC → Product Hunt / There's An AI For That / Futurepedia / BotList
3. Pre go-live Violetta: Playwright E2E su develop (form preventivo, quiz, checkout, blog, admin login)
4. Audit endpoint schedulati: multipreventivi, Fly&Drive, locations, social AI, WA/email AI, cortesia rientro, recensioni post-viaggio
5. TikTok: attendere review
6. 40 città service areas GBP
7. Contatti LCP 11.3s: monitorare field data prima di intervenire

## Sessione 4GO-22 (30 Giugno 2026 — mattina)
blog: normalizeImageUrl dedup foto duplicate, paginazione ~500 articoli, fix BgSlideshow isDark (chi-siamo 95, quando-viaggiare inizialmente non risolto), inbox-organizer schedulato correttamente (maxDuration+Vercel Cron), delete-all email protetto con conferma "ELIMINA"+ActivityLog, Playwright E2E su develop (form preventivo, quiz, blog, admin), bug reali trovati (shortDescription in /api/preventivo, heygen-weekly schema mancante), Actions minutes 90% consumati → workflows limitati.

## Sessione 4GO-23 (30 Giugno 2026 — sera)
Performance PSI mobile analizzata dopo nuovi score. Fix su main:
- `/blog` featured image `priority` (97/2.3s)
- `/quando-viaggiare` server-side fetch packages → `initialPackages` prop (89/2.1s)
- Contatti TBT varianza PSI, non strutturale
- Homepage 92/2.1s senza intervento (monitorare)
