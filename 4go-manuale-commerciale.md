# 4&GO FourTravel — Manuale Commerciale
## Newsletter · AI Generator Preventivi · Multi Hotel PKG

> Documento operativo per gli operatori dell'agenzia. Aggiornato al 2026-04-04.

---

# 1. NEWSLETTER

## Cos'è
La newsletter è lo strumento per inviare campagne email ai clienti via **Brevo**. Si accede da Admin → Newsletter.

## Flusso in 5 step

### Step 1 — Tipo newsletter
Scegli il tipo di campagna:
- **✈️ Viaggi** — proposta di pacchetti e destinazioni
- **🌟 Viaggi al Massimo** — selezione personale di Massimo
- **🎂 Compleanno** — auguri con offerta personalizzata
- **🎁 Voucher regalo** — promozione buoni regalo
- **🔥 Promozione** — offerta speciale a tempo

Compila il campo **Oggetto email** — è il testo che il cliente vede in anteprima nella casella di posta. Deve essere accattivante (es. *"Maldive a maggio — ultimi posti disponibili"*).

### Step 2 — Immagini
Hai due modi per aggiungere immagini alla newsletter:

**Genera foto AI con Gemini:**
- Scrivi nel campo prompt una descrizione dettagliata della foto che vuoi (es. *"spiaggia tropicale con palme al tramonto, acqua cristallina turchese, stile fotografico professionale"*)
- Clicca **✨ Genera foto** — Gemini crea la foto e la salva automaticamente nella libreria
- La chiave API è gestita automaticamente dal sistema, non serve inserirla

**Libreria immagini:**
- Carica foto dal tuo dispositivo con il bottone **Carica immagine**
- Seleziona una o più foto dalla libreria cliccandoci sopra (bordo verde = selezionata)
- Puoi eliminare le foto dalla libreria con il bottone **Elimina**
- Le foto selezionate appariranno nella newsletter

### Step 3 — Contenuto AI
L'AI genera automaticamente il testo della newsletter in base al tipo scelto.

- Clicca **Genera con AI** — Claude scrive titolo, sottotitolo, corpo testo e call to action
- Puoi modificare ogni campo manualmente dopo la generazione
- **Anteprima** — bottone in alto a destra per vedere come apparirà l'email prima di inviarla

Campi modificabili:
- **Titolo** — testo grande in evidenza
- **Sottotitolo** — testo secondario
- **Corpo** — testo principale della newsletter
- **CTA (Call to Action)** — testo del bottone (es. *"Scopri il pacchetto"*)
- **Link CTA** — dove porta il bottone (es. link a un pacchetto su fourgo.it)

### Step 4 — Destinatari
Seleziona chi riceverà la newsletter:

**Filtri disponibili:**
- **Tutti** — tutti i contatti attivi in Brevo
- **Attivi** — solo chi non si è discritto
- **Discritti** — solo chi ha tolto il consenso (normalmente non si usa)

**Ricerca:** cerca per nome o email un contatto specifico.

**Selezione:**
- Clicca singolarmente per selezionare/deselezionare
- Checkbox in alto per selezionare tutti i visibili
- Il contatore in basso mostra quanti sono selezionati

**Attenzione:** i contatti con bounce (❌) o discritti (🔴) sono evidenziati — evita di inviargli email.

### Step 5 — Invia
- Verifica il riepilogo: tipo, oggetto, N destinatari, immagini selezionate
- Clicca **Invia newsletter** — parte immediatamente via Brevo
- Ricevi conferma con il numero di email inviate

---

## Consigli pratici

- **Oggetto email** è la cosa più importante — determina il tasso di apertura. Usa nomi di destinazioni specifiche, non "Newsletter 4GO".
- **Prompt Gemini** più dettagliato = foto più bella. Specifica: luogo, ora del giorno, stile fotografico, colori.
- **Frequenza consigliata:** massimo 2 newsletter al mese per non essere marcati come spam.
- Le foto generate da Gemini rimangono nella libreria — riusale per campagne future sulla stessa destinazione.

---

# 2. AI GENERATOR PREVENTIVI

## Cos'è
Il generatore preventivi crea proposte commerciali personalizzate da inviare ai clienti. Si accede da Admin → AI Generator Preventivi.

## Modalità di creazione

### Modalità 1 — Importa PDF
Ideale quando hai già un preventivo del tour operator in PDF.

