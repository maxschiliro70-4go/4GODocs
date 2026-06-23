# 4GO FourTravel — Sessione 4GO-22 (23 Giugno 2026)

## Stack
Next.js 15.5.16 + Prisma + Neon PostgreSQL + Vercel Pro
- main = fourgo.it | develop = staging.fourgo.it
- Commit range: 3bb63586 → 1ffd0843

## Video Publishing — Stato Finale

### Flusso completo
1. Incolla URL pagina HeyGen → auto-estrazione MP4 via `v1/video_status.get?video_id=` (regex sull'URL)
2. Upload su Vercel Blob → blobUrl pubblico
3. Genera caption AI per ogni piattaforma (Haiku)
4. Publish: Meta (IG/FB/Threads) in call separata, Pinterest e TikTok in call separate (evita timeout 120s)

### Instagram
- Two-step: crea container REELS con `video_url` → polling `status_code` ogni 5s max 90s fino a FINISHED → `media_publish`
- Funziona ✅

### Pinterest
- Flusso 4 step: `POST /v5/media` → upload S3 con FormData nativo Node 18 (ordine campi: key, policy, x-amz-*, Content-Type, file) → polling `/v5/media/{id}` → `POST /v5/pins` con `cover_image_key_frame_time: 0`
- Upload S3 asincrono: nella publish call ritorna `__PENDING__{media_id}`, UI mostra bottone rosso "📌 Pubblica Pin Pinterest" separato (polling 90s + crea pin)
- Funziona ✅

### TikTok
- Endpoint: `POST /v2/post/publish/inbox/video/init/` con `source: FILE_UPLOAD`, chunk unico (videoSize = CHUNK_SIZE, totalChunks = 1)
- PUT all'upload_url senza Content-Type
- Status check via action `tiktok-status` (console browser: fetch POST con publish_id)
- Risultato: `SEND_TO_USER_INBOX` → video arriva in inbox TikTok app mobile (notifiche)
- Caption non supportata via inbox — Direct Post (`video.publish`) richiede approvazione developer TikTok
- Submit for review TikTok inviato
- Funziona tecnicamente ✅ (caption manuale dall'app)

### Facebook / Threads
- In attesa verifica Meta Business ⏳

### UI Social mancanti
- Dopo publish, `publishedPlatforms` traccia i ✅
- Box giallo "⚠️ NON ANCORA PUBBLICATO SU" con bottoni per ripubblicare i ❌
- Quando tutti ✅ → badge "✅ tutti i social" sul pacchetto

### Post AI
- TikTok rimosso (solo video, niente testo/foto)
- Usa `POST_PLATFORMS` (IG/FB/Threads/Pinterest)
- `VIDEO_PLATFORMS` resta per VideoPublishSection (include TikTok)

## Bug risolti
- upload-blob: verifica content-type + dimensione minima 10KB (blocca HTML redirect HeyGen)
- IG polling: rimuove `error_message` da fields (campo invalido → 400)
- TikTok chunk: unico chunk (chunk_size = videoSize) evita invalid_params
- Pinterest S3: FormData nativo invece di multipart manuale
- Pinterest pin: `cover_image_key_frame_time: 0` (campo obbligatorio)
- Client publish: timeout gestito con text() + try/parse invece di r.json()
