# Sessione 4GO-26 — 5 settembre 2026

Sessione divisa in due parti: fix e potenziamento del cron social Violetta, poi costruzione completa (3 pezzi, 3 canali) della feature di preventivi automatici Adamantis Viaggi per il Sud-Est Asiatico.

## Fix font "VIOLETTA" sui post social

Il testo del titolo usciva come quadratini (tofu) su Vercel nonostante il font DM Sans fosse embedded in base64 in `violettaFonts.ts`. Causa reale: **librsvg (il motore SVG di Sharp) non supporta `@font-face`**, risolve i font solo via fontconfig sui font di sistema — e su Vercel serverless non ce n'è nessuno. Il test locale sembrava funzionare per il motivo sbagliato: un font di fallback del sistema mascherava il problema.

**Fix:** testo convertito in path vettoriali con `opentype.js` a partire dallo stesso WOFF embedded, invece di `<text>`. Zero dipendenza da fontconfig, risultato identico ovunque. Nota per il futuro: per testare davvero un caso simile in locale, `FONTCONFIG_FILE=/dev/null node script.js` — altrimenti il fallback di sistema nasconde il bug.

Ridimensionato anche il testo (troppo grande al primo giro): titolo dal 9% al 7% della larghezza immagine, sottotitolo dal 2.8% al 2.3%.

## Cron `violetta-social-post` — potenziato

- Secondo post settimanale (venerdì 18:00 UTC, oltre al lunedì 9:00 UTC esistente)
- Rotazione angolo passata da settimana-ISO a **contatore dei post già pubblicati** (`COUNT(*)` su `SocialPost`) — con 2 post/settimana la rotazione per settimana avrebbe ripetuto lo stesso angolo due volte di fila
- Angolo "funzionalità" non più generico: pesca a rotazione da un array cablato con le 10 feature reali del prodotto (itinerario & mappa, ristoranti vicini, audioguide, meteo, volo real-time, cambio, frasi lingua locale, cosa vedere, sicurezza, prenota per te)
- Anti-duplicato ridotto da 6 a 3 giorni (con 6 il secondo post settimanale veniva sempre scartato come falso doppione)
- Anti-ripetizione destinazioni/scenari: gli ultimi 8 post passati al prompt perché Claude vari geografia invece di ricadere sempre su Tokyo/Giappone come esempio di barriera linguistica (bias osservato, non solo ipotizzato)
- Dicitura trasparenza AI Act aggiunta (stessa `AI_DISCLOSURE` del cron `social-post` esistente), con troncamento sicuro per il limite 500 caratteri di Threads/Pinterest che preserva sempre la dicitura anche quando la caption va tagliata

## Feature Adamantis Viaggi — preventivi automatici Sud-Est Asiatico (4GO-26)

**Obiettivo:** per richieste su destinazioni Sud-Est Asiatico (Thailandia, Indonesia, Malesia, Sri Lanka, Vietnam, Cambogia, Laos, India) non coperte dal catalogo `Package` interno, matchare automaticamente un pacchetto di Adamantis Viaggi (tour operator terzo, specializzato Estremo Oriente) e generare un draft di preventivo che lascia solo volo e prezzo finale da completare a un operatore — mai invio automatico al cliente di dati Adamantis, serve sempre revisione umana.

### Pezzo 1 — indice cache

Nuovo modello `AdamantisPackage` + cron `adamantis-sync` (lunedì 4:00 UTC). Estrazione strutturata via Claude Haiku dall'HTML grezzo di `/tour/` e `/offerte/` — non regex, perché non testabile da questo ambiente contro l'HTML reale del sito (il tool di lettura web trasforma sempre in markdown). Verificato con dati reali: 79/79 Tour, 26/26 Pacchetto Land indicizzati, zero troncamenti dopo aver alzato `max_tokens` da 8000 a 16000 (la pagina Tour con ~79 elementi troncava l'array JSON a metà con 8000).

### Pezzo 2 — matching + generazione draft

`src/lib/adamantisMatch.ts` — dati destinazione/durata/budget cliente:
- Prompt di estrazione dedicato al vero contenuto Adamantis (itinerario day-by-day + quota unica per persona), non riusa quello Alpitour di `parseTravelDocument` esistente (pensato per confronti multi-hotel con rating/pro/contro assenti nei PDF Adamantis)
- **Un solo `leg` per pacchetto, mai frazionato per città** anche sui tour multi-tappa: `/api/proposta-scegli` somma il `totalPrice` di ogni leg per il totale finale — frazionare per città avrebbe moltiplicato il prezzo mostrato al cliente
- **Budget è un tetto ("fino a X"), non un bersaglio da avvicinare in entrambe le direzioni:** tra i candidati entro budget sceglie il più caro (il miglior pacchetto che il cliente può permettersi); se nessuno rientra, il più economico disponibile comunque con flag `sopraIlBudget`
- **I PDF "Tour" non hanno prezzo proprio** — sono moduli itinerario pensati per essere assemblati in pacchetti/viaggi con volo, verificato incrociando il contenuto reale di più PDF. Solo "Pacchetto Land" ha una quota propria. Quando c'è budget da rispettare, i candidati si restringono a `categoria: pacchetto-land`, altrimenti su una destinazione Tour-heavy (es. Thailandia: 15 Tour contro 6 Pacchetto Land) i 6 candidati scelti per vicinanza-durata rischiavano di essere tutti senza prezzo
- Titolo e prima voce "cons" del draft segnalano sempre esplicitamente "solo terra, volo da aggiungere" — per evitare equivoci quando il draft lo apre un operatore diverso da chi ha configurato la feature
- Arricchisce di riflesso `AdamantisPackage.prezzo` per ogni PDF letto durante un match, anche se non scelto — cache che si popola gradualmente con l'uso reale, senza un sync pesante che rilegga tutti i ~105 PDF ogni settimana

### Pezzo 3 — aggancio ai tre canali (form, WhatsApp, email)

- **Form preventivi** (`api/preventivo/route.ts`): matching avviato in parallelo alla generazione del testo AI esistente (zero tempo aggiunto), solo se la destinazione non è nel catalogo interno. Email al cliente sempre identica a oggi; se il match riesce, `Inquiry` e notifica Telegram riportano codice draft e prezzo stimato
- **WhatsApp** (`whatsapp/ai/route.ts` + `whatsapp/webhook/route.ts`): edit chirurgico al prompt live di Violetta — una riga aggiunta alla regola di escalation per qualifica commerciale già esistente, che emette anche `RICHIESTA_VIAGGIO: destinazione=X; budget=Y; durata=Z` sulla riga sotto, stesso principio del marcatore `INFO_MANCANTE` già in uso. Parsing nello stesso blocco dove `ESCALATE` viene già letto e rimosso dal testo cliente
- **Email diretta** (`api/email-ai/poll/route.ts`): qui la classificazione `ESCALATED` è già decisa a livello di codice da `ESCALATION_TRIGGERS` (parole chiave nel testo del cliente), indipendente da cosa scrive l'AI — quindi il marcatore serve solo a fornire i VALORI (destinazione/budget/durata), il gate reale resta `newCategory === 'ESCALATED'` già esistente. Scope limitato al solo blocco prompt italiano (il prompt supporta 5 lingue in blocchi separati: editarli tutti sarebbe stato un rischio molto più alto per un beneficio marginale, dato che la quasi totalità delle richieste a un'agenzia italiana arriva in italiano)
- In tutti e tre i canali: slot "Telegram Bot" nell'admin Richieste (mai scritto da nessuno, verificato via grep prima di riusarlo) rinominato in chiave e etichetta a "Pacchetto Adamantis" — stesso spazio UI, zero migrazione dati necessaria

