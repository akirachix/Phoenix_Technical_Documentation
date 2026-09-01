# Getting Started

## Configure Environment Variables
## Prerequisites

- Python 3.x (see `.python-version`)
- PostgreSQL
- Redis
- Node.js (for the frontend dashboard)

## 1 · Clone the repository

```bash
git clone https://github.com/your-org/pamodzi.git
cd Phoenix_Backend
```

## 2 · Install dependencies

```bash
python -m venv env
source env/bin/activate
pip install -r requirements.txt
```

## 3 · Configure environment

Create a `.env` file with at minimum:

```bash
DATABASE_URL=postgresql://user:password@localhost:5432/pamodzi
JWT_SECRET_KEY=change-me
JWT_ALGORITHM=RS256
CORS_ORIGINS=http://localhost:3000
AT_USERNAME=...
AT_API_KEY=...
FLUTTERWAVE_SECRET_KEY=...
LOCATIONIQ_API_KEY=...
```

!!! danger "Never commit `.env`"
    See [Security → Environment Variables & Secrets](security.md#environment-variables-secrets).

## 4 · Run database migrations

```bash
alembic upgrade head
```

## 5 · Seed data

Seed a super-admin and a reference cooperative:

```bash
python seed_cooperative.py
```

## 6 · Run the server

```bash
uvicorn main:app --reload
```

The interactive API docs are then available at `/docs` (Swagger UI).

---

Next: explore the [Backend](backend.md) architecture, or jump straight to the [API reference](backend.md#api-reference).