1. Clicca **Importa PDF** (o trascina il file nell'area)
2. Il sistema estrae automaticamente: servizi inclusi, prezzi per voce, totale
3. Le voci appaiono nella lista con i prezzi — verifica che siano corretti
4. Se il totale estratto non coincide con la somma delle voci, appare una voce di adeguamento automatica
5. Correggi eventuali errori e procedi

**Importante:** il totale PDF appare come voce indicativa non modificabile — serve solo come riferimento per l'operatore, non viene mostrato al cliente.

### Modalità 2 — Genera con AI
Ideale quando vuoi creare un preventivo da zero per una destinazione.

1. Inserisci destinazione e nome cliente
2. Clicca **Genera con AI**
3. Claude genera voci realistiche con prezzi (volo, hotel, trasferimenti, escursioni, assicurazione)
4. Modifica liberamente le voci generate
5. Aggiungi o rimuovi voci con i bottoni + e ×

### Modalità 3 — Manuale
Compilazione libera campo per campo.

### Modalità 4 — Multi Hotel (PKG)
Vedi sezione dedicata più avanti.

---

## Campi del preventivo

**Dati cliente:**
- Nome e cognome *
- Email * — dove arriverà il preventivo
- Cellulare (opzionale)

**Dati viaggio:**
- Destinazione *
- Data partenza / Data rientro
- Tipo viaggio (seleziona dalla lista)

**Impostazioni commerciali:**
- **Acconto %** — default 30%, modifica se necessario
- **Metodo pagamento:**
  - *Carta* — genera link Stripe
  - *PayPal* — genera link PayPal
  - *Entrambi* — genera entrambi i link
  - *Bonifico / Contanti / Bancomat / Assegno* — nessun link online

**Voci servizi:**
Ogni voce ha:
- Nome del servizio
- Prezzo (€)
- Checkbox "includi nel totale" — deseleziona per escludere una voce dal calcolo
- × per eliminare

**Voci extra** (sotto le voci principali):
Aggiunte dopo la conferma per servizi aggiuntivi (upgrade hotel, guida privata, ecc.). Ogni extra ha anche il metodo di pagamento separato.

**Note:** testo libero visibile nella email al cliente.

---

## Flusso dopo la creazione

### 1. Genera Gamma (opzionale ma consigliato)
- Clicca **Genera Gamma** — crea una brochure visiva del preventivo con foto della destinazione
- Il link Gamma viene salvato e incluso nell'email al cliente
- La brochure include: servizi, prezzi, slide finale con contatti 4GO

### 2. Invia al cliente
- Clicca **Invia al cliente** (o **Reinvia** se già inviato)
- Il cliente riceve una email con: riepilogo servizi, totale, acconto richiesto, link Gamma, bottone **"Confermo il preventivo"**
- Stato diventa **SENT** (giallo)

### 3. Cliente conferma
- Il cliente clicca "Confermo" nell'email
- Il sistema genera automaticamente i link di pagamento Stripe e/o PayPal per l'acconto
- Il cliente riceve una seconda email con i link di pagamento
- Stato diventa **CONFIRMED** (verde)
- Tutti e 3 gli operatori ricevono notifica Telegram

### 4. Cliente paga l'acconto
- Il cliente paga tramite Stripe o PayPal
- Stato diventa **PAID** (verde scuro)
- Notifica Telegram a tutti gli operatori: "💳 Acconto PAID"

### 5. Post pagamento — gestione saldo
Dopo che lo stato è PAID appare il pannello verde con:
- **Totale originale** — il preventivo iniziale
- **Acconto pagato** — quanto già ricevuto
- **Residuo da pagare** — la differenza

**Voci extra aggiungibili:**
- Clicca **+ Aggiungi servizio extra** → inserisci nome e importo
- Per ogni extra puoi: generare link Stripe/PayPal o segnare come pagato manualmente
- Il residuo si aggiorna automaticamente

**Link saldo finale:**
- Quando il cliente è pronto a pagare il saldo, clicca **Genera link saldo**
- Si crea un link Stripe/PayPal per l'importo residuo esatto

### 6. Converti in prenotazione 4GO
- Clicca **Crea 4GO** — converte il preventivo in prenotazione ufficiale
- Viene creato un codice prenotazione `4GO-YYYY-XXXX`
- Il pacchetto appare nelle Prenotazioni
- Stato diventa **CONVERTED** (celeste)

---

## Gestione lista preventivi

**Stati e colori:**
- ⚪ **Bozza (DRAFT)** — creato ma non ancora inviato
- 🟡 **Inviato (SENT)** — email inviata al cliente
- 🟢 **Confermato (CONFIRMED)** — cliente ha confermato
- 🟢 **Pagato (PAID)** — acconto ricevuto
- 🔵 **Convertito (CONVERTED)** — diventato prenotazione 4GO

**Azioni disponibili su ogni card:**
- **Modifica** — apre il form (non disponibile per PAID/CONVERTED)
- **📋 Codice** — copia il codice PRV negli appunti
- **Gamma** — apre la brochure
- **Genera Gamma** — crea la brochure se non esiste
- **Invia/Reinvia** — invia o reinvia l'email al cliente
- **📁 Archivia** — nasconde il preventivo dalla lista attiva
- **🗑 Elimina** — elimina definitivamente (chiede conferma con codice)

**Filtri:**
- Cerca per nome, email, codice, destinazione
- **📁 Archiviati (N)** — visualizza i preventivi archiviati

**Form modifica/creazione** appare SOTTO la lista, non sopra.

---

# 3. MULTI HOTEL PKG

## Cos'è
Il pacchetto Multi Hotel permette di proporre al cliente N alternative di hotel per lo stesso viaggio, con confronto visivo e scelta diretta online. Si accede da Admin → **Multi Hotel PKG**.

## Quando usarlo
- Quando il tour operator propone più opzioni hotel per lo stesso pacchetto
- Quando vuoi far scegliere al cliente tra hotel di categorie diverse (es. 4★ economico vs 5★ lusso)
- Come voce extra aggiuntiva di un preventivo già esistente (anche già convertito)

## Flusso passo per passo

### Step 1 — Associa un preventivo (opzionale)
Se la scelta dell'hotel fa parte di un preventivo già creato:

1. Clicca sul dropdown **"Seleziona preventivo esistente"**
2. Cerca per nome cliente, codice o destinazione
3. Seleziona il preventivo — i campi cliente si compilano automaticamente
4. Nome ed email diventano read-only (presi dal preventivo)
5. Puoi comunque modificare destinazione e date

Se non hai un preventivo associato, compila i dati manualmente.

**Nota:** puoi associare preventivi in qualsiasi stato — anche quelli già CONVERTED. Il PKG è semplicemente una voce extra della pratica.

Per rimuovere l'associazione: clicca **Rimuovi** accanto al preventivo selezionato.

### Step 2 — Dati cliente
Compila (o verifica se auto-compilati):
- Nome cliente *
- Email cliente *
- Cellulare
- Destinazione *
- Data partenza
- Data rientro

### Step 3 — Carica i PDF hotel
1. Clicca sull'area tratteggiata arancione o **trascina i PDF** direttamente
2. Carica **un PDF per ogni opzione hotel** (puoi caricarli tutti insieme)
3. Il sistema analizza automaticamente ogni PDF ed estrae:
   - Nome hotel e stelle
   - Tipo camera e regime alimentare (colazione, mezza pensione, ecc.)
   - Prezzo totale
   - Volo incluso (se presente)
   - Pros e cons dell'hotel
   - Rating su 4 criteri: posizione, comfort, qualità/prezzo, servizi
4. Le opzioni estratte appaiono nella lista con nome, stelle e prezzo
5. Puoi eliminare opzioni con × se non le vuoi includere
6. Puoi aggiungere altri PDF per aggiungere opzioni

**Attenzione:** i PDF devono essere con testo selezionabile (non scansioni). Se il PDF è una scansione, il sistema potrebbe non estrarre correttamente i dati.

### Step 4 — Salva il pacchetto
1. Clicca **Salva pacchetto Multi Hotel**
2. Il sistema:
   - Crea un codice `PKG-YYYY-XXXXX`
   - Estrae la foto dell'ultima pagina di ogni PDF (foto hotel)
   - Carica le foto su Vercel Blob
   - Crea la pagina pubblica di confronto `/scegli-pacchetto?code=PKG-...`
3. Appare il link della pagina con i bottoni: **Apri**, **Copia link**, **Nuovo pacchetto**

### Step 5 — Invia al cliente
Il link della pagina di scelta va inviato al cliente. Il cliente vede:
- **Foto** di ogni hotel
- **Tabella comparativa** con prezzi e caratteristiche
- **Rating visivo** (barre colorate) per posizione, comfort, qualità/prezzo
- **Pros e cons** di ogni opzione
- **Badge ⭐ TOP** sull'hotel con il punteggio più alto
- Bottone **"Scelgo questo hotel"** per ogni opzione

### Step 6 — Genera Gamma (opzionale)
Prima di inviare il link puoi generare una presentazione Gamma:
1. Vai nella sezione PKG (accessibile anche dalla pagina preventivi)
2. Clicca **Genera Gamma**
3. Viene creata una presentazione con: cover destinazione, slide "cosa è incluso", una slide per ogni hotel con foto, slide classifica finale, slide contatti 4GO
4. Il link Gamma può essere inviato al cliente insieme al link di scelta

### Step 7 — Cliente sceglie
- Il cliente clicca "Scelgo questo hotel"
- Lo stato diventa **CONFIRMED**
- Il link di scelta sparisce dalla pagina pubblica
- Tutti gli operatori ricevono notifica Telegram con l'hotel scelto

### Step 8 — Pagamento
Dopo la scelta, genera i link di pagamento:
- Acconto (% configurabile) → Stripe e/o PayPal
- Saldo → link separato generato successivamente

---

## Codici e stati PKG

**Codice:** `PKG-YYYY-XXXXX`

**Stati:**
- **Attivo** — link di scelta visibile al cliente
- **CONFIRMED** — hotel scelto, link scelta nascosto
- **CONVERTED** — convertito in prenotazione 4GO
- **Archiviato** — nascosto dalla lista

---

## Note importanti

- Le foto degli hotel vengono eliminate automaticamente da Vercel Blob quando il PKG viene eliminato o convertito
- Se le foto non vengono caricate (PDF senza ultima pagina con foto), il confronto funziona ugualmente ma senza immagini
- Il PKG può essere creato anche senza preventivo associato — in quel caso il cliente paga il totale direttamente dalla pagina di scelta
- Per PKG associati a preventivi già PAID/CONVERTED, il pagamento viene gestito dal pannello extra del preventivo

