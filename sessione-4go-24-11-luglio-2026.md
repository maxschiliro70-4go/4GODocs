# Sessione 4GO-24 — Continuazione 11 Luglio 2026

Continuazione della sessione 4GO-24 (vedi `sessione-24-riepilogo.md` per la parte 7-8 luglio). Giornata molto lunga, copre: GBP token, Reteimprese, SEO scraper, redesign `/preventivo`, bug pdf-parse, allucinazione Violetta (caso reale cliente), Telnyx→Twilio, policy documenti viaggio, cron nuovo.

**Stato branch a fine sessione:**
- `main`: `08574ad9`
- `develop`: `dda666d0`

---

## 1. Google Business Profile — token OAuth

**Problema**: `❌ GBP post saltato — token non valido: invalid_grant`.

**Diagnosi**: non era il bug del `refresh_token || null` (già corretto in sessione precedente, 24 giugno). Causa reale: l'app OAuth su Google Cloud era stata pubblicata (Testing → Production) la settimana precedente — Google invalida i refresh token emessi durante il periodo Testing quando l'app passa in produzione.

**Fix**: ri-autenticazione manuale via `/api/admin/gbp-auth?secret=4go2026`. Token nuovo emesso sotto stato "Production", testato con successo (`/api/admin/gbp-post?secret=4go2026` → post pubblicato su Uzbekistan/Tajikistan con foto e CTA).

**Nota**: `/api/cron/gbp-post` è disattivato (sostituito da `/api/admin/gbp-post`) — verificare che cron-job.org punti all'endpoint giusto.

---

## 2. Reteimprese.it — profilo completato

- Scritta e inserita **Descrizione Azienda** (139 parole precedenti, sostituita) e sezione **Chi Siamo** (storia/missione/filosofia)
- Compilate **34 parole chiave** (su 200 disponibili) nello Strumento Parole Chiave — mix di query locali (agenzia viaggi + 5 città satellite) e destinazioni di punta
- Minisito approvato e live: `reteimprese.it/fourgo` → contiene link reale a `www.fourgo.it`, utile come citazione locale NAP anche se probabile nofollow

---

## 3. SEO — scraper SERP e correzioni

**Analisi SERP** su query locali (agenzia viaggi + 5 città + Senago) e query destinazione (9 pacchetti):
- `agviaggi.it/citta.cfm?citta=SENAGO` è una directory generica aggregata, non un competitor con contenuti migliori — non facilmente "battibile" solo con copy in pagina
- Trovata **cannibalizzazione OG**: le pagine città avevano l'Open Graph title/description sempre generico anche quando `CITY_SEO` aveva un override dedicato (Senago già deduplicata nel `<title>` principale ma non nell'OG) → fix: l'OG ora segue lo stesso override
- Aggiunto **paragrafo fisso** su `/agenzia-viaggi/senago` con la frase esatta "agenzia viaggi a Senago" (query breve, prima scoperta) accanto a quella lunga già coperta dal title
- **9 pacchetti destinazione** ottimizzati (seoTitle/seoDescription via SQL diretto su `Package`): Route 66, Sudafrica, Vietnam, Uzbekistan (slug corretto dopo tentativo iniziale sbagliato), Tanzania (slug corretto), Nuova Zelanda, Marocco, Giappone, Australia, Seychelles — tutti poi revalidati via `/api/revalidate`

**GSC report (6→8 luglio)**: calo settimanale sembra essersi stabilizzato dopo il tonfo iniziale. "agenzia viaggi paderno dugnano" primo click di sempre. "agenzia viaggi senago" salita costante di posizione/impressioni ma ancora zero click nonostante pos.3 — pattern da monitorare (title poco invitante in SERP?). Indicazioni GBP in calo netto (-14) da tenere d'occhio.

---

## 4. Redesign `/preventivo` — mappa mondo interattiva

Sostituito il form statico con una **mappa del mondo cliccabile a due livelli** (drill-down):

