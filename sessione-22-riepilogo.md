# Sessione 22 — 25 Maggio 2026

## Bot Telegram — Fix e miglioramenti
- `sanitizeAIReply` — rimuove markdown (grassetto, titoli, elenchi) da tutte le risposte AI
- Violetta — tono naturale, no risposte da chatbot generico ("non sono una telecamera")
- Location detection — richiede intento esplicito (trova/cerca/vicino), no falsi positivi su "visitate"
- NearbySearch — rimosso rankby=prominence incompatibile con radius, aumentato radius a 1000m
- Navigazione — risposta con piedi/auto/mezzi + link Google Maps
- Strafalcioni vocali — usa contesto destinazione per interpretare nomi errati ("Piazza Adam" → "Piazza Dam")
- Audioguide iconiche (Auschwitz, Torre Eiffel, Colosseo ecc.) — 800-900 parole con Sonnet (~6 min) vs 300 parole Haiku per luoghi normali
- Pre-messaggio emotivo (50-80 parole) per luoghi iconici prima del tasto Violetta
- Fix build — ripristinata function sanitizeAIReply mancante dopo refactor

## Blog
- blog-expand completato — ~290 post espansi a 1800-2000 parole con Sonnet
- Fix foto duplicate — blog-autogen esclude ora URL gamma.app
- Fix renderContent — salta tag immagine markdown nel content

## Admin
- Bottone Esegui visibile anche per cron remoti (GSC report)
- seo-destinations endpoint per analisi pacchetti senza blog

## Meta App Review
- Chiamata API pages_manage_posts completata via Graph API Explorer
- Rimossi manage_app_solution e threads_basic dalla submission
- Da fare domani: verificare "Completata" e inviare submission

## Telnyx KYC
- Documenti aggiornati (ID Francesco Schilirò + Visura Camerale recente)
- Messaggio inviato a Mia per revisione

## Git
- develop allineato a main con force push (storia incompatibile)

## Pending
- Meta App Review — inviare submission domani
- Cron social-post su cron-job.org (ogni 2 giorni 13:00)
- Cron heygen-weekly su cron-job.org (giovedì 09:00)
- Telnyx KYC approvazione
- GBP number aggiornamento
- GA4 stream aggiornamento
- noindex rimozione /demo-bot e /violetta
- Gamma logo replace in 4 file
