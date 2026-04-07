# 4&GO FourTravel — Manuale Operativo
## Social AI · Prenotazioni

> Documento operativo per gli operatori dell'agenzia. Aggiornato al 2026-04-04.

---

# 1. SOCIAL AI

## Cos'è
La sezione Social AI gestisce i documenti di viaggio per i clienti (via Telegram bot) e le statistiche delle conversazioni. Si accede da Admin → Social AI.

## Tab disponibili
- **Telegram** — documenti di viaggio caricati per le prenotazioni
- **Statistiche** — analisi delle conversazioni del bot

---

## Tab Telegram — Documenti di viaggio

### A cosa serve
Permette di caricare documenti (biglietti aerei, voucher hotel, itinerari dettagliati, polizze assicurative) associati a una prenotazione. Il bot Telegram può poi rispondere alle domande del cliente usando il contenuto di questi documenti.

### Come caricare un documento

1. Clicca **+ Carica documento** nella tab Telegram
2. Seleziona il file (PDF, TXT, DOCX)
3. Il sistema:
   - Salva subito il documento
   - Invia notifica agli operatori: *"📎 Documento ricevuto ⏳"*
   - Estrae il testo (pdf-parse per PDF con testo, Vision OCR per PDF scansionati)
   - Genera la mappa semantica (struttura del documento)
   - Invia notifica finale: *"📄 Documento strutturato — N sezioni"*
4. Il documento appare nella lista

### Badge sul documento
Ogni documento mostra:
- **📝 Xk car.** — testo estratto (es. "📝 12k car." = 12.000 caratteri estratti)
- **📝 No testo** — PDF scansionato o testo non estraibile
- **🗂 N sez.** — mappa semantica generata con N sezioni
- **⚠️ No mappa** — mappa non generata (riprova con 🔄)
- **🤖 Solo bot** — documento visibile solo al bot, non inviabile al cliente come allegato
- **📎 Allegabile** — documento che può essere inviato al cliente via email

### Associare un documento a una prenotazione
Dopo il caricamento, associa il documento alla prenotazione del cliente:
1. Clicca **+ Prenotazione** sulla card del documento
2. Inserisci il codice prenotazione (es. `4GO-2026-1234`)
3. Il bot potrà ora rispondere alle domande di quel cliente usando questo documento

Puoi anche aprire la tab Telegram direttamente dalla scheda prenotazione — il campo si precompila automaticamente.

### Rigenera mappa (🔄)
Se il badge mostra **⚠️ No mappa** o **📝 No testo**:
1. Clicca il bottone 🔄 sulla card
2. Il sistema ri-estrae il testo (usando Vision OCR se necessario)
3. Rigenera la mappa semantica
4. Attendi fino a 5 minuti per documenti lunghi o scansionati

### Toggle Solo bot / Allegabile
- **🤖 Solo bot** → il documento è solo per il contesto AI, non viene inviato come allegato email
- **📎 Allegabile** → può essere selezionato quando si inviano documenti al cliente
- I file TXT e DOCX sono automaticamente "Solo bot"
- I PDF originali sono automaticamente "Allegabile"

---

## Come il bot usa i documenti

Quando un cliente scrive al bot Telegram con una domanda su un viaggio:
1. Il bot cerca i documenti associati alla sua prenotazione
2. Legge le sezioni rilevanti della mappa semantica
3. Risponde con informazioni precise estratte dai documenti

**Esempi di domande che il bot risponde correttamente con i documenti:**
- *"A che ora parte il mio volo?"* → legge il biglietto aereo
- *"Qual è il numero della mia camera?"* → legge il voucher hotel
- *"Cosa è incluso nella polizza?"* → legge l'assicurazione
- *"Qual è l'indirizzo dell'hotel?"* → legge il voucher

**Senza documenti caricati**, il bot risponde solo con le informazioni dell'itinerario del pacchetto.

---

## Tab Statistiche

Mostra l'analisi delle conversazioni del bot Telegram:
- **Sessioni totali** — quanti clienti hanno usato il bot
- **Sessioni attive** — conversazioni in corso
- **Escalation** — casi di emergenza gestiti
- **Tempo medio risposta** — efficienza del bot
- **Topic principali** — argomenti più richiesti dai clienti (clustering AI)

**Nota:** le conversazioni degli operatori sono escluse dalle statistiche (non inquinano i dati).

**Reset statistiche** (solo per test): apri nel browser:
```
https://4go-gamma.vercel.app/api/admin/telegram-reset?secret=4go2026
```

---

# 2. PRENOTAZIONI

## Cos'è
La sezione Prenotazioni gestisce tutte le prenotazioni manuali e online. Si accede da Admin → Prenotazioni.

