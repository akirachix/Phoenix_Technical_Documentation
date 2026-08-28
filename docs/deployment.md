# Deployment

## Backend (Heroku)

The backend is deployed to Heroku. Config vars are managed under the app's **Settings → Config Vars**.

**Required config vars:**

- `DATABASE_URL` (provisioned through Heroku)
- `JWT_SECRET_KEY`
- `JWT_ALGORITHM`
- `JWT_EXPIRE_DAYS`
- `CORS_ORIGINS`

**Deploy via Git:**

```bash
git push heroku main
```

Changes to config variables take effect automatically because Heroku restarts the dyno. Code changes only take effect after you push and deploy.

**Process type (`Procfile`):**

```text
web: uvicorn main:app --host 0.0.0.0 --port ${PORT}
```

**Database URL handling:** Heroku provides `postgres://` environment variables. The Alembic migration context (`env.py`) automatically rewrites incoming `DATABASE_URL` strings from `postgres://` to `postgresql://` prior to establishing connection pools.

## Frontend (Vercel)

The frontend is deployed to Vercel, connected to the frontend repository.

**Required environment variable:**

| Variable | Value |
|---|---|
| `NEXT_PUBLIC_API_URL` | `https://phoenixdashboard.vercel.app/` |

## Release Checklist

- [ ] Confirm `CORS_ORIGINS` on Heroku includes the current production frontend URL.
- [ ] Confirm `NEXT_PUBLIC_API_URL` on Vercel points to the current production backend URL (`https://`).
- [ ] Run `alembic upgrade head` against the production database if there are new migrations.
- [ ] Verify `/docs` loads on the deployed backend, and the deployed frontend can successfully load inventory without console errors.

**Hosting:** Heroku (production environment)
**CI/CD:** GitHub Actions
**Environment isolation:** Production secrets are never committed to source control.

## Deploy Process

Pushes to `main` trigger a GitHub Actions workflow that:

1. Checks out the code and sets up Python.
2. Installs dependencies from `requirements.txt`.
3. Runs the test/lint step (currently limited — see [Testing & QA](backend.md)).
4. Deploys to Heroku using the Heroku CLI/API, authenticated via a `HEROKU_API_KEY` secret stored in the repo's GitHub Actions secrets.
5. Heroku builds the app and starts it via the `Procfile`'s `web` process.

**Rollback:** via Heroku's release rollback:

```bash
heroku releases:rollback
```
