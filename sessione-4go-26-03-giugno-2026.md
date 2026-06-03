# Sessione 4GO-26 — 3 Giugno 2026

## GBP (Google Business Profile)
- Post giornaliero GSC-driven: ogni giorno alle 8:00 pubblica il pacchetto con più impressioni e 0 clic
- Fallback rotazione giornaliera se GSC non trova candidati
- `topicType: STANDARD` (OFFER richiedeva event con date)
- Notifica TG a Emi in caso di errore con dettaglio
- **Business Information** (`/api/admin/gbp-business-info`): GET/PATCH descrizione, telefono, sito
- **Insights** (`/api/admin/gbp-insights`): metriche 90gg per metrica singola (GET params)
- Admin page `/admin/gbp` sotto Sistema (superOnly)
- Bing Places: sincronizzazione automatica da GBP, nessuna API per post

## Traghetti
- `⛴️ Traghetto` aggiunto a `TRANSPORT_TYPES` in admin/trasporto
- PDF drag&drop supportato (Claude document API, context `auto`)
- Auto-detect tipo da PDF → aggiorna `selectedType`
- Fares traghetto: data/ora per tratta con fallback `isReturn` (idx>0 o label "ritorno")
- Bug fix: branded fares block skippato per traghetto (`detectedType !== 'traghetto'`)
- Logo carrier via Brandfetch API (`1id2OiXHek8IlrSXulv`)

## Location admin
- **Rimozione caparra**: rimossa completamente da form, calcoli, visualizzazioni, email
- **Multi-hotel PDF**: drag PDF con 7 pagine → importa tutti come 7 opzioni in un unico record
  - Risposta API array `items[]` + fallback `data` primo elemento
  - max_tokens 4000 per PDF, strip markdown fences nel parsing
- **Titolo pratica + Destinazione**: campi editabili manualmente in cima al form
- **Rigenera foto**: bottone 🖼️ per ogni opzione hotel → Pexels casuale tra 15 risultati
- **Trasporto agganciato**: dropdown seleziona traghetto → email unificata
- **Pagina pubblica** `/location/[code]`:
  - Sezione traghetto con logo Brandfetch, date andata/ritorno
  - Selezione hotel → totale hotel + traghetto in fondo
  - Checkbox "Ho letto le note importanti" obbligatorio
  - Bottone "Invia richiesta" → notifica TG a Massimo
  - Pagina conferma dopo invio
- **Endpoint confirm** `/api/location/confirm`: salva selezione + notifica TG Massimo

## Email AI
- Fix escalation: nuove email classificate subito ESCALATED ora ricevono forward a Massimo + cortesia cliente in lingua + notifica TG

## Fix SSG staging
- Fallback raw SQL per `Package.isTopOffer`, `Review.source`, `Post.source` su staging
- Migrate endpoint: aggiunge colonne mancanti + fix cast `gammaMonthlyBudget::integer`

## TikTok
- Risubmit con descrizione multi-agenzia (107 char): "Platform for Italian travel agencies to manage and publish travel content to their TikTok business accounts."
- Se rigettato: piano B con Buffer (auto-publish, crosspost Instagram/Facebook)

## Pexels
- `pexels-hotel`: 15 risultati, foto casuale invece di sempre la prima

## Migrations applicate
- `20260602_package_urgency`: isTopOffer, isLastMinute, urgencyText, urgencyExpiry
- `20260602_aviationstack_counter`: aviationstackCalls, aviationstackMonth
- `20260603_review_source`: Review.source
- `20260603_location_trasporto`: Location.trasportoId
