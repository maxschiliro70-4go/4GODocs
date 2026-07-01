# 4GO FourTravel — Prompt Agenzia (aggiornato 30 Giugno 2026 — 4GO-23)

## Stato branch
- **main** `98be316` — produzione
- **develop** `a8d9b1d5` — Violetta staging (noindex attivo)

## Stack
Next.js 15 + Prisma + Neon PostgreSQL + Vercel Pro  
Repo: `github.com/emilianomarchesi-droid/4-GO`  
Branch: `main` (prod) / `develop` (staging: `staging.fourgo.it`, DB: `ep-sweet-rain`)

## Persone
- **Massimo** (Schilirò) — titolare, autorizza PAGATO, escalation WA/TG
- **Alessia**, **Inga** — operatori, ricevono notifiche TG via `notifyAll()`
- **Emi** — sviluppatore, riceve alert tecnici

## Account chiave
- `MIGRATE_SECRET=4go2026` | Admin: `/admin` (NextAuth + MFA TOTP)
- Vapi: `maxschiliro70@gmail.com` | Assistant: `640e941e` | PAYG ~$0.11/min
- Twilio: `+390250020031` attivo (bypassato Telnyx KYC)
- GitHub token (giugno 2026): `[GITHUB_TOKEN — vedere Vercel env]`
- Staging bot: `@FourGoTraveltestBot` token `[TELEGRAM_BOT_TOKEN staging — vedere Vercel env]`

## Violetta™
- Trademark UIBM n.302026000109033, 15/06/2026, classi 39+42
- Piani: Explorer €29.90 / Traveller €39.90 / Concierge €49.90
- Pagina: `/violetta` (noindex fino a go-live)
- Voice ElevenLabs: `gfKKsLN1k0oYYN9n2dXX`

## Vapi ristorante
- Assistant sempre Flux `686274c0`
- Transcriber: Nova-3 monolingua per lingue supportate, `multi` per le altre
- `voice.speed=0.8` su dashboard Vapi
- `temperature` NON supportato in `assistantOverrides`
- 36 lingue con template completo + step3 colloquiale + step3_no_alternative
- LANG_TO_ISO: mai codice paese (en non GB)
- Lakera: skip durante flusso ristorante attivo
- Check 50km: se indirizzo >50km dalla destinazione → chiede conferma
- `triedRestaurants` tracciato, radius ×2 se lista esaurita

## Blog autogen
- Cron: 6:00-6:45 ogni 5 min + fix-images alle 7:00
- `normalizeImageUrl()`: confronta solo pathname (no query params Pexels/Pixabay/Unsplash)
- Check anti-duplicato coverImage nel POST /api/admin/blog
- COUNTRY_MAP aggrega destinazioni correlate
- Anno rimosso da titoli/slug nuovi e esistenti
- Actions: `fix-images`, `fix-years`, `fix-slugs`, `audit`, `fix-duplicates`
- Redirect 301 automatico slug-con-anno → slug-senza-anno
- Blog paginato: `?page=N` con skip/take Prisma, ~500 articoli navigabili

## AI Act (scadenza 2 agosto 2026) — COMPLETATO
- Disclosure AI: WA/TG/ChatWidget/social caption ✅
- Alfabetizzazione: Massimo/Alessia/Inga confermato 29/06/2026 ✅
- DPIA: completata, firmata, archiviata ✅
- FRIA: esclusione motivata, firmata, archiviata ✅
- admin/gdpr: 4 card tutte ✅

## Performance (lab PSI mobile — 30 Giugno 2026)
| Pagina | Score | LCP | TBT |
|--------|-------|-----|-----|
| Homepage | 92 | 2.1s | 240ms |
| Pacchetti | 89 | 2.7s | 313ms |
| Chi Siamo | 90 | 3.0s | 173ms |
| Contatti | 65 | 11.3s | 98ms |
| Blog | 97 | 2.3s | 148ms |
| Quando Viaggiare | 89 | 2.1s | 398ms |

Fix applicati in 4GO-23 (main):
- `/blog`: `priority` sulla featured image → 65/9.9s → 97/2.3s
- `/quando-viaggiare`: fetch packages server-side RSC, passa `initialPackages` → 57/29.8s → 89/2.1s
- `/contatti`: TBT 503ms→98ms (BgSlideshow già dynamic(), varianza PSI confermata)
- `ssr:false` NON permesso in RSC Next.js 15 — usare solo in Client Component
- Homepage 16.7s LCP era causato da `1cc86c5` (rimozione opacity:0): ora PSI misura hero image (Pexels via /_next/image) invece di h1. Risolto spontaneamente (92/2.1s) — monitorare field data CrUX.

