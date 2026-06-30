# Sessione 4GO — 30 giugno 2026

## STATO BRANCH (fine sessione)
- **main** `dfb7ba88`-equivalente su `develop`... vedi sotto, branch separati
- **main produzione**: tutti i fix WhatsApp/Duffel/Prisma/sicurezza/GDPR mergiati
- **develop staging**: infrastruttura E2E + Violetta multi-tappa + fix violetta-sub

---

## BUG REALI SCOPERTI E CORRETTI (priorità — impatto produzione)

### 1. WhatsApp — dedup messaggi duplicati (race condition)
Il dedup su `wamid` era un check-then-write non atomico su riga condivisa `SiteSettings`, vulnerabile a race condition con retry ravvicinati Meta. **Fix**: vincolo univoco DB su `WaMessage.wamid` — insert atomico, solo una richiesta riesce in caso di arrivo parallelo. Migrato su staging+produzione.

### 2. WhatsApp AI — allucinazione "Claudio operatore"
Causa reale: il bot riusava `contactName` (il nome del cliente stesso, "Claudio") come se fosse il nome di un consulente umano che lo avrebbe ricontattato — non un'invenzione casuale ma confusione diretta cliente/operatore nel prompt. **Fix**: istruzione esplicita nel system prompt che vieta di riusare il nome del cliente come nome di un operatore fittizio. Operatori reali: solo Massimo, Alessia, Inga.

### 3. WhatsApp — prezzi voli stimati invece di reali (Duffel)
Sharm el-Sheikh e ~30 destinazioni mare (Egitto, Grecia isole, Canarie/Baleari, Portogallo, Croazia, Tunisia, Kenya/Tanzania) mancavano dalla mappa IATA usata per attivare la ricerca Duffel. Il bot stimava prezzi inventati invece di cercare voli reali. **Fix**: mappa centralizzata e ampliata su WhatsApp (eliminata duplicazione codice) e Telegram.

### 4. WhatsApp escalation — notifiche incomplete verso Massimo
Su 3 percorsi di escalation nel webhook, 2 (trigger esplicito "operatore", limite 30 messaggi) non chiamavano mai `sendWA` verso Massimo — solo Telegram+email. **Fix**: aggiunta notifica WA mancante su tutti e 3 i percorsi. Aggiunto anche: update mancante `status: 'ESCALATED'` nel flusso AI-driven (restava "in gestito" anche dopo escalation completata), sostituiti `catch` silenziosi con logging esplicito su `notifyTelegram`/`sendWA` (causa probabile di notifiche perse senza traccia in passato).

### 5. Bug Prisma critico — `/api/preventivo` crashava in produzione
Query selezionava `shortDescription`, campo **inesistente** nel modello `Package` — causava errore Prisma esposto direttamente nella pagina pubblica, preventivi AI generati falliti silenziosamente. Stesso bug minore in `heygen-weekly` cron (description sempre vuota nei video settimanali). **Fix**: uso `subtitle`/`seoDescription` come fallback. Scoperto dal primo run dei test E2E Playwright.

### 6. Cron `inbox-organizer` non era mai stato schedulato
Codice pronto da mesi, ma mai effettivamente collegato a uno scheduler attivo (né cron-job.org né Vercel Cron). Mancava anche `maxDuration` (default 10s, insufficiente per operazioni IMAP batch) — causava timeout silenzioso anche quando testato manualmente. **Fix**: aggiunto a `vercel.json` (lunedì 7:30) come backup ridondante a cron-job.org, aggiunto `maxDuration = 60`.

### 7. Sicurezza accidentale — eliminazione massiva email CRM
Operatore ha cliccato "Elimina tutti" su `/admin/email-ai` per errore (confermato via log Vercel: DELETE con `all=1`, browser reale, referrer corretto — non un attacco). **Fix**: doppia conferma testuale (digitare "ELIMINA"), log esplicito in `ActivityLog` prima dell'esecuzione. Pulsante resta visibile a tutti gli operatori (non solo SUPERADMIN) su richiesta esplicita di Emi.

