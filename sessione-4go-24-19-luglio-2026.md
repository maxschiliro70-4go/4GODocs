# Sessione 4GO-24 — 19 luglio 2026

Giornata lunghissima, dominata da bug reali trovati in conversazioni Violetta
con clienti veri in viaggio (Route 66, Creta), più integrazione Threads
completa e review TikTok/Facebook.

## Riepilogo rapido (dettaglio completo in `prompt-agenzia.md`)

**TikTok**: pagina ad hoc `/admin/tiktok-sandbox-test` per il video review
`video.publish` (da eliminare dopo approvazione), fix bug precisione numerica
ID (Number.MAX_SAFE_INTEGER), fix timezone destinazioni multi-tappa USA.

**Threads**: integrazione completa da zero — OAuth, publish, refresh
automatico, deauthorize/data-deletion. Bug trovato per caso: il bottone
pubblica Facebook/Instagram in Post AI non aveva mai funzionato.

**Facebook**: `pages_manage_posts` approvato e rigenerato, pubblicazione
automatica riattivata (era rimasta disattivata da mesi).

**Violetta — 10 bug reali corretti in produzione**, quasi tutti trovati da
conversazioni clienti reali, non da test: audioguida multi-tappa, categoria
negozio/supermercato, classificazione INFO scartata, ragionamento interno
che trapela nella risposta, invenzione siti/prezzi in audioguida, confronto
prezzi sbagliato, hotel "di stasera" sbagliato (+ doppia conferma errata di
fronte a un cliente arrabbiato), luoghi iconici da lista fissa a
classificazione AI, concordanza di genere gruppo misto.

**Nuova feature**: escalation automatica a Massimo su Telegram quando un
cliente è esplicitamente frustrato/arrabbiato, indipendentemente da come
risponde l'AI.

**WhatsApp**: fix header immagine mancante sul template notifica nuove
richieste (errore 132012), fix invio documenti che apriva WhatsApp manuale
invece di mandare per davvero.

**Altri**: Quiz Destinazione mal categorizzato, preventivo PDF fittizio (era
HTML), budget preventivo raddoppiato per ambiguità form, numero GBP sbagliato,
documenti legali aggiornati con Threads.

## Lezione della giornata

La maggior parte dei bug non erano allucinazioni pure ma dati reali letti o
interpretati male: URL reale ma per lo scopo sbagliato, primo hotel del
documento invece di quello datato giusto, classificazione corretta ma
scartata dal codice. Utile, nei prossimi controlli, verificare sempre se il
dato "sbagliato" esiste comunque da qualche parte prima di dedurre che sia
inventato di sana pianta.

## TODO aperti per la prossima sessione

- Eliminare `tiktok-sandbox-test` + `tiktok-sandbox-publish` dopo approvazione
  review TikTok, ripulire logica sandbox in `tiktok/auth` e `tiktok/callback`
- Valutare passaggio pubblicazione social da settimanale a giornaliera
  (`getDayNumber()` già pronto)
- Attivare fallback Telnyx per chiamate Vapi in uscita quando il flusso
  ristorante andrà in produzione (resta su `develop`)
