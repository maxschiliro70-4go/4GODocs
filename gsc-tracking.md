# Tracking GSC giornaliero — fourgo.it

Log dei report automatici Search Console che Emi incolla ogni giorno. Scopo:
avere storico consultabile tra sessioni diverse, per distinguere un vero trend
da normale variabilità statistica su numeri piccoli.

## Come leggere questi dati (criteri stabiliti il 03/07/2026)

- **1-2 giorni di calo/crescita non bastano** per dire che è un trend reale —
  con numeri piccoli (20-40 clic/settimana) la variazione naturale settimana
  su settimana può facilmente essere del 30-50% senza nessuna causa specifica
- **Servono almeno 5-7 rilevazioni consecutive nella stessa direzione**
  prima di considerare un calo/crescita come segnale da prendere sul serio
- **"Posizione media" va letta con cautela** — non è calcolata su un paniere
  fisso di query, cambia in base a quali query hanno generato impressioni
  quel giorno specifico. Un salto anche grande (es. 11.9→23.4) può riflettere
  solo una composizione diversa del traffico giornaliero, non un peggioramento
  reale sulle query importanti
- **I dati del mese in corso sono parziali** — su GBP in particolare, i primi
  giorni di un nuovo mese mostrano numeri bassi/zero semplicemente perché
  Google non ha ancora aggregato tutto, non per un crollo reale
- **Contesto rilevante**: dal 03/07/2026 `blog-autogen` è passato da 10 a 3
  articoli/giorno (vedi sessione 3 luglio in prompt-agenzia.md) — se il
  traffico organico da blog dovesse scendere nelle settimane successive,
  va letto anche alla luce di questo cambiamento deliberato, non solo come
  problema

## Log

### 2026-07-01
- Ieri vs giorno prima: Clic 6 (+3) · Impressioni 381 (+43) · Pos. media 11.9 · CTR 1.6%
- 7gg vs 7gg prec.: Clic 28 (-14) · Impressioni 1702 (-716)
- Top query: "agenzia di viaggio vicino a me" (1 clic, pos.2) · "4 travel" (0, pos.3) · "abbigliamento canada" (0, pos.20) · "agenzia 4 viaggi" (0, pos.7) · "agenzia di viaggi" (0, pos.5)
- Opportunità: "agenzia viaggi senago" (13 imp, pos.1, 0 clic) · "alpiclub jacaranda" (7 imp, pos.50)
- GBP 7gg vs prec.: Impr.search 0 (-59) · Clic sito 1 (-3) · Indicazioni 3 (-19) · Chiamate 1 (+0)
- Nota: GBP a zero pressoché ovunque — poi confermato (report mensile Business Profile,
  stesso giorno) essere solo dato di inizio mese non ancora aggregato, non un crollo reale

### 2026-07-02
- Ieri vs giorno prima: Clic 12 (+6) · Impressioni 602 (+221) · Pos. media 23.4 · CTR 2.0%
- 7gg vs 7gg prec.: Clic 36 (-9) · Impressioni 2048 (-515)
- Top query: "agenzia di viaggio vicino a me" (1 clic, pos.2) · "4 travel" (0, pos.3) · "4 viaggi" (0, pos.36) · "4travel" (0, pos.33) · "abbigliamento canada" (0, pos.18)
- Opportunità: "agenzia viaggi senago" (12 imp, pos.1, 0 clic) · "alpiclub jacaranda" (7 imp, pos.49) · "alpiclub jacaranda tenerife" (6 imp, pos.50)
- GBP 7gg vs prec.: Impr.search 15 (-38) · Clic sito 1 (-3) · Indicazioni 16 (-2) · Chiamate 1 (+0)
- Nota: secondo giorno consecutivo di calo sui 7gg (dopo -14/-716 di ieri) — stessa
  direzione ma ancora presto per dire se è un vero trend, serve continuare a osservare.
  Salto grande di posizione media (11.9→23.4) probabilmente composizione query, non
  peggioramento reale — vedi criteri di lettura sopra

