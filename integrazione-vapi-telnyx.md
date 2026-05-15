# 4GO FourTravel — Integrazione Telegram / Vapi / Telnyx
**Sessione 4GO-21 · 15 maggio 2026**

---

## 1. Stack Tecnologico

| Componente | Servizio | Account | Costo |
|---|---|---|---|
| Voice AI Agent | **Vapi.ai** | maxschiliro70@gmail.com | PAYG ~$0,11/min |
| Telefonia | **Telnyx** | info@fourgo.it | PAYG ~$0,0055/min outbound |
| LLM | Claude Haiku 4.5 | Anthropic | $0,01/min |
| Voce | ElevenLabs eleven_multilingual_v2 | — | $0,04/min |
| Trascrizione | Deepgram Nova 3 Multilingual | — | $0,01/min |
| **Totale per chiamata** | | | **~$0,11/min · ~$0,25-0,33 per prenotazione** |

**Vapi Assistant ID:** `640e941e-93e7-46ab-a91b-1959d9020374`
**Telnyx numero:** `+39 02 89608767` (KYC in approvazione, 2-5 giorni)
**Auto-reload Vapi:** $10 quando scende sotto $2 (da abilitare su dashboard.vapi.ai → Billing)

---

## 2. Prodotto Violetta — 3 Piani

| Piano | Prezzo | Feature |
|---|---|---|
| **Explorer** | €29,90 | Info destinazione, attrazioni, eventi, meteo, ristoranti, audioguide, mappe |
| **Traveller** | €39,90 | Tutto Explorer + itinerario personale, hotel, voucher, check-in reminder, documenti |
| **Concierge** | €49,90 | Tutto Traveller + chiamate Vapi a ristoranti/hotel/taxi/musei in lingua locale |

**Note comuni a tutti i piani:**
- Risposta 24/7 in italiano
- Voce nativa nella lingua della destinazione per pronuncia frasi locali
- Auto-play audio Violetta su ogni risposta

**Upgrade self-service:** cliente in viaggio può upgradarsi direttamente da Telegram → link Stripe/PayPal personalizzato → webhook sblocca piano immediatamente

---

## 3. Flusso Acquisto e Attivazione

### Acquisto Web (autonomo)
```
Cliente paga su fourgo.it → Stripe/PayPal webhook
→ Crea TelegramBotSubscription {chatId, plan, expiresAt}
→ Bot Telegram: "✅ Piano [X] attivato!"
```

### Attivazione Manuale (Massimo)
```
/admin/bot-subscriptions → cerca cliente → seleziona piano → Attiva
→ Sistema notifica cliente su Telegram
→ Nessun pagamento richiesto (Massimo decide)
```

### Upgrade da Viaggio (self-service)
```
Cliente: "voglio prenotare un ristorante"
Violetta: "Questa funzione è disponibile con il piano Concierge (€49,90)"
→ Link pagamento personalizzato con chatId
→ Cliente paga → webhook sblocca Concierge istantaneamente
```

### Verifica Piano nel Bot
```typescript
TelegramBotSubscription WHERE chatId = X AND expiresAt > NOW()
→ plan = 'concierge' → abilita Vapi
→ plan = 'explorer'/'traveller' → messaggio upgrade
→ nessun record → messaggio acquisto
```

---

## 4. 6 Assistant Vapi

### 4GO-OutboundBooking
**Uso:** Prenotazioni ristoranti (piano Concierge)
**Lingua:** Lingua locale della destinazione
**First Message:** `{{opening_message}}`
**Variabili:** `{{destination_language}}` `{{client_name}}` `{{guests}}` `{{date}}` `{{time}}` `{{special_requests}}`
**Comportamento:** Si presenta come assistente di 4GO FourTravel, chiama il ristorante nella sua lingua, conferma prenotazione, lascia contatto +39 02 9108 4274 se necessario. Se chiesto se è AI: "sono un assistente che chiama per conto dell'agenzia"

### 4GO-PreDepartureReminder
**Uso:** Reminder automatico 24h prima della partenza
**Lingua:** Italiano
**First Message:** `Ciao {{client_name}}, sono Violetta di 4GO FourTravel! Ti chiamo per ricordarti del tuo viaggio a {{destination}} di domani — sei pronto?`
**Variabili:** `{{client_name}}` `{{destination}}` `{{departure_date}}` `{{departure_time}}` `{{transport_details}}` `{{first_hotel}}` `{{transfer_details}}`
**Trigger:** Cron automatico 24h prima della data di partenza

