# Backend

The backend is a central **FastAPI** service that every client — USSD, mobile app, desktop PWA — talks to. It is the only component that reads or writes PostgreSQL directly.

## Tech stack

<div class="grid cards" markdown>

-   **Framework**

    ---
    FastAPI (Python) — internal REST APIs

-   **ORM**

    ---
    SQLAlchemy

-   **Database**

    ---
    PostgreSQL

-   **Caching / Sessions**

    ---
    Redis

-   **Hosting**

    ---
    Heroku (production)

-   **CI/CD**

    ---
    GitHub Actions

</div>

## Architecture pattern

The backend follows a **repository pattern**: each domain model (`Farmer`, `Cooperative`, `DemandCampaign`, …) has a dedicated repository class (`FarmerRepository`, `CooperativeRepository`, …) that encapsulates all database access for that entity. Routers call repository methods rather than querying the ORM directly, which keeps query logic centralized and testable.



## Project structure

```text
Phoenix_Backend/
├── alembic/                # DB migrations
├── core/
├── pamodzi/
│   ├── models/             # SQLAlchemy models
│   ├── repositories/       # All DB access, one class per entity
│   ├── routers/            # FastAPI endpoints
│   ├── schemas/            # Pydantic request/response schemas
│   └── services/           # Business validation & workflows
├── database.py
├── dependencies.py
├── main.py
└── requirements.txt
```

## Authentication flow


    Only Admins have `password_hash` / `email` directly on the `user` table — enforced by a DB check constraint:
    `role = 'admin' OR (password_hash IS NULL AND email IS NULL)`.
    Non-admin login credentials always live on the role-specific table instead.

| Role | Credential | Stored on |
|---|---|---|
| Farmer | PIN | `Farmer.pin_hash` |
| Cooperative Agent | Password | `CooperativeAgent.password_hash` |
| Wholesale Buyer | Password | `WholesaleBuyer.password_hash` |
| Administrator | Password + mandatory MFA | `User.password_hash` |

Farmers authenticate via a **PIN**, not a password — consistent with the USSD-first, low-literacy access pattern.

## API reference

Each endpoint below is shown as a method + path callout, colour-coded by HTTP verb, with its auth requirement pinned to the right — the same "readable endpoint" pattern used across the rest of this site.

=== "Auth"

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/login/admin/form</span><span class="endpoint-auth">No auth</span>
    </div>
    OAuth2 form (`username`, `password`) → access token, or a 2FA session token + email notice.

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/login/buyer</span><span class="endpoint-auth">No auth</span>
    </div>
    `{email, password}` → 2FA session token.

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/login/farmer</span><span class="endpoint-auth">No auth</span>
    </div>
    `{phone_number, pin}` → access token.

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/login/agent</span><span class="endpoint-auth">No auth</span>
    </div>
    `{staff_id, password}` → access token.

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/verify-2fa</span><span class="endpoint-auth">No auth</span>
    </div>
    `{twofa_session_token, otp_code}` → access token.

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/forgot-password/email</span><span class="endpoint-auth">No auth</span>
    </div>
    `{email}` → generic success message.

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/admin/onboard</span><span class="endpoint-auth">Admin</span>
    </div>
    `AdminRegister` → `{message, admin_id}`.

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/auth/logout</span><span class="endpoint-auth">No auth</span>
    </div>
    Returns a success message.

=== "Farmers"

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/farmers/onboard</span><span class="endpoint-auth">Admin or Agent</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/farmers/</span><span class="endpoint-auth">Admin or Agent</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/farmers/{farmer_id}</span><span class="endpoint-auth">Admin or Agent</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method put">PUT</span><span class="endpoint-path">/farmers/{farmer_id}/verification</span><span class="endpoint-auth">Admin or Agent</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method delete">DELETE</span><span class="endpoint-path">/farmers/{farmer_id}</span><span class="endpoint-auth">Admin or Agent</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/farmers/me/change-pin</span><span class="endpoint-auth">Farmer (self)</span>
    </div>

=== "Users"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/users</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/users/{user_id}</span><span class="endpoint-auth">Admin or Agent</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/users</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/users/{user_id}/edit</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/users/{user_id}/archive</span><span class="endpoint-auth">Admin</span>
    </div>