### Verifica compilazione

Ogni singolo commit verificato con `tsc --noEmit` confrontato contro il baseline di errori preesistenti (74, sempre gli stessi, mai variati) — inclusa una verifica per diff normalizzato riga-per-riga su `email-ai/poll/route.ts` (file con diversi errori TypeScript preesistenti non collegati a questa modifica) per essere certi di non aver introdotto nulla di nuovo mascherato da un conteggio totale invariato per coincidenza.

## Lezione trasversale della sessione

Il tool di lettura web disponibile in questo ambiente trasforma sempre l'HTML in markdown e non riesce a rifetchare un URL apparso solo dentro il body di una pagina già letta (serve che sia stato un risultato diretto di search/fetch, non un link trovato dentro il contenuto). Impossibile scrivere e testare una regex contro il markup reale di un sito esterno da qui. Dove serve estrazione strutturata da HTML/PDF esterni, preferire un'estrazione via Claude (con pulizia preventiva di script/style/immagini base64 per non gonfiare inutilmente i token) invece di una regex scritta alla cieca e mai verificata end-to-end.

## Fix critico post-chiusura: nessun riferimento al fornitore terzo deve arrivare al cliente

Trovato dopo la prima chiusura della feature, prima che causasse danni: `TravelProposal.title` finisce in email REALI al cliente (`proposta-scegli.ts`: "Preferenza ricevuta: ${proposal.title}", sia subject che body) e nell'URL della pagina di ringraziamento pubblica — non solo in admin. Il title costruito inizialmente ("... · Adamantis Viaggi") avrebbe rivelato il fornitore terzo al cliente al momento della scelta/conferma, anche dopo revisione operatore.

**Fix su due livelli:** rimosso il nome del fornitore dal title costruito (resta solo linguaggio operativo neutro), e aggiunta `sanitizzaTestoCliente()` come difesa strutturale — applicata a hotelName/roomType/mealPlan/pros/cons/titolo prima che finiscano nel draft, per il caso in cui il PDF stesso nomini l'azienda in un punto non ancora verificato manualmente su tutti i ~105 pacchetti (footer, condizioni, intestazioni). Le notifiche interne agli operatori (Telegram, Inquiry, Escalation) continuano a nominare esplicitamente il fornitore — usano il valore non sanificato nel return value della funzione, separato dai campi che finiscono nel record visibile al cliente.

## Chiusura ricerca voli Duffel (assistita, non automatica)

Ultimo pezzo rimasto aperto, chiuso in coda alla sessione:

- **Mappa IATA centralizzata** in `src/lib/duffelFlights.ts` — prima erano due liste leggermente diverse in `telegram/webhook.ts` e `whatsapp/ai/route.ts` (già causa di un bug reale in passato: Sharm el-Sheikh mancava da entrambe in momenti diversi). Aggiunte le destinazioni Sud-Est Asiatico mancanti: Malesia (KUL), Sri Lanka (CMB), Vietnam (HAN/SGN), Cambogia (PNH/REP), Laos (VTE), India (DEL/BOM)
- **Ricerca vera andata+ritorno** in un'unica richiesta Duffel a 2 slice — le funzioni esistenti fanno solo sola andata, pensate per stime in chat, non per popolare `transports` di una proposta
- **Tasto "Cerca voli (Duffel)"** nella pagina Proposte Viaggio: nuova azione `duffel-search` (solo ricerca, non salva), l'operatore vede il riepilogo e conferma, poi si salva con l'azione `update-proposal` già esistente — nessuna azione nuova per il salvataggio. Resta sempre intervento umano, la ricerca propone soltanto.
