# APEX Genesis — Independent Source Audit

Date: 2026-10-14. Source supplied: `apex_genesis_full_repository.zip` (33 files), `ApexGenesisApp.tsx`, `0001_genesis_schema.sql`, `apex_genesis_command_environment.html`, Gemini summary text.

## Bottom line
**Overall production readiness: 2.8/10 — BLOCKED.** Substantial prototype and useful core modules, but not production-ready, not genuinely autonomous in a deployed cloud, and not safe to connect real candidate data until critical issues are fixed.

## Evidence reproduced
1. `npm run build` fails because `scripts/build.cjs` loads Babel via `/usr/share/nodejs/@babel/core` (absolute path not present). `package.json` declares no dependencies or devDependencies.
2. `tsc -p tsconfig.json --noEmitOnError false` emits JavaScript but exits with errors, including unavailable Node.js type declarations. Compilation through a temporary workaround is **not** a clean build.
3. `node --test dist/tests/*.test.js` passes **35/35** existing test assertions after the workaround. These are largely local, mock-driven, or in-process tests; they do not demonstrate deployed Workers AI, actual Cloudflare D1 binding, or employer submissions.
4. Critical real exploit reproduced: `SecurityGuard.registerUser({emailVerified:true})` trusts caller-provided verification; an unverified email can immediately obtain a session using `issueSession`. Public `/api/auth/register` forwards the submitted body to this method.
5. `src/worker.ts` ignores `env.APEX_DB` and `env.AI`; responds to `/api/health` with hardcoded `OPERATIONAL`, returns a generic response for other routes, and its scheduled handler only logs a message.
6. `src/db/d1-sqlite-engine.ts` uses Node `child_process.spawnSync`, `python3`, local `fs` and `path` — not compatible with Cloudflare Workers runtime. The in-process row counters do not measure actual account-wide Cloudflare billing or free-tier quota.
7. `src/neural-core/index.ts` relies on injected/mock Workers AI binding. The Gemini implementation requires a supplied `customTransport`; it has no native provider HTTP implementation. Token and neuron counts are estimates from text length and a fixed factor, not verified provider accounting.
8. `src/ui/ApexGenesisApp.tsx` is only a navigation rail and a generic title/subtitle/status surface; 23 distinct functional React screens are not implemented there. UI tests only check metadata, not browser rendering or actions.
9. `public/index.html` embeds a static synthetic candidate, claims, jobs, integrations, and audit history. There are **zero `fetch()` calls** to its backend. Its Emergency Stop toggles a JavaScript flag in the browser only. `dispatchInteractivePlan()` clones a prior report, immediately marks a new plan `COMPLETED`, increments fictional D1 writes, and displays a success toast. Newly entered facts are immediately marked `verified:true` based on freely typed evidence source and excerpt. Fake hash entries are inserted when emergency stop toggles.
10. Demo-only record fixtures and tokens appear in the exported HTML. They must never be treated as real data, published as user identity, or displayed as verified provider health.
11. The claimed 10/10 milestones and 24 agents reflect internal interfaces, labels and mock/local handlers; no evidence of live Cloudflare deployment or authorized submissions is included.

## Strict scorecard
- Architecture/modularity 5.5/10
- Reproducible build 1.5/10
- Authentication/security 1.0/10
- Database architecture 3.5/10
- AI inference 2.0/10
- Agents 3.0/10
- Durable execution 3.0/10
- Discovery/matching 3.0/10
- Resume truthfulness 4.0/10
- Application submissions 0.5/10
- Integrations 1.5/10
- UI appearance 4.5/10
- UI/backend connectivity 1.0/10
- Cloud deployment 1.0/10
- Test credibility 4.0/10
- Zero-cost compliance 2.5/10

## Required release gates
P0: Remove client-supplied email verification and secure account ownership; disable unsafe default user and placeholder verified data. P0: Eliminate false COMPLETED, VERIFIED, and quota statuses in UI. P0: Correct portable build and replace Node/Python bridge with actual D1 binding. P1: Wire Worker fetch and scheduled entrypoints to authenticated API, durable queue and AI binding. P1: Separate mock-based tests from live provider tests and run end-to-end browser tests. P1: Move mutable UI state to authenticated server APIs. P2: Build distinct functional premium workspaces and responsive UX. Do not enable real job applications until a provider-authorized adapter and actual receipt are verified.

## GitHub preservation
This audit belongs on a review branch to avoid altering other APEX infrastructure. The supplied Genesis source ZIP is not represented as remotely committed by this audit file. Ask for or identify the dedicated Genesis repository URL before deploying or merging code.