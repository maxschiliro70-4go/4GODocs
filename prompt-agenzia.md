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
- Twilio: `+390250020031` attivo
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
   infrastruttura pronta (vedi sessione 4GO-23, parte 1 luglio sotto)
4. Audit endpoint schedulati: multipreventivi, Fly&Drive, locations, social AI, WA/email AI, cortesia rientro, recensioni post-viaggio
5. TikTok: attendere review
6. 40 città service areas GBP
7. Contatti LCP 11.3s: monitorare field data prima di intervenire

## Pattern risoluzione conflitti cherry-pick (per il prossimo massiccio, post-SIAE)
Emersi durante il recupero di 35 commit in sessione 4GO-23 (1 luglio) — riapplicabili:
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

## Sessione 4GO-23 (continua — 1 Luglio 2026, pomeriggio)

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

## Sessione 4GO-23 (continua — 1 Luglio 2026, sera)

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
Il fix già documentato sopra (2 livelli: SELECT SQL + trasformazione
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

## Sessione 4GO-23 (continua) — 2 Luglio 2026

### LCP homepage risolto: root cause reale trovata (non più ipotesi)
Field data Vercel Speed Insights mostrava LCP 7.59s stabile su `/` mobile,
sempre sull'elemento hero `<img>`. Diagnosi con WebPageTest (Milan, mobile
393px, throttling 3G) invece di continuare a ipotizzare: waterfall mostra
l'immagine LCP da **468KB** (AVIF w=1920 q=85) — su un viewport di 393px.

Causa: `sizes="(max-width: 828px) 828px, 1920px"` in `HeroSection.tsx`
dichiara una larghezza **fissa**, non relativa al viewport — ma la sezione
hero è sempre `w-full` (100% viewport, mai un box vincolato). Su mobile ad
alta densità (DPR 2x/3x), il browser calcola 828×DPR e sceglie sempre la
variante più pesante disponibile in `deviceSizes` (1920w), anche su schermi
stretti. Fix: `sizes="100vw"` — lascia scegliere il breakpoint più vicino al
viewport reale × DPR. Zero impatto visivo (sizes non tocca mai il rendering,
solo quale file scaricare). Allineato anche il preload esplicito in
`page.tsx`: da 2 varianti (828w/1920w) a tutte le 6 di `deviceSizes`, per
evitare mismatch tra ciò che il preload scalda e ciò che il tag reale
richiede.

### Trilogia TDZ "Cannot access X before initialization" — causa reale trovata
Cliente reale, query banale ("cosa mettere in valigia"), bot silenzioso —
zero risposta, cliente costretto a rifare `/start`. Log Vercel:
`ReferenceError: Cannot access 'an' before initialization`, stack minificato
inutile senza sourcemap (non recuperabili né da Vercel dashboard né da build
locale, bloccata dagli stessi limiti di rete già noti — `binaries.prisma.sh`
non whitelisted).

**Primo tentativo (sbagliato)**: consolidamento dei 18 `import('@/lib/db')`
dinamici sparsi nel webhook in un singolo import statico — ipotesi plausibile
(stesso pattern del bug di 4GO-19, maggio) ma **non era la causa reale**:
stesso identico errore riprodotto dopo il deploy.

**Metodo che ha funzionato**: installato ESLint temporaneamente (`--no-save`,
mai in package.json) con solo la regola `@typescript-eslint/no-use-before-define`
attiva, lanciata sul singolo file. Ha trovato con certezza **16 violazioni
reali**, non ipotesi:
- `effectiveDest` (dentro `getAIResponse`): dichiarata riga 1089, usata già
  a riga 1036 — dentro TUTTO il ramo `isFactualQuery`/`isCurrencyQuery`/ecc,
  esattamente quello di "cosa mettere in valigia". Fix: dichiarazione
  spostata subito dopo `destination`, 53 righe più in alto.
