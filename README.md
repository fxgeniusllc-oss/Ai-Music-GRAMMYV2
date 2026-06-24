# 🎵 Grammy Engine — AI Music Production Platform

> **Turn a text prompt into a radio-ready, Grammy-worthy track in under 60 seconds.**

Grammy Engine is an end-to-end AI music production platform. Type a prompt, and the engine enhances it with GPT-4, generates an instrumental with Meta's MusicGen, optionally adds AI vocals via voice cloning, masters the audio to streaming standards, and scores your track's hit potential — all automatically.

---

## ✨ Key Features

| Feature | Description |
|---------|-------------|
| 🎼 **Prompt-to-Track** | Describe your song in plain text; receive a fully produced track in < 60 s |
| 🏆 **Grammy Meter™** | Proprietary AI scoring (0–100) across 5 categories — production quality, commercial appeal, innovation, emotional impact & radio readiness |
| 🎚️ **Professional Mastering** | Reference-based mastering (Matchering) with Spotify, Vinyl, and Radio presets |
| 🎤 **Voice Cloning** | Clone any voice from 30–60 s of audio using So-VITS-SVC |
| 🤖 **GPT-4 Prompt Enhancement** | Automatically enriches your prompt with musical details before generation |
| 📊 **Hit Insights** | Actionable recommendations and Billboard correlation (72% accuracy) |
| 🔌 **Full REST API** | 7 documented API modules, JWT auth, tier-based rate limiting |
| 🏗️ **Enterprise Infrastructure** | FastAPI + Celery workers, auto-scaling on AWS ECS, 99.9% uptime SLA |

---

## 🚀 Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/fxgeniusllc-oss/Ai-Music-GRAMMYV2.git
cd Ai-Music-GRAMMYV2

# 2. Configure environment variables
cp .env.example .env
# Open .env and fill in your API keys (OpenAI, Supabase, etc.)

# 3. Start all services with Docker Compose
docker-compose up -d

# 4. Open the platform
#   Frontend:         http://localhost:3000
#   Backend API docs: http://localhost:8000/api/docs
#   Celery monitor:   http://localhost:5555
```

> **Requirements:** Docker & Docker Compose. GPU recommended for MusicGen (falls back to CPU).

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | Next.js 14 · React 18 · TypeScript 5 · Tailwind CSS 3.4 · Zustand · WaveSurfer.js |
| **Backend** | FastAPI (Python 3.11) · Pydantic · SQLAlchemy |
| **AI / ML** | GPT-4 (prompt enhancement) · MusicGen 3.3B (Meta) · So-VITS-SVC (voice cloning) · Matchering (mastering) · Custom ONNX model (Grammy Meter™) |
| **Async Queue** | Celery 5 · Redis |
| **Database / Storage** | Supabase (PostgreSQL + S3-compatible storage) |
| **Infrastructure** | AWS ECS · Vercel · Cloudflare CDN · Docker |
| **Monitoring** | Sentry · CloudWatch · PostHog |

---

## 🎬 How It Works

```
User prompt  →  GPT-4 enhancement  →  MusicGen generation
                                              ↓
Grammy Meter™ scoring  ←  Matchering mastering  ←  (optional) So-VITS vocals
```

**Step-by-step:**

1. You type: `"Chill lo-fi hip hop, 85 BPM, rainy day mood"`
2. GPT-4 enriches the prompt with chord progressions, instrument suggestions, and production notes
3. MusicGen (3.3B parameters) generates the instrumental audio
4. Matchering masters the track to -14 LUFS (Spotify standard)
5. Grammy Meter™ scores the track and provides actionable insights
6. Your finished track is stored and ready to play, download, or share

---

## 📡 API Overview

All endpoints are under `/api/` and require a JWT bearer token (except `/api/auth`).

| Endpoint | Description |
|----------|-------------|
| `POST /api/auth/register` | Create a new user account |
| `POST /api/auth/login` | Obtain a JWT token |
| `POST /api/prompt/enhance` | Enhance a text prompt with GPT-4 |
| `POST /api/songgen` | Queue a song generation task |
| `GET  /api/songgen/{task_id}` | Poll generation progress |
| `POST /api/vocalgen` | Generate / clone vocals |
| `POST /api/mixmaster` | Master an audio track |
| `POST /api/grammy-meter/analyze` | Score a track's hit potential |
| `GET  /api/grammy-meter/{task_id}` | Retrieve scoring results |
| `POST /api/upload` | Upload audio to storage |

Full API reference: [API_DOCS.md](./API_DOCS.md)

---

## 💰 Pricing Tiers

| Tier | Price | Highlights |
|------|-------|------------|
| **Starter** | Free | 3 tracks / month, 30 s max, basic quality |
| **Pro Creator** | $29 / mo | Unlimited tracks, Grammy Meter™, mastering, voice cloning |
| **Label Plan** | $199 / mo | Multi-user seats, API access, advanced analytics |
| **Enterprise** | $999+ / mo | White-label, custom model training, SLA guarantee |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        USER INTERFACE                               │
│      Next.js Web App  ·  Mobile (React Native)  ·  API Docs        │
└──────────────────────────────┬──────────────────────────────────────┘
                               │ HTTPS
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      API GATEWAY (FastAPI)                          │
│  Auth · Prompt · SongGen · VocalGen · MixMaster · Grammy Meter      │
│  ── Middleware: CORS · Rate Limit · JWT Auth · Request Logging ──   │
└──────────────────┬──────────────────────────┬───────────────────────┘
                   │                          │
        ┌──────────┴──────────┐   ┌───────────┴──────────┐
        ▼                     ▼   ▼                      ▼
┌──────────────┐   ┌──────────────────┐      ┌────────────────────┐
│   SUPABASE   │   │  CELERY WORKERS  │      │   REDIS BROKER     │
│  PostgreSQL  │   │  Generation (GPU)│◄─────│  Task Queues       │
│  + Storage   │   │  Mastering (CPU) │      │  (generation /     │
└──────────────┘   │  Scoring   (CPU) │      │   mastering /      │
                   └────────┬─────────┘      │   scoring)         │
                            │               └────────────────────┘
          ┌─────────────────┼───────────────────┐
          ▼                 ▼                   ▼
  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐
  │  OpenAI      │  │  MusicGen    │  │  Matchering      │
  │  GPT-4       │  │  (Meta AI)   │  │  (Mastering)     │
  └──────────────┘  └──────────────┘  └──────────────────┘
```