- **Dati reali**: 180 paesi estratti dalla fonte originale CC BY-SA (Al MacDonald/Fritz Lekschas, `flekschas/simple-world-map`) — path SVG per singolo paese, non solo per regione. Estrazione corretta gestendo sia `<path id>` singoli sia `<g id>` multi-parte (isole/arcipelaghi) — la prima estrazione naif aveva perso USA, Canada, Cina, UK, Francia, Grecia, Giappone perché usano il secondo formato
- **Zoom animato**: click su regione → `getBBox()` nativo del browser + CSS transform (attributo SVG nativo, non proprietà CSS — la prima versione con `transform-box:view-box` non funzionava in modo affidabile) per centrare/ingrandire sulla regione, poi mostra i singoli paesi cliccabili
- **Ora locale live**: fuso IANA per tutti i 180 paesi + differenza oraria rispetto all'Italia calcolata via Intl API (gestisce DST automaticamente)
- **Hero e disclaimer resi theme-aware**: erano hardcoded su sfondo scuro fisso, non seguivano il toggle chiaro/scuro del sito
- **SEO/AEO/GEO**: schema `Service`+`Offer`, `FAQPage` con 6 domande reali, stessa FAQ visibile in pagina (non solo JSON-LD nascosto, per essere citabile da motori di risposta come Perplexity/ChatGPT)
- Preservata la lista `TRIP_TYPES` estesa (20 voci) che main aveva già, diversa da quella base di develop

Iterazione basata su feedback diretto: prima versione (wizard a step) scartata su richiesta esplicita, sostituita con mappa cliccabile.

---

## 5. Bug pdf-parse — causa radice trovata e centralizzata

**Sintomo**: preventivi multi-proposta con dati mancanti/oggetti al posto di stringhe (es. hotel "Non determinabile"), log Vercel mostravano `text len: 0` su ogni PDF.

**Causa reale**: `pdf-parse` è stato aggiornato alla v2, cambiando API da funzione a classe (`PDFParse.getText()`). La vecchia chiamata falliva sempre silenziosamente (catch vuoto) — il sistema si affidava solo a Sonnet a leggere il PDF grezzo, senza mai avere l'estrazione testuale deterministica come prima linea.

**Fix**: centralizzato in `src/lib/pdfExtract.ts`, condiviso dai 4 punti d'uso (preventivi-poll, tg-documents, telegram webhook x2 testo/voce). Rimossa dipendenza `@napi-rs/canvas` aggiunta per errore (verificato che non serviva). Aggiunta notifica Telegram automatica **solo a Emi** (non a tutti gli operatori — errore tecnico, non operativo) se l'errore rilevato sembra un nuovo cambio di API della libreria.

Anche corretto: bug di `git add` che aveva lasciato `/preventivo2` non rimossa da staging nonostante il filesystem locale fosse pulito.

---

## 6. Allucinazione Violetta — caso reale (cliente Giorgia, tratta Cancun)

**Segnalazione**: LangSmith flag `possible_hallucination: true` — il bot ha inventato orari/date/policy di conferma per traghetti (Bateaux Mouches, Cancun→Isla Mujeres) attribuendoli come confermati alla prenotazione reale della cliente, mescolando fatti generici trovati via Brave Search con dettagli mai verificati.

**Diagnosi in più fasi**:
1. Causa immediata: istruzione "usa SEMPRE conoscenze generali per orari di trasporti, non dire mai che non hai l'informazione" non distingueva tra domanda generica sulla destinazione e domanda sulla prenotazione specifica del cliente → aggiunta eccezione esplicita
2. Il marcatore silenzioso `GIRA_OPERATORE` esisteva già ma era scoped solo a escursioni/voucher, non a trasporti → esteso
3. **Causa strutturale vera**: le notifiche agli operatori (funzione `notifyAll` locale nel webhook, mai collegata a `src/lib/telegram.ts`) passavano da `sendMessage()`/`tg()` che usa il **token staging-aware** (`TELEGRAM_BOT_TOKEN_STAGING` su develop) — su staging le notifiche partivano quindi verso il bot di test, mai attivato da nessun operatore, fallendo silenziosamente. Confermato con log reali (`"error":"Unauthorized"`)
4. Fix: `tgOperator()` dedicato che usa **sempre** il token di produzione per le notifiche interne, indipendentemente da quale bot sta parlando col cliente
5. Rete di sicurezza `redirectsToAgency` (rileva quando il bot reindirizza il cliente senza marcatore esplicito) affinata su **3 casi reali** testati insieme a Emi: lista verbi troppo stretta (mancava "il team risponde subito"), poi controllo a livello di frase invece che sull'intero messaggio (evita falso positivo tipo "chiama [terzi]" in una frase + "l'agenzia" in un'altra — caso Bateaux Mouches)
6. Aggiunto: Perplexity e Brave lavorano **insieme** (non alternativi) per domande booking-specific trasversali a tutte le categorie, con skip automatico se i documenti caricati già coprono l'argomento

