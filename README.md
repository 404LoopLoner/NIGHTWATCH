# NIGHTWATCH — Cybersecurity Monitoring Prototype

NIGHTWATCH is a portfolio-grade defensive security prototype with a Next.js dashboard and a FastAPI analysis service. It demonstrates incident triage, synthetic telemetry analysis, simple detection rules, and an Isolation Forest anomaly detector.

> **Important:** This release uses synthetic demonstration data. It is not a production SIEM, does not monitor real networks, and must not be used to make operational security decisions.

## Features
- Overview dashboard with clearly labelled demonstration metrics.
- Investigation view for four synthetic cases.
- Detection Lab with adjustable request volume, failed-login count, and outbound data.
- FastAPI endpoints for health, incidents, and analysis.
- Hybrid detection: configurable threshold rules plus an Isolation Forest model.
- Analysis history and a system log view in the frontend.
- Basic backend tests and an architecture diagram.

## Architecture

```text
Synthetic telemetry / Detection Lab
                |
                v
        Next.js dashboard
                |
          HTTP / JSON
                |
                v
       FastAPI analysis API
          /           \
         v             v
 Threshold rules   Isolation Forest
         \             /
          v           v
        Explanation + priority result
                |
                v
       Dashboard / case history
```

See `docs/architecture.svg` for a visual diagram.

## Requirements
- Node.js 20 or newer (recommended)
- Python 3.10 or newer
- Windows PowerShell, macOS Terminal, or Linux shell

## Run on Windows

### 1. Start the backend
Open a terminal in the `server` directory:

```powershell
cd server
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

If PowerShell blocks activation, use:
```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

Backend endpoints:
- `GET http://127.0.0.1:8000/api/health`
- `GET http://127.0.0.1:8000/api/incidents`
- `POST http://127.0.0.1:8000/api/analyze`

Interactive API docs: `http://127.0.0.1:8000/docs`

### 2. Start the frontend
Open a **second** terminal in the `frontend` directory:

```powershell
cd frontend
npm install
npm run dev
```

Open `http://localhost:3000`.

The frontend reads `NEXT_PUBLIC_API_BASE_URL`; by default it uses `http://127.0.0.1:8000`. To override it, create `frontend/.env.local`:
```env
NEXT_PUBLIC_API_BASE_URL=http://127.0.0.1:8000
```

## Run backend tests
With the backend virtual environment activated, from `server`:

```powershell
pytest -q
```

## Detection examples
Normal-like:
```json
{"requests_per_minute": 10, "failed_logins": 0, "outbound_mb": 5}
```

Suspicious authentication activity:
```json
{"requests_per_minute": 120, "failed_logins": 25, "outbound_mb": 20}
```

High outbound data:
```json
{"requests_per_minute": 120, "failed_logins": 2, "outbound_mb": 850}
```

Detection outcomes depend on configured thresholds and the synthetic training baseline. An anomaly is not proof of malicious activity.

## Repository hygiene
- `node_modules`, `.next`, `.venv`, Python caches, local environment files, and build artifacts are excluded by `.gitignore`.
- No API keys or credentials are required.
- Never commit secrets, real personal data, or real security logs without authorization.

## Suggested next steps
1. Validate detections against labelled, representative data.
2. Add persistent storage and authenticated users before handling non-demo cases.
3. Add structured log ingestion only from systems you own or are explicitly authorized to assess.
4. Conduct threat modelling and independent security testing before any real deployment.

## Project status
Portfolio / engineering prototype — synthetic telemetry only.