### 4GO-ReviewRequest
**Uso:** Richiesta recensione 5 giorni dopo il rientro
**Lingua:** Italiano
**First Message:** `Ciao {{client_name}}, sono Violetta di 4GO FourTravel! Spero che il viaggio a {{destination}} sia andato alla grande — hai un minuto per dirmi com'è andata?`
**Variabili:** `{{client_name}}` `{{destination}}` `{{return_date}}`
**Comportamento:** Mai insistente, max 1 richiesta di recensione, se problemi → promette follow-up da Massimo
**Trigger:** Cron automatico 5 giorni dopo returnDate

### 4GO-Inbound
**Uso:** Chiamate in arrivo fuori orario ufficio
**Lingua:** Italiano
**First Message:** `Benvenuto in 4GO FourTravel, sono Violetta. In questo momento i nostri consulenti non sono disponibili. Posso aiutarti o raccogliere il tuo messaggio?`
**Orari ufficio:** Lun-Ven 9:30-19:00, Sab 9:30-13:00
**Trigger:** Chiamata in arrivo sul numero Telnyx

### 4GO-ServiceBooking
**Uso:** Hotel upgrade, taxi, musei, attrazioni (piano Concierge)
**Lingua:** Lingua locale della destinazione
**First Message:** `{{opening_message}}`
**Variabili:** `{{destination_language}}` `{{client_name}}` `{{service_type}}` `{{date}}` `{{time}}` `{{service_details}}` `{{pickup_location}}` `{{guests}}`
**Tipi di servizio:** HOTEL_UPGRADE / TAXI / MUSEUM_ATTRACTION

### 4GO-TransferConfirm
**Uso:** Conferma pickup transfer con autista/azienda
**Lingua:** Lingua locale della destinazione
**First Message:** `{{opening_message}}`
**Variabili:** `{{destination_language}}` `{{client_name}}` `{{pickup_location}}` `{{pickup_time}}` `{{date}}` `{{dropoff_location}}` `{{transport_number}}` `{{guests}}` `{{luggage_details}}`

---

## 5. Configurazione Vapi (comune a tutti gli assistant)

| Parametro | Valore |
|---|---|
| Model | Claude Haiku 4.5 |
| Temperature | ~0.3 (Precise) |
| Max Tokens | 250 |
| Voice | ElevenLabs eleven_multilingual_v2 |
| Transcriber | Deepgram Nova 3 Multilingual |
| firstMessageMode | assistant-waits-for-user |
| Max durata | 3 minuti (2 per TransferConfirm) |

**Voce per lingua** — mappatura dal DB `voicesByLanguage`:
- 🇫🇷 fr → Alice `Xb7hH8MSUJpSbSDYk0k2`
- 🇬🇧 en → Alice `Xb7hH8MSUJpSbSDYk0k2`
- 🇪🇸 es → Sarah `EXAVITQu4vr4xnSDxMaL`
- 🇩🇪 de → Jessica `cgSgspJ2msm6clMCkdW9`
- 🇯🇵 ja → Alice `Xb7hH8MSUJpSbSDYk0k2`
- 🇨🇳 zh → Jessica `cgSgspJ2msm6clMCkdW9`
- 🇦🇪 ar → Sarah `EXAVITQu4vr4xnSDxMaL`
- 🇳🇱 nl → Lily `pFZP5JQG7iQjIQuC4Bku`
- 🇧🇷 pt → Brian `nPczCjzI2devNBz1zQrb`
- 🇬🇷 el → George `JBFqnCBsd6RMkjVDRZzb`
- 🇰🇷 ko → `sf8Bpb1IU97NI9BHSMRf`

---

## 6. Flusso Telegram → Vapi (da implementare)

