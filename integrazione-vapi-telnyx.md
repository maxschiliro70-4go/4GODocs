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
| Trascrizione | **Gladia** (multilingua avanzato) | — | ~$0,01/min |
| **Totale per chiamata** | | | **~$0,11/min · ~$0,25-0,33 per prenotazione** |

**Vapi Assistant ID:** `640e941e-93e7-46ab-a91b-1959d9020374`

> **Nota trascrittore:** Deepgram Nova-3 supporta solo 10 lingue (no rumeno). Per lingue meno comuni usare **Gladia** come transcriber su Vapi — supporta 100+ lingue con rilevamento automatico. Cambiare in Vapi → Transcriber → Gladia.
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

**Env var richiesta:** `DEEPGRAM_API_KEY` → aggiungere su Vercel

**Stato:** ✅ Implementato (commit in sessione 4GO-21)

### Fase 2 — Opzione 3: Vapi Live Interpreter (dopo test Telnyx)
1. Cliente scrive "fai da interprete con il cameriere" su Telegram
2. Bot chiama Vapi API con assistant dedicato `4GO-Interpreter`
3. Vapi chiama il numero del cliente (o il cliente chiama il numero Telnyx)
4. Cliente mette in vivavoce al tavolo
5. Violetta interpreta in tempo reale italiano ↔ lingua locale
6. Al termine invia su Telegram un riassunto della conversazione

**Stack:** Telegram → Vapi outbound al numero cliente → ElevenLabs voce nativa → Deepgram STT multilingual → Claude traduce in tempo reale

**Assistant Vapi da creare:** `4GO-Interpreter`
- firstMessageMode: `assistant-waits-for-user`
- System prompt: ascolta in italiano e risponde nella lingua della destinazione, ascolta in lingua locale e risponde in italiano
- Variabili: `{{destination_language}}` `{{client_name}}`

**Nota:** Opzione 3 sostituirà Opzione 2 quando Vapi+Telnyx sarà testato e stabile. L'Opzione 2 (vocale Telegram) resta come fallback per situazioni dove la chiamata non è pratica.

### Uso pratico — Cliente al ristorante

**Scenario reale testato (16 maggio 2026):**
Cliente seduto al tavolo → cameriere parla solo rumeno → cliente usa Violetta come interprete:

1. Cliente scrive a Violetta: "digli che sono celiaco"
2. Violetta risponde con la frase in rumeno + bottone 🔊 per pronuncia
3. Cliente fa sentire il telefono al cameriere
4. Cameriere risponde → cliente registra vocale → manda a Violetta
5. Violetta trascrive (Deepgram STT multilingua) + traduce in italiano

**Funziona già con Opzione 2** — nessun ulteriore sviluppo necessario.
Con **Opzione 3** (Vapi live) il flusso sarà ancora più fluido: Violetta farà da interprete in tempo reale senza scambi di messaggi.

### Vapi per chiamate outbound al ristorante
Flusso separato e già testato in rumeno:
- Bot riconosce intent "prenota ristorante"
- Raccoglie: quante persone, data, ora, intolleranze
- Chiama il ristorante con Vapi nella lingua locale
- Variabili già convertite in lettere native via `vapi-utils.ts`
- Conferma torna su Telegram al cliente

---

## 10. Prompt OutboundBooking — Versione Produzione

**First Message:** `{{opening_message}}`
**firstMessageMode:** `assistant-waits-for-user`

> **Architettura prompt:** il System Prompt su Vapi è un **template fisso** con variabili `{{}}`. Le variabili vengono iniettate dinamicamente da 4GO via API ad ogni chiamata. Non si rigenera il prompt — si cambiano solo i valori delle variabili.

**System Prompt:**
```
You are a professional secretary calling on behalf of 4GO FourTravel,
an Italian travel agency based in Senago, near Milan, Italy.

ABSOLUTE RULE: Speak ONLY in {{destination_language}} for the ENTIRE call. No exceptions.
IMPORTANT: You are the CALLER. You called this restaurant. Never switch roles.

LANGUAGE RULE:
- Calling Italy: no mention of language barrier
- Calling abroad: mention clients do not speak the local language fluently

CONVERSATION RULES:
- ONE sentence at a time, then WAIT for response
- Maximum 1-2 sentences per turn

RESERVATION DETAILS:
- Client: {{client_name}} · Guests: {{guests}} · Date: {{date}} · Time: {{time}}
- Special requests: {{special_requests}}

STEP 1 — Introduce (WAIT): Present yourself as 4GO FourTravel secretary calling for {{client_name}}
STEP 2 — Request table for {{guests}} on {{date}} at {{time}} (WAIT)
STEP 3 — Special requests if any (WAIT)
STEP 4 — Confirm all details and thank
STEP 5 — If unavailable: ask for alternatives

If asked if AI: say you are an agency secretary. Never mention AI. Maximum 4 minutes.
```