---

## Tipi di prenotazione

### Prenotazioni online
Arrivano automaticamente quando un cliente paga sul sito (pacchetti con prezzo fisso). Generano un codice `4GO-YYYY-XXXX` automaticamente.

### Prenotazioni manuali
Create dall'operatore convertendo un preventivo (bottone **Crea 4GO**) o inserendo i dati manualmente.

---

## La scheda prenotazione

Ogni prenotazione mostra:

**Header (sempre visibile):**
- Codice prenotazione `4GO-YYYY-XXXX`
- Nome cliente
- Destinazione / Pacchetto
- Stato (badge colorato)
- Data partenza
- Importo

**Pannello espanso (clic sulla card):**

### Dati cliente
- Nome, email, telefono
- Note operative

### Documenti viaggio
Il badge **📎 N** accanto alla prenotazione indica quanti documenti allegabili (PDF) non ancora inviati al cliente.

Espandendo il pannello documenti:
- Lista di tutti i documenti associati
- Badge testo estratto e sezioni mappa
- Toggle Solo bot / Allegabile
- Bottone per aprire Social AI direttamente sulla prenotazione

### Invia documenti al cliente
1. Seleziona i documenti da inviare (solo quelli "Allegabile")
2. Clicca **Invia documenti**
3. Il cliente riceve un'email con i PDF allegati
4. I documenti inviati mostrano la data di invio

---

## Gestione stati prenotazione

**Stati disponibili:**
- 🔵 **In attesa** — prenotazione creata, non ancora confermata
- 🟢 **Confermata** — prenotazione confermata, documenti da inviare
- 🟡 **In viaggio** — cliente attualmente in viaggio
- ✅ **Completata** — viaggio terminato
- ❌ **Cancellata** — prenotazione annullata

---

## Bot Telegram e prenotazioni

### Come funziona il bot per il cliente
Dopo la prenotazione, il cliente riceve il codice `4GO-YYYY-XXXX`.

Il cliente usa il bot `@FourGoTravelBot` su Telegram:
1. Apre il bot e inserisce il codice prenotazione
2. Il bot verifica il codice e carica il dossier di viaggio
3. Il cliente può chiedere qualsiasi cosa:
   - *"Dimmi il giorno 3 dell'itinerario"*
   - *"Cosa è incluso?"*
   - *"Qual è l'orario del volo?"* (se caricati i documenti)
   - *"Mostrami la mappa del percorso"*

### Dossier di viaggio (bottoni del bot)
Il bot offre accesso rapido a:
- 🗓 **Itinerario** — giorno per giorno
- 🗺 **Mappa** — percorso interattivo
- ✅ **Checklist** — cosa portare
- 📄 **Brochure** — link alla presentazione Gamma
- ✔️ **Inclusi/Esclusi** — cosa è compreso
- 📋 **Documenti** — biglietti e voucher (se caricati)

### Emergenze dal bot
Se il cliente scrive "emergenza" o clicca /emergenza:
1. Sceglie l'operatore (Massimo o Alessia)
2. **Tutti e 3 gli operatori** ricevono notifica privata Telegram
3. Il gruppo operatori riceve notifica con bottone **✅ Risolto**
4. Chiunque può risolvere l'emergenza dal gruppo
5. Il cliente riceve conferma che il team è stato avvisato

**Risoluzione:**
- Dal gruppo: clicca **✅ Risolto**
- Dal privato: scrivi `/risolto_CHATID` (il chatId è nel messaggio ricevuto)

---

## Notifiche Telegram agli operatori

L'amministratore riceve notifiche Telegram per:

| Evento | Chi riceve |
|--------|-----------|
| Nuovo pagamento online | Tutti e 3 |
| Preventivo confermato | Tutti e 3 |
| Acconto preventivo pagato | Tutti e 3 |
| Documento caricato in Social AI | Solo chi ha caricato |
| Import pacchetto da PDF | Solo chi ha caricato |
| Emergenza cliente | Tutti e 3 + gruppo |

---

## Impostare il proprio Chat ID Telegram

Per ricevere le notifiche personali:
1. Apri **@userinfobot** su Telegram
2. Scrivi /start
3. Copia il numero "Id:" che ti dà
4. Vai su Admin → Utenti → il tuo profilo → campo "Telegram Chat ID" → salva

---

## Activity log operatori

In Admin → Utenti → clicca sul nome di un operatore per vedere:
- Tutte le azioni svolte nell'admin (pagine visitate, azioni eseguite)
- Le sezioni mai utilizzate sono evidenziate in arancione
- Utile per monitorare l'uso della piattaforma

