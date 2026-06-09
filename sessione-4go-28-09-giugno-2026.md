# Sessione 4GO-28 — 09 Giugno 2026

## Stack
Next.js 15 + Prisma + Neon PostgreSQL + Vercel Pro | main=prod, develop=staging

---

## LAVORO COMPLETATO

### WhatsApp / Violetta — Bug fix e miglioramenti

**Fix critico: `from` → `phone` in LangSmith trace (`/api/whatsapp/ai`)**
- ReferenceError su `from` causava 500 su ogni messaggio AI
- Violetta non rispondeva a nessun messaggio (inclusa conversazione Mirko crociera Caraibi)
- Commit: `fix: whatsapp/ai - ReferenceError 'from' → 'phone' nel LangSmith trace`

**Fix: salvataggio messaggi outbound su escalation**
- Webhook salvava solo messaggi inbound; le risposte di escalation non erano visibili in admin
- Commit: `fix: WA webhook - salva messaggio outbound anche su escalation`

**Fix: ESCALATE in fondo al reply**
- AI scrive risposta utile + ESCALATE alla fine → testo ESCALATE era visibile al cliente
- Ora: strip ESCALATE dal testo, webhook usa reply AI se presente
- Commit: `fix: ESCALATE in fondo al reply - strip dal testo cliente`

**Fix: history ricostruita da DB se context vuoto**
- Messaggi manuali admin non aggiornano conv.context → AI vedeva conversazione vuota → benvenuto fuori posto
- Ora ricostruisce history dai WaMessage DB quando context è vuoto
- Commit: `fix: webhook WA - ricostruisce history dai messaggi DB se context vuoto`

**Fix: escalation log errori WA+email**
- Errori silenziosi su invio WA a Massimo e email escalation
- Aggiunto logging + usa `MASSIMO_EMAIL` env var
- Commit: `fix: escalation - log errori WA+email, usa MASSIMO_EMAIL env`

**Feat: trigger esplicito "operatore"**
- Parola "operatore"/"consulente" → scala immediatamente senza passare dall'AI
- Notifica Telegram a Massimo, conversazione → ESCALATED
- Commit: `fix: privacy#ai anchor sezione 12, trigger esplicito operatore/consulente`

**Feat: Violetta raccoglie info prima di scalare**
- Trigger Duffel esteso a crociere/preventivi
- Prima fornisce info utili poi ESCALATE con contesto completo
- Commit: `feat: Violetta - fornisce info+voli Duffel prima di escalare`

**Feat: assegnazione operatore con notifica WA+Telegram**
- Da admin social, assegnare Massimo/Alessia/Inga notifica operatore con contesto completo messaggi
- Commit: `feat: assegnazione conversazione WA notifica operatore via WhatsApp + Telegram`
- Upgrade: include tutti i messaggi inbound (non solo lastMessage)
- Commit: `feat: notifica assegnazione WA include contesto completo messaggi inbound`

**Feat: reply manuale da admin social**
- Campo testo + bottone Invia nella conversazione → `/api/admin/wa-send`
- Messaggio salvato nel DB e visibile nella conversazione
- Commit: `feat: social admin - reply manuale WhatsApp dalla conversazione + endpoint wa-send`

---

### Farnesina Banner

**Feat: sezione Avvisi Farnesina in home**
- API JSON ufficiale: `https://www.viaggiaresicuri.it/ultima_ora/totale.json`
- Cache 1h in `SiteSettings.farnesinaCacheData/farnesinaCacheAt`
- Componente `FarnesinaBanner.tsx` con card colorate per tipologia (rosso=salute, arancione=sicurezza, ambra=generico)
- Data visibile, fonte citata, link "Tutti gli avvisi" → viaggiaresicuri.it
- Sviluppato su develop, testato su staging, mergato su main
- Migration: colonne `farnesinaCacheData`, `farnesinaCacheAt`

---

### SERP Scraper fix

**Fix: riconosce solo `fourgo.it` (non vecchio dominio)**
- Aggiunto e poi rollbackato match `agenziaviaggi-fourtravel.it`
- Decision: solo `fourgo.it` conta — vecchio dominio è problema non soluzione
- Commit: `revert: scraper - solo fourgo.it, vecchio dominio non conta`

---

### City Pages SEO

- Generato cityContent AI per 6 città: bollate, cesate, garbagnate-milanese, paderno-dugnano, saronno, senago
- Pagine ora `robots: index:true`
- 200 URL Search Console esaurite — invio manuale rimandato al giorno dopo

---

### Blog autogen

**Fix: vieta date specifiche nel contenuto**
- No anni/mesi per fonti esterne (es. "gennaio 2024 Farnesina")
- Solo formule: "attualmente", "le disposizioni vigenti", "al momento della pubblicazione"
- Commit: `fix: blog-autogen - vieta tutte le date specifiche`

---

### Pacchetti — Mappa

**Fix: ItineraryMap non renderizza se mapCities vuoto**
- Guard: `if (!mapCities?.trim()) return null`
- Ignora geoData residuo in DB da generazioni precedenti
- Commit: `fix: ItineraryMap - mostra solo se mapCities è valorizzato`

---

### GBP Post anti-duplicato