### Performance Targets

| Metric | Target |
|--------|--------|
| API response time | < 200 ms (p95) |
| Song generation | < 60 s (p95) |
| Mastering | < 30 s (p95) |
| Grammy Meter scoring | < 15 s (p95) |
| Uptime | 99.9% |

### Production Deployment

```
Cloudflare CDN  →  Vercel (Frontend)
                →  AWS ECS (FastAPI + Celery workers)  →  Supabase (DB + Storage)
                                                       →  Redis Cloud (queue)
                                                       →  Sentry (monitoring)
```

Full deployment guide: [DEPLOYMENT.md](./DEPLOYMENT.md)

---

## 📁 Project Structure

```
Ai-Music-GRAMMYV2/
├── backend/
│   ├── api/            # Route handlers (auth, songgen, vocalgen, mixmaster, …)
│   ├── workers/        # Celery tasks (song, mix, meter)
│   ├── services/       # AI integrations (OpenAI, MusicGen, Matchering, …)
│   ├── models/         # SQLAlchemy models
│   ├── main.py         # FastAPI entry point
│   └── requirements.txt
├── frontend/
│   ├── pages/          # Next.js pages (landing, dashboard, library, auth)
│   ├── components/     # React components (PromptInput, MeterGauge, …)
│   ├── hooks/          # Custom React hooks
│   └── lib/api.ts      # Typed API client (25+ methods)
├── docker-compose.yml  # Local dev environment (all services)
├── .env.example        # Environment variable template
└── .github/workflows/  # CI/CD (lint → test → build → deploy)
```

---

## 🔐 Security

- **Authentication:** JWT (RS256), 24 h access tokens + 30-day refresh tokens
- **Rate limiting:** 100 req / min per IP (configurable per tier)
- **Input validation:** Pydantic schemas on every endpoint
- **Injection prevention:** SQLAlchemy ORM (no raw SQL)
- **Encryption:** AES-256 at rest, TLS 1.3 in transit
- **Secrets management:** AWS Secrets Manager + 90-day key rotation

---

## 🗺️ Roadmap

| Quarter | Highlights |
|---------|-----------|
| **Q2 2026** | WebSocket real-time progress · GraphQL API · Multi-region (US/EU/Asia) |
| **Q3 2026** | Kubernetes (ECS → k8s) · Kafka event-driven architecture |
| **Q4 2026** | ML model versioning (MLflow) · Edge inference · Microservices split |

Full roadmap: [ROADMAP.md](./ROADMAP.md)

---

## 🤝 Contributing

1. Fork the repository and create a feature branch (`git checkout -b feature/my-feature`)
2. Make your changes and add tests where applicable
3. Run the linter and test suite (`flake8 backend/` · `pytest` · `npm test`)
4. Open a pull request — describe what changed and why

Questions? Join us on [Discord](https://discord.gg/grammyengine) or email [hello@grammyengine.com](mailto:hello@grammyengine.com).

---

## 📚 Documentation Index

| Document | Description |
|----------|-------------|
| [ARCHITECTURE.md](./ARCHITECTURE.md) | Detailed system architecture & diagrams |
| [DEPLOYMENT.md](./DEPLOYMENT.md) | Production deployment guide (AWS / Vercel) |
| [API_DOCS.md](./API_DOCS.md) | Full REST API reference |
| [ROADMAP.md](./ROADMAP.md) | 3-year product roadmap |

---

**Built with ❤️ by [Omni-Tech-Stack](https://grammyengine.com)**
**Version:** 1.0.0 · **Status:** Production-Ready 🚀
