# 4GO FourTravel — Prompt Agenzia (aggiornato 29 Giugno 2026)

## Stato branch
- **main** `0d8ad2df` — produzione
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

## AI Act (scadenza 2 agosto 2026) — COMPLETATO
- Disclosure AI: WA/TG/ChatWidget/social caption ✅
- Alfabetizzazione: Massimo/Alessia/Inga confermato 29/06/2026 ✅
- DPIA: completata, da firmare ✅
- FRIA: esclusione motivata (rischio limitato), da firmare ✅
- admin/gdpr: 4 card tutte ✅

## Regole assolute
1. Leggere file PRIMA di modificare — mai commit speculativi
2. webhook/route.ts, middleware, auth, pagamenti, schema DB → sempre su develop
3. Merge develop→main solo dopo staging + autorizzazione esplicita Emi
4. Nuove colonne DB nel migrate endpoint come `ALTER TABLE IF NOT EXISTS`
5. Prisma schema-external columns → `$queryRawUnsafe` / `$executeRawUnsafe`

## Prossimi step
1. SIAE raccomandata A/R
2. Violetta go-live (post-SIAE): rimuovi noindex → GSC → Product Hunt / There's An AI For That / Futurepedia / BotList
3. DPIA/FRIA: ✅ firmate 29/06, archiviate in admin/gdpr (download diretto PDF)
4. TikTok: attendere review

## Sessione 30 Giugno 2026 — riepilogo (vedi docs/sessione-4go-30-giugno-2026.md per dettaglio)
Mergiati su main: dedup WhatsApp atomico (race condition risolta), fix allucinazione "Claudio operatore" (bot riusava nome cliente come operatore inventato), mappa Duffel ampliata ~30 destinazioni mancanti, escalation WhatsApp completata su 3/3 percorsi, bug Prisma critico /api/preventivo risolto, cron inbox-organizer finalmente schedulato, 9 vulnerabilità sicurezza risolte (Next 15.5.18, CVE-2026-45109 middleware bypass), DPIA+FRIA+Registro Trattamento firmati archiviati, feature multi-tappa ManualBooking.itineraryCities.
Restano su develop: tutto il flusso pubblico /violetta (inutile su main finché noindex attivo), E2E Playwright + CI settimanale, fix violetta-sub (FormFields React bug, PATCH crash pricePaid, auth ridondante rimossa), check coerenza piano violetta-sub↔ManualBooking.
