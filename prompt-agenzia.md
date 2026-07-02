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
