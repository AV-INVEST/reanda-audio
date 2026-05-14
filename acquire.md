# ACQUIRE.md — PLAY REANDA (Documento tecnico per acquisizione)
  
## 1) Executive Summary
PLAY REANDA è un **gioco web multiplayer real‑time** (client isometrico + server authoritative) con un loop di gioco già completo: **esplorazione**, **progressione XP/Livelli**, **economia interna**, **collezionabili**, **missioni giornaliere/settimanali**, **minigiochi competitivi** e **sistema VIP**.
  
Il progetto è già in stato “ship-ready”: **pagamenti Stripe** (Checkout + abbonamento VIP + Webhook idempotenti), **email Resend** (accesso e comunicazioni), deploy web **Vercel** lato client, deploy server **PM2** lato backend, con attenzione concreta a sicurezza (JWT, rate limit, CORS whitelist, CSP Report‑Only, idempotenza pagamenti). Il client è basato su **Phaser 3** (render isometrico + UI DOM/overlay), con supporto mobile (touch camera/pinch).
  
Obiettivo per un buyer: acquisire una base prodotto già pronta per scalare contenuti e monetizzazione, con architettura pulita e modulare.
  
---
  
## 2) Cosa stai acquistando (in parole semplici)
Una piattaforma gioco completa con:
  
- **Mondo isometrico** tile‑based con più mappe/biomi e transizioni.
- **Multiplayer** con sincronizzazione in tempo reale via Socket.IO.
- **Sistema RPG** con fame/energia, professioni, risorse, XP e livelli.
- **Economia**: wallet interno, shop, donazioni al “Nodo”, VIP pass.
- **Missioni** e progressione ricorrente (daily/weekly) per retention.
- **Minigiochi competitivi** e challenge (PVP/PVE‑like) già integrati.
- **UI premium** con overlay, cinematiche video, HUD, inventory, chat, emote.
  
---
  
## 3) Perché può “esplodere” (driver di crescita)
  
### 3.1 Retention nativa (non “appiccicata”)
PLAY REANDA non si regge su una sola meccanica: il player torna per:
  
- Missioni giornaliere/settimanali con target chiari e progress misurabile.
- Progressione XP/Livelli che sblocca cap/benefit (es. energia) e contenuti.
- Minigiochi live a finestre/eventi (competizione + ricompense).
- Collezionabili/album e cosmetici (scie, wearable set) che alimentano status.
  
### 3.2 Monetizzazione “game-native” (non aggressiva)
- Wallet e shop integrati con Stripe (pagamento) e VIP (abbonamento).
- VIP impatta su qualità di vita e progressione (es. XP, energia), senza rompere l’esperienza base.
  
### 3.3 Architettura pronta a crescere contenuti
Il gioco è strutturato per aggiungere:
- nuove mappe, NPC e interazioni,
- nuovi minigiochi come moduli plug‑in,
- nuove missioni/obiettivi senza riscrivere il core.
  
---
  
## 4) Gameplay & Sistemi (cosa c’è già oggi)
  
### 4.1 Esplorazione e mondo
- Mondo diviso in mappe/stanze (es. MAPS0..MAPS26 + aree speciali).
- Transizioni e gestione stato mappa lato client (prefetch/cleanup, HUD mode).
- Portali e aree speciali (lobby minigiochi, zone evento, mappe challenge).
  
### 4.2 Movimento server-authoritative (anti-cheat by design)
- Movimento su griglia: il client invia intent/step e il server valida.
- Collisioni, vincoli mappa e regole speciali gestite centralmente.
- Comportamenti UX avanzati (touch e analogico) senza perdere autorità server.
  
### 4.3 Progressione: XP, livelli, energia
- Stats persistite (XP, livello, fame/energia, valuta) con logica di consumo tempo‑reale.
- Cap energia che scala con il livello (progressione lunga, sensata, estendibile).
- Aggiornamenti real‑time al client (eventi socket per XP/status).
  
### 4.4 Professioni e risorse (economia “da MMO light”)
- Professione selezionabile (es. farmer/woodcutter/fisherman).
- Raccolta risorse + crafting (ricette estendibili).
- Consumo oggetti (food) per recupero energia.
  
### 4.5 Inventory, collezionabili, cosmetici
- Inventory UI completa: risorse, oggetti, dettagli, lock durante minigiochi.
- Sistema cosmetico: scie e wearable (gestione server+client, sync real‑time).
- “Album” collezionabili già previsto nello state utente e nella UX.
  
### 4.6 Missioni (daily/weekly) e obiettivi
Pool missioni già attivo con esempi concreti:
- raccolta/donazione,
- chat/emote/speed boost,
- partecipazione minigiochi (CoinRush/GridCollapse/CosmoPush),
- “non cadere” (performance-based),
- social (amicizie, DM).
  
