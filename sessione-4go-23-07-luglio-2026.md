# Sessione 4GO-23 (chiusura) — 7 Luglio 2026

Sessione ampia, multi-giorno nella pratica (tracciata dai report GSC datati 01-07
luglio). Chiude la numerazione 4GO-23; la prossima sessione parte come 4GO-24.

---

## 1. WhatsApp — finestra 24h e template Meta

**Problema**: notifiche con `type:'text'` accettate da Meta (200 OK) ma **non
consegnate** se il destinatario non ha scritto al bot nelle ultime 24h — fallimento
silenzioso, nessun errore visibile.

**Casi reali**: cliente Claudio (Sharm el-Sheikh, 3.800€) mai ricontattato — escalation
WA non partita. Cliente Emanuela Minusso — notifica operatori mai arrivata.

**Fix**: sostituito testo libero con template Meta approvati ovunque:
- `notifica_nuova_richiesta` (operatori) — `whatsapp/webhook`, `contact/route.ts`,
  `ai/quiz/route.ts`, `location/confirm/route.ts`
- `notifica_documento_cliente` (clienti) — `sendClientNotification.ts`,
  `send-welcome/route.ts`, `location-send-unified`, `location-send-info`

Regola ESCALATE riscritta come principio generale: destinazione+periodo+budget+volontà
di procedere → sempre scalato a un operatore. Nuovo marcatore `INFO_MANCANTE:` che
Violetta aggiunge autonomamente (non regex) quando rimanda il cliente all'agenzia per
un'informazione mancante — notifica silenziosa, nessun cambiamento visibile al cliente.

---

## 2. SEO — indicizzazione blog

113 articoli "rilevati ma non indicizzati" da mesi. Causa: `blog-autogen` pubblicava 10
articoli/giorno, troppo per il crawl budget del dominio (confermato: vecchi ~96%
indicizzati vs recenti 4-12%). Falsa pista: Google Indexing API, ufficialmente limitata
a JobPosting/BroadcastEvent, non aiuta per blog nonostante risposte 200.

Fix: `blog-autogen` ridotto a 3 articoli/giorno. Nuovo `index-check` (Search Console URL
Inspection API, monitoraggio settimanale + `?all=1` per lo storico, paginato batch 25).
`blog-settings`/`blog-check`: UI riallineata al modello di calcolo reale.

---

## 3. Bounce-check — mancato recapito email

`smtp.sendMail()` si risolve quando Aruba **accetta** il messaggio, non quando arriva —
un vero fallimento arriva dopo come DSN/NDR, invisibile al codice. Nuovo `bounce-check`:
legge IMAP di info@fourgo.it 4×/giorno, riconosce DSN veri (RFC 3464), estrae
destinatario fallito + booking code, notifica operatori, sposta in cartella dedicata.

---

## 4. Bug reali trovati e corretti

- `admin/location`: crash totale (`item is not defined`) su "Nuova Location" —
  variabile fuori scope, residuo copia-incolla
- `location/confirm`: `totalPrice` non aggiornato alla scelta cliente — solo il nome
  salvato in una nota testuale
- `free-payment`: notifica Telegram agli operatori mancante del tutto
- `email-ai` (admin): contatore "Potenziali" su tutto il DB, lista solo sui 50 thread
  più recenti — disallineati
- `benvenuto-desktop.html`: bug CSS nel ventaglio fotografico (varco 8%, triangolo
  nero) — preesistente, mai notato
- Logo email non uniformato su 6 endpoint (vecchio logo sfondo nero) — corretti tutti

---

## 5. Pinterest — refresh token mai implementato

Stesso pattern del bug GBP di sessioni precedenti: `refreshToken` salvato al primo OAuth
ma mai riutilizzato. Aggiunta `refreshPinterestToken()` con retry automatico in
`cron/social-post` e `admin/social-post`. Aggiunto `continuous_refresh: true` nello
scambio codice→token per il refresh token continuo (non il legacy 365gg non rinnovabile).

---

## 6. Playwright E2E — smoke test admin

Login automatico (TOTP generato al volo, stesso algoritmo del backend), visita le 35
pagine admin verificando assenza di errori JS non gestiti — pensato per intercettare bug
come `admin/location`.

Risolto: scheduling GitHub Actions richiede workflow sul branch default (main), non
partiva perché solo su develop; file test/config mai portati su main; account test con
credenziali hardcoded rilevato da GitGuardian (2 incidenti) → ruotate, endpoint
temporanei rimossi da entrambi i branch — lezione: mai più segreti hardcoded nemmeno in
endpoint "usa e getta".

Divergenza develop/main: 2 differenze reali trovate e corrette (variabili TEST_ADMIN
mancanti su develop, file test mai portato lì). Nota salvata per il cherry-pick massiccio
post-SIAE.

---

## 7. Contenuti — pagine benvenuto e ufficio stampa

Pagine benvenuto (mobile+desktop): box "È arrivata Violetta" e "Consulenza remota",
numero WhatsApp aggiornato, foto hero reali (da Unsplash generico a foto blog), fix bug
geometrico nel layout a ventaglio.

Contatti stampa (storia: piccola agenzia che si costruisce da sola l'AI, controcorrente
rispetto all'83% delle PMI italiane senza AI): TTG Italia, Travel Quotidiano, L'Agenzia
di Viaggi Magazine (follow-up), FIAVET Lombardia, Confcommercio Lombardia. Contenuto
rivisto per accuratezza: firma corretta (Massimiliano Schilirò, non Emiliano), stato SIAE
preciso ("in corso di registrazione", non "protetto"), canali WhatsApp/Telegram distinti
(prevendita vs assistenza viaggio).

---

## 8. Infrastruttura — migrazione Neon (Azure → AWS)

**Causa**: Neon deprecando le regioni Azure (scadenza email: 5 ottobre 2026;
documentazione ufficiale cita 27 agosto — discrepanza segnalata, meglio non aspettare
l'ultimo giorno).

**Analisi**: database piccolissimi (35MB staging, 77MB produzione) — Import Data
Assistant o `pg_dump`/`psql` sufficienti, migrazione quasi istantanea. Verificato: nessun
impatto su backup giornaliero (Prisma standard) né su privacy policy (dice solo "server
in Europa", resta accurata migrando dentro l'UE).

**Stato attuale**: 2 tentativi con Neon Import Data Assistant (Beta) falliti/parziali —
approccio cambiato a `pg_dump | psql` manuale, più affidabile. PostgreSQL 18 installato
sul computer di Emi (Windows, Git Bash), verificato funzionante con percorso completo.
**Prossimo passo**: eseguire il comando di migrazione vero e proprio (in sospeso).

---

## Cose ancora aperte

- Migrazione Neon da completare (staging prima, poi produzione)
- Playwright E2E — attendere il primo vero run schedulato di lunedì per conferma finale
- Query preventivo→pacchetto: fix applicato, integrazione quiz→preventivo completata
  (bottone precompilato)
- Contatti stampa: in attesa di risposte

---

## Numerazione sessione

Chiusa **4GO-23**. Da questo punto in avanti: **4GO-24**.