## Cron attivi (tutti su fourgo.it)
- preventivi-poll (*/5), blog-autogen (4×/day 06:00–06:15 + fix-images 07:00)
- blog-check (1° del mese 8:00), password-expiry (09:00), appointment-reminders (ogni min)
- inbox-organizer (Lun 7:30 — su cron-job.org E Vercel Cron, maxDuration=60)
- gbp-post (daily)
- **Regola**: ogni cron va registrato SIA su cron-job.org SIA in vercel.json

## Regole assolute
1. Leggere file PRIMA di modificare — mai commit speculativi
2. webhook/route.ts, middleware, auth, pagamenti, schema DB → sempre su develop
3. Merge develop→main solo dopo staging + autorizzazione esplicita Emi
4. Nuove colonne DB nel migrate endpoint come `ALTER TABLE IF NOT EXISTS`
5. Prisma schema-external columns → `$queryRawUnsafe` / `$executeRawUnsafe`
6. Branch main: solo fix urgenti produzione — tutto il resto su develop

## Prossimi step
1. SIAE raccomandata A/R inviata — attendere conferma/attestazione
2. **Alla conferma SIAE, esplicitamente autorizzata da Emi**: cherry-pick massiccio
   develop→main di tutto l'arretrato accumulato (fix pagamenti 24/6 — Stripe year
   undefined, VioletaSubscription idempotency, PayPal validation, parser importi IT
   — più `e2e-tests.yml`, con schedule lunedì 07:15 riportato a testare produzione
   invece di staging, bypass header già corretto, pronto all'uso). Consigliato
   Claude Code per il volume atteso (settimane di sviluppo Violetta divergente,
   più conflitti di quelli di oggi) — leggere prima la sezione "Pattern risoluzione
   conflitti cherry-pick" sotto. Violetta go-live: rimuovi noindex → GSC →
   Product Hunt / There's An AI For That / Futurepedia / BotList
3. ~~Pre go-live Violetta: Playwright E2E su develop~~ — COMPLETATO 4GO-22/23,
   infrastruttura pronta (vedi sessione 4GO-24 sotto, parte pomeriggio/sera)
4. Audit endpoint schedulati: multipreventivi, Fly&Drive, locations, social AI, WA/email AI, cortesia rientro, recensioni post-viaggio
5. TikTok: attendere review
6. 40 città service areas GBP
7. Contatti LCP 11.3s: monitorare field data prima di intervenire

## Pattern risoluzione conflitti cherry-pick (per il prossimo massiccio, post-SIAE)
Emersi durante il recupero di 35 commit in sessione 4GO-24 — riapplicabili:
- **develop ha già una versione più avanzata/refactorizzata** (es. `openEditBooking()`
  estratto invece di logica inline) → tenere HEAD, scartare la patch in arrivo se il
  fix è già coperto lì. Verificare SEMPRE cercando il campo/fix nella versione develop
  prima di assumere serva la patch.
- **Entrambi i lati hanno colonne/campi diversi non sovrapposti** (es. develop ha
  `testVapiPhone`/`violettaPlan`, main ha `itineraryCities`, stessa riga SELECT) →
  UNIRE entrambe le righe, non scartare nessuna delle due.
- **File di soli docs** (`docs/prompt-agenzia.md`) → tenere sempre la versione develop
  (più recente/specifica), scartare la patch in arrivo da main.
- **Commit che si annullano a vicenda** (debug aggiunto poi rimosso nello stesso set) →
  cherry-pick comunque tutti in ordine, il risultato netto è identico a saltarli.
- Verificare **subito dopo** ogni pick con `grep` mirato sul campo/funzione in gioco,
  non solo `tsc --noEmit` — TS non cattura una riga persa in una struttura dati.

## Sessione 4GO-22 (30 Giugno 2026 — mattina)
blog: normalizeImageUrl dedup foto duplicate, paginazione ~500 articoli, fix BgSlideshow isDark (chi-siamo 95, quando-viaggiare inizialmente non risolto), inbox-organizer schedulato correttamente (maxDuration+Vercel Cron), delete-all email protetto con conferma "ELIMINA"+ActivityLog, Playwright E2E su develop (form preventivo, quiz, blog, admin), bug reali trovati (shortDescription in /api/preventivo, heygen-weekly schema mancante), Actions minutes 90% consumati → workflows limitati.

## Sessione 4GO-23 (30 Giugno 2026 — sera)
Performance PSI mobile analizzata dopo nuovi score. Fix su main:
- `/blog` featured image `priority` (97/2.3s)
- `/quando-viaggiare` server-side fetch packages → `initialPackages` prop (89/2.1s)
- Contatti TBT varianza PSI, non strutturale
- Homepage 92/2.1s senza intervento (monitorare)

