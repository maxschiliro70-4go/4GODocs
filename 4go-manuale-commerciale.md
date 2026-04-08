# 4&GO FourTravel — Manuale Commerciale
## Newsletter · AI Generator Preventivi · Pacchetti · Multi Hotel PKG

> Documento operativo per gli operatori dell'agenzia. Aggiornato al 2026-04-08.

---

# 1. NEWSLETTER

## Cos'è
La newsletter è lo strumento per inviare campagne email ai clienti via **Brevo**. Si accede da Admin → Newsletter.

## Flusso in 5 step

### Step 1 — Tipo newsletter
- **✈️ Viaggi** — proposta di pacchetti e destinazioni
- **🌟 Viaggi al Massimo** — selezione personale di Massimo
- **🎂 Compleanno** — auguri con offerta personalizzata
- **🎁 Voucher regalo** — promozione buoni regalo
- **🔥 Promozione** — offerta speciale a tempo

### Step 2 — Immagini
**Genera foto AI con Gemini:** scrivi una descrizione dettagliata → clicca **✨ Genera foto**
**Libreria immagini:** carica dal dispositivo o seleziona dalla libreria

### Step 3 — Contenuto AI
Clicca **Genera con AI** → modifica titolo, sottotitolo, corpo, CTA e link CTA.

### Step 4 — Destinatari
Filtra per Tutti / Attivi / Discritti. I contatti con bounce (❌) o discritti (🔴) sono da evitare.

### Step 5 — Invia
Verifica il riepilogo e clicca **Invia newsletter**.

---

# 2. AI GENERATOR PACCHETTI

## Cos'è
Genera nuovi pacchetti viaggio importando un PDF del tour operator. Si accede da Admin → AI Generator Pacchetti.

## Import PDF

1. Seleziona il **Tipo viaggio** (individuale, gruppo, città, cultura, ecc.) — obbligatorio
2. Carica il PDF del tour operator
3. Il sistema in streaming mostra il progresso:
   - **15%** — Estrazione metadati e itinerario
   - **45%** — Parsing dati estratti
   - **85%** — Geocoding destinazioni
   - **100%** — Completato
4. Viene generata automaticamente:
   - Checklist del viaggio
   - Brochure Gamma (in background)
   - Mappa con città e attrazioni geocodificate
   - Foto della destinazione

### Operazioni in background
Se navighi via mentre il sistema lavora, il **pannello operazioni** in basso a destra mostra l'avanzamento. Al completamento clicca **"📦 Apri in lista pacchetti →"** per trovare il pacchetto già salvato.

### Salvataggio automatico
Il pacchetto viene salvato come **bozza** automaticamente prima che parta la generazione Gamma — non perdi il lavoro anche se navighi via.

---

## Tipi di viaggio disponibili

| Tipo | Descrizione |
|------|-------------|
| individuale | Viaggio singolo/coppia |
| gruppo | Tour di gruppo |
| partner | Viaggio di coppia |
| fly&drive | Auto a noleggio |
| nozze | Viaggio di nozze |
| crociera | Crociere |
| città | City break |
| cultura | Turismo culturale |
| enogastronomia | Cibo e vino |
| divertimento | Parchi, eventi |
| lusso | Viaggi premium |
| natura | Ecoturismo |
| benessere | Spa e relax |
| adrenalina | Sport estremi |
| studio | Viaggi studio |

---

# 3. GESTIONE PACCHETTI

Admin → Pacchetti Viaggio

## Badge sulla card pacchetto
- **DRAFT** / **PUBLISHED** / **ARCHIVED** — stato pubblicazione
- **⭐ Featured** — mostrato in evidenza in homepage
- Codice pacchetto (es. `PKG1775644580765`)
- **👤 Nome** — operatore che ha creato il pacchetto
- **Gamma** — link alla brochure
- Codice PKG associato a eventuali preventivi