**Fix: match per parole chiave destination**
- Bug: salvava "Canarie" ma il pacchetto aveva "Tenerife" → match non scattava
- Ora confronta tutte le parole chiave ≥4 chars della destination
- Commit: `fix: GBP post anti-duplicato - match per parole chiave destination`

---

### Campagne Email Commerciali

**Feat: pagina admin `/admin/campagne`**
- Model DB: `CampagnaContatto`, `CampagnaTemplate`
- 37 contatti B2B pre-caricati (Alpitour, Costa, Veratour, Quality Group, Boscolo, Going, ecc.)
- Template con variabili `{{nome}}` e `{{azienda}}`
- Invio singolo + bulk send in batch da 5 (evita timeout Vercel 60s)
- From: `info@fourgo.it` (SMTP Aruba), Reply-To: `massimo@fourgo.it`, CC: `info@fourgo.it`
- Firma ufficiale Massimo da `fourgo.it/firme-email`
- Retry 3x su errore 421 Aruba con backoff 5/10/15s, pausa 3s tra mail
- Anteprima personalizzata per ogni contatto
- Risultato primo invio bulk: 27/37 inviate, 11 errori (421 rate limit)

**Template email Violetta**
- Oggetto: *"Il tuo cliente ti chiama alle 2 di notte perché il timezone del suo viaggio è ore avanti?"*
- Corpo: scenario cliente in difficoltà (infortunio, gate cambiato, ristorante chiuso, non capisce la lingua)
- Feature list accurata (no piatti nel riconoscimento immagini, intolleranze chieste in loco, prenotazione telefonica SOON)

---

### AI Act Compliance — Reg. UE 2024/1689

**Scadenza**: 2 agosto 2026 (transitorio fino al 2 dicembre 2026 per sistemi già live)

**Implementazioni:**
- Welcome message Violetta WA: disclosure "sistema di intelligenza artificiale", opzione "operatore"
- Welcome message Violetta Telegram: completo con capacità full + AI Act
- ChatWidget: welcome message AI Act compliant + nota footer con link Informativa AI
- Privacy Policy: nuova sezione 12 "Sistemi di Intelligenza Artificiale", numerazione corretta 1-13, versione 2.1
- Termini e Condizioni: sezione 12 aggiornata con art. 50 Reg. UE 2024/1689
- Footer: link "Informativa AI" → `/privacy#ai`
- Anchor `id="ai"` sulla sezione 12 della privacy page

**Welcome messages differenziati:**
- ChatWidget: "destinazioni e viaggi" (base)
- WhatsApp: "destinazioni, viaggi, voli, ristoranti, meteo, traffico e traduzioni"
- Telegram: tutto + audioguide, riconoscimento immagini, ETA, tasso di cambio

---

### Telnyx KYC — Escalation

- Quarta escalation inviata a `numbering@telnyx.com` (team specifico numeri)
- Requirement Group `93cf51b4` ancora Unapproved — problema lato piattaforma
- 4 numeri italiani tutti in purchase-failed

---

### Itinerario Spagna Nord (Giuseppe Isola, 21-28 giugno)

- Guida completa creata: `/mnt/user-data/outputs/itinerario-spagna-nord.md`
- Gaztelugatxe: link corretto `https://public.tiketa.eus/gaztelugatxe` (prenotazione obbligatoria dal 15 giugno)
- Messaggio WhatsApp preparato per Giuseppe con lista attrazioni + teaser Violetta
- Template risposta a Cicero (competitor) preparato — ribaltamento ruoli

---

### Cicero / Risposta commerciale

- Risposta email preparata: ribaltamento ruoli — 4GO non è il cliente, è il fornitore
- Proposta partnership invece di vendita

---

### GBP / Search Console

- GBP maggio 2026: +75% chiamate, +28% indicazioni stradali, +20% visualizzazioni profilo, +53% ricerche
- Query top: "agenzia viaggi senago" pos.4 (23 imp), "agenzia viaggi" pos.1 (9 imp)

---

## REGOLE OPERATIVE CONFERMATE

- **Branch**: develop → staging → main dopo verifica
- **DB columns**: sempre in migrate endpoint come `ALTER TABLE IF NOT EXISTS`
- **Farnesina**: API ufficiale `ultima_ora/totale.json`, no deep link SPA (404), card senza link
- **AI Act**: disclosure "sistema di intelligenza artificiale" obbligatorio nel primo messaggio
- **"operatore"**: trigger diretto → escalation immediata senza AI
- **ChatWidget**: apostrofi in stringhe JS → usare doppi apici o backtick

---

## PENDING

- [ ] Telnyx KYC: attesa risposta `numbering@telnyx.com`
- [ ] Meta App Review: attesa risposta (26 maggio, ~15 giugno deadline)
- [ ] Search Console: inviare 6 city pages (quota ripristinata domani)
- [ ] Farnesina: deploy develop → merge main (già fatto in sessione)
- [ ] Campagne: merge develop → main (campagne admin su staging)
- [ ] Violetta itinerario Spagna: integrare guida come contesto per Giuseppe
- [ ] noindex `/demo-bot` e `/violetta`: rimuovere post-Telnyx
- [ ] E015 Regione Lombardia API: valutare integrazione POI/eventi per city pages e Violetta
