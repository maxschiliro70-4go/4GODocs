# Sessione 4GO-24 — 20 agosto 2026

Sessione lunghissima, densissima di bug reali trovati da test in produzione con clienti veri in viaggio. Organizzata per categoria invece che cronologicamente data la mole.

## Sistema Escalation — costruito da zero

Prima: ogni fonte (email/whatsapp/telegram/preventivi) mandava solo notifiche "spara e dimentica", nessun record persistente, nessun modo di segnare "gestita".

**Costruito:**
- Nuova tabella `Escalation` (type/detail/bookingCode/clientName/status/notifiedMessages)
- `src/lib/escalation.ts` → `creaEscalation()` condivisa, notifica TUTTI gli operatori (Emi/Massimo/Alessia/Inga) con bottone "✅ Gestita"
- Collegati tutti i punti di escalation esistenti: Telegram (1 punto), WhatsApp (3 punti), Email (4 punti) — tutti trovati mancanti uno alla volta
- Cron `preventivi-scaduti` (Inquiry senza risposta >8h) — cercatore attivo, trova anche backlog vecchio
- Cron `escalation-digest` (riepilogo periodico dettagliato, non solo nomi)
- Cron `escalation-backlog` (recupera email/whatsapp già scalate prima che il sistema esistesse)
- Pagina `/admin/escalation` — gestione manuale diretta, bypassa Telegram se il bottone ha problemi
- Bottone manuale "Escalata" in admin ora crea davvero una escalation tracciata (prima solo un campo status)

