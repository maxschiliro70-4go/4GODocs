# Sessione 23 — 26 Maggio 2026

## Bot Telegram / Violetta
- GO→Violetta in tutti i file: webhook, ai/chat, ChatWidget, restore-sessions
- sanitizeAIReply: rimuove grassetto, titoli, elenchi da tutte le risposte AI
- Audioguide iconiche (Auschwitz, Torre Eiffel, Colosseo ecc.): 800-900 parole Sonnet ~6min
- Pre-messaggio emotivo 50-80 parole per luoghi iconici prima del tasto Violetta
- nearbySearch: rimosso rankby=prominence, radius aumentato a 1000m
- Navigazione: risposta con piedi/auto/mezzi + link Google Maps
- Domande info (ingresso/prezzo/orari): non triggera più audioguida
- Strafalcioni vocali: usa contesto destinazione per interpretare nomi errati
- Callback loop fix: answerCallback immediato su photo_audio
- ChatWidget: voce ElevenLabs invece di SpeechSynthesis, "four go" per TTS

## Blog
- blog-autogen: 10 post/giorno in 4 run (06:00, 06:05, 06:10, 06:15), 3 per run
- Countdown giornaliero via blogCronMax nel DB
- blog-expand: ~290 post espansi a 2000 parole con Sonnet — completato
- GSC report: filtra query sistema con -site: operator

## ElevenLabs
- Violetta voice ID: gfKKsLN1k0oYYN9n2dXX
- Polacco aggiunto alla lista voci: 74Aolxyv6bUq7ZAIooY5
- Endpoint /api/admin/elevenlabs-voices creato

## SEO
- site:fourgo.it non indicizzato (sito ha 1 mese, normale)
- GBP verificato, Pagine Gialle/Virgilio attivi
- Redirect agenziaviaggi-fourtravel.it non possibile (Castellino)
- Mail inviata a Castellino per link in footer

## Meta App Review
- pages_manage_posts: chiamata API eseguita via Graph API Explorer
- manage_app_solution e threads_basic rimossi dalla submission
- Da fare: verificare "Completata" e inviare submission

## Admin
- Bottone Esegui visibile per tutti i cron (inclusi remoti)
- seo-destinations endpoint per analisi pacchetti senza blog

## Infrastruttura
- WhatsApp pricing update Meta: nessun impatto su uso attuale
- Claude Code /ultrareview da usare per implementazione Telnyx+Vapi pre-merge

## Pending
- Meta App Review submission
- Telnyx KYC approvazione → Vapi trilaterale
- Cron social-post su cron-job.org
- Cron heygen-weekly su cron-job.org (giovedì 09:00)
- GBP number aggiornamento
- GA4 stream aggiornamento
- noindex rimozione /demo-bot e /violetta
- Gamma logo replace in 4 file