- `history`/`session` nel blocco FOTO (audioguida da immagine — funzionalità
  reale di Violetta): entrambe dichiarate molto più avanti in POST, ma quel
  blocco gira prima. Bonus: `session.destination` non esiste nemmeno sullo
  schema Prisma di `TelegramSession` (solo `bookingCode`) — era sempre
  `undefined` a prescindere dal TDZ. Fix: riusa una query già presente poco
  sopra nello stesso blocco (`sessionPhoto`/`photoDestination`, con la vera
  destination via JOIN su `ManualBooking`) invece di crearne una duplicata.
- `history` nel blocco AUDIOGUIDA VIA TESTO: qui `session` era già
  disponibile, solo `history` mancava — costruita localmente da
  `session.context`, usata per il contesto conversazionale reale della
  chiamata AI (non solo logging).

Verificato a 0 problemi con ESLint prima di ogni commit. **Metodo da riusare**
se ricapita altrove in questo file (4500+ righe, il punto più fragile del
sistema per questo tipo di bug) — molto più efficace della caccia manuale a
tappeto fatta nei tentativi precedenti.

**Bug collaterale trovato durante il refactor**: cherry-pick del fix TDZ da
develop a main ha introdotto una query duplicata (main non aveva ancora il
consolidamento import di prima, quindi aveva già una query equivalente più
completa nello stesso blocco). Corretto con un secondo commit di pulizia,
stesso fix applicato a entrambi i branch.

**Regressione auto-inflitta**: durante l'aggiunta della notifica Telegram
sul blocco token GBP (vedi sotto), una `str_replace` ha eliminato per
distrazione `const dryRun = ...` — TS check "pulito" subito dopo, ma falso
negativo da cache incrementale (`tsconfig.tsbuildinfo` scartato da git ma
non eliminato fisicamente tra check consecutivi sullo stesso file). Lezione:
**eliminare fisicamente la cache prima di check critici dopo modifiche
rapide in sequenza sullo stesso file**, non solo scartarla da git dopo.

### Ricerca interprete Violetta Concierge — pattern lingua già esistente
Partito da una domanda su MX→spagnolo per l'interprete WebRTC Concierge.
`interpreteAttivo` è solo un flag booleano su `ManualBooking`, nessuna logica
di chiamata collegata — confermato con ricerca sistematica (`vapi-utils.ts`
è codice completamente orfano, mai importato da nessun file).

Il pattern reale, collaudato, vive in **`src/lib/restaurant-flow.ts`**
(usato dal flusso "prenota tavolo ristorante" via Vapi, già in produzione):
```
countryCode (da ManualBooking) → COUNTRY_LANGUAGE[cc] → nome lingua italiano
  → LANG_TO_ISO[nome] → codice ISO
  → transcriber esplicito: NOVA3_SUPPORTED.has(langCode)
        ? { provider:'deepgram', model:'nova-3', language: langCode }
        : { provider:'deepgram', model:'nova-3', language:'multi' }
```
`COUNTRY_LANGUAGE['MX'] = 'spagnolo'` confermato. `firstMessage` generato al
volo con Claude Haiku, direttamente nella lingua del ristorante. `LANG_TO_ISO`
è definita **inline dentro il webhook** (non esportata da `restaurant-flow.ts`)
— da estrarre in un punto condiviso se si replica per l'interprete Concierge.
Piano selezionabile in `prenotazioni-manuali`: il radio Concierge accende
`interpreteAttivo` automaticamente, più un toggle manuale indipendente
(operatore può "regalarlo" su altri piani, o disattivarlo pur restando
Concierge).

### gbp-post fermo da 2 giorni — OAuth refresh token silenzioso
`refreshGbpToken()` non controllava mai `res.ok` né il body errore di Google
— ritornava sempre `null` a prescindere dalla causa reale (refresh token
assente vs rifiutato da Google — `invalid_grant`, comune con OAuth consent
screen in modalità "Testing": Google limita i refresh token a **7 giorni**
in quel caso, indipendentemente dall'uso. Da verificare su Google Cloud
Console se la consent screen di GBP è ancora in Testing — se sì, ricapiterà
ogni settimana finché non viene pubblicata/verificata).

