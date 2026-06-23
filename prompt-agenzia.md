# 4GO FourTravel — Sessione 37 (22 Giugno 2026)

## Stack
Next.js 15.5.16 + Prisma + Neon PostgreSQL + Vercel Pro
- main = fourgo.it | develop = staging.fourgo.it
- Test bot: @FourGoTraveltestBot

## Regole Branch (CRITICHE)
- develop → staging, test, feature
- main → fix urgenti produzione DIRETTI (no cherry-pick da develop)
- Mai double-build: se fix va su main, committi su main direttamente

## Flusso Ristorante — Stato Finale
- Assistant base: sempre VAPI_RESTAURANT_FLUX (686274c0)
- Transcriber override: nova-3 multi Flux, nova-3 monolingua per altre lingue
- Modello: Haiku (NO temperature in override — rompe Vapi)
- Posto → "non c'è problema ovunque va bene" nella lingua del ristorante
- Orario alternativo → accetta sempre senza discutere
- Post-call: Haiku estrae orario confermato → notifica cliente se diverso
- temperature:0 in assistantOverrides rompeva tutto l'override → rimosso

## Notifiche — Flusso Completo
### Quando scala (tutti i canali):
- Email a OPERATOR_EMAIL (Massimo) — NON a info@fourgo.it (loop AI)
- TG a tutti (Massimo, Alessia, Inga, Emi)
- WA a Massimo

### Contact Form ✅
- Risposta immediata al cliente via email
- Email a OPERATOR_EMAIL + CC info@fourgo.it
- TG a tutti + WA Massimo

### WhatsApp chatbot ✅
- Risposta immediata al cliente via WA
- Su escalation: TG tutti + WA Massimo + email Massimo
- Scala automaticamente dopo qualificazione completa (destinazione + periodo + preferenze)

### Email-ai ✅
- Risposta immediata al cliente via email
- Su escalation: TG tutti + WA Massimo + email Massimo
- Guard doppio invio: skip se thread aggiornato negli ultimi 60s

### Quiz ✅ (aggiunto oggi)
- TG a tutti + WA Massimo + email OPERATOR_EMAIL dopo completamento

### WhatsApp deduplicazione ✅
- Guard su wamid: skip se già processato (evita doppia risposta su retry Meta)

## Archiviazione Post-Viaggio
- Giorno 1: ringraziamento TG+WA
- Giorno 2-3: recensione email+TG+WA
- Giorno 5: ARCHIVIATA + notifica TG a Emi SOLO se violettaPlan presente
- Case study admin: mostra solo booking con violettaPlan

## Lakera Whitelist
- Aggiunto: mangi, mangiamo, si mangia

## Infra-Status Fix
- Vapi check: GET /assistant?limit=1 (non assistant ID hardcoded)
- Vercel soglie: 950 orange, 1200 red (deploy illimitati su Pro)

## SEO Città
- 41 pagine in sitemap e homepage
- Link interni da ogni post blog
- MilanToday su tutte le pagine città
- type=cities: https://fourgo.it/api/admin/index-google?secret=4go2026&type=cities

## Pending
- SIAE raccomandata A/R
- Telnyx numero italiano
- Merge develop→main completo (molte feature su develop)
- noindex /violetta e /demo-bot da rimuovere al lancio
- WhatsApp Business Meta pending