### 2026-07-03
- Ieri vs giorno prima: Clic 5 (-7) · Impressioni 378 (-224) · Pos. media 9.7 · CTR 1.3%
- 7gg vs 7gg prec.: Clic 35 (-13) · Impressioni 2051 (-542)
- Top query: "agenzia di viaggio vicino a me" (1 clic, pos.3) · "4 travel" (0, pos.3) · "4 viaggi" (0, pos.36) · "4travel" (0, pos.33) · "abbigliamento canada" (0, pos.18)
- Opportunità: "agenzia viaggi" (7 imp, pos.3, 0 clic) · "agenzia viaggi senago" (18 imp, pos.2, 0 clic) · "alpiclub jacaranda tenerife" (6 imp, pos.49)
- GBP 7gg vs prec.: Impr.search 15 (-60) · Clic sito 1 (-4) · Indicazioni 17 (+6) · Chiamate 2 (+1)
- Nota: terzo giorno consecutivo con il confronto 7gg in territorio negativo su clic/impressioni
  (-14, -9, -13 clic; -716, -515, -542 impressioni) — direzione consistente ma ancora sotto la
  soglia di 5-7 rilevazioni stabilita per parlare di trend reale. Da notare: GBP mostra segnali
  misti oggi, non uniformemente negativo (indicazioni e chiamate in miglioramento, mentre
  impressioni search e clic sito restano in calo) — non tutto il quadro punta nella stessa direzione

<!-- Aggiungere qui i prossimi giorni, stesso formato -->

---

## Snapshot mensili GA4 + Search Console (28 giorni)

Diverso dal log giornaliero sopra — qui si tracciano le foto periodiche più ampie
(28 giorni), utili per vedere il quadro generale senza il rumore statistico dei
confronti giorno-per-giorno.

### 2026-07-06 (periodo 2026-06-08 → 2026-07-06)

**Search Console (28gg)**
- Clic totali: 142 · Impressioni: 8748 · CTR medio: 1.6% · Posizione media: 12.6
- Top query: "agenzia viaggi senago" · "fourgo" · "agenzia di viaggio" · "agenzia di viaggio vicino a me" · "agenzia viaggi paderno dugnano" · "agenzia viaggi vicino a me" · "agenzie viaggi vicino a me" · "fourtravel" · "tanzania situazione attuale farnesina" · "4 gio"
- Top pagine per crescita: vari articoli blog "cosa mettere in valigia per [destinazione]" con crescita marcata (Canada 113%, Cina/vaccini 72%, Sicilia 62%, Finlandia 45%, Scozia 45%, Messico 50%, Estonia 40%, Chia Sardegna 27%, Turchia 23%) — percentuali di crescita periodo su periodo, non quota sul totale clic

**GA4 (28gg, 6 giu – 5 lug)**
- Sessioni: 640 (+155% vs mese scorso) · Utenti attivi: 361 (+160%) · Visualizzazioni: 3806 · Durata media: 8m 40s · Bounce: 48%
- Pagine più visitate: Homepage 450 (21%) · /login 198 (9%) · /interprete 182 (8%) · Pacchetti 172 (8%) · /demo-bot 93 (4%) · Contatti 75 (3%) · /come-funziona 71 (3%) · Chi Siamo 57 (3%)

**Nota**: crescita sessioni/utenti molto marcata (+155%/+160%) — a differenza dei
confronti giornalieri GSC (numeri piccoli, rumore statistico alto), un salto di
questa entità su 28 giorni è un segnale reale, non casuale. Buon segno anche la
presenza di /interprete e /demo-bot tra le pagine più visitate — indica interesse
concreto per le funzionalità prodotto, non solo traffico homepage generico.

<!-- Aggiungere qui i prossimi snapshot mensili, stesso formato -->