Fix: `getValidToken`/`refreshGbpToken` ora ritornano `{token, error}` con
il messaggio Google reale, propagato sia nella risposta HTTP sia in una
notifica Telegram (mancante fino ad oggi su questo specifico fallimento —
per questo il cron ha fallito 2 giorni senza che nessuno se ne accorgesse,
nonostante esistesse già notifica su successo/fallimento della
*pubblicazione* vera). Ricollegato manualmente da Emi, funziona di nuovo.

### Secondo incidente Aruba SMTP nello stesso weekend — causa diversa
Non collegato al blocco mailbox di ieri (525 5.7.13, risolto). Oggi:
`550 5.1.0 <info@fourgo.it> Connessione da 13.38.72.136 temporaneamente
rifiutata` — blocco a livello di **IP sorgente**, cade già su `MAIL FROM`.
Ricerca web ha confermato lo stesso pattern documentato altrove (utenti
Outlook.com con connected-accounts verso Aruba, stesso messaggio esatto con
IP Azure diverso) — Aruba applica blocchi IP-based contro provider cloud,
non solo blocchi account-based.

**Ma la cronologia non torna con un blocco strutturale permanente**: zero
incidenti da aprile fino a ieri, poi due in due giorni consecutivi — lettura
più coerente: conseguenza a cascata dell'incidente di ieri (sistemi Aruba
più aggressivi nello scrutinare le connessioni della stessa casella/dominio
per un periodo dopo un incidente antispam), non un problema strutturale
sempre-presente contro IP AWS/Vercel. Si è risolto da solo in poche ore,
confermato con `smtp-check`. Ticket Aruba aggiornato chiedendo conferma se
i due episodi sono collegati.

### resend-confirm — notifica errore + WhatsApp cliente
Due lacune emerse durante il recupero del cliente `4GO-2026-KQLC1` (email
di conferma fallita per il blocco IP sopra, mai notata perché nessun alert
visibile — causa non confermata con certezza, singolo episodio non
riprodotto):
1. Nessuna notifica Telegram su fallimento invio — solo `console.error`
   server-side + risposta HTTP dipendente dall'`alert()` browser. Aggiunto
   `notifyAll()` esplicito con il messaggio d'errore reale incluso (non
   serve più controllare i log Vercel per il testo dell'errore).
2. `sendClientNotification()` (WA+Telegram+email interna, già esistente e
   usata per preventivo/location/trasporto/multi-hotel/fly) non era mai
   chiamata da `resend-confirm` — cliente con telefono registrato non
   riceveva WhatsApp. Aggiunto `'confirm'` come sesto `docType`, chiamata
   dopo un invio riuscito. Entrambe le notifiche saltate quando `useCI=true`
   (test automatico settimanale), per non generare rumore sul booking
   fittizio di test.

### Metodo di lavoro consolidato in questa sessione
- **ESLint `no-use-before-define`** (installazione temporanea, mai in
  package.json) per diagnosi TDZ certa invece di ricerca manuale a tappeto
  — riusare su questo file se ricapita.
- **WebPageTest** (location Milan, mobile, throttling reale) per waterfall
  LCP concrete quando i tool interni (Chrome remote debug via USB) falliscono
  per attrito di setup — nessuna registrazione richiesta, dati sufficienti
  per diagnosi definitiva.
- **Eliminare fisicamente `tsconfig.tsbuildinfo`** prima di TS check critici
  dopo modifiche rapide in sequenza sullo stesso file — la cache incrementale
  può dare falsi negativi.
- Verificare sempre `res.ok` + il body errore reale nelle chiamate OAuth/API
  esterne prima di collassare tutto in `return null` — pattern ripetuto
  (Aruba SMTP, Google OAuth refresh) che nasconde la causa reale dietro un
  errore generico.

