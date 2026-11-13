# Easy DevOps: Flask -> Next.js (TypeScript) migration

Status: Phase 3 (3a-3d) done: commands + chat/api/file routes. Naya repo me migration ban raha hai.

## Phases
1. Inventory - done
2. Backend core (lib/) - done: config, http shim, fernet, itsdangerous, db, auth, security, firestore, providers
3. Commands: 3a-3d sab done
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

## Phase 3b-3d notes
- Naye lib files: commands/{executor,ai_fallback,code_generate,bulk_actions,env_transfer,render_blueprint,render_create_service,pyutil,errors}.ts,
  lib/zip.ts (built-in zlib se chhota zip reader, koi nayi npm dependency nahi), lib/api-utils.ts.
- Routes (same URL paths): /chat, /api/repos, /api/vercel-projects, /api/vercel-repo-linked, /api/netlify-sites,
  /api/render-services, /api/repo-folders, /api/repo-files, /api/file-source (GET+POST), /api/bulk-action, /api/list-files,
  /api/vercel/deploy-events, /api/vercel/analyze-error, /upload, /upload-zip, /upload-env, /download, /download-env, /download-repo-zip.
- BUG-1 (old code, abhi waisa hi): executor.py `time` import nahi karta -> VERCEL_ROLLBACK / VERCEL_LIST_DEPLOYMENTS error.
  Fix: lib/commands/executor.ts me `FIX_TIME_BUG = true`.
- Vercel request body limit ~4.5MB hai (Flask version me bhi same thi) - bade upload/zip pehle bhi fail hote the.
- X-Env-Export-Warning header me em-dash '—' ko '-' kiya (Node headers sirf latin-1 lete hain).
