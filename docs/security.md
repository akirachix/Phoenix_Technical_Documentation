# Security

## Authentication & Authorization

- **RBAC:** Role-Based Access Control for Farmer, Cooperative Agent, Wholesale Buyer, and Administrator — enforced via the `role` enum on `user` plus role-specific tables.
- **Password security:** Argon2 hashing for all stored credentials (confirmed in code via `core.security.get_password_hash`).
- **API security:** JWTs signed with RS256.
- **MFA:** Mandatory for Administrator accounts via SMS OTP (Africa's Talking).
- **Farmer authentication:** PIN-based (not password), reflecting the USSD/feature-phone access pattern.


## Data Protection / Classification

| Sensitivity | Examples |
|---|---|
| Highly Sensitive | Passwords, API keys, signing secrets |
| Sensitive | Farmer/buyer personal data, order records, location data |

## Token Lifecycle

- Revoked JWTs are recorded in `token_blacklist` (token string as PK, blacklisted timestamp) — this is how logout/invalidation is enforced despite JWTs being stateless by default.
- Access tokens expire after **120 minutes (2 hours)**; no refresh token mechanism confirmed.
- Hard-delete and soft-delete operations are handled by separate endpoints. Since many database records are tied to multiple downstream processes, permitting full deletion everywhere would make tracking difficult — see the [soft/hard delete conventions](backend.md#architecture-pattern) in the backend docs.

## CORS

The backend uses CORS (Cross-Origin Resource Sharing) to control which frontend applications are allowed to communicate with the API. Allowed frontend origins are configured through the `CORS_ORIGINS` environment variable. The application uses an **explicit allow-list** rather than a wildcard — important because the API is configured to allow credentials where required.

```bash
CORS_ORIGINS=https://example-frontend.com,https://www.example-frontend.com
```

The actual production frontend URL should be used in the deployment configuration.

## Environment Variables & Secrets

Sensitive configuration values are stored as environment variables or platform configuration variables instead of being committed to source code. This includes:

- `JWT_SECRET_KEY`
- Database credentials
- Other deployment-specific configuration values

The `.env` file and other files containing secrets should never be committed to the repository.