### gbp-post — risolto in modo definitivo (non più workaround settimanale)
Causa confermata: OAuth consent screen del progetto Google Cloud (`go-maps-491010`,
client ID `341870753269-d1l2pmh7ngf71uvkncbpnnf4uv0ao0a4`) era in stato **Testing**
— Google limita i refresh token a 7 giorni fissi in quello stato, indipendentemente
dall'uso, anche con utenti già whitelistati come test user (2/100: emiliano.marchesi@,
maxschiliro70@).

Verificato che lo scope `https://www.googleapis.com/auth/business.manage` è
**non sensibile** (nessuna icona lucchetto nella lista "Aggiungi o rimuovi ambiti"
— quell'icona è l'indicatore ufficiale Google per sensitive/restricted). Con solo
scope non sensibili, il passaggio da Testing a **In produzione** non richiede
nessuna revisione Google: dichiarato lo scope in Accesso ai dati → Save, poi
Pubblico → Publish App → conferma immediata, nessuna coda di verifica.

Da questo momento il refresh token non ha più il limite dei 7 giorni — dura fino
a revoca esplicita, cambio password Google, o 6 mesi di inattività (limiti
standard). Il ricollegamento manuale settimanale non dovrebbe più servire.
Test naturale: il cron di lunedì prossimo (07:00 UTC) — se il token regge,
il problema è chiuso per davvero, altrimenti arriva comunque la notifica
Telegram con l'errore reale (fix di oggi) invece del silenzio precedente.

### WhatsApp escalation — bug reale scoperto con cliente vero, fix su main
Cliente reale (Claudio, preventivo Sharm el-Sheikh già esistente — Reefoasis/
Jaz Fanara, 25 dic-2 gen, 3 persone, 3.800€) scrive lamentando di non essere
stato ricontattato. Il bot risponde promettendo "ora lo faccio subito, un
consulente ti raggiungerà a breve" — ma nessuna escalation reale è partita.
Confermato sui log Vercel: nessuna chiamata a `api.telegram.org` nell'invocazione
`/api/whatsapp/webhook` di quel turno, nessun log `[ESCALATION]`.

**Causa**: `escalate` non è un campo JSON strutturato — è un pattern testuale
(`ESCALATE: motivo` come prima riga della risposta grezza di Claude, letto via
regex in `/api/whatsapp/ai`). Le istruzioni esistenti coprivano solo 3 casi
stretti: emergenza reale, richiesta di un **nuovo** preventivo complesso,
qualificazione **appena completata in questo turno**. Il caso di Claudio non
rientrava in nessuno dei tre (info già note da prima, non "una richiesta nuova"
né "appena qualificata ora") — Claude ha correttamente capito che non c'era
match letterale, quindi non ha scritto `ESCALATE:`, ma ha comunque generato
testo che semanticamente promette un passaggio umano. Testo e azione disallineati.

**Fix** (principio generale, non un quarto caso aggiunto alla lista): il bot non
può gestire pagamenti né confermare prenotazioni, quindi ogni volta che il
contesto ha destinazione+periodo+budget (anche da scambi precedenti, non
necessariamente in questo turno) E il cliente esprime in qualunque forma
volontà di procedere/essere ricontattato → sempre urgente da scalare,
indipendentemente da come è formulata la richiesta. In caso di dubbio con
qualifica commerciale completa, scala sempre — WhatsApp è il canale più
commerciale, l'obiettivo esplicito è non perdere clienti per un pattern troppo
stretto nelle istruzioni (rischio reale: recensione negativa nel migliore dei
casi, vendita persa a un concorrente nel peggiore).

Portato **subito su main** (non atteso il batch SIAE) per l'impatto commerciale
diretto e immediato — stesso tipo di eccezione urgente già usata oggi per il
silenzio email cliente reale. Il fix protegge le conversazioni future, non
recupera automaticamente Claudio — serve un contatto manuale su quel caso
specifico, ancora da fare.