```
Cliente Telegram (piano Concierge):
"prenota al ristorante Le Saint-Hilaire stasera alle 20 per 2"

Bot legge TelegramBotSubscription → plan = 'concierge' ✅
Bot identifica intent: RESTAURANT_BOOKING
Bot legge destinazione dalla sessione → Normandia → lingua = 'fr'
Bot legge voicesByLanguage['fr'] → Alice

Bot chiama Vapi API:
POST https://api.vapi.ai/call/phone
{
  assistantId: "640e941e-...",
  phoneNumberId: "[telnyx_number_id]",
  customer: { number: "+33XXXXXXXXX" },  // numero ristorante da Brave Search
  assistantOverrides: {
    variableValues: {
      opening_message: "Bonjour, je vous appelle de la part de l'agence 4GO FourTravel de Milan.",
      destination_language: "French",
      client_name: "Antonio Ferrari",
      guests: "2",
      date: "15 maggio 2026",
      time: "20:00"
    },
    voice: { voiceId: "Xb7hH8MSUJpSbSDYk0k2" }
  }
}

Vapi chiama il ristorante con voce Alice in francese
Al termine → webhook Vapi → bot Telegram avvisa cliente:
"✅ Prenotato! Tavolo per 2 alle 20:00 da Le Saint-Hilaire"
```

---

## 7. Mapping Destinazione → Lingua

Già implementato nel webhook Telegram (`DEST_LANG_MAP`):

| Destinazioni | Codice | Voce |
|---|---|---|
| Normandia, Parigi, Provenza, Bretagna | fr | Alice |
| Londra, USA, Australia, Canada | en | Alice |
| Madrid, Barcellona, Siviglia, Ibiza | es | Sarah |
| Berlino, Monaco, Vienna | de | Jessica |
| Giappone, Tokyo, Kyoto, Osaka | ja | Alice |
| Cina, Shanghai, Pechino | zh | Jessica |
| Dubai, Marocco, Oman, Egitto | ar | Sarah |
| Amsterdam, Olanda | nl | Lily |
| Lisbona, Porto, Brasile | pt | Brian |
| Grecia, Atene, Santorini | el | George |
| Corea, Seoul | ko | sf8Bpb1IU97NI9BHSMRf |
| Praga, Cechia | cs | Jessica |

---

## 8. Nota sulla Privacy Vapi

Vapi non dichiara di essere AI se non esplicitamente interrogato. Script:
- Si presenta come: *"assistente di 4GO FourTravel travel agency di Milano"*
- Se chiede se è AI: *"sono un assistente che chiama per conto dell'agenzia"*
- Chiama a nome dell'agenzia → normale per i ristoranti (succede ogni giorno)
- Disclaimer sul sito: *"In alcuni casi il ristorante potrebbe preferire prenotazione diretta — Violetta fornisce il numero e la frase esatta da usare"*

---

## 9. Traduzione Bidirezionale al Tavolo

**Scenario:** cliente seduto al ristorante, cameriere parla solo la lingua locale.

### Fase 1 — Opzione 2: Vocale Telegram (da implementare)
1. Cliente scrive "traduci questo" e allega un **vocale** registrato dal cameriere
2. Violetta riceve il file audio via Telegram
3. Trascrive con Whisper/Deepgram nella lingua locale
4. Traduce in italiano e risponde al cliente

**Stack:** Telegram voice message → `/api/telegram/webhook` legge `voice.file_id` → download file → Deepgram STT → Claude traduce → risposta testo

### Fase 2 — Opzione 3: Vapi Live Interpreter (dopo test Telnyx)
1. Cliente chiede "fai da interprete con il cameriere"
2. Vapi apre chiamata — cliente mette in vivavoce
3. Violetta interpreta in tempo reale italiano ↔ lingua locale
4. Sostituisce completamente l'Opzione 2

**Nota:** Opzione 3 sostituirà Opzione 2 quando il flusso Vapi+Telnyx sarà testato e stabile.

---

## 10. Prossimi Passi

1. ⏳ **Attendere approvazione KYC Telnyx** (2-5 giorni lavorativi)
2. **Acquistare numero** +39 02 89608767 su Telnyx
3. **Collegare Telnyx a Vapi** (Phone Numbers → Import from Telnyx)
4. **Test chiamata reale** da dashboard Vapi
5. **Implementare webhook Telegram → Vapi** (intent detection + API call)
6. **Schema DB** `TelegramBotSubscription` (chatId, plan, expiresAt, activatedBy)
7. **Flusso acquisto** Stripe/PayPal → attivazione automatica
8. **Pannello admin** `/admin/bot-subscriptions` per Massimo
9. **Pagina prodotto** Violetta + video HeyGen demo
10. **SEO/AEO** keyword "chatbot viaggio AI", "assistente viaggio telegram"
