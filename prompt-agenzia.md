# 4GO FourTravel — Sessione 4GO-22 (24 Giugno 2026)

## Stato branch
- **main** `4391c2ed` — produzione, stabile, tutti i fix applicati
- **develop** `679f2c82` — Violetta/Vapi/interprete + 17 cherry-pick fix da main + schema allineato

## Review completa con Claude Code
Sessione di review sistematica develop..main su tutto il codebase. Trovati e fixati:

### Bug critici (crash garantiti)
- Stripe webhook: `year` undefined nel path normale → ReferenceError su ogni pagamento
- preventivi/package: TDZ `chosen` prima di `totalAmt` → crash sendPaymentLinks
- preventivi/package: `updateInsurance` senza `data` → PrismaClientValidationError
- cron/gbp-post: `pkg` out of scope → ReferenceError su ogni run
- cron/email-followup: enum invalidi ThreadStatus → 500 su ogni run
- case-study: `firstName` non dichiarato → crash su ogni generazione
- send-documents: `getFirmaAgenzia` usa `botUsername` non in scope
- telegram/webhook: `getAIResponse` con argomenti sbagliati → crash Explorer/Traveller senza ristoranti
- gemini-translate-token: espone GEMINI_API_KEY raw → fix proxy server-side

### Sicurezza
- GBP auto-reply: rispondeva con testo positivo a recensioni 1★ → skip rating ≤3
- GBP business-info: updateMask non encodato → Google poteva cancellare campi
- PayPal capture: plan preso da URL non validato → possibile upgrade non pagato
- VioletaSubscription: no idempotency → duplicati su retry Stripe/PayPal
- blog-autogen: delete-dupes cancellava TUTTI i draft → guard su publishedSlugs
- WA dedup: colonna `value` inesistente → dedup mai funzionato

### Fix funzionali
- Email-ai/list: enum invalidi ThreadStatus → sempre vuoto
- Email-ai/poll: `MASSIMO_WA_NUMBER` sbagliato → WA escalation mai inviata
- WA webhook: `tgProcessedUpdates` → fix colonna
- Cron password-expiry: solo 3 admin hardcoded → tutti gli admin notificati
- Reviews: take:100 → limit=all per consumers
- Brevo-import: unbounded Promise.all → batch 5 + skip 429
- Pacchetti page: originalPrice mancante dal map → badge sconto mai visibile
- ItineraryMap: early return prima degli hooks → Rules of Hooks violation
- Cron/inbox-organizer: seqno invece di UID → email sbagliate archiviate
- Social page: POST_PLATFORMS duplicata → build error

### Schema e DB
- Aggiunti a schema.prisma: `flowContext` (TelegramSession), `interpreterAssistants`, `tgProcessedUpdates` (SiteSettings), `TgProcessedUpdate` model, `EmailThread.repliedAt`
- Migrate endpoint aggiornato con tutte le colonne
- develop schema riconciliato con tutti i modelli raw-SQL

## Video Publishing — Completato su main
- Instagram: polling status_code FINISHED (max 90s)
- Pinterest: 4-step flow S3 + bottone "Pubblica Pin" separato (async, evita timeout 120s)
- TikTok: chunk unico, SEND_TO_USER_INBOX, caption manuale dall'app
- Facebook/Threads: pending Meta business verification
- HeyGen: auto-estrazione MP4 da URL pagina via `v1/video_status.get`
- UI: publishedPlatforms traccia ✅, box giallo social mancanti, TikTok rimosso da Post AI

## Pending aperti
- Secret `4go2026` nel bundle client admin (design task)
- SSRF video-publish allowlist domini (design task)
- Merge develop→main Violetta: dopo test su staging + autorizzazione esplicita
- Ruotare GitHub PAT in origin URL PC Violetta

## Regola assoluta confermata
NESSUN merge develop→main senza:
1. Test su staging (develop) con viaggi reali
2. Review con Claude Code /review develop..main
3. Autorizzazione esplicita di Emi

## ultrareview cost
4 run a ~$49 ciascuna = ~$198 totale. Le run gratuite erano scadute il 5 maggio 2026.