### Template WhatsApp "notifica_nuova_richiesta" — attivo, risolve la finestra 24h
Causa più profonda del fallimento silenzioso trovato con l'escalation di Claudio:
qualsiasi notifica **proattiva** a Massimo (il sistema scrive per primo, non
risponde a un suo messaggio) via testo libero è soggetta alla finestra delle
24 ore di WhatsApp Business — fuori da quella finestra l'API accetta la
richiesta (200, ID messaggio valido) ma non consegna mai, senza nessun errore.
`operatorNumbers.includes(from) → continue` nel webhook scarta il messaggio
solo a livello applicativo nostro (Violetta non risponde a un operatore); la
finestra Meta si basa sulla ricezione lato loro, non su questo — verificato
non essere la causa del problema.

Creato e approvato template Meta **"notifica_nuova_richiesta"** (categoria
Utility, parametri **nominati** non posizionali — `{{nome_cliente}}`
`{{contatto}}` `{{richiesta}}`, scelta deliberata: i nominati matchano per
nome invece che per ordine nell'array, evitando lo stesso tipo di bug di
disallineamento silenzioso trovato più volte oggi se il template viene mai
riordinato). Periodo di validità esteso oltre il default di 10 minuti
(altrimenti stesso problema di scadenza silenziosa, spostato ma non risolto).
Zero pulsanti/CTA — solo testo informativo, per restare in categoria Utility
e non rischiare riclassificazione automatica a Marketing (Meta la applica
retroattivamente dal 9 aprile 2025).

Nuova funzione condivisa `sendWAOperatorTemplate()` in `src/lib/waTemplate.ts`,
sostituisce il testo libero in **3 punti** (non 4 come inizialmente previsto —
il quarto, "limite 30 messaggi raggiunto", è stato scartato: quel caso non è
una "nuova richiesta cliente", il contenuto non calzava col template dichiarato
a Meta, resta solo su Telegram+email come prima):
1. `contact/route.ts` — form contatti sito (contatto = email, o "email · telefono" se presente)
2. `whatsapp/webhook` — trigger esplicito "operatore"/"consulente"
3. `whatsapp/webhook` — escalation AI (`aiData.escalate`)

Portato su main, template approvato da Meta più rapidamente del previsto —
attivo e funzionante, non solo teoricamente pronto per quando arriverà
l'approvazione.

---

## Sessione 3 luglio 2026 (pomeriggio/sera) — SEO, email, WhatsApp cliente, newsletter

### Secondo template WhatsApp — lato clienti
Scoperto che le notifiche WA ai **clienti** (preventivo/location/trasporto/multi-hotel/fly/confirm,
via `sendClientNotification()`) avevano lo stesso problema di finestra 24h già risolto ieri
solo lato operatori — testo libero, silenziosamente non consegnato se il cliente non aveva
scritto di recente al numero business (es. preventivo nato da form/telefonata, mai da chat WA).

Creato secondo template Meta **"notifica_documento_cliente"** (Utility, 3 variabili nominate:
`nome_cliente`, `oggetto`, `link` — `oggetto` unisce label+destinazione già composti lato
codice, dato che i template non supportano variabili condizionalmente vuote). Header immagine
fissa (`logo-4go-fb.png`, caricato da Emi, trasparente, salvato nel repo per URL stabile).
Un solo template copre tutti e 6 i docType, dato che la struttura del messaggio è identica in
tutti i casi. Approvato e integrato in `sendClientNotification.ts`.

### Loghi email — sfondo nero → trasparente
`logo-4go-hd.png` aveva sfondo nero incorporato nel file stesso (RGB, no alpha) — non un CSS,
il file. Sostituito con `logo-4go-transparent.png` (vero RGBA) in `contact/route.ts` ed
`emailFooter()`. Due trattamenti diversi per contesto diverso (testati visivamente prima di
applicare): su header con gradiente rosso/blu il trasparente puro si mimetizza quasi del tutto
(stessi colori del logo e dello sfondo) — aggiunto un box nero semi-trasparente arrotondato
(`rgba(0,0,0,0.55)`, radius 8px, variante "D" tra 6 alternative testate) dietro al logo. Su
sfondo bianco (firma email standard) il trasparente puro basta, nessun box necessario. Stessa
soluzione applicata anche al logo header della newsletter (stesso gradiente, stesso problema).