Sistema: selezione missioni con gestione conflitti, target, progress counters e reset time‑zone aware (Europa/Roma).
  
### 4.7 Social e community
- Chat (con controlli) e emote.
- Friends system (richieste, accettazione, stato online).
- DM/privati presenti come metriche e supporto legacy.
  
### 4.8 Minigiochi competitivi e challenge
Minigiochi multiplayer:
- **CoinRush** (competizione live).
- **GridCollapse** (round/elim/skill).
- **CosmoPush** (competizione con eventi “eject” e tracking).
  
Challenge/esperienze guidate (single/multi con gestione stato):
- Labirinto (livelli, HP/timer, reward).
- Numerose challenge/biomi (gestione inventory lock, HUD dedicato, teletrasporti e reward).
  
### 4.9 Cinematiche, audio e presentazione premium
- Sistema cinematico con overlay full‑screen video, progress bar, “tap for audio” su mobile e ducking dell’audio del gioco.
- Pipeline asset pronta per scalare (caching aggressivo, headers, CDN per asset selezionati).
  
---
  
## 5) Monetizzazione: Stripe + VIP + Wallet (già collegati)
  
### 5.1 Stripe Checkout (one‑shot)
- Endpoint server `/api/payments/checkout` che crea sessione Checkout e ritorna URL.
- Metadati robusti (userId, packId, importo/cevium, email).
- Redirect ritorno gestito client‑side per refresh dello shop e UI di successo.
  
### 5.2 VIP Pass (subscription)
- Endpoint server `/api/payments/subscribe` per abbonamento.
- Gestione stato VIP (active/trialing/past_due/unpaid/canceled…) e enforcement di accesso mappe premium.
  
### 5.3 Webhook Stripe idempotente (anti‑double credit)
- Endpoint `/webhooks/stripe` con body raw + signature.
- Idempotenza: tracking eventi processati su DB e/o Redis set.
- Aggiornamento wallet/feature flags e reconciliation per consistenza.
  
### 5.4 Wallet / prodotti / shop
- Catalogo pacchetti “CEVIUM” con price_id Stripe configurabile.
- Store wallet interno già implementato lato server e riflesso lato client.
  
---
  
## 6) Email (Resend) e onboarding
  
### 6.1 Login via email
- Endpoint server per inviare link di accesso via **Resend**.
- Gestione base URL (dominio) e sender dinamico (es. login@domain).
- Rate limit dedicato agli invii per ridurre abuso.
  
### 6.2 Email transazionali
- Hook e notifiche (es. conferme) già predisposte nel backend pagamenti/webhook.
  
---
  
## 7) Architettura tecnica (spiegata semplice, ma seria)
  
### 7.1 Client (web)
- Client statico (Node 20) con build script e serving static.
- Rendering: isometrico tile‑based, UI mista (Phaser + overlay DOM per pannelli).
- Deploy tipico: **Vercel** con rewrite verso API (`/api/*` e `/webhooks/*`).
  
### 7.2 Server (real‑time authoritative)
- Node 20 + Express + Socket.IO.
- Core game loop separato (gameCore) e handlers socket (gameSockets).
- Schedulazione eventi/minigiochi.
  
### 7.3 Persistenza e consistenza
- Persistenza locale con SQLite (better‑sqlite3) per utenti, features, missioni, wallet, idempotenza pagamenti.
- Redis opzionale per deduplica/guardie (dove disponibile).
  
### 7.4 Sicurezza e compliance operative
- JWT con “secure by default”: in produzione il server rifiuta di partire se mancano secret critici.
- CORS whitelist con fallback sicuro per domini playreanda.com.
- CSP (Report‑Only) e hardening headers (nosniff, frame‑ancestors deny, ecc.).
- Rate limiting per endpoint sensibili (auth/resend, otp, ecc.).
- Logging con redazione di token/secret.
  
---
  
## 8) Operatività: deploy, manutenzione, strumenti
- PM2 config pronta per run in produzione.
- Endpoint status e metriche runtime (event loop lag, memory).
- Modalità maintenance con controllo admin.
- Script e test: suite test Node (`node --test`) + tool admin per investigare utenti e stati.
  
---
  
## 9) Asset, UX e cura del dettaglio (valore “non copiabile” facilmente)
Questo progetto non è solo “funzionante”: è curato.
  
- UI con pannelli coerenti, animazioni, feedback immediati, anti‑flicker e gestione overlay.
- Cinematiche integrate (video + audio) con UX mobile-friendly.
- Sistemi di onboarding e retention (missioni, progressione, eventi).
- Libreria di item/icone e contenuti già vasta (inventory e collezionabili).
  
