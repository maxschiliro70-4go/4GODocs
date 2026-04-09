# Prompt base — Progetti Claude.ai per operatori 4&GO

> Copia questo prompt nel campo **"Istruzioni progetto"** di ogni progetto Claude.ai.
> Allega il file `catalogo-pacchetti.md` con i pacchetti aggiornati da DB.
> Aggiungi in fondo la **riga personalizzata** specifica per ogni operatore.

---

## PROMPT BASE (istruzioni progetto — unico progetto, 3 chat distinte)

```
Sei l'assistente AI interno di 4&GO FourTravel, agenzia viaggi di Senago (Milano).

AGENZIA:
- Via Alessandro Volta 331 – 20030 Senago (MI)
- Tel: 02 9108 4274 | WhatsApp: +39 366 260 8567
- info@fourgo.it | fourgo.it
- Affiliata Geo Travel Network (Welcome Geo)
- Partner ufficiali: Alpitour, Francorosso, Turisanda, Veratour, Bravo Club, Costa Crociere, MSC Crociere
- Specializzata in viaggi su misura, safari, viaggi di nozze, tour culturali, Fly&Drive, crociere

TEAM:
- Massimiliano Schilirò — Titolare
- Alessia — Consulente Viaggi
- Inga — Consulente Viaggi
- Emiliano — Sviluppatore del sito (non opera in agenzia, contattare solo per problemi tecnici)

TUO RUOLO:
- Aiutare nella gestione delle richieste clienti (email, WhatsApp, telefono)
- Redigere preventivi, email, risposte clienti in modo caldo e professionale
- Suggerire itinerari e soluzioni di viaggio dal catalogo allegato
- Supportare la pianificazione operativa quotidiana
- Rispondere a domande sui pacchetti con informazioni precise su itinerario, inclusi/esclusi, mappa

CATALOGO: vedi file allegato con i pacchetti aggiornati

STRUTTURA PACCHETTO (come leggere il catalogo):
Ogni pacchetto contiene:
- title, subtitle, destination, durationDays/Nights, priceFrom
- itinerary: array di giorni con titolo (formato "Città - Attività") e descrizione dettagliata
- mapCities: città per giorno separate da virgola (usate per la mappa interattiva del sito)
- included/excluded: cosa è compreso o meno
- whyChoose: 5 motivi per scegliere il viaggio
- availability: date e periodi disponibili
- transport, meals, supplements, notes

REGOLE OPERATIVE:
- Parla sempre in italiano salvo diversa indicazione
- Non inventare prezzi o disponibilità — usa solo i dati del catalogo allegato
- Se non hai l'informazione, di' "verifico con il team"
- Tono: caldo, professionale, esperto di viaggi — mai robotico
- Non condividere dati personali dei clienti tra conversazioni diverse
- Per prezzi speciali, sconti o decisioni commerciali importanti: coinvolgi Massimiliano
- I prezzi nel catalogo sono "a partire da" — il prezzo finale dipende da periodo e composizione gruppo

RISPOSTA AI CLIENTI:
- Ogni risposta finisce sempre con UNA sola domanda per mantenere il dialogo
- Raccoglie progressivamente: destinazione → periodo → persone → budget
- Non conferma mai prezzi o disponibilità in modo definitivo
- Se il cliente vuole prenotare: "Passo la tua richiesta a [operatore] che ti contatta entro oggi"
- Per domande sull'itinerario: usa i dati precisi dal campo itinerary del pacchetto
- Per domande sulla mappa: le città visibili nella mappa sono quelle in mapCities, una per giorno

ESCALATION TECNICA A EMILIANO:
Se riscontri un bug, un errore nel sito o hai bisogno di una modifica tecnica,
NON scrivere "contatta Emiliano" in modo generico.
Genera invece questo messaggio strutturato pronto da incollare nella chat con Emiliano su claude.ai:

---
🛠️ SEGNALAZIONE TECNICA 4GO

👤 Operatore: [nome]
📍 Pagina/sezione: [es. /admin/pacchetti → modifica pacchetto ID xyz]
🔴 Problema: [descrizione precisa di cosa è successo]
✅ Comportamento atteso: [cosa dovrebbe fare invece]
🔁 Frequenza: [sempre / a volte / una volta sola]
📱 Dispositivo/browser: [es. iPhone Safari / Windows Chrome]
🏷️ Tipo: [Bug | Modifica | Nuova funzionalità]
📝 Note extra: [tutto ciò che può aiutare a capire il problema]
---

Emiliano ha accesso diretto al codice e al repo — con queste info risolve senza avanti-e-indietro.
```

---

## SETUP PROGETTO

