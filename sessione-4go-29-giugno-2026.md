# Sessione 4GO-29 — 29 Giugno 2026

## Stato branch
- **main** `0d8ad2df` — produzione
- **develop** `a8d9b1d5` — Violetta staging (noindex attivo)

---

## Violetta™ — develop

### Lingue ristorante
- Aggiunte **serbo (sr)** e **albanese (sq)** — ora 36 lingue totali
- `LANG_TO_ISO` aggiornato con sr e sq
- `NOVA3_SUPPORTED` esteso a tutte le lingue supportate da Nova-3
- `getRestaurantAssistantId` usa sempre Flux `686274c0` — transcriber dagli override

### Fix Arbat→Rabat
- Prompt Haiku: non correggere mai il nome scritto dal cliente (esempio esplicito Arbat→Mosca, non Rabat)
- Check 50km: se indirizzo geocodificato è >50km dalla destinazione del viaggio → chiede conferma
- Step `confirm_location` aggiunto nel flusso ristorante

### Intolleranze
- `step3` colloquiale in tutte le 36 lingue: "c'è un ospite celiaco — è un problema?"
- `step3_no_alternative` aggiunto in tutte le lingue
- Traduzione intolleranze in lingua nativa (cirillico per russo, cinese per zh ecc.)
- System prompt aggiornato: gestione rifiuto → chiede alternative → saluta e chiude
- Webhook Vapi: rifiuto per intolleranza → ripropone lista → radius ×2 se esaurita
- `triedRestaurants` tracciato per evitare ristoranti già provati

### Ricerca ristoranti
- 5 risultati inclusi quelli senza telefono
- Keyword filter rimosso — usa solo `type=restaurant`

### Pagina /violetta
- Sezione "Cosa sa fare" 10 card in griglia 5×2
- JSON-LD `SoftwareApplication` + `FAQPage` (6 FAQ)
- Metadata SEO completi in `layout.tsx` (keywords AEO/GEO)
- Sezione FAQ visibile accordion in fondo alla pagina
- CTA "✨ Prova Violetta™" rosso-blu lampeggiante prima di "Cosa sa fare"
- Badge App Store / Google Play sotto ogni bottone piano
- Sfondo nero (BgSlideshow rimosso dopo tentativi falliti)

---

## AI Act — main (entro 2 agosto 2026)

### ✅ Punto 2 — Disclosure social (art. 50)
- Caption Instagram: `✨ Contenuto creato con assistenza AI | #AIGenerated`
- Caption Facebook/Threads: `✨ Contenuto creato con assistenza AI.`
- Caption TikTok: `✨ #AIGenerated #AIContent`
- Pinterest: escluso (testo SEO puro)

### ✅ Punto 3 — Alfabetizzazione (art. 4)
- Email formazione inviata il 29/06/2026
- Conferme ricevute: Alessia ✅ Inga ✅ Massimo ✅
- Registro formazione scaricabile da `/admin/gdpr`
- Endpoint: `GET /api/admin/ai-act-archivio` → scarica file .txt

### ✅ Punto 4 — DPIA (art. 35 GDPR)
- Documento Word generato: DPIA_FRIA_Violetta_4GO.docx
- Rischi: bassi. Nessuna consultazione Garante necessaria
- Da firmare da Massimo

### ✅ Punto 5 — FRIA (art. 27 AI Act)
- Violetta™ = rischio limitato — FRIA completa non obbligatoria
- Esclusione motivata documentata nel documento Word
- Da firmare da Massimo

### admin/gdpr
- 4 card stato AI Act: DPIA ✅ FRIA ✅ Alfabetizzazione ✅ Disclosure social ✅

---

## Blog — main

### Fix foto duplicate (causa root)
- **Bug**: Pexels/Unsplash/Pixabay restituiscono stessa foto con URL diversi (parametri query variabili)
- **Fix**: `normalizeImageUrl()` estrae solo il pathname prima del confronto `usedImages.has()`
- **Fix**: check anti-duplicato nel `POST /api/admin/blog` al momento del create (confronto con `split_part(coverImage, '?', 1)`)

### Nuovi strumenti
- `action=fix-images`: sostituisce foto duplicate con nuove da Pexels→Unsplash→Pixabay, batch 20, con `offset`
- `action=fix-years`: rimuove anno (202x) dai titoli esistenti (16 sistemati)
- `action=fix-slugs`: rimuove anno dagli slug esistenti (4 sistemati)
- Redirect 301 automatico in `blog/[slug]/page.tsx`: slug-con-anno → slug-senza-anno
- Cron `blog-fix-images` alle 7:00 con notifica TG dopo le run 6:00-6:45

### Prevenzione futura
- `COUNTRY_MAP`: aggrega destinazioni correlate (Gili/Lombok/Bali → indonesia) per evitare saturazione
- Anno rimosso dai nuovi titoli prima del salvataggio (regex su `article.title`)
- Anno rimosso nei nuovi slug (derivato dal titolo già pulito)

---

## SEO — main
- Homepage title: `'Agenzia viaggi Senago Milano — 4GO FourTravel | Vicino a te'`
- Homepage description: Alpitour/Costa/MSC + ☎ per CTR
- GSC: "agenzia viaggi senago" pos. 3, 25 imp — description aggiornata

---

## TikTok Developer Portal
- Redirect domain `fourgo.it` verificato in URL Properties
- Web/Desktop URL corretto: `https://fourgo.it/admin/social`
- Descrizione scope aggiornata con `video.publish` esplicito
- Risottomesso per review

---

## Prossimi step
1. **Russo** — test completo end-to-end su staging
2. **Cherry-pick develop→main** — dopo autorizzazione Emi post-staging
3. **SIAE** — spedire raccomandata A/R (tutto pronto)
4. **DPIA/FRIA** — firmare da Massimo e conservare
5. **TikTok** — attendere risposta review
6. **Violetta go-live** — dopo SIAE: rimuovi noindex → submit GSC → listing directory AI

---

## Regole invariate
- Branch protection: webhook/route.ts, middleware, auth, pagamenti, schema DB → sempre su develop
- REGOLA ASSOLUTA: leggere file prima di modificare, mai commit speculativi
- `temperature` NON supportato in `assistantOverrides` Vapi
- Nuove colonne DB sempre nel migrate endpoint come `ALTER TABLE IF NOT EXISTS`
