# Sessione 20 — 23 Maggio 2026

## WhatsApp Cloud API ✅
- Numero +39 379 363 8232 attivato su Meta Cloud API
- Webhook `/api/whatsapp/webhook` funzionante
- AI Violetta (Haiku) con read receipt + typing indicator
- Escalation: Telegram tutti + WhatsApp a Massimo
- Numero aggiornato in 29 file (366→379)
- Firme email: Massimo 366, Alessia+Inga 379
- Env: WHATSAPP_TOKEN, WHATSAPP_PHONE_ID, WA_SYSTEM_TOKEN, MASSIMO_WA_NUMBER

## Meta App Review ⏳
- App pubblicata live, business verificato
- 14 permessi in submission
- Chiamate API di prova da verificare domani → inviare submission
- Instagram posting funzionante ✅ / Facebook attende approvazione

## Social Post Automatico ✅
- Cron `social-post` ogni 2 giorni alle 13:00 → foto + testo AI → Instagram
- **Da aggiungere su cron-job.org**: https://fourgo.it/api/cron/social-post?secret=4go2026

## Video Social — Flusso Manuale HeyGen ✅
- Ogni giovedì cron genera prompt AI + invia su Telegram a Emi
- Massimo genera video su HeyGen web (Vesperi + Violetta) → scarica → carica su Blob
- Endpoint pubblicazione: https://fourgo.it/api/admin/social-video?secret=4go2026&url=BLOB_URL&dest=DESTINAZIONE
- **Da aggiungere su cron-job.org**: https://fourgo.it/api/cron/heygen-weekly?secret=4go2026 ogni giovedì 09:00
- Vesperi: talking_photo ID ace6714938c84cc789d50ff999686fd1 ✅ confermato
- Voce Violetta: bf04704b87d94e4cb4f2d8f27d8c6e3a

## Bot Telegram — Fix ✅
- Identità: GO → Violetta, genere maschile → femminile (righe 812, 2055)
- Deduplicazione update_id: previene retry Telegram su audioguide lente (cache 100 update_id in siteSettings)
- Audioguide: tutti i system prompt con "ZERO titoli (#), ZERO elenchi, ZERO markdown"
- Rilevamento correzione posizione: se cliente scrive "siamo a X" dopo audioguida → offre tasto per rigenerare
- Callback `audio_place:LUOGO` e `dismiss` aggiunti
- Lista partecipanti senza numeri (evita risposta "2")
- Nome partecipante: rimuove parte email `<email>` da clientName prima di salvare in DB
- WhatsApp AI: aggiunto "MAI calchi inglese (mi dimmi→dimmi)"

## Admin/Social ✅
- Crash fix: `messages = JSON.parse(context)` → `Array.isArray(p) ? p : []`
- Migrazione DB: tabella `WaConversation` creata

## Endpoint temporanei creati
- `/api/admin/heygen-test-avatar` — test avatar ID
- `/api/admin/heygen-video-detail` — lista avatar groups
- `/api/admin/creatomate-status` — status render (Creatomate abbandonato, $45/mese)
- `/api/admin/social-video` — pubblica video da Blob URL
- `/api/creatomate/webhook` — webhook Creatomate (non usato)

## Env aggiunte su Vercel Production
- WHATSAPP_TOKEN, WHATSAPP_PHONE_ID, WA_SYSTEM_TOKEN, MASSIMO_WA_NUMBER
- FB_PAGE_TOKEN, FB_PAGE_ACCESS_TOKEN, META_PAGE_ACCESS_TOKEN
- META_PAGE_ID=1078312105357113, INSTAGRAM_BUSINESS_ID=17841443227987195
- NEXT_PUBLIC_WA_NUMBER=393793638232, NEXT_PUBLIC_WA_DISPLAY=+39 379 363 8232
- CREATOMATE_API_KEY (account Massimo, non usato)

## Pending
- [ ] Verificare chiamate API su Meta App Review domani → inviare submission
- [ ] Aggiungere cron social-post su cron-job.org (ogni 2 giorni 13:00)
- [ ] Aggiungere cron heygen-weekly su cron-job.org (giovedì 09:00)
- [ ] GBP number aggiornamento (+39 379 363 8232)
- [ ] GA4 stream aggiornamento
- [ ] Telnyx KYC → Vapi trilaterale
- [ ] noindex rimozione /demo-bot e /violetta
- [ ] Gamma logo replace in 4 file

## Commit principali
- be70b754 WhatsApp AI no calchi inglese
- 08973b39 bot Telegram GO→Violetta
- 62740f11 deduplicazione update_id
- 3b87749c audioguide no markdown + correzione posizione
- e5774469 admin/social messages fix
- fcfc5550 sanitizza email da participantName
- 46ab54f0 lista partecipanti senza numeri