## Azioni disponibili
- **Modifica** — apre il form completo
- **Visualizza** — apre la pagina pubblica
- **Duplica** — crea una copia con nuova immagine
- **Pubblica/Bozza** — cambia stato
- **Converti in PRV** — crea un preventivo dal pacchetto (con voci incluse/escluse importate)
- **Archivia** — nasconde dalla lista
- **Elimina**

## Notifiche al salvataggio
Quando salvi un pacchetto, **tutti e 3 gli operatori** ricevono notifica Telegram con titolo, destinazione, prezzo e chi l'ha caricato.

---

# 4. AI GENERATOR PREVENTIVI

## Cos'è
Crea proposte commerciali personalizzate da inviare ai clienti. Admin → AI Generator Preventivi.

## Modalità di creazione

### Modalità 1 — Importa PDF
1. Clicca **Importa PDF**
2. Il sistema estrae servizi, prezzi e totale
3. La voce indicativa (totale PDF) appare in cima — bloccata, serve come riferimento

### Modalità 2 — Genera con AI
Inserisci destinazione e nome cliente → **Genera con AI** → modifica le voci.

### Modalità 3 — Da pacchetto
Dalla lista Pacchetti → **Converti in PRV** — importa automaticamente inclusi/esclusi come voci descrittive e salva il codice PKG di riferimento (badge arancio 📦).

### Modalità 4 — Manuale
Compilazione libera campo per campo.

---

## Campi del preventivo

- Nome/Email cliente *
- Destinazione, date partenza/rientro
- Tipo viaggio
- **Acconto %** — default 30%
- **Metodo pagamento:** Carta / PayPal / Entrambi / Bonifico / Contanti / Bancomat / Assegno
- Voci servizi con checkbox includi/escludi e × per eliminare

---

## Flusso dopo la creazione

1. **Genera Gamma** — brochure visiva con foto destinazione
2. **Invia al cliente** → email con riepilogo, link Gamma, bottone "Confermo"
3. **Cliente conferma** → link pagamento Stripe/PayPal → stato CONFIRMED
4. **Acconto pagato** → stato PAID, notifica Telegram
5. **Voci extra** → aggiungi servizi post-pagamento con link separati
6. **Crea 4GO** → converte in prenotazione con codice `4GO-YYYY-XXXX`

---

## Gestione lista preventivi

**Stati:**
- ⚪ DRAFT — non ancora inviato
- 🟡 SENT — email inviata
- 🟢 CONFIRMED — cliente ha confermato
- 🟢 PAID — acconto ricevuto
- 🔵 CONVERTED — diventato prenotazione 4GO

---

# 5. MULTI HOTEL PKG

## Cos'è
Propone al cliente N alternative di hotel per confronto visivo e scelta diretta online. Admin → Multi Hotel PKG.

## Flusso

1. **Associa preventivo** (opzionale) — anche già CONVERTED
2. **Compila dati cliente**
3. **Carica i PDF hotel** — un PDF per ogni opzione
4. **Salva pacchetto** → codice `PKG-YYYY-XXXXX` + pagina pubblica
5. **Invia link al cliente** → sceglie l'hotel → notifica Telegram
6. **Genera Gamma** (opzionale) → presentazione comparativa
7. **Genera link pagamento** → acconto + saldo

## Cosa vede il cliente
- Foto hotel, tabella comparativa, rating, pros/cons, badge ⭐ TOP
- Checkbox "Ho preso visione della nota legale" obbligatoria prima della scelta

---

# 6. BLOG

Il blog viene generato automaticamente ogni lunedì con articoli sulle destinazioni dei pacchetti pubblicati.

Ogni destinazione può avere articoli di tipo: guida, periodo migliore, bagaglio, budget, FAQ, itinerario, sostenibilità.

**Non vengono generati duplicati** — il sistema tiene traccia del tipo di articolo già scritto per ogni destinazione.

Le foto vengono prese automaticamente dalla foto del pacchetto corrispondente.

Per rimuovere post duplicati o aggiornare le foto: Admin → Sistema → **🧹 Pulisci blog**.