### Newsletter — allineamento e miglioramenti
- Handle social disallineati tra `emailFooter()` (corretti, `@4go_fourtravel`) e il template
  newsletter (vecchi, `@4fourtravelagency`) — allineati tutti e 6 (incluso WhatsApp, mancante
  in emailFooter, aggiunto con logo ufficiale scaricato da Simple Icons via GitHub — dominio
  whitelisted — e composto su cerchio verde con cairosvg+PIL)
- Logo header ingrandito (60px→90px→108px, +80% totale)
- Icone social: da box colorati testuali a PNG reali (stesse immagini di emailFooter)
- Libreria immagini: rimosso limite artificiale di 20, aggiunta scroll dopo 9 (3 righe) invece
  di espandere la pagina indefinitamente
- Bottoni extra: nuova posizione "Nel testo" con placeholder `[BTN N]` (stesso pattern già
  esistente per `[CTA]` e `[FOTO N]`), esclusi da top/bottom quando impostati inline

### Indagine SEO — 113 articoli "rilevati ma non indicizzati"
Report Search Console: 130 URL (113 blog, 9 city-page, 6 pacchetti, 3 statiche) mai scansionati
da Google (`1970-01-01` = placeholder "mai scansionato", non una data reale).

**Causa identificata**: `blog-autogen` pubblicava **10 articoli/giorno** (~300/mese) — volume
troppo alto rispetto al crawl budget che Google dedica al dominio. Confermato con dati reali
via nuovo sistema `index-check` (vedi sotto): batch a offset 0-100 (articoli recenti) mostravano
88-96% non indicizzato, batch a offset 200-400 (articoli di mesi fa) mostravano 96% **indicizzato**
— conferma netta che è un problema di volume/tempo, non di qualità/struttura dei contenuti.

**Falsa pista scartata**: l'Indexing API di Google (già in uso via cron `index-google`, ogni ora)
NON aiuta per contenuto blog — è ufficialmente limitata a JobPosting/BroadcastEvent, policy
chiarita da Google a maggio 2025. Il cron "funziona" (risposta 200, notifica "✅ Inviate: N")
ma Google ignora silenziosamente le richieste fuori scope — il messaggio Telegram diceva
letteralmente "Inviate", mai "Indicizzate", distinzione persa nel tempo. Cron lasciato attivo
(non fa danno) ma non risolve nulla per il blog.

**Due alert GSC risultati falsi allarmi, verificati e chiusi**:
- Redirect error su un URL blog specifico → `curl -IL` mostra 308→200 OK, catena funzionante,
  alert probabilmente riferito a uno stato precedente già risolto
- "Bloccata da robots.txt" → tutti i 33 URL nel report sono asset CSS interni Next.js
  (`/_next/static/`), bloccati **intenzionalmente e correttamente**, zero relazione col blog

