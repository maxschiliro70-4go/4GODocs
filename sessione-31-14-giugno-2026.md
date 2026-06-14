# Sessione 4GO-31 — 14 Giugno 2026

## LAVORO COMPLETATO

### Analytics / GA4
- GBP Insights API: abilitata Business Profile Performance API su Google Cloud Console → dati reali ora visibili in /admin/gbp
- Report GSC: aggiunta sezione GBP con delta 7gg vs 7 precedenti (impressioni search, clic sito, indicazioni, chiamate) — inviata solo a Emi
- 404: nessuna azione necessaria, già corretti con 301
- form_submit: configurato come evento chiave GA4 senza codice

### Review Request
- Notifica post-viaggio: Google Maps link come principale (https://g.page/r/CVUWOuYjlTnCEBM/review), Trustpilot come secondario
- Canali: giorno 1 TG+WA ringraziamento (Haiku personalizzato, anticipa recensione), giorni 2/3 WA+TG+email con link Google+Trustpilot
- "Bentornato/a" → "Bentornat*" inclusivo su tutti i canali

### Case Study — Feature Completa
**DB**: tabella CaseStudy (id, bookingCode, clientLabel, destination, travelType, content, excerpt, coverImage, slug, status)
**Flusso booking TG**: pratica → ARCHIVIATA → notifica Emi TG con link genera → generazione auto o /casestudy CODICE da Telegram
**Flusso WA**: tab "Da conversazione WA" con conversazioni ESCALATED/CLOSED/GESTITA
**Generazione**: Sonnet (non Haiku), 5 gold standard, angoli narrativi rotativi, anti-ripetizione da storie esistenti, anti-allucinazione assoluta, no markdown
**Privacy**: SOLO nome battesimo, MAI cognomi/telefoni/codici 4GO/email/importi/operatori
**Pagina pubblica**: /storie (in public) con Navbar/Footer, tema CSS vars, JSON-LD Article+BreadcrumbList+CollectionPage
**Sitemap**: /storie + ogni singola storia
**Homepage**: sezione tra ReviewsStrip e CTAFinale, condizionale (solo se storie pubblicate), revalidate 60s
**Admin /admin/case-study**: in menu Comunicazioni dopo Invia Benvenuto, icona Newspaper
  - Tab "Da prenotazione": dropdown ricercabile bookings ARCHIVIATA, escluse destinazioni già coperte
  - Tab "Da conversazione WA": lista conversazioni, filtro per nome
  - Lista PENDING con Pubblica/Rifiuta
  - Lista PUBLISHED con X (nascondi), 🗑 (elimina definitivamente con confirm)
  - Lista REJECTED con Ripristina
**Endpoint API**: list, bookings, wa-conversations, generate-wa, approve, reject, delete, reimage, fix-markdown, export, debug
**Reimage automatico**: dopo ogni approvazione (blog → Pexels → Pixabay)
**Comandi Telegram Emi**: /casestudy CODICE
**15 storie pubblicate**, fix-markdown applicato su 2

### Icone Admin
- Prenotazioni: BookOpen → ClipboardCheck
- Case Study: BookOpen → Newspaper

### Activity Log
- take: 500 → 5000 per coprire 30 giorni completi

## PENDENTE
- [ ] Eliminare duplicati: 2x Gabriele/Madrid e 1x Marco/Albania (usare pulsante 🗑 in admin/case-study)
- [ ] Reimage storie senza foto: https://fourgo.it/api/admin/case-study?secret=4go2026&action=reimage
- [ ] Migrate produzione: https://fourgo.it/api/admin/migrate?secret=4go2026
- [ ] Twilio: ticket #27499373, attesa risposta
- [ ] Telnyx: KYC requirement group 93cf51b4
- [ ] Meta Business verification: WhatsApp blocked
- [ ] UIBM: registrazione marchio "Violetta" classi 39+42
- [ ] EUIPO Voucher: rimborso 75% tasse deposito marchio UE
- [ ] RateHawk/ETG: mail inviata a start@ratehawk.com

## COMMIT PRINCIPALI OGGI
- fix: activity log take 500→5000
- feat: gsc-report sezione GBP con delta 7gg
- fix: review-request Google come principale, Trustpilot secondario + WA
- feat: case-study DB, API, pagina /storie, homepage, admin completo
- fix: storie in (public) per Navbar/Footer/tema
- feat: case-study Sonnet, gold standard x5, anti-allucinazione, privacy assoluta
- feat: case-study reimage auto dopo approvazione + Pixabay fallback
- feat: /casestudy CODICE da Telegram
- fix: apostrofi curvi in array angles
- fix: case-study esclude destinazione già coperta dal dropdown