In pratica: un buyer non compra solo codice, compra **tempo**, **rifinitura** e una base pronta a produrre contenuto.
  
---
  
## 10) Cosa può fare un buyer dal day‑1
- Spingere marketing su un prodotto già “end‑to‑end” (login → gioco → progress → shop → pagamento).
- Aggiungere contenuto in modo incrementale (nuove missioni, nuove aree, nuove ricette, nuove challenge).
- Estendere la monetizzazione senza rifare infrastruttura (nuovi pack, battle pass, cosmetici, bundle, eventi).
- Integrare analytics/telemetria più profonda usando una base già predisposta (CSP, consent layer; Meta Pixel già condizionato al consenso; GA4 installabile come step successivo, se desiderato).
  
---
  
## 11) Checklist tecnica di “pronto vendita”
  
- Client deploy: Vercel con rewrite verso API e headers/caching.
- Backend deploy: PM2 + Node 20.
- Pagamenti: Stripe Checkout + Subscription + Webhook + idempotenza.
- Email: Resend integrato per onboarding/login.
- DB: SQLite pronto (persistenza e audit tecnico).
- Realtime: Socket.IO con eventi di gioco, inventory, progress, minigames.
- Sicurezza: secret enforcement in prod, rate limit, CORS, CSP, logging redatto.
  
---
  
## 12) Allegati (orientamento nel repository)
Riferimenti utili per chi fa due diligence:
  
- Backend entrypoint: `server/index.js`
- Game core: `server/gameCore.js`
- Socket handlers: `server/gameSockets.js`
- Pagamenti: `server/api/payments.js`
- Webhook Stripe: `server/webhooks/stripe.js`
- Feature flags + missioni + album: `server/lib/featuresStore.js`
- Professioni/energia/XP: `server/store/professionStore.js`
- Deploy PM2: `server/ecosystem.config.cjs`
  
- Client Vercel: `REANDA-client/vercel.json`
- Client shop/Stripe bridge: `REANDA-client/ui/shop.js`
- Client cinematic overlay: `REANDA-client/ui/cinematic.js`
- Inventory UI: `REANDA-client/scene/ui/inventoryPanel.js`
- Minigiochi (client): `REANDA-client/scene/minigames/*`
  
---
  
## 13) Conclusione (posizionamento per acquisizione)
PLAY REANDA è un prodotto raro perché unisce:
  
- **stack tecnico solido** (real‑time authoritative + pagamenti idempotenti + sicurezza),
- **contenuto già vivo** (mondo, minigiochi, challenge, inventory, social),
- **monetizzazione già collegata** (Stripe + VIP) e **onboarding** (Resend),
- una **cura UX** che di solito arriva molto dopo (e costa molto).
  
Se l’obiettivo è acquistare un progetto che può scalare velocemente con contenuti e marketing, senza rifare infrastruttura e senza “rifondare” il gameplay, PLAY REANDA è pronto.

---

## 14) Punti forti (in 60 secondi)
- **Architettura server-authoritative**: movimento e regole validate lato server → anti‑cheat “by design”, meno exploit, meno edge-case client.
- **Contenuto già “vivo”**: molte mappe/zone + minigiochi + challenge + missioni ricorrenti → retention senza dover inventare il loop da zero.
- **Monetizzazione già collegata e difesa**: Stripe + webhook idempotenti + catalogo prodotti → aggiungere pack/bundle è un’estensione, non un progetto.
- **Qualità UX sopra la media**: overlay, cinematiche, shop ridisegnato, UI mobile-friendly → valore difficilmente replicabile “veloce”.
- **Operatività reale**: backup DB (con restore verificabile), strumenti admin, test automatici, hardening headers/CORS.

---

## 15) Struttura progetto (cosa è “server” e cosa è “client”)
Nel workspace attuale ci sono due repository principali:
- **Backend game server**: `c:\REANDA\server` (Node 20, ESM)
- **Web client**: `c:\REANDA-client` (Phaser 3 + build statico + server statico Express per dev)

Nota tecnica: nel backend esiste anche una cartella `server/maps/` che appare come **mirror legacy** di parti del server. Non è la sorgente primaria usata in produzione; va trattata come area “storica/compatibilità” da ripulire o riallineare durante la due-diligence (vedi §19).

---

## 16) Setup rapido (dev)
### 16.1 Avvio backend (locale)
Da `c:\REANDA\server`:
```bash
npm install
npm start
```
Default: `http://127.0.0.1:4001/status`