## Fix aggiuntivi 4GO-23 (push 34969d2)
- warmup-images: 3→18 immagini, q=80→q=85 (allineato HeroSection quality=85 LCP)
- getPackagesFromDB revalidate 5→300s
- preload mobile q=80→q=85
- vercel.json: cron warmup automatico 05:55 ogni giorno
- Warmup manuale eseguito post-deploy: 36/36 varianti in cache ✅

## Sessione 4GO-24 (1 Luglio 2026)

### Bug critico risolto: tappe itinerario non mantenute dopo salvataggio
Causa reale in **2 livelli separati** di `src/app/api/admin/bookings/route.ts`
(l'endpoint GET usato da load() per rileggere la lista booking):
1. La query SQL raw con SELECT esplicita non includeva `mb."itineraryCities"`
   (dimenticata quando il campo fu aggiunto al modello) — fix in a11e7c1
2. Anche dopo il fix SQL, la funzione `manuals.map()` subito dopo ricostruisce
   l'oggetto booking elencando ~24 campi uno per uno per la risposta JSON
   finale — itineraryCities non era in quella lista neanche lì — fix in 3ae5c4a

**Il salvataggio (PATCH) ha sempre funzionato correttamente** — il DB aveva
sempre i dati giusti. Il bug era interamente nel percorso di lettura/reload.

**Lezione permanente**: ogni nuova colonna aggiunta a ManualBooking (o
qualsiasi modello) va verificata in TUTTI questi punti, non solo nel
migrate endpoint:
1. `ALTER TABLE IF NOT EXISTS` nel migrate endpoint (già noto)
2. Ogni query SQL raw con SELECT esplicita che legge quella tabella
3. Ogni trasformazione/mapping JS che ricostruisce l'oggetto per la risposta API
   (spesso fatto per fondere dati da più tabelle, es. payments + manuals)

Metodo di diagnosi che ha funzionato: log end-to-end in sequenza (payload
inviato → risposta PATCH → risposta grezza GET → oggetto finale nel
frontend) per isolare ESATTAMENTE in quale passaggio il dato si perde,
invece di ipotizzare. Rimuovere sempre i log di debug una volta confermato
il fix.

### Altri fix sessione
- Firme email (`fourgo.it/firme-email`): logo+social incorporati come
  base64 (Outlook blocca download immagini remote di default — base64
  bypassa il blocco). Icone social da Simple Icons (CC0, no attribuzione,
  loghi ufficiali). Layout: contatti → riga rossa → logo → social sotto.
- Login (`/login`): campo TOTP ora dentro un `<form>` vero (era `<div>` +
  onClick/onKeyDown manuale) — eliminato warning Chrome password-fuori-form.
- Brand rietichettato ovunque: 4GO FourTravel/4Travel → FOURGO/4TRAVEL
  (prenotazioni-manuali + multi-hotel), value DB invariati.
- Importo/Diritti agenzia/50-50 ora condizionati a bookingStatus==='ARCHIVIATA'.
- Fix stale-closure su handler tappe form edit (setEditForm(prev=>) invece
  di spread diretto — pattern ora coerente col form di creazione).

## Sessione 4GO-24 (continua) — 1 Luglio 2026, pomeriggio/sera

### Firma email — scoperta critica e fix definitivo
Tentativo iniziale: logo+icone social in base64 per bypassare il blocco Outlook
sulle immagini remote. **Scoperta**: Gmail (web e app mobile) non renderizza MAI
immagini base64/data-URI — nessuna azione di recupero per l'utente, a differenza
di Outlook dove basta "scarica immagini". Il fix per Outlook rompeva Gmail,
compromesso peggiore. **Soluzione definitiva**: tornati a URL remoti
(`fourgo.it/logo-4go-hd.png` + `fourgo.it/social/{facebook,instagram,tiktok,
pinterest,threads}.png`, appena caricati), mantenuti `width`/`height` espliciti
(fix separato e valido a prescindere, altrimenti Outlook usa la dimensione
nativa del file scaricato).

**`emailFooter()` era codice morto**: mai importata da nessuna route prima
di questa sessione. Ora collegata e unificata su **10 route reali**:
`manual-bookings` (sendBookingEmail — la più usata), `preventivi`,
`preventivi/confirm`, `preventivi/resend-payment`, `send-welcome`,
`review-request` (cron), `appointments`, `email-ai/poll`, più i 2 webhook
`stripe/webhook` ed `email-ai/route.ts` (portati su main solo su richiesta
esplicita, essendo webhook).

### Bug reale scoperto grazie ai test: errori SMTP nascosti silenziosamente
`sendBookingEmail()` aveva un catch che solo loggava (`console.error`) senza
rilanciare l'errore — `resend-confirm` rispondeva sempre `{"ok":true}` anche
quando l'invio falliva davvero. Il test è "passato" diverse volte senza che
nessuna email arrivasse. Fix: la funzione ora ritorna `{ok, error}`, e
`resend-confirm` (l'unica azione il cui scopo è mandare quella email) controlla
l'esito e risponde 502 con l'errore reale. Le altre 4 chiamate a
`sendBookingEmail` (creazione, cambio stato) ignorano volutamente l'esito —
non devono fallire l'operazione principale per un problema SMTP a valle.

**Causa radice trovata grazie a quel fix**: Aruba aveva disabilitato l'invio
per `info@fourgo.it` (525 5.7.13) — probabilmente innescato dai nostri stessi
test ripetuti ravvicinati (pattern tipico rilevato come sospetto dai sistemi
antispam). Cambiare la password NON basta per riattivare l'invio (richiede
un passaggio esplicito extra su Aruba, "Stato casella → Abilita" da pannello
postmaster) — ma nel nostro caso lo stato era già "Attiva" e il blocco era
altrove (probabilmente lato server, non visibile da pannello self-service).
Si è sbloccato da solo dopo un po' (o Aruba è intervenuta), confermato con
l'endpoint diagnostico creato apposta.

### Nuovo endpoint diagnostico: smtp-check
`GET /api/admin/smtp-check?secret=4go2026` — usa `nodemailer.verify()`,
controlla l'autenticazione SMTP **senza inviare nessuna email**, zero rischio
di ri-triggerare un blocco mentre si verifica se si è sbloccato. Con
`&send=1&to=...` manda anche un invio reale di conferma quando serve la
prova definitiva.

### Casella CI separata — prevenzione blocchi futuri
Creata una casella dedicata (`SMTP_CI_USER`/`SMTP_CI_PASS` su Vercel) usata
**esclusivamente** dai test automatici, mai da `info@fourgo.it`:
- `test-email-signature.yml` (create + resend-confirm): flag esplicito `useCI`
  nel body della richiesta
- `preventivo.spec.ts` / `quiz.spec.ts` (Playwright): rilevamento automatico
  dal pattern email sottomesso (`e2e-test-`/`e2e-quiz-`), stesso pattern già
  riconosciuto da `e2e-cleanup` — sicuro su endpoint pubblici, non richiede
  fidarsi di un flag lato client

### Bug trovato in `e2e-tests.yml`: mai esistito su main
GitHub Actions esegue i trigger `schedule` **solo** con la versione del
workflow presente sul branch default (main) — `e2e-tests.yml` esisteva solo
su develop dalla sua creazione (4GO-22), quindi lo schedule lunedì 07:15 non
è probabilmente MAI scattato in automatico, solo i lanci manuali (da develop)
hanno mai funzionato. Non portato su main in questa sessione — **volutamente,
su richiesta esplicita**: resta lì fino al merge massiccio post-conferma SIAE
insieme a tutto il resto (vedi "Prossimi step"). Il fix (bypass header
mancante nel cleanup, target dinamico) resta pronto su develop per allora.

### test-email-signature.yml — nuovo workflow, attivo su main
Creato da zero in questa sessione, **lasciato attivo** su richiesta esplicita
(diversamente da e2e-tests, era già validato end-to-end oggi: email arrivata,
firma verificata visivamente su Gmail mobile, pulizia confermata). Schedule
lunedì 07:30 UTC (dopo security-scan 07:00 ed e2e-tests 07:15, quando quello
verrà riattivato) testa **produzione** (`fourgo.it`) — crea booking di test,
invia conferma reale via `emailFooter()`, pulisce (`e2e-cleanup`). Dispatch
manuale libero di puntare a staging per test mirati.

### Bug tappe — sync develop↔main completata
Il fix già documentato in 4GO-24 (2 livelli: SELECT SQL + trasformazione
`manuals.map()`) era solo su main. Verificato e confermato integro anche
su develop durante il cherry-pick massiccio di questa sessione (35 commit,
vedi "Pattern risoluzione conflitti" sopra).

### Decisione di processo: basta doppio-commit per abitudine
Il cherry-pick dei 35 commit era un recupero arretrato una tantum, esplicitamente
richiesto. Da qui in avanti: commit solo sul branch pertinente per ogni singola
modifica (main per hotfix non-webhook, develop per webhook/auth/pagamenti/schema),
NESSUNA sincronizzazione automatica ad ogni commit. Il riallineamento completo
si fa un'unica volta, tutto insieme, alla conferma SIAE — esplicitamente
autorizzato da Emi prima di procedere.
