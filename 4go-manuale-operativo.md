# 4&GO FourTravel — Manuale Operativo
## Social AI · Prenotazioni · Sistema

> Documento operativo per gli operatori dell'agenzia. Aggiornato al 2026-04-08.

---

# 1. SOCIAL AI

## Cos'è
La sezione Social AI gestisce i documenti di viaggio per i clienti (via Telegram bot) e le statistiche delle conversazioni. Si accede da Admin → Social AI.

## Tab disponibili
- **Telegram** — documenti di viaggio caricati per le prenotazioni
- **Statistiche** — analisi delle conversazioni del bot
- **Post Social** — generazione contenuti per i social

---

## Tab Telegram — Documenti di viaggio

### A cosa serve
Permette di caricare documenti (biglietti aerei, voucher hotel, itinerari dettagliati, polizze assicurative) associati a una prenotazione. Il bot Telegram risponde alle domande del cliente usando il contenuto di questi documenti.

### Come caricare un documento

1. Clicca **Scegli file** nella tab Telegram
2. Seleziona il file (PDF, TXT, DOCX)
3. Associa il documento:
   - **Seleziona pacchetto** — tutti i pacchetti disponibili (non solo quelli con prenotazione)
   - **Seleziona prenotazione** — codice 4GO oppure inseriscilo manualmente
4. Il sistema estrae il testo, genera la mappa semantica e notifica via Telegram

### Documenti raggruppati per prenotazione
I documenti appaiono **raggruppati per codice prenotazione** — clicca sul codice `🎫 4GO-2026-XXXX` per espandere e vedere i documenti associati. I documenti senza prenotazione appaiono in fondo sotto "— Senza prenotazione".

### Badge sul documento
- **📝 Xk car.** — testo estratto (es. "📝 24k car.")
- **🗂 N sez.** — sezioni della mappa semantica
- **⚠️ No mappa** — riprova con 🔄
- **🤖 Solo bot** — solo per il chatbot, non allegabile
- **📎 Allegabile** — può essere inviato al cliente come allegato email

### Rigenera mappa (🔄)
Se la mappa non è stata generata, clicca 🔄 sulla card del documento. Attendi fino a 5 minuti per documenti lunghi.

---

## Come il bot usa i documenti

Il bot cerca i documenti associati alla prenotazione del cliente e risponde con informazioni precise estratte dai contenuti:
- *"A che ora parte il mio volo?"* → biglietto aereo
- *"Qual è il numero della camera?"* → voucher hotel
- *"Cosa è incluso nella polizza?"* → assicurazione
- *"Dimmi l'itinerario del giorno 3"* → brochure Gamma del pacchetto

Il bot usa anche il **testo della brochure Gamma** del pacchetto (checklist, inclusi/esclusi, perché sceglierlo, impatto ambientale) se disponibile — anche senza documenti caricati manualmente.

---

## Utenti Bot (solo Superadmin)
In fondo alla tab Telegram c'è la sezione **🤖 Utenti che hanno avviato il bot** — mostra chi ha effettivamente avviato il bot, con nome, codice prenotazione, stato sessione e data di registrazione.

---

## Tab Statistiche

- **Sessioni totali** — quanti clienti hanno usato il bot
- **Sessioni attive** — conversazioni in corso
- **Escalation** — casi di emergenza
- **Topic principali** — argomenti più richiesti

---

# 2. PRENOTAZIONI

## Cos'è
Gestisce tutte le prenotazioni manuali e online. Admin → Prenotazioni.

---

## La scheda prenotazione

Ogni prenotazione mostra: codice `4GO-YYYY-XXXX`, nome cliente, destinazione, stato, data partenza, importo.

### Partecipanti aggiuntivi
Puoi aggiungere altri viaggiatori alla prenotazione nel campo **Partecipanti** con il formato:
```
Nome Cognome <email@dominio.it>, Nome2 Cognome2 <email2@dominio.it>
```
Quando invii l'email di conferma, puoi scegliere a quali destinatari inviarla (con checkbox).

