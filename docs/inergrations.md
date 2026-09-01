# Integrations

| Service | Purpose | Configuration |
|---|---|---|
| **Africa's Talking** | USSD gateway; SMS delivery for `sms_notification` records (listing confirmations, drop-off reminders, etc.) | `AT_USERNAME`, `AT_API_KEY` |
| **Flutterwave** | Payment gateway for buyer transactions; merchant ledger updates | `FLUTTERWAVE_SECRET_KEY`, `FLUTTERWAVE_PUBLIC_KEY`, webhook signature secret |
| **LocationIQ** | Geocoding — resolves cooperative hub coordinates | `LOCATIONIQ_API_KEY` |
| **Gmail SMTP** (via `aiosmtplib`) | Sends real transactional email: MFA one-time passcodes and password-reset links | `MAIL_USERNAME`, `MAIL_PASSWORD`, `MAIL_FROM`, `MAIL_PORT` (587), `MAIL_SERVER` (`smtp.gmail.com`) |


See [Getting Started](getting-started.md#configure-environment-variables) for where these variables are set locally, and [Deployment](deployment.md) for production config.