**Bug trovati e corretti nel sistema stesso:**
- Escape caratteri Markdown nel dettaglio — testo grezzo di email/messaggi con `_ * \`` non bilanciati faceva fallire SILENZIOSAMENTE l'intero invio Telegram (nessuna eccezione, solo `ok:false` ignorato)
- Un errore su una singola escalation nel digest interrompeva l'intero ciclo, saltando quelle successive
- Cron duplicati (Vercel + cron-job.org entrambi attivi） → notifiche doppie, rimossi da Vercel
- Header `x-admin-secret` diverso da quello che stavo usando — trovato solo chiedendo direttamente a Emi

## Flusso Ristorante — riscritto in profondità

**Riconoscimento nome esplicito:** `searchRestaurantByName()` (Google Places Text Search) — se il cliente nomina un locale specifico invece di chiedere "cosa c'è vicino", salta la richiesta posizione. Esteso anche a: testo durante `waiting_location`, cronologia recente (conferme tipo "prenota pure"), richieste di modifica di prenotazioni già fatte.

**Bug critico del giorno:** il classificatore AI con accesso alla cronologia (costruito oggi stesso) faceva ripartire l'intero flusso da zero se un cliente rispondeva con un numero/orario durante uno step intermedio (es. "13" come risposta a "che orario preferisci"), perdendo il ristorante già selezionato. Aggiunta guardia critica: se c'è un flusso attivo in uno step intermedio, il trigger fresco viene sempre saltato.

**Conferma numerica obbligatoria:** anche il match per nome ora passa da `show_results` (scrivi 1 per confermare) invece di procedere direttamente — mai chiamare senza conferma esplicita del cliente.

**Percorso modifica dedicato:** richieste di modifica (cambia orario, sposta) saltano dritte a `confirm` invece di rifare tutte le domande già risposte, e lo script della chiamata vera dice "stiamo modificando" invece di "vorremmo prenotare".

## Riconoscimento foto/luoghi — controllo geografico

**Bug critico:** Google Vision Landmark Detection non aveva MAI nessun controllo di incertezza — trattava ogni risultato come certo al 100%. Caso reale: "Casa Batlló" (Barcellona) descritta con sicurezza a un cliente a Lisbona. Aggiunto in Vision E Lens: il contesto ora include la destinazione reale del cliente, con istruzione di verificare plausibilità geografica prima di procedere con sicurezza.

**Google Lens — falsi positivi:** allargato il controllo di affidabilità oltre ai testi che sembrano domande, anche ai titoli stile blog/ricetta ("It's SO good! ... Easy recipe") — stesso principio, titoli di pagine web trovate per somiglianza visiva casuale, non riconoscimenti veri.

## Continuità della memoria conversazionale

**Bug critico trovato per ultimo:** il finder di luoghi vicini (`nearbySearch`) mandava la risposta al cliente ma non aggiornava MAI `session.context` — invisibile alla memoria dell'AI generale. Un messaggio vocale arrivato quasi in contemporanea ha ricevuto risposta oltre un'ora dopo, ririspondendo da zero alla stessa domanda già risposta con link generici invece dei risultati reali già mostrati.

**Marco/itinerario lungo:** finestra di cronologia letta dal modello alzata da 10 a 20 messaggi (il salvataggio ne teneva già 20, la lettura ne sprecava metà).

## Date e calendario

- Calendario esplicito dei prossimi 14 giorni con giorno della settimana corretto, calcolato deterministicamente — il modello non deve più calcolare da solo (causa di un errore reale: 26 agosto dato come martedì invece di mercoledì, confermando per errore un pranzo in un ristorante chiuso quel giorno)
- Rafforzata l'istruzione: la data di sistema è sempre autorevole, mai ricalcolarla in base a quello che dice il cliente

## Regole hotel/voucher — allargate

- Regola "hotel/tappa di oggi" estesa anche alle richieste aperte ("proponi tu"), non solo alle domande dirette
- Regola dati voucher allargata a dotazioni generiche camera e importi deposito, non solo minibar/policy
- Regola promesse di prenotazione: aggiunto esempio esplicito su dare i recapiti dell'agenzia invece di usare il marcatore — stessa categoria di rimando senza meccanismo reale

## Audioguide

- Percorso foto: rinnovo periodico indicatore "sta registrando" (mancava, causa di duplicati)
- Percorso foto: catch vuoto senza log né retry → aggiunto entrambi
- Classificazione "luogo iconico" allargata oltre patrimonio antico/UNESCO, per includere luoghi famosi via cultura pop/cinema/scienza (caso: Griffith Observatory classificato come non-iconico)
- Nuovo endpoint per cercare/cancellare voci specifiche dalla cache, per invalidare dopo un fix di classificazione

## Blog — foto duplicate e produzione

- `blog-fix-images`: mescolamento ordine (Fisher-Yates) ad ogni esecuzione — prima rischiava di restare bloccato sugli stessi articoli difficili senza mai avanzare
- `blog-autogen`: `refreshUsedImages` univa invece di sostituire il set locale — corretta race condition prevista da un commento diagnostico già presente
- Confermato con test reale: entrambi i fix funzionano, 635 duplicati sistemati in un'esecuzione
- "Solo 3 articoli/giorno invece di 10": non un bug, il pool di combinazioni destinazione+tipo si sta esaurendo con 743 articoli pubblicati — la logica anti-duplicati sui contenuti funziona come previsto

## Strumenti costruiti oggi

- `/api/admin/tg-export` — export JSON conversazioni, tracciamento automatico ultimo export
- Bottone "Esporta JSON" in admin/social
- Foto clienti ora persistite (prima cancellate dopo Google Lens) e mostrate come immagini vere in admin
- `docsContext` su TelegramMessage — mostra il contesto documenti usato per ogni risposta, niente più bisogno di Vercel per verificare
- `/api/admin/blog-duplicates-check` — conteggio duplicati senza generare nuovi articoli
- `/api/admin/post-check` — stato di un articolo per slug
- `/api/admin/audioguide-cache` — gestione cache audioguide

## Sicurezza

- `deepmerge-ts` aggiornata a 8.0.1 (vulnerabilità alta, usata da mailparser per email in arrivo)

## Pending / da verificare

- Bottone "Gestita" su Telegram: funziona lato database (conferma via log diagnostico) ma il bottone non si disabilita visivamente per tutti gli operatori — diagnostica aggiunta, in attesa di un nuovo test reale
- 56 pagine 404 su Search Console: probabile cancellazione manuale passata, nessun bug trovato — da monitorare che il numero scenda nelle prossime settimane
