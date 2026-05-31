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