=== "Cooperatives"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/cooperatives/</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/cooperatives/{id}</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method put">PUT</span><span class="endpoint-path">/cooperatives/{id}</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/cooperatives/</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method delete">DELETE</span><span class="endpoint-path">/cooperatives/{id}</span><span class="endpoint-auth">No auth</span>
    </div>

=== "Cooperative agents"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/cooperative-agents/</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/cooperative-agents/{id}</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/cooperative-agents/onboard</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method put">PUT</span><span class="endpoint-path">/cooperative-agents/{id}/status</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method delete">DELETE</span><span class="endpoint-path">/cooperative-agents/{id}</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/cooperative-agents/me/change-password</span><span class="endpoint-auth">Agent (self)</span>
    </div>

=== "Wholesale buyers"

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/wholesale-buyers/register</span><span class="endpoint-auth">Public</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/wholesale-buyers/</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/wholesale-buyers/{id}</span><span class="endpoint-auth">Admin or Buyer</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method put">PUT</span><span class="endpoint-path">/wholesale-buyers/{id}</span><span class="endpoint-auth">Admin or Buyer</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method delete">DELETE</span><span class="endpoint-path">/wholesale-buyers/{id}</span><span class="endpoint-auth">Admin or Buyer</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/wholesale-buyers/me/change-password</span><span class="endpoint-auth">Buyer (self)</span>
    </div>

=== "Produce listings"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/produce-listings/</span><span class="endpoint-auth">—</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/produce-listings/{id}</span><span class="endpoint-auth">—</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/produce-listings/</span><span class="endpoint-auth">—</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method put">PUT</span><span class="endpoint-path">/produce-listings/{id}</span><span class="endpoint-auth">—</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/produce-listings/{id}/archive</span><span class="endpoint-auth">—</span>
    </div>

=== "Produce pools"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/produce-pools/</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/produce-pools/{id}</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/produce-pools/trigger-allocation/{campaign_id}</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/produce-pools/verify-listing/{produce_id}</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/produce-pools/cancel-campaign/{campaign_id}</span><span class="endpoint-auth">No auth</span>
    </div>

=== "SMS notifications"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/sms-notifications/</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/sms-notifications/{id}</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/sms-notifications/</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/sms-notifications/{id}/respond</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/sms-notifications/{id}/read</span><span class="endpoint-auth">No auth</span>
    </div>

=== "Merchant account"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/merchant-account/</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/merchant-account/initialize</span><span class="endpoint-auth">No auth</span>
    </div>

=== "Payments"

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/payments/initiate</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/payments/verify/{internal_ref}</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/payments/history</span><span class="endpoint-auth">Admin</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/payments/payout/{campaign_id}/{cooperative_id}</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/payments/refund</span><span class="endpoint-auth">No auth</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/payments/refund/{campaign_id}</span><span class="endpoint-auth">No auth</span>
    </div>

=== "Market prices"

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/market-prices</span><span class="endpoint-auth">—</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/market-prices/current</span><span class="endpoint-auth">—</span>
    </div>

    <div class="endpoint" markdown>
    <span class="endpoint-method get">GET</span><span class="endpoint-path">/market-prices/history</span><span class="endpoint-auth">—</span>
    </div>

=== "USSD"

    <div class="endpoint" markdown>
    <span class="endpoint-method post">POST</span><span class="endpoint-path">/ussd/callback</span><span class="endpoint-auth">—</span>
    </div>

## Error handling conventions

| Status | Meaning | Typical cause |
|---|---|---|
| `400` / `422` | Bad Request / Unprocessable Entity | Domain validation errors raised as `ValueError` in the repository/service layer, or a request body that fails Pydantic schema validation. |
| `401` | Unauthorized | Missing, invalid, expired, or blacklisted JWT — no token, a malformed/expired token, or a failed RS256 signature check in `decode_access_token`. |
| `403` | Forbidden | Valid token, but the user's role doesn't match the endpoint's requirement — raised by `require_admin` / `require_agent` / `require_farmer` / `require_buyer` guards. |
| `404` | Not Found | Resource ID doesn't exist, or is already archived/inactive. |
| `429` | Too Many Requests | Rate limit exceeded — 100/min globally, 5/min on login endpoints, 3/min on password-reset requests (`slowapi`). |
| `500` | Internal Server Error | Unhandled `SQLAlchemyError` from the repository layer, or another uncaught exception. Repositories log and roll back before re-raising — check `heroku logs --tail`. |