**Variabili passate dall'API 4GO:**
```json
{
  "destination_language": "French",
  "opening_message": "Bonjour, je vous appelle de la part de l'agence 4GO FourTravel de Milan.",
  "client_name": "Antonio Ferrari",
  "guests": "2",
  "date": "16 maggio 2026",
  "time": "20:00",
  "special_requests": ""
}
```

**Mapping destinazione → lingua → voce:**
| Destinazione | destination_language | opening_message | voice_id |
|---|---|---|---|
| Normandia/Parigi | French | Bonjour, je vous appelle... | Alice Xb7hH8MSUJpSbSDYk0k2 |
| Romania/Bucarest | Romanian | Bună ziua, vă sun din partea... | (voce rumena) |
| Madrid/Barcellona | Spanish | Buenos días, llamo de parte... | Sarah EXAVITQu4vr4xnSDxMaL |
| Tokyo/Kyoto | Japanese | こんにちは、4GO FourTravelの... | Alice Xb7hH8MSUJpSbSDYk0k2 |
| Italia | Italian | Buongiorno, sono la segretaria... | Violetta |

---

## 11. Gestione CountryCode nelle Prenotazioni

Il `countryCode` è il campo che determina la configurazione Vapi per la chiamata (lingua, voce, opening message).

**Fonti in ordine di priorità:**
1. `ManualBooking.countryCode` — impostato manualmente da Massimo (sovrascrive tutto)
2. `Package.countryCode` — derivato automaticamente durante l'import del pacchetto
3. Auto-detect da `destination` tramite `DEST_COUNTRY_MAP` — usato nel form admin

**Admin prenotazioni manuali:**
- Campo destinazione → auto-detect country code (es. "Zanzibar" → `TZ`)
- Selezione pacchetto → auto-compila destinazione → auto-detect country code
- Dropdown multi-selezione con ricerca per destinazioni multiple (es. `PE,AW` per Perù+Aruba)
- Badge 🌍 visibile nella lista, modificabile nel form
- Vapi usa il **primo** codice per la lingua della chiamata

**Prenotazioni automatiche (online):**
- `countryCode` letto da `Package.countryCode` (già popolato durante import AI)
- Se mancante → Massimo può aggiungere manualmente dalla lista prenotazioni

**TODO — flusso acquisto bot Violetta:**
- Il campo `destination` diventa **obbligatorio** nel form acquisto
- Al completamento Stripe/PayPal → auto-populate `countryCode` da destinazione
- Il bot Telegram avrà accesso al `countryCode` per configurare Vapi automaticamente

---

## 12. Prossimi Passi

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

---

## 12. Mapping Country → Vapi Config Ottimale

Quando 4GO chiama l'API Vapi, passa la configurazione ottimale in base alla country della destinazione:

