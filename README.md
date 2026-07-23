# WealthOS AI

AI-powered wealth management platform for Indian investors.

This repository has been **initialized only** — folder structure, tooling,
and infrastructure are in place, but no business logic, domain models, or
dashboards have been built yet.

## Stack

| Layer          | Technology                                              |
|----------------|----------------------------------------------------------|
| Frontend       | Next.js (App Router) · TypeScript · Tailwind CSS · shadcn/ui · Recharts |
| Backend        | FastAPI (Python, async)                                  |
| Database       | PostgreSQL (async SQLAlchemy 2.0 + Alembic migrations)    |
| Auth           | Local auth (MVP) — password hashing + JWT access/refresh |
| PDF            | ReportLab                                                 |
| AI             | OpenAI-compatible, provider-configurable client architecture |

## Project structure

```
wealthos-ai/
├── docker-compose.yml        # Local PostgreSQL
├── frontend/                 # Next.js app
│   ├── src/
│   │   ├── app/               # App Router pages/layouts
│   │   ├── components/ui/     # shadcn/ui primitives
│   │   ├── hooks/
│   │   ├── lib/                # cn() utility, shared helpers
│   │   ├── services/           # API client layer (to be built)
│   │   ├── types/
│   │   └── config/
│   ├── components.json        # shadcn/ui config
│   └── .env.local.example
└── backend/                   # FastAPI app
    ├── app/
    │   ├── main.py             # App entrypoint (health check + CORS wired)
    │   ├── api/v1/              # Versioned API routers (stubs, no routes yet)
    │   │   └── endpoints/       # auth, users, portfolio, transactions, goals, insights, reports
    │   ├── core/                # Settings, security (JWT/password hashing) primitives
    │   ├── db/                  # Async engine/session, declarative Base, mixins
    │   ├── models/               # SQLAlchemy models (empty — added per feature)
    │   ├── schemas/              # Pydantic schemas (empty — added per feature)
    │   ├── crud/                 # Data-access layer (empty — added per feature)
    │   ├── services/
    │   │   ├── ai/                # AIProvider interface + OpenAI-compatible provider + factory
    │   │   └── pdf/                # ReportLab report generator scaffold
    │   └── utils/
    ├── alembic/                  # DB migrations (wired to app settings/models)
    ├── requirements.txt
    └── .env.example
```

## Getting started

### 1. Database

```bash
docker compose up -d postgres
```

### 2. Backend

```bash
cd backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # then fill in SECRET_KEY, AI_API_KEY, etc.

# Run the API
uvicorn app.main:app --reload
```

- Health check: `GET http://localhost:8000/health`
- OpenAPI docs: `http://localhost:8000/docs`

Once models are added, generate/apply migrations with:

```bash
alembic revision --autogenerate -m "add <feature> models"
alembic upgrade head
```

### 3. Frontend

```bash
cd frontend
cp .env.local.example .env.local
npm install
npm run dev
```

- App: `http://localhost:3000`

## Notes on this initialization

- The `shadcn` CLI was **not** used to add components in this environment because
  it requires network access to `ui.shadcn.com`. The `new-york`/neutral theme
  tokens, `components.json`, and base primitives (`Button`, `Card`, `Input`,
  `Label`) were added by hand to match what the CLI would generate. If your
  environment can reach `ui.shadcn.com`, `npx shadcn add <component>` will work
  normally from here.
- All domain routers (`auth`, `users`, `portfolio`, `transactions`, `goals`,
  `insights`, `reports`) exist as empty `APIRouter()` stubs — no endpoints,
  models, or schemas have been implemented yet, per the initialization-only
  scope of this pass.
- The AI integration is built around a provider-agnostic `AIProvider` abstract
  base class with an `OpenAICompatibleProvider` implementation, selected via
  `AI_PROVIDER`/`AI_BASE_URL` in settings — no prompts or AI features are wired
  up yet.
