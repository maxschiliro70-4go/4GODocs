# 4GO FourTravel — Sessione 4GO-22 (24 Giugno 2026)

## Stato branch
- **main** `fea79ec1` — produzione stabile, tutti i fix applicati
- **develop** `e02216cb` — Violetta/Vapi/interprete + 17 cherry-pick fix + schema + fix intolleranze

## Review completa codebase con Claude Code
Bug critici trovati e fixati su main, poi cherry-pick su develop (17 commit).

### Bloccanti fixati
- Stripe: year undefined → ReferenceError su ogni pagamento
- preventivi/package: TDZ chosen + updateInsurance senza data
- cron/gbp-post + email-followup: crash garantiti su ogni run
- case-study: firstName non dichiarato
- send-documents: botUsername out of scope
- telegram/webhook: getAIResponse args sbagliati
- gemini-translate-token: API key esposta raw

### Sicurezza
- GBP auto-reply positiva su recensioni negative → skip rating ≤3
- PayPal plan da URL non validato → fix price check
- WA dedup colonna sbagliata → fix

### Schema e migrate
- flowContext, interpreterAssistants, tgProcessedUpdates, repliedAt, retryCount, nextRetryAt aggiunti al migrate endpoint e schema Prisma

## Video Publishing (main)
Instagram ✅ Pinterest ✅ TikTok ✅ (inbox) HeyGen auto-extract ✅

## Violetta develop
- Lakera: indirizzi stradali in whitelist
- botUsername dinamico staging/prod
- Intolleranze: tradotte con Haiku su TG, aggiunte al firstMessage Vapi

## Regola assoluta
NESSUN merge develop→main senza test staging + review Claude Code + autorizzazione Emi

## Pending
- Secret 4go2026 nel bundle client (design task)
- SSRF video-publish (design task)  
- Merge Violetta: dopo test staging completati
- Rotare PAT GitHub PC Violetta