**Azioni concrete**:
1. `blog-autogen`: quota giornaliera ridotta da 10 a **3** articoli/giorno (countdown nel DB
   `blogCronMax`, non moltiplicato da nessuna sessione — era già così prima, mai "per sessione
   ×10" come la UI assumeva). Vercel Cron ridotto da 10 a 3 trigger (06:00/06:20/06:40 UTC).
   `blog-settings` (pagina admin) e `blog-check` (valore "consigliato") erano disallineati dal
   vero modello di calcolo — corretti entrambi, coerenti col nuovo valore.
2. **Nuovo sistema di monitoraggio**: `src/app/api/cron/index-check/route.ts` — usa la Search
   Console **URL Inspection API** (`urlInspection.index:inspect`, diversa dall'Indexing API:
   supportata ufficialmente per qualsiasi contenuto, non solo JobPosting/BroadcastEvent, 2000
   controlli/giorno gratis). Riusa lo stesso service account già configurato per l'Indexing API
   (`GOOGLE_INDEXING_SA_JSON`, nuova funzione condivisa `src/lib/googleServiceAuth.ts`
   parametrizzata per scope), ma richiede un permesso separato: il service account va aggiunto
   come utente nella proprietà Search Console (Impostazioni → Utenti e permessi) — permesso
   fisico sulla proprietà, indipendente dallo scope OAuth. Formato proprietà GSC confermato
   `sc-domain:fourgo.it` (proprietà di tipo dominio, non URL-prefix — trovato riusando lo stesso
   formato già in uso in `gsc-report/route.ts`).
   - **Modalità `?all=1`**: fotografa tutto il pregresso (636 articoli), paginato (`offset`/
     `limit`, default 25 — 100 andava già in timeout) per restare sotto i 300s di maxDuration
   - **Modalità default**: controlla solo articoli pubblicati 14-21 giorni prima, notifica solo
     se il tasso di non-indicizzati supera 30% — intercetta il prossimo problema entro 2-3
     settimane invece di mesi. Cron settimanale, lunedì 07:45 UTC
   - Notifica raggruppa i non-indicizzati per `articleType`, per capire a colpo d'occhio se il
     problema è concentrato su un tipo specifico
   - **Nota per l'interpretazione dei risultati**: gli stati `coverageState` intermedi
     ("Discovered - currently not indexed" vs "URL is unknown to Google") si sono dimostrati
     **instabili** — lo stesso URL può mostrare l'uno o l'altro tra due chiamate a distanza di
     minuti. Solo il flag binario indicizzato/non-indicizzato è affidabile come segnale. Lo
     stato "Crawled - currently not indexed" (diverso — Google ha scansionato e scelto di non
     indicizzare) si è invece dimostrato **stabile** su ripetizione, quindi è un segnale reale;
     trovato su un piccolo gruppo di articoli più vecchi (alcuni `articleType: null`, non
     generati dal sistema automatico) — da approfondire separatamente, non urgente.

### Bounce-check — rilevamento mancato recapito email
Scoperto con un caso reale (Cristina Orsatti, hotmail.it, prenotazione 4GO-2026-KQLC1):
`smtp.sendMail()` risolve con successo quando Aruba **accetta** il messaggio per l'inoltro,
non quando arriva davvero a destinazione. Il vero fallimento (in questo caso: 24h di tentativi
falliti da parte di Aruba nel raggiungere il server Microsoft, nessun codice SMTP specifico,
solo "non è stato possibile contattare il server destinatario") arriva dopo, in modo asincrono,
come email DSN/NDR — invisibile al codice che ha fatto l'invio originale. Verificato con
mail-tester.com (9.5/10, SPF/DKIM/DMARC tutti PASS, zero blocklist) che l'infrastruttura di
invio è sana — il problema è specifico alla connessione verso quel destinatario in quel momento,
non un problema strutturale di reputazione/autenticazione.

Nuovo cron `src/app/api/cron/bounce-check/route.ts` (4×/giorno, 08/12/16/20 UTC) — legge INBOX
di `info@fourgo.it` via IMAP (riusa `IMAP_CONFIG` e pattern già in uso in `email-ai/poll`),
riconosce i veri DSN (RFC 3464: `content-type multipart/report;report-type=delivery-status`,
o mittente/oggetto tipici mailer-daemon/postmaster/Undeliverable), estrae con regex il
destinatario fallito (`Final-Recipient`), il booking collegato se recuperabile (pattern
`4GO-YYYY-XXXXX` nel testo) e il motivo. Notifica tutti gli operatori via Telegram, sposta
l'email in `INBOX.4GO.Bounce` per non rielaborarla. **Da verificare**: se serve anche la
registrazione su cron-job.org oltre a Vercel Cron, coerente con la regola dei cron doppi.

### Altri fix minori
- `selezione-viaggio/page.tsx`: unica pagina cliente pubblica scritta interamente con colori
  hardcoded (`background: '#0a0a1a'` ecc.), zero integrazione col sistema tema chiaro/scuro del
  sito — sempre dark indipendentemente dalla scelta utente. Verificato che il problema è isolato
  a questa pagina (le altre pagine cliente simili, `location`/`trasporto`, sono già ben integrate
  con `var(--...)`). Sostituiti sistematicamente sfondi/bordi/testo con le variabili tema,
  lasciati fissi i colori semantici (badge stagione, verde selezione, rosso prezzo brand) e il
  testo bianco su sfondi sempre scuri (foto, bottoni, header gradiente)
- Biglietto da visita digitale generato via Gamma per la figlia di Inga (modella, passaparola
  in Sardegna estate) — contatto sito in primo piano, impersonale (non nominativo). QR code
  generato localmente (libreria `qrcode` Python, colori brand) e verificato funzionante
  (decodificato e testato prima di consegnarlo)

### Non fatto — in sospeso
- **Sistema voucher recensioni** (€50 per chi lascia una recensione Google): design completo
  discusso e approvato (mockup email pronto, stile "biglietto" con perforazione), ma **non
  implementato** su richiesta esplicita — si riprende quando deciso. Schema: verifica non per
  matching nome (inaffidabile) ma per autodichiarazione nel canale esistente + controllo
  temporale incrociato con recensioni Google reali importate (`gbp-import-reviews`); per
  recensioni spontanee, il prompt di `gbp-reply-reviews` andrebbe esteso con invito a
  contattarci in privato. Modello DB `Voucher`, generatore codice, e la logica di verifica
  restano da costruire.
- **Differenziazione contenuti blog-autogen**: discusso un possibile secondo passaggio AI
  (Haiku, solo per scegliere angolo/apertura tra opzioni date — mai per inventare fatti, per
  evitare il rischio di allucinazione) prima della scrittura vera (Sonnet), per ridurre la
  somiglianza strutturale tra i 660 articoli generati sui 16 template fissi. Non implementato
  — i dati raccolti oggi (indicizzazione quasi perfetta sugli articoli vecchi) suggeriscono che
  il volume fosse la causa dominante, non la struttura — da rivalutare se il problema persiste
  dopo che la riduzione a 3/giorno avrà avuto tempo di agire.
- **develop vs main**: divergenza strutturale ampia (156 commit solo-develop, 300+ solo-main,
  con pattern di cherry-pick paralleli passati che hanno creato hash duplicati per lo stesso
  contenuto su entrambi i lati). Deciso di non sincronizzare ora — resta così fino alla
  conferma SIAE, poi cherry-pick mirato del necessario per il go-live Violetta, non una sync
  completa.

## File chiave aggiunti/modificati oggi

| File | Modifica |
|------|----------|
| `src/lib/waTemplate.ts` → esteso, `src/lib/sendClientNotification.ts` | Template WA clienti |
| `src/lib/emailSignature.ts`, `src/app/api/contact/route.ts` | Logo trasparente + box smerigliato |
| `src/app/(admin)/admin/newsletter/page.tsx` | Handle social, logo, icone reali, scroll libreria, bottoni inline |
| `public/social/whatsapp.png`, `public/logo-4go-fb.png` | Nuovi asset logo |
| `src/app/api/cron/blog-autogen/route.ts`, `vercel.json` | Quota 10→3 |
| `src/app/(admin)/admin/blog-settings/page.tsx`, `src/app/api/cron/blog-check/route.ts` | UI allineata al nuovo modello |
| `src/lib/googleServiceAuth.ts`, `src/app/api/cron/index-check/route.ts` | Monitoraggio indicizzazione (nuovo) |
| `src/app/api/cron/bounce-check/route.ts` | Rilevamento mancato recapito (nuovo) |
| `src/app/(public)/selezione-viaggio/page.tsx` | Fix tema chiaro/scuro |
