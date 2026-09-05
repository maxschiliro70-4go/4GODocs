# Manuale Proposte Viaggio — 4GO FourTravel

## Cos'è una Proposta Viaggio

La **Proposta Viaggio** è uno strumento per inviare al cliente un programma completo con:
- Più destinazioni (es. Napoli + Roma)
- Più hotel per ogni destinazione da confrontare
- Treni/trasporti con orari e prezzi per classe

Il cliente riceve un link personalizzato, sceglie hotel e orari, e conferma tutto in una volta.

---

## Come creare una Proposta Viaggio

### 1. Vai su Admin → Proposte Viaggio

### 2. Carica il PDF del programma

Trascina o clicca per caricare il PDF ricevuto da RateHawk/TravelComposer con:
- Hotel per ogni destinazione
- Tabelle orari treno

Il sistema analizza automaticamente il documento e estrae:
- Titolo e date del viaggio
- Hotel per ogni tappa con prezzi, stelle, servizi
- Trasporti con tutti gli orari e prezzi per classe

**Attendi la preview** — vedrai le tappe, gli hotel e i trasporti estratti.

### 3. Inserisci i dati cliente

- **Nome cliente** (obbligatorio)
- **Email cliente** (opzionale — serve per inviare il link)
- **Cellulare** (per WhatsApp, quando attivo)

### 4. Salva la proposta

Clicca **Salva proposta viaggio**. Viene assegnato un codice (es. `TP-2026-ABC12`).

### 5. Genera la brochure Gamma

Prima di inviare, clicca **Genera brochure Gamma** per creare una presentazione con tutti gli hotel e i treni. Allegala all'email come anteprima visiva.

### 6. Invia al cliente

Clicca **Invia** — il cliente riceve un'email con:
- Link alla pagina di scelta
- Bottone per aprire la brochure Gamma

---

## Cosa vede il cliente

La pagina è organizzata in **ordine cronologico**:

1. 🚆 Treno Milano → Napoli (con tabella orari e prezzi Economy/Business/Executive)
2. 🏨 Hotel Napoli (3 opzioni da confrontare)
3. 🚆 Treno Napoli → Roma
4. 🏨 Hotel Roma (4 opzioni)
5. 🚆 Treno Roma → Milano

Il cliente **clicca direttamente sul prezzo** per scegliere orario + classe del treno.

Per ogni hotel vede: foto, stelle, posizione, servizi, pros/cons, prezzo → clicca **Scelgo questo hotel**.

In fondo alla pagina un **riepilogo sticky** mostra il totale aggiornato (hotel + treni scelti). Il cliente sceglie acconto 30% o totale e conferma.

> **Nota:** se la partenza è entro 30 giorni, viene richiesto solo il pagamento totale.

---

## Gestione post-conferma (Admin)

Quando il cliente conferma, ricevi una notifica Telegram con le sue scelte.

Nella lista Proposte Viaggio, la proposta passa a stato **CONFIRMED** e appaiono due sezioni:

### Pagamento principale

- **Genera link** → crea link Stripe + PayPal per acconto e saldo separati
- **Pagato in agenzia** → segna direttamente come pagato senza link online

### Voci extra / servizi aggiuntivi

Puoi aggiungere voci extra dopo la conferma (assicurazione aggiuntiva, escursione, transfer, ecc.):

1. Inserisci **descrizione** e **importo €**
2. Clicca `+` per voci positive o `-` per sconti/rettifiche
3. Clicca **Aggiungi**
4. Per ogni voce extra puoi generare un **link pagamento separato** (Stripe + PayPal)
5. Copia il link e invialo al cliente via email o WhatsApp

---

## Proposte generate automaticamente da Adamantis Viaggi (Sud-Est Asiatico)

Da 4GO-26, oltre al caricamento manuale di un PDF RateHawk/TravelComposer, esiste un **secondo modo** in cui nasce una Proposta Viaggio: automatico, per richieste su destinazioni Sud-Est Asiatico (Thailandia, Indonesia, Malesia, Sri Lanka, Vietnam, Cambogia, Laos, India) **non coperte dal catalogo Package interno**.

**Come funziona:** quando un cliente chiede un preventivo per una di queste destinazioni — da form sito, WhatsApp o email diretta — il sistema cerca in automatico un pacchetto compatibile tra quelli di Adamantis Viaggi (tour operator terzo, indicizzato settimanalmente dal cron `adamantis-sync`), legge il PDF trovato e genera una Proposta Viaggio in bozza (stato `DRAFT`, mai inviata al cliente da sola).

**Dove la trovi:** Admin → Richieste, card **"Pacchetto Adamantis"**. Il messaggio della richiesta riporta il codice (`TP-2026-XXXXX`) e il prezzo stimato.

⚠️ **Il nome del fornitore terzo (Adamantis) è visibile SOLO qui, in admin.** Titolo, tappe e hotel della Proposta Viaggio generata non lo nominano mai — sanificato automaticamente per costruzione, perché il titolo finisce anche in email reali al cliente (conferma scelta) e nella pagina pubblica. Non serve nessuna attenzione manuale su questo, ma se per qualsiasi motivo lo vedessi comparire nel titolo o negli hotel di una proposta, segnalalo subito: sarebbe un bug, non un comportamento normale.

**Cosa manca sempre, per costruzione:**
- **Il volo internazionale** — Adamantis vende solo componente terra (hotel, trasferimenti, escursioni). Il titolo della proposta e la prima voce "non incluso" lo segnalano esplicitamente ("solo terra, volo da aggiungere").
- **Il prezzo finale** — quello estratto è la quota Adamantis, da sommare al costo del volo che aggiungi tu (ricerca Duffel manuale per ora, integrazione assistita in arrivo).

**Prima di inviare al cliente:** apri la proposta come faresti con una caricata a mano, aggiungi la tappa/i trasporti per il volo, verifica il prezzo finale, poi procedi come da procedura normale (Gamma, invio, conferma).

**Se il prezzo estratto sembra sballato:** i pacchetti "Tour" (escursioni/itinerari, categoria interna `tour`) non hanno mai un prezzo proprio — sono moduli pensati per essere assemblati, il prezzo esiste solo su "Pacchetti Land" (categoria `pacchetto-land`). Il sistema già lo sa e scarta i "Tour" quando c'è un budget da rispettare, ma se lo vedi comunque a 0 o mancante, è quello il motivo.

---

## Note operative

| Situazione | Cosa fare |
|---|---|
| PDF non parsificato correttamente | Ricarica il PDF. Se persiste, inserisci dati manualmente |
| Prezzi treni a €0 | Controlla che il PDF sia originale RateHawk, non una scansione |
| Email cliente non arriva | Verifica email in spam. Aggiungi `info@fourgo.it` ai contatti |
| Cliente ha già confermato | La pagina mostra "Scelta già effettuata" con il riepilogo |

---

*Versione 1.0 — Aggiornato 2026-04-09*
