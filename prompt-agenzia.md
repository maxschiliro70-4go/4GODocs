# 4GO FourTravel — Sessione 4GO-24 (25 Giugno 2026)

## Stato branch
- **main** `7beb850e` — produzione stabile
- **develop** `e02216cb` — Violetta/Vapi/interprete (non mergiato)

## Completato in sessione 4GO-24

### ChatWidget beep — storia completa
Il beep funziona con questo meccanismo:
- `_sharedAudioCtx` creato a livello modulo al primo gesto utente (click/touch/keydown)
- Il beep usa `getAudioCtx()` → `_sharedAudioCtx` già sbloccato
- Se `suspended` → `ctx.resume().then(playBeep)`, se `running` → `playBeep()` diretto
- Note: 523Hz + 880Hz + 1320Hz, dur 0.4s, gap 0.5s, volume 1.0
- Commit funzionante: `7beb850e`

**Cosa NON funziona:**
- `new AudioContext()` dentro setTimeout → bloccato da Chrome autoplay policy
- `new Audio('/beep.wav')` → Chrome blocca senza gesto utente previa
- File WAV generato con Python → silenzioso in Chrome (formato non compatibile)
- Note brevi (<0.2s) → spesso non si sentono

**Regola**: non toccare il meccanismo del beep. Se si deve modificare, cambiare solo durata/frequenza/volume.

### Fix applicati su main
- ChatWidget beep: resume AudioContext prima di suonare (`26b6fe8b`)
- ChatWidget: X popup chiude ma non dismisses permanentemente (`62e03678`)
- beep.wav stereo 44100Hz in `/public/`
- `/api/beep` route con Content-Type audio/wav corretto
- next.config: Content-Type audio/wav per /beep.wav

### Pending
- Beep: note "slow motion" — accettabile per ora
- X popup ricompare dopo 12s: da verificare dopo deploy
- Merge develop→main Violetta: dopo test staging + autorizzazione Emi
- Facebook/Threads video: pending Meta business verification
- TikTok Direct Post scope: in attesa approvazione
