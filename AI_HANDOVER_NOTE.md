# AI Handover Note
**Project:** ai-marketplace-skeleton (GitHub: `https://github.com/ezekiel1214/ai-marketplace-skeleton`)
**From:** Kimi (Moonshot AI)
**To:** Next AI Assistant / Human Maintainer
**Date:** 2024-06-11

---

## 1. Repo Purpose in One Sentence
Minimal full-stack starter for an “AI-agent marketplace” — React + Vite frontend, Express + MongoDB backend, Docker-ready, searchable agent catalog, webhook health-check, and a stub chat interface.

---

## 2. Tech Stack Snapshot
| Tier | Tech | Key Files |
|------|------|-----------|
| Front-End | React 18, Vite, SWR, MUI | `apps/web/` |
| Back-End | Node 20, Express 4, MongoDB 6, Redis 7 | `apps/api/` |
| Shared Types | Zod schemas in `packages/shared/` | `agent.schema.ts` |
| Search | MongoDB text index (Atlas Search optional) | `models/agent.model.js` |
| DevOps | Docker Compose, GitHub Actions, pnpm workspaces | `docker-compose.yml`, `.github/workflows/ci.yml` |

---

## 3. Local Quick-Start (verified)
```bash
git clone https://github.com/ezekiel1214/ai-marketplace-skeleton.git
cd ai-marketplace-skeleton
cp .env.example .env
docker compose up -d   # mongo + redis
pnpm i
pnpm dev               # http://localhost:5173
pnpm db:seed           # optional fake data
```

---

## 4. Current Feature Completeness
✅ Search agents by keyword / tags
✅ Paginated results (12 per page)
✅ Add agent with webhook validation (must echo `{pong:true}`)
✅ Health-check on webhook URL at creation
✅ Hot-reload dev environment
✅ GitHub-Actions CI → Railway preview URL on PR
⬜ Atlas Search fuzzy index (text index only)
⬜ Stripe Connect onboarding
⬜ Rasa / Dialogflow bot wiring beyond stub
⬜ Prometheus metrics (docker-compose ready, not scraped)

---

## 5. Next Recommended Tasks (in order)
1. Replace text index with Atlas Search index for typo-tolerance (see mapping in previous chat).
2. Add Stripe Connect KYC and split-charge example (`apps/api/src/billing/` already stubbed).
3. Introduce rate-limiting on webhook endpoint (`express-rate-limit` + Redis store).
4. Write integration tests for `/agents` route using supertest + in-memory MongoDB.
5. Add Storybook stories for `AgentCard` and `SearchBar` in `packages/ui`.

---

## 6. Environment Variables
| Key | Default | Meaning |
|-----|---------|---------|
| `MONGODB_URI` | `mongodb://localhost:27017/ai-marketplace` | DB connection |
| `REDIS_URL` | `redis://localhost:6379` | Rate-limit / pub-sub |
| `SEARCH_ENGINE` | `mongo` | `mongo` or `atlas` |
| `STRIPE_SECRET_KEY` | — | Needed for billing task |
| `NODE_ENV` | `development` | Switches CORS policy |

---

## 7. Common Pitfalls
- **CORS in dev:** frontend runs on `:5173`, api on `:4000`; dev CORS regex allows `localhost:any-port`.
- **Webhook timeout:** currently 4 s; lower to 2 s + 1 retry before release.
- **Atlas Search:** if enabled, remember to create the index in Atlas UI or Terraform; otherwise queries fall back to text index silently.

---

## 8. Useful Commands
```bash
pnpm lint          # eslint + prettier
pnpm test:api      # jest in apps/api
pnpm storybook     # launch Storybook on :6006
pnpm db:seed       # 30 fake agents
pnpm build         # production build for both apps
```

---

## 9. Architecture Diagram (text)
```
┌--------------┐      ┌-------------┐      ┌--------------┐
│  React 5173 │<---->│ Express 4000 │<---->│ Mongo 27017 │
└-------------│      └------┬------┘      └------┬-------┘
             │           │webhook ping          │text/search
             │           ▼                      │
             │      ┌--------┐                 │
             └------│ Agent  │(webhookUrl)------┤
                    └--------┘                 │
                                          ┌------┴------┐
                                          │ Redis 6379 │
                                          └------------┘
```

---

## 10. Contacts & Links
- **Repo:** https://github.com/ezekiel1214/ai-marketplace-skeleton
- **Live Preview (latest main):** https://ai-marketplace-skeleton.up.railway.app
- **Staging (per-PR):** linked in PR comments by GitHub Actions bot

That’s it—happy shipping!
