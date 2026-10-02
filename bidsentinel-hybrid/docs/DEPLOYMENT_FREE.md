# Free Deployment Guide — BidSentinel

## Architecture for Free Tier
```
Vercel (Frontend)  →  Render/Koyeb (FastAPI)  →  Neon/Supabase (PostgreSQL)
                                              →  Upstash (Redis)
```

---

## Step 1: Database — Neon (Free PostgreSQL)

1. Go to https://neon.tech → Sign up free
2. Create project: `bidsentinel`
3. Copy the connection string:
   ```
   postgresql://user:pass@ep-xxx.us-east-2.aws.neon.tech/bidsentinel?sslmode=require
   ```
4. Enable pgvector: In Neon SQL editor run:
   ```sql
   CREATE EXTENSION IF NOT EXISTS vector;
   ```

---

## Step 2: Redis — Upstash (Free)

1. Go to https://upstash.com → Create Redis database
2. Copy the `REDIS_URL` (starts with `rediss://`)

---

## Step 3: Backend — Render (Free)

1. Push `backend/` to a GitHub repo
2. Go to https://render.com → New Web Service
3. Connect repo, set:
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`
4. Add environment variables:
   ```
   DATABASE_URL=postgresql+asyncpg://...neon.tech/bidsentinel?ssl=require
   SYNC_DATABASE_URL=postgresql://...neon.tech/bidsentinel?sslmode=require
   REDIS_URL=rediss://...upstash.io:...
   CELERY_BROKER_URL=rediss://...upstash.io:...
   SECRET_KEY=your-strong-secret-key
   CONNECTOR_MODE=mock
   AI_PROVIDER=mock
   ALLOWED_ORIGINS=https://your-app.vercel.app
   ```
5. Deploy → note your URL: `https://bidsentinel-api.onrender.com`

### Run migrations on Render
In Render Shell:
```bash
alembic upgrade head
python -m app.tests.seed_mock_data
```

---

## Step 4: Frontend — Vercel (Free)

1. Push `frontend/` to GitHub
2. Go to https://vercel.com → New Project → Import repo
3. Set environment variable:
   ```
   NEXT_PUBLIC_API_URL=https://bidsentinel-api.onrender.com
   ```
4. Deploy → your app is live at `https://bidsentinel.vercel.app`

---

## Step 5: Celery Worker — Render Background Worker (Free)

1. In Render → New Background Worker
2. Same repo, same env vars
3. Start Command: `celery -A app.workers.celery_app worker --loglevel=info --concurrency=1`

---

## Notes
- Render free tier sleeps after 15min inactivity — first request may be slow
- Neon free tier: 0.5 GB storage, 1 compute unit
- Upstash free tier: 10,000 commands/day
- For production: upgrade to paid tiers or use AWS (RDS + ElastiCache + ECS)

## Storage (Optional)
For document uploads on free tier, use **Cloudinary** (free 25GB):
```
STORAGE_BACKEND=cloudinary
CLOUDINARY_URL=cloudinary://api_key:api_secret@cloud_name
```
