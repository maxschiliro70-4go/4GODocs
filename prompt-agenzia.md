# 4GO FourTravel — Sessione 35 (18 Giugno 2026)

## Stack
Next.js 15.5.16 + Prisma + Neon PostgreSQL + Vercel Pro | branch develop = staging.fourgo.it (ep-sweet-rain) | main = produzione (ep-rapid-scene)

## Completato oggi

### Security
- Semgrep + njsscan GitHub Action su push main/develop
- Dependabot: da 38 → 3 vulnerabilità residue
- Next.js aggiornato a 15.5.16, middleware matcher fix CVE auth bypass
- 42 Semgrep code findings → tutti Acceptable risk

### Vapi Interprete — develop
- Campo `interpreteAttivo Boolean @default(false)` su ManualBooking (schema Prisma + migrate + migrazione ufficiale)
- Toggle UI admin nel dossier prenotazione (€49.90/viaggio)
- API PATCH manual-bookings include `interpreteAttivo`
- API GET bookings include `interpreteAttivo` con conversione boolean corretta
- Tasto 🌐 Interprete nella keyboard Telegram — visibile solo se `interpreteAttivo=true`
- Fix: keyboard mostrata anche senza pacchetto associato

### Staging setup risolto
- DATABASE_URL Preview → ep-sweet-rain (separato da produzione)
- Webhook bot test: `https://staging.fourgo.it/api/telegram/webhook?x-vercel-protection-bypass=IIyeP5zHwfzo3zyFAwtZuheCIds3YN7c`
- Sync schema staging: 123/135 colonne mancanti aggiunte via migrate endpoint
- Migrazioni Prisma create per: interpreteAttivo, Inquiry (aiReply/source), ManualBooking fields

### DeepL
- Piano API Developer Free: 1.000.000 caratteri/anno, scade 18/06/2027
- DEEPL_API_KEY configurata su Vercel
- Sezione in /admin/sistema e /admin/impostazioni
- Auto-detect endpoint Free (:fx) vs Pro

### Newsletter
- Bounce: blocca su Brevo invece di eliminare
- Import preventivo: check bounce/unsubscribed prima dell'importazione
- Mostra "✅ già in blocklist" se bounce già protetti

### Blog
- Fix duplicati: existingDestTypeSet su KEYWORD_TARGETS e pool pacchetti
- Eliminato source.unsplash (causa foto duplicate)
- Notifica TG per articoli senza foto + auto-fix reimage (soglia >1)
- action=draft-ids, restore-drafts, update-meta
- Meta SEO: Grecia, Vaccini Cina+Giappone+Corea, Valigia Sicilia
- Slug corti: Marocco x2, Australia-Fiji + redirect 301

### Violetta Bot
- Classificatore AI intent (Haiku) per navigazione vs audioguida + fallback regex
- Foto: regola anti-allucinazione, chiede luogo se non riconosciuto
- LangSmith trace analisi foto con alert allucinazione
- Traduzione con audio nativo ElevenLabs
- Lock anti-doppio click audioguida (8 min)
- Petit Palais e musei parigini in ICONIC_PLACES → Sonnet
- Fallback foto: "Bella foto! 📸 Non sono riuscita a identificare..."
- Filtro email sistema Aruba da inbox preventivi

## Pendente
- [ ] Passo 3 Vapi: callback tasto 🌐 → genera link WebRTC con lingua dalla destinazione
- [ ] SIAE: stampare Mod.349, firmare, CD-R, bonifico €126,62, raccomandata Roma
- [ ] Voci ElevenLabs per lingua in /admin/sistema (almeno fr) per audio traduzione
- [ ] Gamma logo replacement (4 file)
- [ ] noindex su /demo-bot e /violetta
- [ ] Meta Business verification
- [ ] GBP: 40 città aree di servizio
- [ ] Meta App Review: risottomettere con nuovi screencast

## Roadmap security
- GitLeaks — secrets in git history
- Zizmor — GitHub Actions workflow vulnerabilities
- Lakera Guard — protezione Violetta da prompt injection (al lancio piani commerciali)
- Harak — red-teaming LLM pre-lancio

## Roadmap Vapi Concierge
- Deepgram Nova-3: $0.0077/min streaming, $200 free tier
- DeepL API: 1M chars/anno free fino 18/06/2027
- Twilio +390250020031 configurato su Vercel
- Passo 3: callback 🌐 → Vapi WebRTC link → lingua automatica dalla destinazione

## Dati tecnici
- Staging webhook bypass: `IIyeP5zHwfzo3zyFAwtZuheCIds3YN7c`
- Staging DB: Neon ep-sweet-rain-a9rqftzk.gwc.azure.neon.tech
- Bot test: @FourGoTraveltestBot
- MIGRATE_SECRET: `4go2026`
- Next.js: 15.5.16
- Blog: 366 articoli PUBLISHED, 0 duplicati, 0 foto mancanti
