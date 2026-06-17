# 4GO FourTravel — Sessione 34 (17 Giugno 2026)

## Stack
Next.js 15.5.16 + Prisma + Neon PostgreSQL + Vercel Pro | Repo: `github.com/emilianomarchesi-droid/4-GO` | main=prod | develop=staging

## Completato oggi

### Security
- Next.js aggiornato 15.5.14 → 15.5.16 (fix SSRF WebSocket + auth bypass CVE-2026-44574/75/45109)
- Middleware matcher fix: esclude .rsc e _next/data (CVE auth bypass)
- Dipendenze vulnerabili aggiornate: nodemailer, uuid, postcss, @anthropic-ai/sdk
- GitHub Actions: Semgrep + njsscan su push main/develop (CodeQL rimosso — richiede Advanced Security)
- Dependabot abilitato: da 38 vulnerabilità → 3 residue non modificabili
- Semgrep: 42 code findings tutti Acceptable risk (TLS Aruba, dangerouslySetInnerHTML da DB, regex admin-only)

### Violetta Bot
- Classificatore AI intent (Haiku, ~200ms) per navigazione vs audioguida — fallback regex
- Pattern navigazione esteso: "come posso arrivare", "con la metro", "mezzi da/per"
- Messaggio fallback foto: "Bella foto! 📸 Non sono riuscita a identificare il luogo automaticamente..."
- Foto: chiede il luogo quando non riconosciuto
- Regola anti-allucinazione geografica nel prompt analisi foto
- LangSmith trace analisi foto con alert TG su allucinazione geografica
- Traduzione con audio nativo ElevenLabs (come si dice X in francese → testo + audio)
- Lock anti-doppio click audioguida (audioguideLock TIMESTAMPTZ, 8 min)
- Petit Palais + musei parigini in ICONIC_PLACES → Sonnet
- Fix filtro email sistema Aruba da inbox preventivi

### Blog & SEO
- Fix duplicati: existingDestTypeSet su KEYWORD_TARGETS e pool pacchetti
- action=draft-ids per mettere in DRAFT articoli specifici
- action=restore-drafts per ripristinare autogen in DRAFT
- action=update-meta per aggiornare title/excerpt via URL
- Notifica TG per articoli senza foto + auto-fix reimage
- Soglia foto duplicate abbassata a >1
- Eliminato source.unsplash fallback (causa foto duplicate URL diversi stessa foto)
- Slug lunghissimi accorciati nel DB + redirect 301 (Marocco x2, Australia-Fiji)
- Sitemap: priority faq 0.8, privacy 0.5, changeFrequency aggiornata
- Meta SEO ottimizzati: Grecia sicurezza, Vaccini Cina+Giappone+Corea, Valigia Sicilia

### Newsletter
- Bounce: "Blocca" invece di "Elimina" (emailBlacklisted:true su Brevo)
- Import preventivo: check bounce/unsubscribed prima dell'importazione
- UI: mostra "✅ già in blocklist" se bounce già protetti

### Roadmap aggiornata
- Lakera Guard — protezione Violetta da prompt injection (al lancio piani commerciali)
- Harak — red-teaming LLM pre-lancio commerciale Violetta
- Tasto 🌐 Interprete Violetta Concierge: WebRTC Vapi, €49.90/viaggio, interpreteAttivo su ManualBooking
- Twilio configurazione su develop quando si parte

## Pendente
- [ ] SIAE: stampare Mod.349, firmare, CD-R, bonifico €126,62, raccomandata Roma
- [ ] Voci ElevenLabs per lingua in /admin/sistema (almeno fr) per audio traduzione
- [ ] Twilio + Vapi Interprete su develop
- [ ] Campo interpreteAttivo su ManualBooking
- [ ] Gamma logo replacement (4 file): 4go-gamma.vercel.app → fourgo.it
- [ ] noindex su /demo-bot e /violetta
- [ ] Meta Business verification
- [ ] GBP: 40 città aree di servizio
- [ ] GSC: richiedere indicizzazione manuale per 14 URL scoperti non indicizzati

## Dati tecnici
- GitHub token: vedi Vercel env GITHUB_TOKEN (non salvare in chiaro nei docs)
- MIGRATE_SECRET: `4go2026`
- Twilio: ACCOUNT_SID, API_KEY, API_SECRET, PHONE_NUMBER=+390250020031 su Vercel
- Blog: 366 articoli PUBLISHED, 0 duplicati reali, 0 foto mancanti
- Next.js: 15.5.16