1. Vai su [claude.ai](https://claude.ai) → **Progetti** → **Nuovo progetto**
2. Nome: `4GO FourTravel`
3. **Istruzioni progetto** → incolla il PROMPT BASE sopra (uguale per tutti)
4. **Aggiungi file** → carica `prompt-agenzia.md` (questo file)
5. Crea **3 chat** dentro il progetto, una per operatore:
   - Chat `Massimo` — prima cosa da scrivere: *"Sono Massimiliano, titolare. Ho pieno accesso a prezzi, fornitori e decisioni commerciali."*
   - Chat `Alessia` — *"Sono Alessia, consulente viaggi. Per prezzi speciali coinvolgo Massimiliano."*
   - Chat `Inga` — *"Sono Inga, consulente viaggi. Per prezzi speciali coinvolgo Massimiliano."*
6. Claude impara il contesto di ciascun operatore e lo mantiene nelle rispettive chat

---

## SITO WEB fourgo.it

Il sito è sviluppato e manutenuto da Emiliano su Next.js 15 + Prisma + Neon + Vercel Pro.

### Funzionalità principali

**Catalogo e pacchetti**
- Ogni pacchetto ha una pagina pubblica con descrizione, itinerario filtrable per città, mappa interattiva Google Maps
- La mappa mostra un checkpoint numerato per ogni giorno dell'itinerario (coordinate salvate nel DB)
- I checkpoint sono geocodificati server-side al salvataggio — mai nel browser

**Import pacchetti da PDF**
Flusso AI per creare un nuovo pacchetto:
1. Operatore carica PDF in Admin → AI Pacchetti
2. **Claude Sonnet** estrae: metadata (titolo, prezzo, durata, inclusi/esclusi), itinerario completo con descrizioni dettagliate, luoghi geografici per giorno
3. **Claude Haiku** estrae: città per la mappa (una per giorno) e dati volo se presenti
4. **Google Maps API server-side** geocodifica immediatamente tutte le città → coordinate salvate nel DB
5. **Telegram** notifica l'operatore che ha caricato con report: titolo, prezzo, città geocodificate, avvisi su dati mancanti
6. Operatore verifica il report, apre l'admin, controlla e pubblica

**Formato itinerario**
Il titolo di ogni giorno segue il formato `"Città - Attività principale"`.
La prima parola è sempre la città geografica del giorno — usata dal Telegram bot per rispondere a domande precise.

**Campo mapCities**
Stringa di città separate da virgola, una per giorno (es: `Siviglia, Siviglia, Granada, Córdoba, Malaga`).
Ogni salvataggio del pacchetto rigenera automaticamente le coordinate nel DB tramite Google Maps API.

**Email AI** — info@fourgo.it gestita da Claude Haiku
- Risponde automaticamente in IT/EN/FR/DE/ES
- Escalation a massimo@fourgo.it con label criticità (🔥/🟡/🟢) dopo 4 messaggi o parole chiave di acquisto
- Non conferma mai prezzi o disponibilità

**Bot Telegram @FourGoTravelBot** — assistenza post-vendita
- Accede all'itinerario completo del pacchetto acquistato
- Risponde a domande su giorni specifici, attrazioni, mappa del percorso
- Emergenze: cliente seleziona operatore → notifica + 3×5min retry

**Prenotazione online**
- Pagamento Stripe (carta) + PayPal
- Conferma automatica con bookingCode, link tracking, info Telegram bot

**Admin panel** /admin
- Accesso solo con NextAuth + MFA TOTP
- Import PDF, gestione pacchetti, blog, email AI, calendario, newsletter, social, analytics
- Monitor attività operatori: visite pagine e azioni tracciate per Massimo/Alessia/Inga

**Telegram Chat ID operatori**
Ogni operatore ha il proprio Chat ID Telegram salvato nel profilo admin (Admin → Utenti).
Usato per notifiche personalizzate: import pacchetto, emergenze, segnalazioni.
Per ottenere il proprio ID: aprire @userinfobot su Telegram.

Per segnalazioni tecniche usa il formato strutturato indicato sopra.

---

---

## SESSIONE 2026-04-02 — Sistema Preventivi e Multi Hotel

### Sistema Preventivi (PRV)

**Creazione preventivo** — Admin → AI Generator Preventivi → 4 modalità:
- **Importa PDF**: carica preventivo tour operator → Haiku estrae voci e prezzo totale
- **Genera con AI**: Haiku genera voci realistiche dalla destinazione
- **Manuale**: form libero
- **⚡ Multi Hotel**: carica N PDF hotel → Haiku estrae dati+stelle+pros/cons+rating

**Campi preventivo**: nome/email/telefono cliente, destinazione, data partenza/rientro, acconto %, metodo pagamento (Carta/PayPal/Entrambi/Bonifico/Contanti/Bancomat/Assegno), voci servizi, extra con metodo per-voce, note, gammaUrl.

**Codice**: `PRV-YYYY-XXXXX`

**Flusso PRV:**
1. Operatore crea e verifica il preventivo
2. Clicca "Invia al cliente" → email HTML Outlook-safe con `&euro;`, tabella servizi, acconto, bottone conferma
3. Cliente clicca "Confermo" → sistema genera Stripe checkout session + PayPal → email con link pagamento
4. Stripe webhook intercetta → conversione automatica PRV → 4GO (ManualBooking CONFIRMED + Package BOZZA)
5. Gamma generato automaticamente alla conversione

**Archivia PRV**: bottone 📁 nell'header di ogni card → PRV scompare dalla lista attiva; badge "📁 Archiviati (N)" per visualizzarli.

**Gamma PRV**: bottone "Genera Gamma" → brochure con prezzi e date → apre Gamma in nuova tab e salva link.

---

### Sistema Multi Hotel (PKG)

**Codice**: `PKG-YYYY-XXXXX`

**Flusso PKG:**
1. Carica PDF hotel (drag & drop o click) → Haiku estrae: nome, stelle (cerca su web se mancano), indirizzo, camera, pensione, prezzo, volo, pros, cons, rating (posizione/comfort/qualità/prezzo)
2. Prima pagina di ogni PDF → JPEG → upload Vercel Blob in `4go/pkg-photos/PKG-XXX/hotel-N.jpg`
3. Operatore verifica la pagina `/scegli-pacchetto?code=PKG-XXX` con foto, tabella comparativa, valutazioni, pros/cons
4. Genera Gamma → presentazione con: cover, cosa è incluso, N slide hotel con foto PDF, slide classifica, ultima slide contatti
5. Invia email → tabella hotel con foto Blob + bottone "Vedi le opzioni" + bottone "Guarda la presentazione"
6. Cliente sceglie hotel → status CONFIRMED → link scelta e "Copia link" spariscono
7. Cliente paga → Stripe webhook → auto-conversione PKG → 4GO + pulizia Blob

**Blob cleanup automatico**: al delete PKG o alla scelta del cliente → cartella `4go/pkg-photos/PKG-XXX/` eliminata. Bottone "Pulizia Blob" in admin per cartelle orfane.

**Archivia PKG**: bottone 📁 Archivia su ogni card.

**Pagina /scegli-pacchetto**: sotto layout pubblico con Navbar/Footer del sito, CSS variables tema, foto hotel `objectFit:contain`, tabella comparativa, cerchi score SVG, barre rating, badge "⭐ TOP", bottone "Scelgo questo hotel".

---

### Logo e Gamma

**Logo trasparente**: `public/logo-4go-transparent.png` — PNG con alpha, sfondo rimosso. Usato in tutti i prompt Gamma al posto di `logo-4go-hd.png` (che aveva sfondo nero).

**Ultima slide Gamma**: sfondo NERO, logo centrato max 40% larghezza tutto visibile, testo bianco. Istruzione esplicita nel prompt.

**Polling Gamma**: client-side ogni 3s max 90s via `/api/ai/gamma-poll`. Usa `status === 'completed'` e fallback `https://gamma.app/docs/${id}`.

---

### Struttura cartelle Aruba (info@fourgo.it)

Le email classificate vengono spostate automaticamente via IMAP in:
- `4GO/Spam` → email spam
- `4GO/Scalati` → escalation (cliente pronto all'acquisto)
- `4GO/Gestiti` → POTENTIAL e MANAGED
- `4GO/Chiusi` → thread chiusi
- `4GO/Cortesia` → complimenti/ringraziamenti

**Nota**: le cartelle devono esistere su Aruba (create manualmente una tantum). ✅ Fatto il 2026-04-02.

---

*Ultimo aggiornamento: 2026-04-02*

---

## SESSIONE 2026-04-03 — Documenti Telegram, Bug Fix Build, Emergenze

### Build fix critici

**send-documents/route.ts** — 3 errori sintassi che bloccavano il build:
1. Template literal innestato in `getFirmaAgenzia` dentro `encodeURIComponent()` → SWC chiudeva anticipatamente il template
2. Ternario malformato in `htmlBody` con `}` extra prima di `: ''}`
3. `}` mancante per chiudere la funzione `getFirmaAgenzia`

**Risultato**: tutti i deploy successivi alla sessione 4GO-7 fallivano. Fix applicato nel commit `6328ada`.

---

### Sistema Documenti Telegram (TelegramDocument)

**Flusso completo post-vendita** — Admin → Social AI → tab Telegram:

**Upload documento:**
1. Operatore carica PDF/TXT per la prenotazione del cliente
2. Server salva subito il documento nel DB (senza mappa)
3. **Notifica immediata** `📎 Documento ricevuto ⏳` agli operatori Telegram
4. Estrazione testo + generazione mappa semantica
5. **Notifica finale** `📄 Documento strutturato — N sezioni`

**Estrazione testo (priorità):**
1. `pdf-parse` (nativo, veloce) — se testo ≥ 300 char/pagina → OK
2. Se pdf-parse crasha (`DOMMatrix is not defined` — Vercel Node.js non ha API browser) → **Vision OCR** automatico
3. **Vision OCR**: raw fetch all'API Anthropic con modello `claude-sonnet-4-6` (no beta header per Claude 4.x) — estrae testo da pagine scansionate/immagine

**Strutturazione (semanticMap):**
- `callHaikuForMap()`: Haiku con `max_tokens: 8192`, testo fino a 80.000 char
- Retry automatico con testo dimezzato se JSON troncato/invalido
- Risultato: `{docType, subject, sections: [{category, label, keywords, content}]}`

**Campo botOnly:**
- `true` = solo contesto AI bot, mai inviato come allegato email al cliente
- Auto-true per file `.txt` e `.docx`
- PDF originali: `botOnly=false` (allegabili), TXT estratti: `botOnly=true`
- `send-documents` filtra `botOnly: false` per non inviare doc bot-only al cliente

**Limiti Vercel:**
- Request body max 4.5MB → PDF > 3.2MB: base64 inviato per OCR ma fileUrl non salvato in DB (flag `_largeFile`)
- `maxDuration = 300s` (max Vercel Pro) per tg-documents route
- Client timeout: 280s per upload e remap

**Bottone 🔄 Rigenera mappa:**
- PATCH `remap: true` → ri-estrae testo da `fileUrl` se `extractedText` è vuoto
- Poi rigenera `semanticMap`
- Risposta include `remapResult: {hasText, hasMap, chars, error}`

**UI card documento:**
- Badge `📝 Xk car.` (testo estratto) / `📝 No testo` (PDF scansionato o vuoto)
- Badge `🗂 N sez.` (mappa OK) / `⚠️ No mappa` (fallita)
- Toggle `🤖 Solo bot` / `📎 Allegabile`
- Bottone `+ Prenotazione` con edit inline per associare bookingCode dopo upload
- Tab Telegram si apre automaticamente da `/admin/social?uploadBooking=4GO-...`

**GET documenti slim:** restituisce `extractedText` come lunghezza numerica (non il contenuto) per evitare payload enormi.

**Bot Telegram — comportamento:**
- Cerca in DOCUMENTI CARICATI prima di rispondere a domande su voli, orari, itinerari
- Label sezione documenti include: "voli, hotel, assicurazione, noleggio, itinerario dettagliato"
- Termini ambigui (es. "compagnia") → interpreta nel contesto viaggio, risponde senza chiedere chiarimenti
- Fallback senza semanticMap: usa `extractedText.slice(0, 15000)`

---

### Sistema Emergenze Telegram

**Flusso emergenza cliente:**
1. Cliente scrive "emergenza" o preme /emergenza
2. Sceglie operatore (Massimo o Alessia dalla tastiera)
3. **TUTTI e 3 gli operatori** ricevono notifica privata:
   - Operatore scelto: `emergencyMsg` completo con `/risolto_CHATID`
   - Altri 2: messaggio osservatore 👁 con `/risolto_CHATID` di backup
4. **Gruppo operatori**: notifica con bottone inline `[✅ Risolto] [📞 Info]`
   - ✅ Risolto: aggiorna sessione ESCALATED → ACTIVE, manda conferma al cliente, edita il messaggio nel gruppo
   - 📞 Info: popup con chatId, bookingCode, stato sessione

**Callback `em:resolve:CHATID`** — gestito nel handler `callback_query`.

---

### Altro

**Gemini image generation** — modello aggiornato:
- `gemini-2.0-flash-exp-image-generation` → `gemini-2.0-flash-preview-image-generation` (deprecato)
- Fix in `/api/admin/generate-image/route.ts`

---

*Ultimo aggiornamento: 2026-04-03*

---

## SESSIONE 2026-04-03 sera — Preventivi completo + Multi Hotel separato

### Flusso Preventivi (PRV) — completo

**Stati:** `DRAFT → SENT → CONFIRMED → PAID → CONVERTED`

**Import PDF → voci:**
- `pdf-parse` estrae testo lato client (pdf.js), Haiku genera voci con prezzi
- Voci a 0 se non riconosce il prezzo
- Totale PDF mostrato come voce indicativa non modificabile ("Totale preventivo indicativo rif. PDF €X")
- Il totale delle voci deve matchare il totale PDF

**Invio al cliente:**
- Email con Gamma brochure linkata (prompt aggiornato: slide finali + foto reali + theme completo)
- Link conferma: `/api/admin/preventivi/confirm?code=PRV-...&token=...`

**Pagina approvazione `/preventivo-confermato`:**
- Riepilogo: nome cliente, destinazione, partenza, totale, acconto 30%
- Link brochure Gamma
- Bottoni pagamento Stripe/PayPal per acconto
- Badge verde "Acconto ricevuto" se già PAID

**Webhook Stripe → PAID:**
- Metadata `preventivoId` nei link generati da `confirm/route.ts`
- Webhook legge `preventivoId` → status `PAID`, `paidAt`, `depositPaid`
- NON converte più a CONVERTED automaticamente (operatore decide)
- Notifica Telegram a tutti 3 operatori: "💳 Acconto PAID"

**Pannello post-PAID nell'admin:**
- Riquadro verde: Totale originale / Acconto pagato / Residuo da pagare
- Voci extra aggiungibili (label + importo) via `prompt()` inline
- Per ogni extra: genera link Stripe/PayPal o segna pagato manualmente
- Residuo si riduce automaticamente man mano che extra vengono pagati
- Link saldo finale generato su richiesta

**Schema Preventivo (nuovi campi):**
- `paidAt DateTime?` — quando acconto pagato
- `depositPaid Decimal?` — importo acconto effettivo
- `extraItems Json?` — voci extra post-pagamento `[{id,label,price,paid,stripeLink,paypalLink}]`
- Migration: `20260403180000_preventivo_paid_extras`

**Fix UX:**
- Form modifica/creazione ora appare SOTTO la lista preventivi (non sopra)
- Bottone Modifica nascosto per status PAID/CONVERTED
- Crea 4GO visibile anche da status PAID
- Colori: tutti i #002366 fissi → var(--text-primary) / #0ea5e9 (visibili su tema scuro)

**API pubblica preventivo:**
- `GET /api/preventivo/info?code=PRV-...` → dati sicuri (senza items/prezzi interni)

---

### Multi Hotel PKG — pagina separata

**Posizione nel nav:** Pacchetti → Multi Hotel PKG → `/admin/multi-hotel`

**Caratteristiche:**
- Pagina completamente separata dai preventivi
- Step 1: Associa PRV (opzionale) — tutti i PRV non archiviati (qualsiasi stato, inclusi CONVERTED)
  - Il PKG può essere voce extra aggiuntiva di un PRV già convertito
  - Auto-compila: clientName, clientEmail, destination, date
  - Nome/email read-only se PRV associato
  - Pulsante "Rimuovi" per compilazione manuale
- Step 2: Dati cliente (manuali o auto-compilati, destination/date sempre modificabili)
- Step 3: Carica PDF hotel (drag&drop multiplo, stessa logica di prima)
- Salva → link `/scegli-pacchetto?code=PKG-...` + Copia link + Nuovo PKG

**Associazione PRV → PKG:** il campo `preventivoId` e `preventivoCode` vengono passati al backend `preventivi/package` POST.

---

*Ultimo aggiornamento: 2026-04-03 sera*

---

## SESSIONE 2026-04-04 — Fix vari + Gemini newsletter + Preventivi UX

### Gemini image generation
- Modello definitivo: `gemini-3.1-flash-image-preview` (Nano Banana 2)
- Campo chiave rimosso dalla newsletter — il server legge `googleAiApiKey` da `SiteSettings` DB
- Newsletter: solo campo prompt visibile + bottone ✨ Genera foto
- Route `/api/admin/generate-image`: fallback automatico a chiave DB se non passata dal client

### Preventivi — fix UX
- Elimina preventivo: sparisce immediatamente dalla lista (ottimistico) + confirm mostra codice PRV
- Status PAID aggiunto con colore verde nelle card
- Bottone Modifica nascosto per PAID/CONVERTED

### Documenti Telegram — fix OCR
- `remapDoc` nel client: rimosso AbortController (killava il fetch prima del completamento)
- `load()` ora sempre nel `finally` → badge si aggiorna anche dopo errori
- Badge documenti prenotazioni: filtro `!d.botOnly` — solo PDF allegabili contano nel badge

### Multi Hotel PKG
- Dropdown preventivi: mostra TUTTI i PRV non archiviati inclusi CONVERTED
  (il PKG può essere voce extra di una pratica già chiusa)

### Prompt Gamma — gamma-brochure PRV aggiornato
- Slide finali aggiunte: `## Prezzi e Prenotazione` + `## 4&GO FourTravel` con layout NERO
- `additionalInstructions`: foto reali luoghi, cover iconica destinazione
- Theme esteso a 9 destinazioni (allineato a generate-gamma-auto)

### Emergenze Telegram
- Tutti e 3 gli operatori ricevono notifica privata (non solo l'operatore scelto)
- Gruppo: bottone inline ✅ Risolto con callback `em:resolve:CHATID`
- Operatori non scelti ricevono messaggio osservatore con `/risolto_CHATID` di backup

---

*Ultimo aggiornamento: 2026-04-04*


---

## SESSIONE 2026-04-04 sera — PRV completo: Gamma PDF, Telegram auto, voci extra, pacchetto da PRV

### Gamma brochure PRV (gamma-brochure/route.ts)
- **Haiku pre-processa** il testo grezzo PDF → markdown strutturato con sezione `##` per ogni tappa/giorno
- **textMode: preserve** — Gamma rispetta la struttura Haiku senza riscrivere
- **numCards dinamico** — conta le sezioni `##` del markdown (target: 30-32 per PDF complessi)
- Passa `clientName` e `totalAmount` → inclusi nella brochure
- `exportAs:'pdf'` rimosso (causava `status:pending` infinito)
- Polling client-side max 150s via `/api/ai/gamma-poll`
- Logo finale: istruzione doppia (inputText + additionalInstructions) per sfondo nero + logo centrato 30%

### PreventivoDocument — testo estratto in DB
- PDF importato → pdf.js estrae testo client-side → salvato in `PreventivoDocument.extractedText` (solo DB, no Blob)
- Gamma usa il testo estratto come `richContent` (max 25k chars)
- Al salvataggio PRV (nuovo o esistente): testo automaticamente salvato senza UI separata

### Conversione 4GO → TelegramDocument automatico
- Alla conversione PRV → 4GO: se `gammaExportUrl` disponibile → scarica PDF Gamma → crea `TelegramDocument` associato al `bookingCode`
- Bot ha subito il contenuto disponibile

### Bottone "Genera Pacchetto Viaggi" (solo CONVERTED)
- Route `/api/admin/prv-to-package`
- Haiku estrae itinerario da testo PDF PRV (omette prezzi/costi/date specifiche)
- Crea `Package` DRAFT in Pacchetti Viaggi

### Voci extra post-PAID
- Selezione metodo pagamento per ogni voce: Stripe / PayPal / Entrambi
- Link Stripe include `extraItemIds` nei metadata → webhook spunta voci automaticamente
- PayPal → segna pagato manuale operatore

### Cron prv-autoarchive
- URL: `/api/cron/prv-autoarchive?secret=4go2026`
- Archivia PRV con `returnDate` > 30 giorni fa e status != CONVERTED
- Elimina `PreventivoDocument` records (solo DB)
- Da aggiungere su cron-job.org: `0 9 * * *`

### UX preventivi
- Barra ricerca sticky in cima alla lista
- "Rigenera Gamma" bottone viola nel form (solo in modifica)
- Un solo bottone Gamma viola nella card quando non in modifica
- "Totale voci" mostrato sotto "Totale preventivo indicativo" nel form
- La voce "rif. PDF" non entra nel calcolo "Totale voci"

### QR Telegram nelle email
- Colore rosso `#D10000` su sfondo bianco
- Punta a `fourgo.it/bot?code=4GO-...` (pagina di atterraggio)
- Pagina `/bot`: deep link `tg://`, App Store/Play Store, codice in grande

---

*Ultimo aggiornamento: 2026-04-04 sera*

---

## SESSIONE 2026-04-06 — PRV UX, Gamma fix, saldo pagamenti

### Fix UX Preventivi
- Barra ricerca: spostata in cima alla lista (era in fondo), rimossa duplicata
- Bottone Gamma card: rimosso grigio duplicato — rimane solo viola
- In modifica: bottone diventa "Rigenera Gamma" viola
- Totale voci: mostrato sotto "Totale preventivo indicativo", somma tutti gli items escluso rif.PDF, aggiornato live
- numCards Gamma PRV: fisso 32

### Gamma PRV — estrazione dati reali
- Haiku istruito a estrarre dati ESATTI dal testo (orari voli, nomi hotel, stelle, treni) — può migliorare la presentazione ma NON omettere nulla
- 30 sezioni esplicite nel prompt Haiku per garantire copertura completa
- textMode: condense→preserve (condense causava perdita dati)
- Testo PDF estratto client-side (pdf.js) salvato in PreventivoDocument al salvataggio PRV

### Gamma — nome documento
- Tutte e 3 le route (gamma-brochure PRV, generate-gamma-auto PKG, package multi-hotel) passano `name` a Gamma
- Formato: `PRV-2026-NNOVG · 06/04 14:32` — codice + data + ora, unico ad ogni rigenerazione
- Cercabile in Gamma.app

### Pannello POST-PAID — saldo pagamenti
- Riepilogo: Extra aggiunti / Extra pagati (verde) / Saldo finale da incassare (rosso)
- Saldo si aggiorna ad ogni azione (aggiungi voce, segna pagato, rimuovi)
- Bottone ✕ per rimuovere ogni voce extra
- Bottone "Genera link saldo (€X)" sul totale residuo esatto
- Formula: Totale pacchetto - Acconto pagato + Extra aggiunti - Extra pagati = Saldo finale

### Gamma folder
- API Gamma non supporta creazione folder via API — solo lista (GET /folders)
- Soluzione adottata: nome documento con codice PRV/PKG + data+ora

---

*Ultimo aggiornamento: 2026-04-06*

---

## SESSIONE 2026-04-07 — 4GO-9: fix vari, nearby search Telegram, Nano Banana, aggiornamento automatico Gamma

### Fix impostazioni admin
- Saldo crediti Gamma: campo dinamico aggiornato da `gamma-poll` dopo ogni generazione (`gammaCreditsRemaining`, `gammaCreditsUpdatedAt` in SiteSettings)
- Sezione Anthropic: rimosso budget manuale, consumo mensile dinamico con soglie fisse (€8 warning, €13 critical)
- Sezione Gamma: rimosso budget manuale, notifiche Telegram su crediti reali (<1500 warning, <500 critical)
- Saldo Gamma editabile manualmente in impostazioni come fallback

### Nano Banana (admin/nanobanana)
- Nuova pagina `/admin/nanobanana` solo per Inga
- Generazione immagini AI con Gemini (stesso engine newsletter)
- Galleria creazioni con download e eliminazione
- Logo 4GO watermark automatico su ogni immagine (blend:screen, base64 embedded nel codice)
- Immagini salvate in Blob sotto `4go/nanobanana/`

### Telegram nearby search (posizione GPS)
- Bot intercetta keyword: ristorante, cena, pranzo, bar, aperitivo, cocktail, cosa vedere, museo, attrazione, discoteca, nightlife, shopping, negozi, souvenir
- Risponde con pulsante "Condividi posizione" → cliente manda GPS → Google Places Nearby Search (raggio 600m) → 3 risultati con rating, stato aperto/chiuso, link Maps
- Funziona solo per sessioni ACTIVE (cliente con prenotazione)
- Regola sistema prompt: bot non risponde a meteo/info su destinazioni fuori itinerario
- Keyword aggiornate in /help e nei messaggi di benvenuto/interim
- Variabile: `NEXT_PUBLIC_GOOGLE_MAPS_KEY` (Places API abilitata richiesta)

### Sync docs operatori (4GODocs)
- Repo pubblico `maxschiliro70-4go/4GODocs` con 4 file .md
- GitHub Action `sync-docs.yml` su `4-GO` sincronizza automaticamente ad ogni push su `docs/*.md`
- Token `DOCS_SYNC_TOKEN` come secret GitHub su `4-GO`
- Progetti Claude.ai Massimo/Alessia/Inga collegati al repo via connettore GitHub

### Progress bar Gamma PRV
- Polling aumentato a 100 iterazioni (300s)
- Barra di avanzamento con step e percentuale durante generazione
- maxDuration 120s sulla route gamma-brochure

### Aggiornamento automatico Gamma al cambio hotel
- Flusso: cliente sceglie hotel → notifica operatore **originale** (chi ha creato il PKG, non sempre Massimo) via Telegram con bottone inline
- `PreventivoPackage` ora salva `preventivoId` e `createdBy` (massimo/alessia/inga)
- Bottone "🎨 Aggiorna brochure Gamma" → rigenera PRV con `hotelOverride` iniettato nel prompt Haiku
- Se PKG aveva già Gamma generato → rigenera automaticamente anche quello
- Migration: `preventivoId TEXT`, `createdBy TEXT` su `PreventivoPackage`

### Multi-selezione extra PRV
- Checkbox su ogni voce extra positiva in PRV CONFIRMED
- "Seleziona tutti" / "Deseleziona tutti"
- Selezione 2+ voci → pannello "Genera link unico" con totale aggregato
- Singolo link Stripe/PayPal per tutte le voci selezionate

### Fix vari
- Confirm su tutti i cestini (calendario aggiunto)
- Gamma PRV: rimosso campo `name` non supportato da API v1.0
- model Haiku corretto: `claude-haiku-4-5-20251001`
- Limiti testo Gamma PRV: 40k chars input Haiku, maxDuration 120s

---

## SESSIONE 2026-04-07/08 — 4GO-10: fix critici, nuove feature, SEO, prenotazioni manuali

### Fix getServerSession → getToken
Tutte le route API che usavano `getServerSession()` senza `authOptions` crashavano in produzione con `NO_SECRET`. Sostituite con `getToken()` da `next-auth/jwt` in: packages, activity, email-reply, blog, blog/[id], social-post, send-newsletter, preventivi/package, generate-gamma-auto.

### Fix additionalEmails ManualBooking
Campo `additionalEmails` (formato `Nome Cognome <email>, ...`) su prenotazioni manuali. Problema: la lista prenotazioni veniva da `/api/admin/bookings` (non `/api/admin/manual-bookings`) — raw query aggiunta al route corretto. Lezione: tracciare sempre il flusso UI→fetch→route prima di modificare.

### Email partecipanti aggiuntivi
- Email inviata automaticamente a tutti i partecipanti aggiuntivi al cambio stato
- Bottone "📧 Invia a partecipanti" con checkbox per selezione individuale
- Bottone "✓ Segna già inviata" per marcare senza inviare (prenotazioni storiche)
- Campo `emailedParticipants` (JSON array) traccia chi ha già ricevuto → checkbox disabilitato con "✓ già inviata"
- Flag `_onlyAdditional` e `_selectedEmails` nel PATCH per invio selettivo

### QR email → vercel.app durante COMING_SOON
`buildQrSignature` usa `4go-gamma.vercel.app` se `COMING_SOON=true`, `fourgo.it` dopo il lancio. Middleware: `/bot` bypass coming soon per QR già inviati ai clienti.

### Fix FK violation Package.createdById
`createdById` FK verso `User` ma operatori sono in `AdminUser` → impostato sempre `null`.

### Multi-hotel PKG
- Notifica all'operatore **originale** (createdBy: massimo/alessia/inga) invece di sempre Massimo
- Scelta pagamento acconto/totale dal cliente su `/scegli-pacchetto` → `paymentChoice` salvato in DB
- Admin genera **un solo link** (acconto o totale) in base alla scelta del cliente
- `PkgPaymentBlock` come componente separato (fix crash lista)

### Gamma PRV → ManualBooking
Alla conversione PRV→4GO: `gammaUrl` e `gammaText` copiati nel ManualBooking. Bot usa `gammaUrl` per mandare brochure e `gammaText` come contesto itinerario per rispondere a domande specifiche.

### Gamma auto-generazione
- `generate-gamma-auto`: rimosso campo `name` (Gamma API 400)
- `resultRef` sincronizzato con state per evitare stale closure in `savePackage`
- Progress bar visibile indipendentemente da `showVisual`
- Gamma si avvia automaticamente dopo salvataggio bozza da import PDF

### Import pacchetti (parse-document)
- `availability`: sempre stringa vuota (no date importate)
- `priceFrom`: prezzo totale, non per persona
- `passportRequired`: false se CI sufficiente (Albania, EU)
- `ecoData` completo: co2KgPerPerson, ecoRating, localEconomy, flightHours
- `visual` completo: primaryColor, secondaryColor, fontHeading, mood, coverTagline
- No email tour operator nel testo estratto

### Palette e font per destinazione (generate-gamma-auto)
Stile visivo specifico per: Africa/Safari, Giappone, Vietnam/Asia, Maldive/Caraibi, Italia/Grecia, Giordania/Marocco, Norvegia/Islanda, Albania, Spagna, USA/Florida, Portogallo, America Latina, Francia, Austria/Vienna, Praga/Boemia, Budapest/Balkani, Polonia, Olanda, Germania, Svizzera, UK, Scandinavia, Russia, Turchia, Egitto, India, Cina, Corea, Australia, Canada, Messico.

### SEO pacchetti
- Schema JSON-LD: `@type: ['Product', 'TouristTrip']` + itinerary + ecoData CO2 + touristType
- Blog autogen linka al pacchetto correlato con slug reale
- Pagina pacchetto mostra sezione "📖 Leggi anche" con blog correlati per destinazione

### Pacchetti admin
- Filtro città nel dropdown lista pacchetti
- Badge Gamma cliccabile per pacchetti con gammaUrl
- Bottone "Crea PRV" da pacchetto (icona FileText)
- Link "Visualizza" punta a vercel.app (fourgo.it in coming soon)
- Tipo viaggio: aggiunti "città" e "avventura" in AI generator, modifica pacchetto, filtro pubblico

### Migration ecoData.localEconomy
Backfill automatico di `localEconomy` su tutti i pacchetti esistenti in base a countryCode/destination. Albania/EU ~60-65%, USA/Canada ~30%, Asia ~50%, Dubai ~25%.

### Fix vari
- `NEXTAUTH_URL` → `fourgo.it` su Vercel ✅
- URL fallback in code da vercel.app → fourgo.it (eccetto logo Gamma — da fare post-lancio)
- Email firme: logo da vercel.app → fourgo.it
- Statistiche: `ActivityLog` table creation in migration + `getToken` fix
- Città tripType: match `citta↔città` in PacchettiClient
- PKG lista multi-hotel: crash risolto con `PkgPaymentBlock` separato

---

## SESSIONE 2026-04-08 — 4GO-11: operazioni background, mappe, blog, Social AI

### Pannello operazioni globale (background)
- `OperationsContext` + `OperationsPanel` fisso in basso a destra
- Gamma/import PDF/mappe continuano anche navigando tra le pagine admin
- Auto-save bozza PRIMA di avviare Gamma (fix stale closure con `savedIdRef`)
- `PATCH /api/admin/packages/[id]` per aggiornamenti parziali (gammaUrl, status, ecc.)
- Al completamento Gamma: link "📦 Apri in lista pacchetti →"

### Streaming SSE parse-document
- `parse-document` risponde con SSE stream: 15% → 45% → 85% → 100%
- Progress bar in tempo reale durante import PDF pacchetti
- `readSSEStream()` helper nel client

### Tipi viaggio allineati
- Costante `TRIP_TYPES` in `src/lib/tripTypes.ts` (15 tipi: individuale, gruppo, partner, fly&drive, nozze, crociera, città, cultura, enogastronomia, divertimento, lusso, natura, benessere, adrenalina, studio)
- Sincronizzata tra FilterBar pubblica e tutti i dropdown admin

### Import pacchetti — nuove feature
- Notifica Telegram a tutti gli operatori al salvataggio di un nuovo pacchetto
- `createdByName` (String?) su Package — tag autore nella lista pacchetti
- `packageCode` nel PRV creato da pacchetto (badge amber 📦)
- `gammaText` (Text?) su Package — estratto dalla brochure Gamma per il bot Telegram
- Bot Telegram: fallback `Package.gammaText` se `ManualBooking` non ha gammaText
- Geocoding con `countryCode` come filtro Google Maps (evita match errati es. Cipro→Tanzania)

### ItineraryMap — fix definitivi
- `ItineraryMap` include sia `type:'city'` che `type:'attraction'` come marker numerati
- Skip localStorage se `geoData` dal DB è disponibile (no più cache stantia)
- Cache key include lunghezza geoData → si invalida automaticamente se DB cambia
- `geocodedStops` state esposto per link Google Maps
- Google Maps link usa `lat,lng` coordinate reali (non nomi città) + dedup
- Chip filtro città visibile anche con una sola città (city break)

### Restore geoData
- Endpoint `/api/admin/restore-geodata`: estrae attrazioni dall'itinerario via Haiku + geocodifica
- Bottone "📍 Ripristina attrazioni" in `/admin/sistema`
- Salta automaticamente pacchetti con attrazioni già presenti (filter `type:'attraction'`)
- Limit 20 pacchetti per run

### Blog — dedup e foto
- `packageSlug` + `articleType` su modello `Post` come chiave univoca di deduplica
- `blog-autogen` controlla combo `packageSlug|articleType` prima di generare
- Query immagini: `"${location} city"` invece di `"travel landscape"` (risultati più precisi)
- Bottone "🧹 Pulisci blog" in `/admin/sistema` (dedup + fix foto da pacchetto corrispondente)
- `destinationImages.ts` aggiornato con tutte le città europee principali

### Social AI — documenti
- Documenti raggruppati per prenotazione con accordion (chiusi di default)
- Sezione "🤖 Utenti che hanno avviato il bot" visibile solo SUPERADMIN
- Dropdown pacchetti mostra tutti i pacchetti (rimosso filtro `booked=1`)

### Sezione Sistema — Manutenzione Contenuti
- "🖼️ Avvia verifica" — controlla immagini rotte
- "🗺️ Ricalcola mappe" — rigenera geoData da mapCities
- "📍 Ripristina attrazioni" — rigenera attrazioni da itinerario via AI
- "🧹 Pulisci blog" — dedup post e fix foto

### Performance
- `/preventivo-confermato` convertita da client-side a SSR
- `/login` logo con `priority` e dimensioni ottimizzate
- Hero carousel parte da indice random, carousel2 da indice 2

### Migration (4go2026) — nuovi campi
```sql
ALTER TABLE "Package" ADD COLUMN IF NOT EXISTS "createdByName" TEXT;
ALTER TABLE "Package" ADD COLUMN IF NOT EXISTS "gammaText" TEXT;
ALTER TABLE "Post" ADD COLUMN IF NOT EXISTS "packageSlug" TEXT;
ALTER TABLE "Post" ADD COLUMN IF NOT EXISTS "articleType" TEXT;
ALTER TABLE "Preventivo" ADD COLUMN IF NOT EXISTS "packageCode" TEXT;
```

### Lezioni apprese
- **Non toccare ItineraryMap/geoData senza leggere le sessioni precedenti** — il geoData include type:'city', type:'attraction', type:'optional' e tutti devono essere mostrati
- **Regeocode sovrascrive le attrazioni** — usare solo per pacchetti con geoData assente/sbagliato
- **savedIdRef** invece di `savedId` in closures async per evitare stale state
- **PATCH vs PUT** — verificare sempre quale metodo HTTP espone la route

---

## SESSIONE 2026-04-08 pomeriggio — fix mappe, Social AI, email, blog

### Google Maps link
- `ItineraryMap`: link "Apri in Google Maps" usa `lat,lng` reali da `geocodedStops` (non nomi città)
- Deduplicazione coordinate identiche prima di costruire il link
- Il link mostra il percorso completo con tutte le attrazioni del viaggio

### Import PDF → geoData con attrazioni
- Il geoData viene salvato correttamente con `type:'city'` e `type:'attraction'` durante l'import
- Skip localStorage se `geoData` DB disponibile → niente cache stantia dopo import
- Cache key include `_${geoData.length}` → invalidazione automatica se DB cambia
- Bottone "📍 Ripristina attrazioni" in Sistema: salta pacchetti con `type:'attraction'` già presenti

### Email prenotazione — QR Telegram
- Font grandi, colori compatibili tema chiaro
- Testo "👆 copia questo codice, poi apri Telegram" sotto il codice prenotazione
- Testo non in corsivo
- Bottone Apri assistente: 15px, padding 12px 22px, rosso 4GO
- Nota finale non in corsivo: "L'assistente virtuale ti accompagnerà durante il viaggio..."

### Social AI — nuove feature
- Documenti raggruppati per prenotazione con accordion (chiusi di default, clicca per aprire)
- Sezione "🤖 Utenti che hanno avviato il bot" — solo SUPERADMIN, in fondo alla tab Telegram
  - Mostra nome, codice prenotazione, stato sessione, data
  - Sessioni con `state !== 'WAITING_CODE'` (chi ha effettivamente avviato il bot)
- Pacchetti associabili a documenti: tutti i pacchetti (non solo quelli con prenotazioni)

### Blog — foto e dedup
- Foto blog: se destinazione non in `destinationImages.ts` → query Pexels con `"${location} city"`
- `destinationImages.ts` arricchito: Berlin, Vienna, Paris, Brussels, Tallinn, Tirana, Cipro, Helsinki, Copenhagen, Sofia, Florida, Chicago
- Bottone "🧹 Pulisci blog" elimina duplicati per destinazione (mantiene più recente)
- Foto copiate automaticamente dal pacchetto corrispondente

### Performance
- Carousel homepage: hero da indice random, carousel2 da indice 2
- Hero ridotto da 80/85vh a 65/72vh per vedere il contenuto subito sotto
- `/preventivo-confermato`: SSR invece di client-side fetch (score Speed Insights 22→89 desktop)

*Ultimo aggiornamento: 2026-04-08*

---

## SESSIONE 2026-04-08 sera — fix minori

### Brevo contacts — fix conteggio
- `brevo-contacts` filtrava tutti i contatti restituiti dall'API senza verificare `listIds.includes(7)`
- Brevo può restituire contatti con `listIds:[]` (rimossi dalla lista ma non eliminati)
- Fix: `.filter(c => c.listIds.includes(7))` prima di concatenare → conteggio allineato a Brevo

### Ripristina attrazioni — fix loop infinito
- Bottone "📍 Ripristina attrazioni" skippava sempre gli stessi 10 pacchetti già aggiornati
- Fix: filtro `type:'attraction'` già presenti nel geoData + limit aumentato a 20

### Google Maps link ItineraryMap
- Link "Apri in Google Maps" usava `stops` state (vuoto al render) → URL vuoto
- Fix: `geocodedStops` state esposto dall'useEffect e usato nel link
- Filtro city-only rimosso: ora include tutte le attrazioni con dedup per coordinate identiche

*Ultimo aggiornamento: 2026-04-08*

---

## SESSIONE 2026-04-09 — Proposte Viaggio (TravelProposal)

### Nuovo modello: TravelProposal
- Modello Prisma separato da PreventivoPackage e Multi Hotel PKG
- Route admin: `/api/admin/travel-proposal` (GET lista, POST actions, DELETE)
- Route pubblica: `/api/proposta-viaggio?code=XXX` (no auth)
- Route conferma: `/api/proposta-scegli` (GET, salva selezioni)
- Admin: `/admin/proposte-viaggio`
- Cliente: `/proposta-viaggio?code=XXX` (whitelisted nel middleware)

### Flusso operativo
1. Admin carica PDF programma viaggio (multi-tappa, hotel + treni)
2. Claude Sonnet legge il PDF visivamente (base64, `anthropic-beta: pdfs-2024-09-25`) ed estrae:
   - `legs[]`: destinazioni con hotel, prezzi, ratings
   - `transports[]`: treni con orari, durate e prezzi per classe (economy/business/executive)
3. Admin inserisce dati cliente (email non obbligatoria), salva
4. Genera brochure Gamma (tutti gli hotel per tappa + tutti gli orari treni)
5. Invia email al cliente con link scelta e link Gamma
6. Cliente sceglie: ordine cronologico (treno→hotel→treno→hotel→treno)
   - Ogni cella prezzo è cliccabile → seleziona orario + classe insieme
   - Se partenza entro 30gg: solo pagamento totale (no acconto)
   - Totale = hotel selezionati + trasporti selezionati + eventuale assicurazione
7. Operatore: genera link pagamento (Stripe/PayPal acconto+saldo) o "Pagato in agenzia"
8. Extra items post-conferma: aggiungi voci nome/importo, genera link pagamento separato per ciascuna

### Struttura dati transports
```json
{
  "label": "Milano Centrale → Napoli Centrale",
  "date": "2026-10-03",
  "options": [{
    "dep": "06:40", "arr": "11:48", "duration": "5h 8m",
    "prices": {
      "economy": { "economy": 66.90, "business": 83.90, "executive": 109.90 },
      "flex": { "economy": 94.90, "business": 119.90, "executive": 159.90 }
    }
  }]
}
```

### Note tecniche
- PDF parsing: `FileReader.readAsDataURL` lato client → base64 → Claude API con `type: 'document'`
- Prezzi nel PDF sono in tabelle visive (non testo) → necessario PDF vision, non pdftotext
- `selectedTransports`: struttura `{tIdx: {opt: number, cls: string}}` (orario + classe)
- Brevo API key: deve iniziare con `xkeysib-` (REST), NON `xsmtpsib-` (SMTP)

*Ultimo aggiornamento: 2026-04-09*