### 8. `violetta-sub` — bug React strutturale (FormFields ricreato ad ogni render)
`FormFields` era definito **dentro** il componente principale invece che a livello modulo — React lo trattava come componente sempre nuovo, causando perdita di stato su select/input (il piano tornava sempre a "Explorer", il campo "Collega prenotazione" perdeva il valore). **Fix**: spostato a livello modulo.

### 9. `violetta-sub` PATCH — causa reale crash "Errore sconosciuto"
`pricePaid` arrivava come stringa vuota `''` da `editForm` quando il sub non aveva prezzo — `parseFloat('')` = `NaN`, inserito in colonna Postgres `Float`, query falliva silenziosamente (nessun try/catch, Next.js restituiva HTML 500 invece di JSON). **Fix**: gestione esplicita stringa vuota/NaN, aggiunto try/catch con errore reale propagato al frontend.

### 10. `violetta-sub` — doppio controllo auth ridondante e instabile
L'endpoint usava `requireAdminAPI()`/`getServerSession` **in aggiunta** al middleware — pattern noto come instabile in Next.js 15 (già documentato nelle note progetto). Falliva con 401 silenzioso che il vecchio frontend ignorava completamente. **Fix**: rimosso, allineato al pattern stabile già usato con successo in `manual-bookings` (solo middleware).

---

## FEATURE NUOVE

### Multi-tappa Violetta per booking custom (caso reale: Inga/Creta)
Problema operativo reale: un cliente alloggia a Kissamos (Creta) ma visita anche Balos/Elafonissi/Chania — il campo `destination` singolo non bastava, Violetta rispondeva solo sulla città scritta lì. **Soluzione**: nuovo campo `ManualBooking.itineraryCities` (JSON tappe), UI con pulsante "+ Tappa" (stesso pattern UX del Country Vapi esistente), letto dal webhook Telegram come `botNotes` anche senza `Package` collegato. **Live su main e develop**, migrato su staging+produzione.