### Email di conferma con QR Telegram
L'email di conferma include:
- **Bottone 🤖 Apri assistente** — apre direttamente il bot Telegram (o installa l'app se non presente)
- **QR code** — da scansionare con un secondo dispositivo
- **@FourGoTravelBot** — per chi preferisce cercarlo manualmente, con il codice prenotazione da copiare
- Nota finale: *"L'assistente virtuale ti accompagnerà durante il viaggio, ma noi restiamo sempre a tua disposizione per qualsiasi necessità. 😊"*

---

## Gestione stati

- 🔵 **In attesa** — non ancora confermata
- 🟢 **Confermata** — confermata, documenti da inviare
- 🟡 **In viaggio** — cliente in viaggio
- ✅ **Completata** — viaggio terminato
- ❌ **Cancellata** — annullata

---

## Bot Telegram — come funziona per il cliente

1. Il cliente riceve l'email di conferma con il bottone **Apri assistente** o il QR code
2. Clicca il bottone → Telegram si apre (o viene installato) → il bot si avvia automaticamente
3. Il bot carica il dossier di viaggio con: itinerario, mappa, checklist, brochure, inclusi/esclusi, documenti

### Emergenze
Se il cliente scrive "emergenza":
1. Sceglie l'operatore
2. Tutti e 3 gli operatori ricevono notifica Telegram
3. Il cliente riceve conferma

---

## Notifiche Telegram agli operatori

| Evento | Chi riceve |
|--------|-----------|
| Nuovo pagamento online | Tutti e 3 |
| Preventivo confermato | Tutti e 3 |
| Nuovo pacchetto salvato | Tutti e 3 |
| Import pacchetto da PDF | Solo chi ha caricato |
| Documento caricato Social AI | Solo chi ha caricato |
| Emergenza cliente | Tutti e 3 |

---

## Impostare il proprio Chat ID Telegram

1. Apri **@userinfobot** su Telegram → scrivi /start → copia il numero "Id:"
2. Admin → Utenti → tuo profilo → campo "Telegram Chat ID" → salva

---

# 3. SISTEMA — MANUTENZIONE CONTENUTI

Admin → Sistema → sezione **🛠️ Manutenzione Contenuti**

| Bottone | Cosa fa |
|---------|---------|
| **🛡️ Avvia verifica** | Controlla i link immagine rotti — sostituisce automaticamente |
| **🗺️ Ricalcola mappe** | Ricalcola coordinate GPS dei pacchetti |
| **📍 Ripristina attrazioni** | Rigenera punti di interesse sulla mappa — salta i pacchetti già aggiornati |
| **🧹 Pulisci blog** | Elimina post duplicati e aggiorna le foto dal pacchetto corrispondente |

> Per i nuovi pacchetti importati via PDF non è necessario usare questi bottoni.

---

# 4. MAPPE INTERATTIVE

Le mappe sui pacchetti mostrano:
- **Marker rossi numerati** — città e attrazioni per giorno
- **Filtri città** — clicca per evidenziare in giallo tutti i punti di quel giorno
- **Satellite** — toggle mappa/satellite
- **Apri in Google Maps →** — percorso completo con tutte le tappe

---

# 5. ACTIVITY LOG

Admin → Utenti → clicca sul nome di un operatore:
- Tutte le azioni svolte nell'admin
- Sezioni mai utilizzate evidenziate in arancione

---

## Aggiornamento sessione 13 — 2026-04-09

### Mappe pacchetti
- La mappa interattiva usa solo punti `type:'city'` per il percorso (no attrazioni)
- Le attrazioni vengono rigenerate automaticamente al termine di ogni import PDF
- Bottone **🔄 Forza rigenera attrazioni** in Sistema sovrascrive anche pacchetti già aggiornati
- **Ricalcola mappe** ora forza sempre l'aggiornamento (force:true di default)
- Alias città italianizzate: Gibilterra→Gibraltar, Londra→London, ecc. (senza vincolo paese)
- Bottone **Rigenera mappe statiche** in Sistema (equivalente a chiamare `/api/admin/generate-static-maps` con `force:true`)

### Preventivi
- Selezione multipla preventivi: checkbox su card → "Genera Gamma (N)" → max 10 in parallelo background
- Conferma preventivo cliente: email senza link pagamento — operatore li genera dall'admin
- Pagine cliente (preventivo-confermato, scegli-pacchetto, ecc.) accessibili anche con COMING_SOON=true

### Gamma PRV
- Polling aumentato a 300s, maxDuration route 300s
- Testo PDF adattivo: ≤15k tutto, 15k-40k → 17k, >40k → 28k
- Progress visibile nel pannello operazioni durante generazione

### Pexels
- Query immagini senza "landscape", pagina max 3, idx max 10

### Bug fix
- extractJSON gestisce bad escaped characters nel JSON itinerario
- allSections activity log include tutte le pagine admin
- mapCities riconosce formato "CITTÀ X GIORNI" nei PDF Massimo
- Regeocode limit 50 (era 10), esclude ARCHIVED