Tutto testato in diretta con Emi su `@FourGoTraveltestBot`, poi portato su `main` dopo validazione multipla.

---

## 7. Telnyx → Twilio

Card `/admin/impostazioni` sostituita: Twilio (`+39 02 5002 0031`, ticket #27499373) ora principale, Telnyx retrocessa a card secondaria "dismesso" con saldo residuo visibile per riferimento (non eliminata del tutto).

---

## 8. Policy documenti di viaggio — 7gg / 48h

**Incoerenza trovata**: i termini legali di `/violetta` promettevano "documenti entro 24 ore lavorative dall'acquisto" — in contraddizione con la policy operativa reale (7 giorni prima della partenza). Probabile causa radice dell'aspettativa sbagliata di clienti come Giorgia.

**Corretto**:
- Explorer: nessun documento necessario, attivazione entro 48h (clausola prima mancante)
- Traveller/Concierge: documenti entro 7gg dalla partenza, non prima — testo legale, descrizione piano e email di conferma prenotazione tutti allineati
- Email di conferma prenotazione: aggiunto in grassetto l'avviso 7gg + disclaimer privacy/trattamento dati (Anthropic, LangSmith, Perplexity, Groq, ElevenLabs) mancante nella firma QR — esisteva solo nella welcome email generica, non nel vero punto di attivazione

**Pulsante "Documenti viaggio" in Telegram**: prima tentativo di nasconderlo quando non ci sono documenti (scartato su richiesta esplicita — "non nasconderlo mai"), sostituito con **messaggio countdown-aware**: oltre 7gg dalla partenza tono rilassato, sotto 7gg più operativo. Pulsante sempre visibile.

**Nuovo cron `missing-docs-alert`**: notifica tutti gli operatori ogni 2 ore (8-20 IT) quando mancano **9 giorni** alla partenza (= 2gg prima della soglia dei 7gg promessa al cliente) e i documenti non sono ancora caricati. Si ferma da sola al caricamento (query esclude automaticamente prenotazioni con documenti presenti, nessuno stato/flag da gestire). Schedulato via Vercel Cron nativo (non cron-job.org, scelta esplicita) — 7 entry in `vercel.json` (6,8,10,12,14,16,18 UTC = 8-20 CEST). **Nota**: i cron Vercel girano solo su produzione, non su preview/staging.

---

## 9. Altre modifiche minori

- **Reteimprese**: link recensione Google aggiunto sulla pagina `/violetta/grazie` (punto di massima soddisfazione post-acquisto) — deciso di NON aggiungerlo su email preventivo/prenotazione né su WhatsApp (timing sbagliato, ridondante con sistema post-viaggio già esistente, costo template)
- **Chiarimenti Concierge** (nessuna modifica codice, solo verifica): acquisto standalone attiva `interpreteAttivo` automaticamente; interprete è via link web con Vapi SDK (browser, non telefonata reale); foto/audioguide sono native in Telegram e disponibili su tutti i piani, non solo Concierge
- **Pulizia**: 2 chiavi duplicate innocue rimosse (IS in COUNTRY_TZ telegram, lisbona in IATA_MAP whatsapp) — stesso valore in entrambe le occorrenze, nessun impatto funzionale

---

## Verifica finale

Commit `08574ad9` su `main` verificato con controllo completo: bilanciamento parentesi/graffe/quadre, compilazione TypeScript (nessun errore nuovo, solo 2 preesistenti già noti), nessuna funzione duplicata, firma `buildDossierKeyboard` invariata (23 chiamate preesistenti compatibili), `vercel.json` valido senza duplicati. Il conteggio backtick "dispari" segnalato durante la verifica è un falso positivo del metodo di conteggio grezzo (template literal multi-riga legittimi), non un problema reale — confermato dalla compilazione TypeScript pulita.

## Pending / da monitorare

- GSC: monitorare se il calo settimanale continua a stabilizzarsi nei prossimi report
- "agenzia viaggi senago" e query simili: posizione buona ma zero click — valutare A/B test su title più orientati all'azione
- MP-2026-ZJH1R dest #2: hotel "Non determinabile" per PDF parzialmente estratto — richiede PDF originale
- Registrare `missing-docs-alert` anche su cron-job.org se in futuro si decide di duplicare lo scheduling (per ora solo Vercel nativo, scelta esplicita)
- Interprete Concierge via telefonata reale (Telnyx/Twilio, non solo browser): non ancora costruito, se richiesto in futuro
