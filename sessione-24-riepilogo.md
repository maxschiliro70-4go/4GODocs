# Sessione 24 — 30-31 Maggio 2026

## Security fixes (Claude Code review admin)
- Middleware auth gate per /api/admin/* (sessione + secret) ✅
- GitHub PAT hardcoded rimosso da infra-status ✅
- db-query endpoint rimosso (SQL injection) ✅
- Newsletter HTML sanitization ✅
- Preventivo confirm token HMAC-SHA256 ✅
- OAuth CSRF state (GBP, Pinterest, TikTok) ✅
- Auth gate: analytics, newsletter-drafts, send-test-logo ✅
- .env.example: credenziali Neon reali sostituite con placeholder ✅

## Bug fix
- Admin UI: loadPkgs, loadContacts, bulk-parse shape ✅
- Calendario CURRENT_USER da sessione NextAuth (non hardcoded massimo) ✅
- badges: waAvgMessages calcolo reale ✅
- Webhook: callback auth gate (op:/em:) ✅
- Webhook: hasDateRef regex backslash fix ✅

## Refactor
- GRAD constant centralizzata in src/lib/constants.ts ✅
- Geo tables consolidate in src/lib/geoData.ts ✅
- Booking codes in src/lib/codes.ts ✅
- PaymentLinks component estratto ✅
- Pinterest shared in src/lib/pinterest.ts ✅
- Amsterdam dupe rimosso ✅

## Performance
- Brave Search Promise.race 2s cap (no blocking latency) ✅
- Paginazione endpoint admin (blog/inquiries/preventivi/packages/reviews/email-stats) ✅
- Dead code webhook rimosso (~102 righe) ✅

## Nit
- FID → INP in vitals ✅
- preventivi split() fix ✅
- blocklist extract in multi-preventivo ✅

## Blog
- Post "A Marrakech serve il passaporto?" creato con contenuto specifico ✅
- Endpoint create-blog-post per generare + espandere singolo post ✅
- blog-expand accetta slug singolo ✅

## Telnyx
- Escalation finale inviata (quasi 1 mese, 2 giorni senza risposta)

## Pending
- Telnyx KYC approvazione → Vapi trilaterale
- Meta App Review submission
- GBP number aggiornamento (+39 379 363 8232)
- noindex rimozione /demo-bot e /violetta
- Gamma logo replace in 4 file
- Claude Code run 2 e 3 riservate per Telnyx+Vapi

## Aggiornamenti 01 Giugno 2026

### Security
- Middleware auth gate /api/admin/* ✅
- GitHub PAT rimosso da infra-status ✅
- db-query rimosso ✅
- Newsletter HTML sanitization ✅
- Preventivo confirm HMAC ✅
- OAuth CSRF state (GBP, Pinterest, TikTok) ✅
- Auth gate analytics, newsletter-drafts, send-test-logo ✅
- .env.example credenziali Neon sostituite ✅

### Blog/SEO
- create-blog-post endpoint (genera + espande) ✅
- blog-expand accetta slug singolo ✅
- seoTitle aggiornati: Tenerife Jacaranda, vaccini Egitto/Marocco, crociere Costa, Grecia sicuro ✅
- Post Marrakech passaporto creato e aggiornato ✅
- GSC report: aggiunge 7gg vs 7gg precedenti ✅
- GSC report cron su Vercel alle 08:00 ✅

### Social
- Pinterest OAuth + pubblicazione ✅ (board Viaggi 1123014925772463181)
- Cron social-post: Instagram + Pinterest ogni lunedì 09:00 ✅
- social-post notifica Emi su Telegram dopo pubblicazione ✅
- TikTok appeal inviato (rejected per internal use)

### WhatsApp
- Limite 30 messaggi cliente: avviso a 25, stop a 30 ✅
- A 30 → stato ESCALATED + riassunto AI su Telegram a tutti ✅
- Contact form: risposta AI da info CC massimo + TG + WA ✅

### Email AI
- Estrae email cliente dal corpo (GulliverLAB e portali B2B) ✅
- CC sempre massimo@fourgo.it ✅
- Notifica Telegram tutti + WhatsApp Massimo dopo risposta AI ✅
- Escalation automatica su dettagli concreti (budget/date/persone) ✅
- Cron email-followup Lun/Mer/Ven 10:00 per potential/escalated ✅
- email-reply: fix sentAt non esistente ✅

### Admin
- 3 bug UI fix (loadPkgs, loadContacts, bulk-parse) ✅
- Calendario CURRENT_USER da sessione ✅
- Blog admin limit=all per conteggio reale ✅
- email-ai include_messages=1 per corpo email ✅
- Richieste admin: mostra risposta AI per verifica allucinazioni ✅

### Telnyx
- Escalation finale inviata, risposta "actively working" 31 maggio
- Follow-up inviato 01 giugno