### 16.2 Avvio client (locale)
Da `c:\REANDA-client`:
```bash
npm install
npm run build
npm start
```
Default: `http://127.0.0.1:5500/`

Il client prova a raggiungere il server su `:4001` automaticamente; in alternativa puoi forzare:
`http://127.0.0.1:5500/?server=http://127.0.0.1:4001`

---

## 17) Variabili ambiente (per operare senza sorprese)
### 17.1 Minime per produzione (sicurezza “fail-fast”)
- `NODE_ENV=production`
- `JWT_SECRET` (obbligatoria in prod)
- `REANDA_ADMIN_SECRET` (o `ADMIN_SECRET`) (obbligatoria in prod)

### 17.2 Pagamenti Stripe (se attivi)
- `STRIPE_SECRET_KEY` (o `STRIPE_TEST_KEY` in dev)
- `STRIPE_WEBHOOK_SECRET` (verifica firma webhook)
- `VIP_PRICE_ID` (price id subscription VIP)

### 17.3 Email/Onboarding (Resend)
- `RESEND_API_KEY`
- `RESEND_FROM` (es. `login@playreanda.com`)

### 17.4 CORS / URL base
- `ALLOWED_ORIGINS` (CSV; in assenza usa default `*.playreanda.com`)
- `WEB_BASE_URL` (fallback: primo origin consentito)

### 17.5 Operazioni e LiveOps (opzionali ma utili)
- `MAINTENANCE_MODE` / `MAINTENANCE_MESSAGE` / `MAINTENANCE_ETA`
- `MAINTENANCE_ADMIN_EMAILS` (CSV)
- `LOG_LEVEL` (es. `info|debug`) e `LOG_JSON=true`
- `REDIS_URL` e `REDIS_PREFIX` (abilita deduplica/guardie cross‑process dove previsto)
- `SHARD_ID` e `MAX_ROOM` (parametri multi‑istanza)
- `MAPS_CONFIG_PATH` (override percorso maps.json)

### 17.6 Porte e networking
- `PORT` (default 4001 per il server; 5500 per il client static)
- `STRICT_PORT` (se abilitato, controlli più rigidi su binding/avvio)

---

## 18) Operatività e qualità (prove concrete)
- **Backup DB**: script `server/scripts/backup_db.js` (snapshot consistente via `VACUUM INTO` + gzip) con retention; restore verificabile con `server/test/verify_backup_restore.js`.
- **Suite test**: `server/package.json` espone `npm test` (Node `--test`) con test su missioni, lobby, claim periodici e verifiche sicurezza/atomicità.
- **Strumenti**: `server/scripts/*` include utility per migrazioni, load test, investigazione utenti e verifiche walkable/spawn.

---

## 19) Due diligence: rischi noti e quick wins (valore aggiunto post-acquisizione)
### 19.1 Rischi/ambiguità tecniche emerse dalla struttura repo
- Presenza di `server/maps/` come mirror legacy: ridurre duplicazione e allineare test/entrypoint alla sorgente canonica (`server/`) per evitare drift e confusione in handover.
- Script locale `start-dev.bat` (in root) assume una cartella `client/` non presente in questo workspace: aggiornare o sostituire con istruzioni “2-repo” coerenti (vedi §16).

### 19.2 Quick wins “alto ROI”
- **Pulizia mirror/packaging**: consolidare backend in un solo tree e rivedere lo script `npm test` per eliminare percorsi legacy.
- **Telemetry layer**: aggiungere eventi prodotto (funnel login→first session→first purchase) mantenendo consenso/Privacy (GDPR.md) e senza impattare performance real-time.
- **LiveOps**: introdurre un sistema “eventi settimanali” configurabile via DB (stesso paradigma missioni/features) per moltiplicare retention senza shipping frequente.

---

## 20) Licenze, IP e asset (punto chiave per acquisizione)
- Il backend include una licenza proprietaria: `server/LICENSE.txt` (Reanda Proprietary License v1.0).
- Il progetto usa dipendenze OSS (Node/Express/Socket.IO/Stripe/better-sqlite3) e asset serviti via CDN (es. jsDelivr). In fase di acquisizione è consigliato centralizzare la proprietà/gestione degli asset (repo CDN) e produrre un inventario IP (loghi, musiche, sprite, video cinematiche).

---

## 21) Punti forti finali (per un buyer)
Un buyer non compra solo “un prototipo”: compra un **MMO browser già operativo** con:
- real‑time robusto e difeso (server-authoritative + hardening)
- contenuto e loop già pronti per scalare (missioni, minigiochi, zone, cosmetici)
- monetizzazione già funzionante e verificabile (Stripe + webhook idempotenti)
- una base LiveOps che si estende senza rifare il core
