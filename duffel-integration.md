# Duffel Integration — 4GO FourTravel

## Stato attuale
- Account creato: maxschiliro70@gmail.com
- **Flights: PRODUZIONE APPROVATA** ✅
- **Stays: test mode, approvazione produzione pending** ⏳
- API Key configurata su Vercel come `DUFFEL_API_KEY`
- Voli integrati su WhatsApp AI (trigger entro 10 messaggi)

---

## Flusso target (da implementare dopo approvazione produzione)

### WhatsApp — Richiesta pacchetto (es. "quanto costano 6 giorni a Tenerife + volo 3 persone?")

1. **Violetta** interroga in parallelo:
   - Duffel Flights → voli da MXP/LIN verso destinazione
   - Duffel Stays → hotel nella destinazione
2. Applica **markup standard predefinito** (da definire con Massimo)
3. Risponde al cliente con stima reale:
   > "Per 6 giorni a Tenerife per 3 persone trovo voli da ~€320 a persona e hotel 4⭐ da ~€90/notte — pacchetto indicativo ~€1.800. I prezzi possono variare, contatta i nostri consulenti per un preventivo confermato 👇"
4. Scala automaticamente a **tutti gli operatori** su Telegram con:
   - Nome e numero cliente
   - Destinazione, date, persone
   - Dettaglio voli trovati (compagnia, orari, prezzo netto + markup)
   - Dettaglio hotel trovati (nome, stelle, prezzo netto + markup)
   - **Link diretto Duffel** per completare l'ordine volo
   - **Link diretto Duffel** per completare l'ordine hotel

---

## Markup — DEFINITO

- **Corto raggio** (Europa) → **15%**
- **Medio raggio** (Nord Africa, Medio Oriente, Turchia, Egitto) → **17%**
- **Lungo raggio** (America, Asia, Oceania, Africa subsahariana) → **20%**

Il markup viene applicato automaticamente da Violetta nella stima al cliente.
Gli operatori vedono sempre prezzo netto + markup + totale finale.

---

## Roadmap implementazione

### Fase 1 — Flights produzione ✅ / Stays pending
- [x] Sostituire API key test con live su Vercel (Flights)
- [x] Definire markup standard con Massimo
- [ ] Implementare Duffel Stays su WhatsApp (dopo approvazione produzione)
- [ ] Notifica operatori con link Duffel diretti (volo + hotel)

### Fase 2
- [ ] Duffel Notifications → Violetta notifica cliente su Telegram per ritardi/cancellazioni/cambio gate
- [ ] Duffel Cars → noleggio auto per pacchetti fly&drive
- [ ] Duffel Identity → raccolta automatica dati passeggeri pre-partenza

### Fase 3
- [ ] Duffel Payments con timer (link pagamento brandizzato 4GO)
- [ ] 3DS integrato per pagamenti corporate

### Nice to have
- [ ] **Duffel Links (€99/mese)** — checkout brandizzato 4GO con timer, zero gestione carta lato nostro. Da valutare dopo aver capito i volumi.

---

## Da esplorare — Preventivi ad hoc su Duffel

Come generare un preventivo personalizzato (volo + hotel + markup) da mandare al cliente senza che l'operatore debba costruirlo manualmente?

Opzioni da analizzare:
- Creare un endpoint `/api/admin/duffel-preventivo` che riceve destinazione/date/persone e restituisce volo + hotel con markup applicato
- Generare un PDF/link condivisibile con il dettaglio del pacchetto
- Integrare nella pagina admin preventivi esistente un tab "Cerca su Duffel"

> ⚠️ Da approfondire con Massimo — capire il workflow operativo prima di sviluppare

---

## Note tecniche

### API Duffel
- Base URL: `https://api.duffel.com`
- Auth: `Bearer {DUFFEL_API_KEY}`
- Header: `Duffel-Version: v2`

### Endpoints rilevanti
- Voli search: `POST /air/offer_requests`
- Hotel search: `POST /stays/search`
- Ordine volo: `POST /air/orders`
- Ordine hotel: `POST /stays/bookings`
- Notifiche: `POST /air/webhooks`

### Costi
- Voli: 1% del valore ordine
- Hotel: % variabile (da verificare con Duffel)
- Ricerche in eccesso: $0.005/search oltre ratio 1500:1

---

## Link utili
- Dashboard: https://app.duffel.com
- Docs voli: https://duffel.com/docs/api/flights
- Docs hotel: https://duffel.com/docs/api/stays
- Docs notifiche: https://duffel.com/docs/api/notifications
