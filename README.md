# tool-media-auth-dashboard

> Deepfake and media-authentication dashboard: upload an image or video (or send it over WhatsApp) and get a trust score from provenance, metadata and forensic checks.

## Overview

tool-media-auth-dashboard (formerly `Mediaauthenticationdashboard`) is a full-stack prototype for AI media forensics. A React + TypeScript dashboard (Vite, Tailwind, shadcn/ui, MUI) talks to a Python FastAPI backend that runs a multi-phase analysis pipeline and combines the results into a weighted trust score and verdict. A Twilio WhatsApp hook lets users submit media from a chat and receive the verdict back as a templated message.

## Features

- **Upload and scan** media from the dashboard (`POST /analyze`), then poll results (`GET /scans/{id}`)
- **Provenance (C2PA)**: checks Content Credentials and flags `c2pa.ai_generated` assertions
- **Metadata analysis**: EXIF extraction and forensic metadata checks
- **Error Level Analysis (ELA)** with heatmap output
- **Deepfake detector** (PyTorch/torchvision, frame sampling for video)
- **Weighted scoring** into a trust score, verdict (authentic / manipulated) and confidence
- **WhatsApp integration** via Twilio (sandbox or approved sender, content templates), plus an experimental Evolution API webhook (`POST /webhooks/evolution`)
- Dashboard views: verdict card, recent scans, deepfake trends, logic-flow and phase diagrams

## Quickstart

### Backend (Python 3.10+)

```bash
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env        # fill in your own Twilio credentials
uvicorn main:app --reload --port 8000
```

### Frontend

```bash
cp .env.example .env.local  # VITE_API_URL=http://localhost:8000
npm i
npm run dev
```

### Deploy

`railway.json` starts the backend with `cd backend && uvicorn main:app --host 0.0.0.0 --port $PORT`. Set the Twilio variables in your hosting provider's secrets, never in the repository.

## Configuration

| Variable | Where | Purpose |
|---|---|---|
| `TWILIO_ACCOUNT_SID` | backend | Twilio account SID |
| `TWILIO_AUTH_TOKEN` | backend | Twilio auth token |
| `TWILIO_WHATSAPP_NUMBER` | backend | WhatsApp sender, e.g. `whatsapp:+1...` |
| `PORT` | backend | Server port (default 8000) |
| `VITE_API_URL` | frontend | Backend base URL |

See `backend/.env.example` and `.env.example`.

## Project structure

```
├── backend/
│   ├── main.py                 # FastAPI app: /analyze, /scans/{id}, webhooks, scoring
│   ├── analyzers/              # deepfake_detector, ela_analyzer, metadata_analyzer, provenance
│   ├── integrations/whatsapp.py# Twilio WhatsApp handler
│   ├── requirements.txt
│   └── .env.example
├── src/                        # React + TypeScript dashboard
│   ├── app/components/         # VerdictCard, RecentScans, DeepfakeTrends, flow diagrams, ui/
│   └── services/api.ts         # API client (VITE_API_URL)
├── index.html, vite.config.ts, package.json
└── railway.json
```

## Results / Screenshots

_Screenshots to be added._

## Roadmap

- Wire the real forensic models into the scoring path (currently partly simulated)
- Persistent storage (Postgres) instead of the in-memory scan store
- Complete Evolution API media download
- Restrict CORS for production

## Disclaimer

Prototype for research and education. Detection results are probabilistic and can be wrong; do not rely on them as the sole basis for decisions about the authenticity of media.

## License

Copyright (c) 2026 MrStauss. **All rights reserved.** No license is granted to copy, modify or redistribute this code. Third-party components keep their own licenses; see [ATTRIBUTIONS.md](ATTRIBUTIONS.md).
