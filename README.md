# Lt-ops — Liontech Support Bot Platform

This workspace bundles the two repositories that make up the **Liontech Support
Bot** product: an embeddable AI support widget for ERP-style web apps, and the
multi-tenant backend + admin platform that powers it.

| Repository | Path | What it is |
|---|---|---|
| `Lt-support-bot` | [`dom/`](dom/README.md) | Node/Express/MongoDB API, the super-admin dashboards, and everything server-side |
| `Lt-ops-widget` | [`widget/`](widget/README.md) | The embeddable JS chat widget + password reset page served to end customers |

Each folder has its own `git` history (`dom/.git`, `widget/.git`) and its own
README, so they can be developed and deployed independently even though they
ship together as one product.

## How the pieces fit together

```
                ┌─────────────────────────────────────────┐
                │        Client's ERP web page             │
                │  <script src=".../widget.min.js"          │
                │          data-api-key="lts_live_...">     │
                └───────────────────┬───────────────────────┘
                                    │  widget.js (widget/)
                                    │  chat UI, task engine, file tools
                                    ▼
                ┌─────────────────────────────────────────┐
                │   liontech-support-bot-backend (dom/)     │
                │   Express API — auth, AI routing,         │
                │   billing, knowledge base, audit logs     │
                └───────┬───────────────────┬───────────────┘
                        │                   │
                        ▼                   ▼
                ┌───────────────┐   ┌───────────────────┐
                │   MongoDB      │   │  Cloudflare R2     │
                │  (clients,     │   │  (workspace files, │
                │   users, logs) │   │   knowledge files) │
                └───────────────┘   └───────────────────┘
                        ▲
                        │  same-origin static files
                ┌───────┴───────────────────────────────┐
                │   liontech-admin (dom/liontech-admin)   │
                │   Super-admin dashboard: manage clients,│
                │   knowledge base, billing, audit logs   │
                └─────────────────────────────────────────┘
```

## Product summary

Liontech Support Bot is a **white-labelled, multi-tenant AI support
assistant** that a business ("client" / organisation) embeds into its own
internal web application (typically an older-style ERP or intranet). It:

- Answers staff questions about how to use the host application, using a
  per-client knowledge base (pages, FAQs, documents, terminology).
- Can execute predictable, scripted actions ("tasks") inside the host page —
  e.g. approving a request, filling a form — through a deterministic task
  engine, without needing an LLM call for known workflows.
- Tracks token usage and cost per client for billing, with configurable
  monthly caps.
- Is administered through a separate super-admin dashboard, distinct from the
  per-organisation staff logins used inside the widget itself.

## Live demo

- [Pre-Authorization — MediCare HMO](https://dominicyogi.github.io/Lt-ops-widget/test.html)

## Repository quick links

- Backend API and business logic → [`dom/liontech-support-bot-backend/README.md`](dom/liontech-support-bot-backend/README.md)
- Super-admin dashboards (HTML/CSS/JS, no build step) → [`dom/liontech-admin/README.md`](dom/liontech-admin/README.md)
- Embeddable chat widget → [`widget/README.md`](widget/README.md)

## Getting started (local development)

1. **Backend**: see [`dom/liontech-support-bot-backend/README.md`](dom/liontech-support-bot-backend/README.md)
   for environment variables and `npm start`.
2. **Admin dashboards**: served automatically by the backend at `/lt-ops`
   once it's running — no separate build or server needed.
3. **Widget**: point a test HTML page at your local backend and load
   `widget/widget.js` with a valid `data-api-key`; see
   [`widget/README.md`](widget/README.md).

## Security notes

- Real secrets belong only in `dom/liontech-support-bot-backend/.env`, which
  is git-ignored. Never commit real API keys, JWT secrets, or database URIs.
- The `data/` folder inside the backend contains **sample/seed data** for
  local development, not production data — see
  [`dom/liontech-support-bot-backend/data/README.md`](dom/liontech-support-bot-backend/data/README.md).
