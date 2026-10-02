# BidSentinel

AI-powered bid compliance verification for GeM procurement (MoPNG/CPSEs).

## Quick Start

```bash
# 1. Clone and setup
cp .env.example .env

# 2. Start everything
docker compose up -d

# 3. Wait ~30s for DB to be ready, then seed mock data
docker compose exec backend python -m app.tests.seed_mock_data

# 4. Open browser
# Frontend:  http://localhost:3000
# API Docs:  http://localhost:8000/api/docs
# MinIO:     http://localhost:9001  (minioadmin/minioadmin)
```

## Demo Login
| Role | Email | Password |
|------|-------|----------|
| Officer | officer@bidsentinel.gov.in | Officer@1234 |
| Admin | admin@bidsentinel.gov.in | Admin@1234 |
| Auditor | auditor@bidsentinel.gov.in | Auditor@1234 |

## Stack
- **Frontend**: Next.js 14 + TypeScript + Tailwind + Framer Motion + D3
- **Backend**: FastAPI + SQLAlchemy + Alembic + Celery + Redis
- **DB**: PostgreSQL (pgvector)
- **Storage**: MinIO
- **AI**: Mock (swap to Gemini/Claude via `AI_PROVIDER` env var)

## Key Features
1. 12 portal connectors (Udyam, GSTN, PAN/ITD, MCA21, EPFO, ESIC, Startup India, NSIC, DigiLocker, DPIIT/MII, BIS, GeM Blacklist)
2. Weighted compliance scoring (0-100) with knockout rules
3. What-If simulator — toggle checks, see score delta (never persisted)
4. AI recommendations + clarification letter drafts
5. SHA-256 hash-chained audit log
6. Batch verification via Celery
7. PDF compliance report export
8. RBAC: Officer / Reviewer / Auditor / Admin

## Mock Bidders (seeded)
| Company | PAN | Expected Result |
|---------|-----|-----------------|
| TechBuild Solutions Pvt Ltd | AAACB1234C | HIGH score, LOW risk |
| Bharat Manufacturing Co | BBBCD5678D | MEDIUM score, MEDIUM risk |
| Global Infra Traders | CCCDE9012E | KNOCKOUT (debarred) |

## Deployment
See `docs/DEPLOYMENT_FREE.md` for free-tier deployment on Vercel + Render + Neon.
