# APEX Genesis V2 — Refactor Mandate

Read the independent audit in this folder first. This review branch deliberately does not modify the independent APEX or Cloudflare-brain code in `main`.

The uploaded Genesis project is a **greenfield-derived, existing 33-file prototype**. Preserve its legitimate modules; refactor the actual source, not merely write a new design brief. Target a dedicated private `apex-genesis-os` repository for production code once the owner supplies/authorizes it.

## Ordered P0 release gates

1. **Security:** Fix the caller-controlled `emailVerified` registration vulnerability; verify real identity ownership before a session is issued; add failing-then-passing exploit and cross-tenant tests.
2. **Portable build:** Replace hardcoded `/usr/share/nodejs/@babel/...` paths with reproducible package dependencies and a lockfile. Make `npm ci && npm run typecheck && npm run build && npm test` pass on clean GitHub Actions.
3. **Real Worker runtime:** Replace stub `src/worker.ts` with authenticated HTTP/API routing, actual `env.APEX_DB` and `env.AI` binding use, and a real `scheduled()` task queue. Replace Python `spawnSync` storage with async native Cloudflare D1 access.
4. **Honest AI:** Distinguish live LLM inference, mocks, and deterministic fallback. Use actual provider responses; never call mock-injected inference a live provider verification. Bound free-tier usage.
5. **UI correctness:** Replace standalone `INITIAL_BACKEND_STATE` snapshots. No fake candidate resume, claims, 100% verification, successful submissions, emergency-stop status, or fictional D1 accounting. React must use real authenticated API calls, not just 23 navigation metadata labels. An emergency stop must halt server workers.
6. **Recruitment integrity:** Evidence-backed candidate facts, job source metadata, permission-gated employer actions, genuine receipts, and no CAPTCHA bypass.
7. **Design upgrade:** A visually original premium APEX Genesis interface with real functional workspaces, responsive navigation, accessibility, charts, command center and tested browser interactions.
8. **Evidence:** No false deployment status, no fake logs or screenshots. Commit small verified milestones to the dedicated Genesis repo; score by observed capability. 

The original 35 local tests passed after TypeScript emitted JS through a workaround, **but a normal build failed**. Make the negative tests and clean CI meaningful before enabling live candidate data.
