# Sessione 4GO-27 — 5-6 Giugno 2026

## Completato

### Notifiche WhatsApp su invio documenti
- `sendClientNotification` ora logga dettagliatamente WA (✅/❌/⚠️/ℹ️) nei Vercel Runtime Logs
- WhatsApp già attivo via Meta (WHATSAPP_TOKEN configurato)
- Test confermato: Sharon Giannino 3334625094, msgId wamid

### Location — email unificata fix
- Nome hotel: legge `o.name` (non `o.label` che era undefined)
- Subject senza prezzo: "Preventivo Sardegna — Alloggio + Traghetto"
- Totale "Da definire" se opzioni hotel presenti
- Bottone CTA "👉 Scegli la tua soluzione →" aggiunto

### Blog admin
- Subtitle: "Pagina X · N di TOT articoli" (era solo N)

### WhatsApp escalation
- Email a massimo@fourgo.it su ogni escalation (oltre Telegram+WA)
- Copertura: escalation AI, limite 30 messaggi

### GBP post
- Foto random tra pacchetti e media multipli + fallback Pexels
- Rotazione topic per giorno dell'anno (non settimana) → destinazione diversa ogni giorno
- 12 angolature creative rotate giornalmente
- Dettagli pacchetto specifico nel prompt + opener vietati (Scopri, Paradiso, Vola a...)
- Anti-duplicato: se topic oggi == ieri → salta al successivo, salva in SiteSettings.gbpLastTopicDest

### Pacchetti — urgency e promozioni
- Schema: `originalPrice Decimal?` su Package
- Admin: prezzo barrato con badge % sconto calcolato automaticamente
- Admin lista: badge 🔥TOP / ⚡LAST MIN / ⏰scadenza + prezzo barrato con %
- Pagina pubblica: prezzo barrato + badge sconto + urgency con data in italiano
- PUT endpoint: aggiornato per salvare isTopOffer, isLastMinute, urgencyText, urgencyExpiry, originalPrice
- Sempre "IVA inclusa"

### Import catalogo PDF
- `/api/admin/import-catalog`: estrae N pacchetti da PDF tour operator via Claude Sonnet
- Crea DRAFT con tutti i campi: title, subtitle, destination, priceFrom, transport, availability, guideInfo, meals, included (uno per riga), seo completo
- Auto-genera ecoData basato sulla destinazione
- Anti-slug-duplicato con indice
- Pulsante "📄 Importa catalogo" in `/admin/pacchetti`
- Testato con catalogo BaoBab/ITC Turchia 2026 — 6 tour creati

### Password expiry
- Allineato a 90 giorni ovunque (era 60 nel login, 90 nel cron)
- Warning a 10 giorni (era 7)
- Al login: notifica Telegram solo all'operatore diretto (non a Massimo se è Alessia/Inga)
- Cron: stessa logica individuale

### Inbox organizer
- Fix critico: `imap.move(seqno, ...)` invece di `imap.move(uid, ...)` — UID e numero di sequenza IMAP sono diversi

### Blog autogen
- Haiku → Sonnet + max_tokens 6000→8000 per articoli 1800-2200 parole
- Batch 3→1 articolo per run (Sonnet ~45s/articolo, cron-job.org timeout 30s)
- blog-expand: aggiunto `?force=1` per ri-espandere post già espansi ma corti + ordine desc per trovare più recenti
- Fix risposta paginata `{posts,total,page}` in blog-autogen e blog-expand

### Admin packages
- Default take 100→500 (erano nascosti 10 pacchetti)

### SEO
- paderno-dugnano: title e desc ottimizzati per CTR
- vaccini-marocco: slug accorciato + redirect 301

### Violetta routing
- Google Maps Directions API con `departure_time=now` per traffico reale
- Origine automatica dal contesto viaggio se non specificata
- Ferry: tabella 20 rotte principali + fallback Perplexity
- Link prenotazione rimossi dalla risposta ferry

### GSC report
- Nota lag ⚠️ quando dati ieri sono zero

### Migrate endpoint
- Aggiunta colonna `gbpLastTopicDest` su SiteSettings
- ecoData generato per tutti i pacchetti che non ce l'hanno
- bookingCode + locationId su Trasporto/Location/MultiProposal

## REGOLA CONFERMATA
Ogni nuova colonna DB va aggiunta nel migrate endpoint come `ALTER TABLE IF NOT EXISTS`.

## File chiave modificati
```
src/app/api/cron/gbp-post/route.ts — rotazione giornaliera, angolature, anti-duplicato
src/app/api/cron/blog-autogen/route.ts — Sonnet, batch 1, fix paginazione
src/app/api/admin/blog-expand/route.ts — force=1, ordine desc
src/app/api/admin/import-catalog/route.ts — NUOVO, estrae pacchetti da PDF
src/app/api/admin/packages/[id]/route.ts — urgency fields nel PUT
src/app/api/admin/packages/route.ts — take 500
src/app/api/admin/migrate/route.ts — gbpLastTopicDest, ecoData, colonne varie
src/app/api/auth/mfa-status/route.ts — password expiry 90gg, notify operatore diretto
src/app/api/cron/password-expiry/route.ts — 10gg warning, notify individuale
src/app/api/cron/inbox-organizer/route.ts — fix seqno vs uid
src/app/api/cron/gsc-report/route.ts — nota lag
src/app/api/whatsapp/ai/route.ts — Google Directions routing, ferry table
src/app/api/whatsapp/webhook/route.ts — email escalation a Massimo
src/components/admin/PackageFormFull.tsx — campo originalPrice + anteprima badge
src/components/public/PackageCard.tsx — prezzo barrato + badge sconto
src/app/(public)/pacchetti/[slug]/page.tsx — prezzo barrato + urgency
src/app/(admin)/admin/pacchetti/page.tsx — badge lista + import catalogo
src/app/(public)/agenzia-viaggi/[citta]/page.tsx — paderno-dugnano SEO
next.config.mjs — redirect vaccini-marocco
prisma/schema.prisma — originalPrice su Package
```

## Pending
- Telnyx KYC: ordine ancora FAILED dopo KYC approvato — escalation inviata con richiesta responsabile, deadline 7 giugno
- 10 cron runs blog-autogen su cron-job.org (06:00-06:45 ogni 5 min)
- Migrate da eseguire su staging ep-sweet-rain
- Multi-preventivo ↗ 4GO ancora da aggiungere
