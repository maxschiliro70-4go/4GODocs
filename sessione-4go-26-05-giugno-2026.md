# Sessione 4GO-26 — 5 Giugno 2026

## Completato

### sendClientNotification — lib condivisa
`src/lib/sendClientNotification.ts` — chiamata dopo ogni "Invia a cliente" su tutti i tipi di documento.
1. WhatsApp al cliente (se phone presente) con link alla pagina
2. Telegram a tutti gli operatori con conferma + link
3. Email interna a info@fourgo.it con dati + bottone link

Log in Vercel Runtime Logs: `[SEND-NOTIFY] ✅/❌/⚠️/ℹ️`

Integrato in: preventivi, trasporto-send-link, location-send-unified, multi-proposal, travel-proposal.

### Location — multi-trasporti + lock
- Checkbox multi-selezione al posto del dropdown singolo
- Campo `trasportoIds Json @default("[]")` su Location
- Campo `locationId String?` su Trasporto — lock automatico al salvataggio
- Al save: trasporti selezionati ricevono `locationId`; nelle altre location appaiono grigi con badge "In uso"
- Sblocco automatico se rimossi
- Auto-migrazione al load: `trasportoId` legacy → `trasportoIds` array
- Fix critico: retroactive UPDATE SQL nel migrate endpoint per popolare `locationId` dai dati esistenti

### ↗ 4GO — bottone conversione uniforme
- Gradient `linear-gradient(135deg,#D10000,#002366)` su tutte le sezioni
- Condizione uniforme: `SENT/PAID` + no `bookingCode`
- Presente su: Location, Trasporto, Multi-hotel, Fly (proposte-viaggio)
- Preventivi: già esistente con logica propria (CONFIRMED/PAID)
- `convert-to-booking` endpoint unificato per tutti i tipi

### Blog
- blog-autogen: fix `q.map is not a function` — gestisce risposta paginata `{posts,total,page}` dall'API blog
- fix-blog-photos: aggiunge `revalidatePath` dopo update foto + shuffle Pexels
- Admin packages: default take 100→500 (erano nascosti 10 pacchetti)

### SEO
- paderno-dugnano: title con destinazioni specifiche, desc con "Scopri" + CTA forte
- vaccini-marocco: slug accorciato + redirect 301 dal vecchio slug lungo (160 char)

### WhatsApp escalation
- Aggiunta email a massimo@fourgo.it su ogni escalation (oltre a Telegram+WA)
- Copertura: escalation AI, limite 30 messaggi raggiunto

### Location email unificata
- Fix nome hotel: legge `o.name` invece di `o.label` (era undefined)
- Subject senza prezzo
- Totale "Da definire" se opzioni hotel presenti
- Bottone CTA "👉 Scegli la tua soluzione →" sempre presente

### Blog admin paginazione
- Subtitle: "Pagina X · N di TOT articoli"

## REGOLA CRITICA appresa
**Ogni nuova colonna DB va aggiunta nel migrate endpoint come `ALTER TABLE IF NOT EXISTS`, non solo nel migration file Prisma.**

Il migration file viene eseguito da `prisma migrate deploy` durante il build Vercel. Ma se il build fallisce (come con l'errore CookieConsent) o se la migration è già marcata come applicata, le nuove colonne non vengono create. Il migrate endpoint è l'unico modo garantito per applicare colonne a DB live.

Per retroattively popolare dati esistenti, aggiungere anche `UPDATE` SQL nel migrate endpoint.

## File chiave modificati
```
src/lib/sendClientNotification.ts — NUOVO
src/app/api/admin/convert-to-booking/route.ts — NUOVO
src/app/api/admin/migrate/route.ts — locationId/bookingCode + retroactive UPDATE
src/app/api/admin/location/route.ts — lock/unlock trasportoIds nel PATCH
src/app/api/admin/location-send-unified/route.ts — fix email + CTA
src/app/api/admin/trasporto-send-link/route.ts — sendClientNotification
src/app/api/admin/preventivi/route.ts — sendClientNotification
src/app/api/admin/multi-proposal/route.ts — sendClientNotification + ↗4GO
src/app/api/admin/travel-proposal/route.ts — sendClientNotification + ↗4GO
src/app/api/admin/packages/route.ts — take 500
src/app/api/admin/fix-blog-photos/route.ts — revalidatePath + shuffle
src/app/api/admin/whatsapp/webhook/route.ts — email escalation Massimo
src/app/api/cron/blog-autogen/route.ts — fix risposta paginata
src/app/(admin)/admin/location/page.tsx — multi-trasporti, lock, ↗4GO
src/app/(admin)/admin/trasporto/page.tsx — ↗4GO
src/app/(admin)/admin/multi-preventivo/page.tsx — ↗4GO
src/app/(admin)/admin/proposte-viaggio/page.tsx — ↗4GO
src/app/(admin)/admin/blog/page.tsx — subtitle paginazione
src/app/(public)/agenzia-viaggi/[citta]/page.tsx — paderno-dugnano SEO
next.config.mjs — redirect vaccini-marocco slug lungo
prisma/schema.prisma — Location.trasportoIds, Location.bookingCode, Trasporto.locationId, Trasporto.bookingCode, MultiProposal.bookingCode
prisma/migrations/20260604_multi_trasporto_convert/migration.sql — NUOVO
prisma/migrations/20260604_multiproposal_bookingcode/migration.sql — NUOVO
```

## Pending
- Applicare migrate su staging (ep-sweet-rain)
- Multi-preventivo ↗4GO (da aggiungere)
- Telnyx KYC ancora pending
- Demo-bot noindex da rimuovere al lancio Violetta commercial
