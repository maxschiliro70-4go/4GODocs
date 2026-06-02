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

## Roadmap — Violetta Traduttore Live

**Feature:** Traduzione in tempo reale via WebRTC — nessuna telefonata necessaria.

**Flusso:**
1. Cliente apre widget su fourgo.it/violetta o Telegram Web
2. Preme "🎤 Avvia traduzione" — microfono attivo
3. Parla in italiano tenendo il telefono in mezzo
4. Groq Whisper trascrive → Claude traduce con contesto sessione → ElevenLabs riproduce in lingua locale
5. Locale risponde → traduzione inversa in italiano nell'auricolare

**Violetta conosce già:** destinazione, hotel, preferenze alimentari, itinerario — traduce con contesto, non solo parole.

**Stack:** WebRTC (browser) + Groq Whisper streaming + Claude Haiku + ElevenLabs TTS

**Lingue prioritarie:** Giapponese, Coreano, Russo, Arabo, Cinese, Francese, Spagnolo, Portoghese

**Piano:** Concierge €49.90 (differenziatore unico sul mercato italiano)

**Sviluppo:** dopo Telnyx KYC, su branch develop, Claude Code review obbligatoria prima del merge

## Strumenti da valutare/integrare

### LangSmith (Langchain) — PROSSIMA SESSIONE
- Traccia ogni chiamata AI (Telegram, WhatsApp, Email, Blog, Preventivi)
- Dashboard per identificare allucinazioni sistematiche
- Mostra prompt + risposta + latenza per ogni interazione
- Piano gratuito: 5.000 trace/mese
- **Integrazione:** webhook Telegram come primo test
- **Non richiede Telnyx/Vapi** — funziona subito

### Bland.ai
- Testing automatico voice AI — simula centinaia di chiamate
- Trova edge case e allucinazioni prima del lancio live
- **Quando:** pre-lancio Vapi/Telnyx

### Hamming.ai
- Testing automatico voice agents
- Complementare a Bland.ai per edge case
- **Quando:** pre-lancio Vapi/Telnyx

### Deepgram
- STT alternativo a Groq Whisper
- Più accurato su giapponese e coreano
- **Quando:** sviluppo Violetta Traduttore Live

### Speechmatics
- STT specializzato lingue europee meno comuni
- **Quando:** sviluppo Violetta Traduttore Live

### Resemble AI
- Clonazione voce + dizionario pronuncia personalizzato
- Alternativa ElevenLabs con più controllo fonetica
- **Quando:** ottimizzazione pronuncia pre-lancio voice

### Braintrust
- Monitoring LLM leggero, alternativa LangSmith
- **Quando:** valutare se LangSmith non soddisfa

## Aggiornamenti 02 Giugno 2026

### LangSmith
- Tracing attivo su Telegram, WhatsApp, Email AI
- Input utente visibile, tag production/staging, alert allucinazioni su Telegram Emi
- Contatore chiamate/token 30gg in admin/sistema

### Integrazioni Violetta Telegram (6 layer paralleli)
- **Perplexity sonar-pro + citations** — visti, vaccini, sicurezza, gastronomia
- **Frankfurter** — tassi di cambio live (30+ valute)
- **MAE viaggiaresicuri.it** — allerte sicurezza via Brave Search
- **Google Places Details** — orari, telefono, link Maps luoghi specifici
- **Rome2Rio** — trasporti multimodali (durate, no prezzi)
- Farnesina API — email inviata a unita.crisi@esteri.it, aspettiamo risposta

### Integrazioni Violetta WhatsApp (pre-vendita)
- **Perplexity sonar-pro** — domande fattuali + gastronomia
- **Duffel** — voli live con prezzi reali, trigger solo entro 10 messaggi
- Duffel in modalità TEST — produzione in approvazione (1-3 giorni)

### WhatsApp
- Limite 30 messaggi: avviso a 25 con data/ora esatta reset
- Reset automatico dopo 48 ore di inattività
- Escalation con riassunto AI + contatti su TG e WA Massimo

### Admin
- admin/sistema: aggiunto LangSmith, Perplexity, Frankfurter, MAE, Open-Meteo, Google Places, Rome2Rio
- admin/impostazioni: sezioni complete per tutti i nuovi servizi
- Notifiche TG su soglie: ElevenLabs 80%, SerpApi 80%, LangSmith 80%, Telnyx <€3

### Pending
- Duffel produzione — approvazione in corso
- Farnesina Web Services — email inviata
- TikTok appeal — in attesa
- Meta Business verification — SIM ok, in attesa auth pubblicazione
