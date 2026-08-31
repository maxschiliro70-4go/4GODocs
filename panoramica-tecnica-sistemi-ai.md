# 4GO FourTravel — Panoramica Tecnica e Sistemi AI

*Documento di riepilogo — Agosto 2026*

## Il progetto in breve

4GO FourTravel è la piattaforma digitale di 4FOURTRAVEL SRL, agenzia viaggi e tour operator a Senago (MI), attiva dal 1998. Il sito (fourgo.it) integra un catalogo pacchetti, un sistema di prenotazione, un blog automatizzato per la SEO, presenza social automatizzata, e **Violetta™** — l'assistente di viaggio AI, marchio registrato (UIBM n. 302026000109033), disponibile su Telegram, WhatsApp e telefono.

**Stack tecnico:** Next.js 15 + Prisma + Neon PostgreSQL, hosting Vercel Pro.

---

## Sistemi AI utilizzati

### Modelli linguistici (Anthropic Claude)
- **Claude Sonnet** — generazione contenuti complessi: metadata SEO, itinerari, geocoding, landing page, brochure Gamma per i preventivi
- **Claude Haiku** — compiti veloci e ad alto volume: classificazione mappe città, ricerca voli, classificazione email, chiamate Vapi (voce), generazione contenuti città, classificatori di intento (es. "il cliente vuole prenotare un ristorante?", "serve una ricerca web?")

### Voce e chiamate
- **Vapi** — piattaforma di chiamate vocali AI: Violetta chiama davvero ristoranti e hotel per conto del cliente (piano Concierge), con interpretariato simultaneo in tempo reale
- **Twilio** (numero primario) e **Telnyx** (fallback automatico) — infrastruttura telefonica sottostante alle chiamate Vapi
- **ElevenLabs** — voce di Violetta (sintesi vocale)
- **Groq Whisper** — trascrizione dei messaggi vocali inviati dai clienti su Telegram/WhatsApp

### Ricerca e traduzione
- **Perplexity** — verifica di fatti specifici e recenti (orari, prezzi, eventi) prima di rispondere a domande fattuali
- **Brave Search** — ricerche web con contesto di destinazione
- **DeepL API** — traduzioni scritte (piano Traveller+)
- **Google Cloud Vision + SerpApi Google Lens** — riconoscimento di monumenti/luoghi da foto inviate dai clienti

### Generazione immagini e presentazioni
- **Gamma** — generazione di brochure/preventivi in PDF con presentazioni curate
- **HeyGen** — generazione video (solo interfaccia web, nessuna API)

### Dati geografici e luoghi
- **Google Maps API** — geocoding lato server (mai nel browser), ricerca ristoranti/attrazioni vicine, dati sui trasporti pubblici

---

## Cosa fa Violetta, in pratica

Violetta è l'assistente AI che accompagna il cliente durante il viaggio, accessibile su Telegram, WhatsApp e telefono:

- Risponde su itinerario, meteo, documenti di viaggio, traduzioni
- **Trova ristoranti/locali vicini** tramite Google Places, con nome, valutazioni, orari — riconosce anche punti di riferimento nominati a parole (es. "vicino all'Holiday Inn"), non solo la posizione GPS condivisa
- **Chiama davvero** ristoranti/hotel per conto del cliente (piano Concierge) — prenota tavoli, comunica ritardi, gestisce imprevisti, facendo da interprete simultaneo
- Riconosce documenti di viaggio caricati (voucher, biglietti, prenotazioni) ed estrae informazioni specifiche per rispondere con precisione, senza inventare dettagli non presenti nei documenti
- Riconosce foto di monumenti/luoghi e genera audioguide

---

## Automazioni di contenuto e marketing

- **Blog automatico** — generazione quotidiana di articoli SEO-mirati (Claude + verifica anti-duplicati su foto e temi)
- **Social media automatico** — pubblicazione giornaliera su Facebook, Instagram, Pinterest, Threads e Google Business Profile, con testo e foto generati dall'AI e verificati contro lo storico per evitare ripetizioni
- **Pagine città dedicate** — landing page SEO per ogni comune vicino a Senago, con contenuto generato specificamente per ciascuna
- **Case study/storie di viaggio** — generazione automatica di racconti di viaggio da conversazioni reali con Violetta, sia a livello di intera prenotazione sia per singola persona su viaggi con più partecipanti
- **Sistema di escalation unificato** — quando un cliente ha bisogno di un operatore umano (email, WhatsApp, Telegram, preventivi scaduti), il sistema traccia e notifica tutti gli operatori, con gestione centralizzata in admin

---

## Sicurezza e conformità

- Nessuna vulnerabilità nota nelle dipendenze (verificato e aggiornato regolarmente)
- Conformità AI Act (UE 2024/1689) — ogni contenuto generato da AI è esplicitamente segnalato
- Software registrato SIAE (Registro Pubblico Speciale Programmi per Elaboratore)
- Marchio "Violetta" registrato UIBM

---

*Documento generato con l'assistenza di Claude (Anthropic) su richiesta del team 4GO FourTravel.*