```typescript
const COUNTRY_VAPI_CONFIG: Record<string, {
  language: string,        // Deepgram language code
  destLanguage: string,    // Testo per system prompt
  openingMessage: string,  // First message nella lingua locale
  voiceId: string,         // ElevenLabs voice ID
}> = {
  // EUROPA
  FR: { language: 'fr', destLanguage: 'French', openingMessage: 'Bonjour, je vous appelle de la part de l\'agence for go FourTravel de Milan.', voiceId: 'Xb7hH8MSUJpSbSDYk0k2' },
  IT: { language: 'it', destLanguage: 'Italian', openingMessage: 'Buongiorno, sono la segretaria dell\'agenzia for go FourTravel di Milano.', voiceId: '[voiceId Violetta]' },
  ES: { language: 'es', destLanguage: 'Spanish', openingMessage: 'Buenos días, llamo de parte de la agencia for go FourTravel de Milán.', voiceId: 'EXAVITQu4vr4xnSDxMaL' },
  DE: { language: 'de', destLanguage: 'German', openingMessage: 'Guten Tag, ich rufe im Namen der Reiseagentur for go FourTravel aus Mailand an.', voiceId: 'cgSgspJ2msm6clMCkdW9' },
  PT: { language: 'pt', destLanguage: 'Portuguese', openingMessage: 'Bom dia, ligo em nome da agência for go FourTravel de Milão.', voiceId: 'nPczCjzI2devNBz1zQrb' },
  NL: { language: 'nl', destLanguage: 'Dutch', openingMessage: 'Goedemorgen, ik bel namens reisagentschap for go FourTravel uit Milaan.', voiceId: 'pFZP5JQG7iQjIQuC4Bku' },
  GR: { language: 'el', destLanguage: 'Greek', openingMessage: 'Καλημέρα, τηλεφωνώ εκ μέρους του τουριστικού γραφείου for go FourTravel από το Μιλάνο.', voiceId: 'JBFqnCBsd6RMkjVDRZzb' },
  RO: { language: 'ro', destLanguage: 'Romanian', openingMessage: 'Bună ziua, vă sun din partea agenției for go FourTravel din Milano.', voiceId: 'EXAVITQu4vr4xnSDxMaL' },
  CZ: { language: 'cs', destLanguage: 'Czech', openingMessage: 'Dobrý den, volám jménem cestovní agentury for go FourTravel z Milána.', voiceId: 'cgSgspJ2msm6clMCkdW9' },

  // ASIA
  JP: { language: 'ja', destLanguage: 'Japanese', openingMessage: 'こんにちは、ミラノのfor go FourTravelという旅行代理店からお電話しています。', voiceId: 'Xb7hH8MSUJpSbSDYk0k2' },
  CN: { language: 'zh-CN', destLanguage: 'Chinese', openingMessage: '您好，我是来自米兰for go FourTravel旅行社的助理。', voiceId: 'cgSgspJ2msm6clMCkdW9' },
  KR: { language: 'ko', destLanguage: 'Korean', openingMessage: '안녕하세요, 밀라노 for go FourTravel 여행사에서 전화드립니다.', voiceId: 'sf8Bpb1IU97NI9BHSMRf' },

  // MEDIO ORIENTE / AFRICA
  MA: { language: 'ar', destLanguage: 'Arabic', openingMessage: 'مرحباً، أتصل بكم من وكالة سفر for go FourTravel في ميلانو.', voiceId: 'EXAVITQu4vr4xnSDxMaL' },
  EG: { language: 'ar', destLanguage: 'Arabic', openingMessage: 'مرحباً، أتصل بكم من وكالة سفر for go FourTravel في ميلانو.', voiceId: 'EXAVITQu4vr4xnSDxMaL' },

  // AMERICHE / OCEANIA (inglese)
  US: { language: 'en', destLanguage: 'English', openingMessage: 'Hello, I\'m calling on behalf of for go FourTravel travel agency from Milan, Italy.', voiceId: 'Xb7hH8MSUJpSbSDYk0k2' },
  AU: { language: 'en', destLanguage: 'English', openingMessage: 'Hello, I\'m calling on behalf of for go FourTravel travel agency from Milan, Italy.', voiceId: 'Xb7hH8MSUJpSbSDYk0k2' },
  GB: { language: 'en', destLanguage: 'English', openingMessage: 'Hello, I\'m calling on behalf of for go FourTravel travel agency from Milan, Italy.', voiceId: 'Xb7hH8MSUJpSbSDYk0k2' },

  // DEFAULT
  DEFAULT: { language: 'en', destLanguage: 'English', openingMessage: 'Hello, I\'m calling on behalf of for go FourTravel travel agency from Milan, Italy.', voiceId: 'Xb7hH8MSUJpSbSDYk0k2' },
}
```

**Uso nel webhook Telegram:**
```typescript
const country = getCountryFromDestination(destination) // es. 'FR' da 'Normandia'
const vapiConfig = COUNTRY_VAPI_CONFIG[country] || COUNTRY_VAPI_CONFIG['DEFAULT']

// Chiamata API Vapi
const vapiCall = {
  assistantOverrides: {
    model: { provider: 'openai', model: 'gpt-4o-mini' },
    transcriber: { provider: 'deepgram', model: 'nova-3', language: vapiConfig.language },
    voice: { provider: '11labs', voiceId: vapiConfig.voiceId },
    variableValues: {
      destination_language: vapiConfig.destLanguage,
      opening_message: vapiConfig.openingMessage,
      client_name: session.participantName,
      guests: parsedGuests,
      date: parsedDate,
      time: parsedTime,
      special_requests: parsedSpecialRequests,
    }
  }
}
```
