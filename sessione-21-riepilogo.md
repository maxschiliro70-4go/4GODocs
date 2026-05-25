# Sessione 21 — 24 Maggio 2026

## SEO / Blog
- Blog Alpiclub Jacaranda Tenerife pubblicato (1.925 parole, slug: alpiclub-jacaranda-tenerife-recensione-costa-adeje)
- Blog Tour Giappone 12 giorni pubblicato (1.931 parole, slug: tour-giappone-tokyo-kyoto-hiroshima-12-giorni)
- blog-autogen: target aggiornato da 600-800 → 1800-2200 parole, max_tokens 3000 → 6000
- blog-expand: endpoint che espande post esistenti a 2000 parole con Sonnet
  - Migrazione DB: campo `expandedAt` su tabella Post
  - Cron Vercel ogni 5 minuti, 3 post per run
  - ~290 post espansi, cron rimosso al completamento
  - Notifica Telegram ogni 50 post + crediti esauriti
- endpoint seo-destinations: analisi pacchetti senza blog per opportunità SEO

## Bot Telegram — Fix
- Violetta: tono naturale, no risposte da chatbot generico ("non sono una telecamera")
- Location detection: richiede intento esplicito (trova/cerca/vicino) — no falsi positivi su "visitate"
- Lista partecipanti: rimossi numeri (evita risposta "2"), sanitizzata email da clientName

## Meta App Review
- Rimosso `manage_app_solution` e `threads_basic` dalla submission
- Rimanenti: `pages_manage_posts` — attende conferma chiamate API (24h)
- Da fare domani: verificare chiamate e inviare submission

## Infrastruttura
- Notifica Telegram su crediti Anthropic esauriti (blog-expand)

## Pending
- Meta App Review submission (domani)
- Cron social-post su cron-job.org (ogni 2 giorni 13:00)
- Cron heygen-weekly su cron-job.org (giovedì 09:00)
- GBP number aggiornamento (+39 379 363 8232)
- GA4 stream aggiornamento
- Telnyx KYC → Vapi trilaterale
- noindex rimozione /demo-bot e /violetta
- Gamma logo replace in 4 file
