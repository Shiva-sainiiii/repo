# Easy DevOps: Flask -> Next.js (TypeScript) migration

Status: Phase 2 done (backend core). Flask app abhi bhi chal raha hai, kuch nahi toota.

## Phases
1. Inventory - done
2. Backend core (lib/) - done: config, http shim, fernet, itsdangerous, db, auth, security, firestore, providers
3. Commands (intent_parser, executor, confirmation, code_generate, bulk, render, env_transfer) - pending
4. API routes (/chat, /api/*, auth, sessions, files) - pending
5. Frontend (script.js / style.css -> components) - pending
6. vercel.json cutover + old Flask files hatana - pending

## Compatibility (verify ho chuka hai)
- Env vars same naam: FLASK_SECRET_KEY, FERNET_KEY, GITHUB_CLIENT_ID, GITHUB_CLIENT_SECRET,
  OPENROUTER_KEY, APP_BASE_URL, DATABASE_URL, FIREBASE_SERVICE_ACCOUNT_JSON (aur FLASK_ENV).
- Login cookie (agent_session) Python itsdangerous ke saath dono taraf compatible -> users logout nahi honge.
- Neon mein stored encrypted tokens (Fernet) naye code se decrypt hote hain, aur ulta bhi.
- Postgres table aur Firestore collections (chat_sessions/...) same hain.

## Chhote farq (jaan-bujh ke)
- Env validation ab pehli use pe hoti hai (import-time nahi), warna `next build` env ke bina toot jaata. Error messages same.
- DB: har query pe naya connection ki jagah chhota connection pool (max 3).
- Python `requests` ka timeout (headers aane tak) lib/http.ts mein same semantics ke saath.