### Check coerenza piano `violetta-sub` ↔ `ManualBooking.violettaPlan`
`violetta-sub` è solo registro commerciale — il comportamento reale del bot dipende esclusivamente da `violettaPlan` su `ManualBooking`. Rischio di disallineamento (registri "Concierge" su un lato, bot vede "Nessuno" sull'altro). **Soluzione**: check live con avviso quando i due piani non coincidono, pulsante "🔧 Allinea" che propaga sia `violettaPlan` che `interpreteAttivo` (true solo se concierge) sul booking collegato. Link diretto cliccabile tra le due pagine con apertura automatica via `?open=bookingCode`.

### Infrastruttura E2E Playwright + CI
4 suite di test (preventivo, quiz, blog, admin-auth) contro staging.fourgo.it. Workflow GitHub Actions con reporter JSON per estrarre dettagli fallimento nel messaggio TG, link diretto alla run. Cleanup automatico dati di test (`e2e-test-*`/`e2e-quiz-*@fourgo.it`) ad ogni run, pulsante manuale in `admin/sistema`. Pannello "Workflow CI" in admin/sistema per lanciare E2E/Security Scan a richiesta.

### Sicurezza dipendenze — 9 vulnerabilità Dependabot risolte
Next.js 15.5.16→15.5.18 (fix critico: middleware/proxy bypass CVE-2026-45109, rilevante perché il middleware è l'unico gate per `/api/admin/*`). nodemailer/postcss/uuid aggiornati. Aggiunto `overrides` in package.json per forzare versioni patchate anche nelle copie nidificate vulnerabili (mailparser→nodemailer, next→postcss, next-auth→uuid) e nelle transitive (semver, undici). `npm audit`: 0 vulnerabilità. Mergiato su main e develop.

### Documenti GDPR firmati archiviati
DPIA+FRIA (29/06/2026) e Registro Attività di Trattamento (29/06/2026), firmati da Massimo Schilirò, caricati su Vercel Blob tramite nuovo endpoint `/api/admin/gdpr-documents`, link diretti in `/admin/gdpr` sostituendo il generatore dinamico precedente.

---

## INFRASTRUTTURA CI — frequenza ridotta (budget Actions minutes)
**Causa**: 90% consumo mensile raggiunto il 30/06 per via di `security-scan.yml` triggerato su ogni push a `develop`/`main` (ritmo di sviluppo intenso = decine di run/giorno). **Fix**: `security-scan.yml` ora solo lunedì 7:00 UTC + PR verso main + manuale. `e2e-tests.yml` lunedì 7:15 UTC + manuale. Notifica TG su entrambi (successo+fallimento per le run schedulate, solo fallimento per i lanci manuali).

---

## CHERRY-PICK SU MAIN (oggi)
1. `69f39751` — fix bug Prisma preventivo
2. `d43fb500` — dedup WhatsApp + mappa Duffel ampliata
3. `0f9f183f` + `4cb00341` — fix prompt anti-allucinazione "Claudio"
4. `d2b00a72` + `c287e9f1` — fix escalation/status/logging WhatsApp
5. `c785af6d` — fix itineraryCities mancante in editForm (bug introdotto stessa sessione)
6. `ca506cef` — endpoint upload documenti GDPR firmati
7. `9c377317` — feature multi-tappa Violetta (ManualBooking.itineraryCities)
8. `a95c00dd` — fix sicurezza dipendenze (9 vulnerabilità)

## RESTA SU DEVELOP (in attesa go-live Violetta)
- Tutto il flusso `/violetta` pubblico (pagina, checkout, violetta-sub) — inutile su main finché Violetta non è live (noindex attivo, in attesa SIAE)
- Infrastruttura E2E Playwright + workflow CI
- Lingue Violetta aggiuntive, fix Arbat/Rabat, fix intolleranze (da sessione precedente)

---

## NOTE OPERATIVE PER INGA/MASSIMO/ALESSIA

**Compilazione tappe itinerario** (`prenotazioni-manuali` → modifica booking → "🗺️ Tappe itinerario"):
- Cliente con hotel fisso + auto a noleggio (es. Kissamos base + giro Creta): inserisci prima la città dell'hotel, poi le città/zone che visiteranno
- Cliente con itinerario costruito da voi (più hotel, più notti in città diverse): inserisci tutte le tappe in ordine

**Collegamento Violetta-sub ↔ 4GO**: il campo "Collega prenotazione 4GO" su `violetta-sub` è solo per il vostro registro — il comportamento reale del bot dipende dal piano impostato su `prenotazioni-manuali`. Se vedete l'avviso giallo di disallineamento, usate il pulsante "🔧 Allinea" per sincronizzare automaticamente (propaga anche l'attivazione interprete vocale se piano Concierge).

**Pulsante "Elimina tutti" email** (`admin/email-ai`): ora richiede di scrivere "ELIMINA" per confermare — più sicuro contro click accidentali, resta visibile a tutti.

---

## REGOLE INVARIATE
- Branch protection: webhook/route.ts, middleware, auth, pagamenti, schema DB → SEMPRE su develop
- REGOLA ASSOLUTA: leggere file prima di modificare, mai commit speculativi
- Dopo ogni nuova colonna DB: aggiungere a `/api/admin/migrate` E chiamare l'endpoint su staging+produzione
- MAI fidarsi di `.catch(() => ({}))` silenzioso su operazioni critiche — propagare sempre l'errore reale
- `requireAdminAPI`/`getServerSession` in route handler: pattern instabile, preferire solo middleware (secret/cookie)
- Verificare SEMPRE che ogni cron sia effettivamente schedulato (non solo "pronto nel codice") sia su cron-job.org sia su Vercel Cron in vercel.json
