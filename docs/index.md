# Welcome to Pamodzi

<div align="center" markdown>
![Pamodzi](assets/images/logo-assets/pamodzi-mark.png){ .pamodzi-logo }


## Connecting Farmers, Cooperatives, and Buyers

An agricultural ecosystem platform built for Zambia's produce markets
</div>

---

Pamodzi is an agricultural ecosystem platform that connects farmers, cooperatives, and wholesale buyers in Zambia. It solves a coordination problem: farmers producing crops in scattered locations have no efficient way to aggregate their produce into volumes large enough to interest wholesale buyers, and buyers have no reliable channel to source produce at scale directly from rural producers.

**Prepared by:** Glory Mung'athia · Kudakwashe Paradza · Prudent Muthoni · Christabel Aluoch · Stacy Cherenge

---

## Platform Highlights

<div class="grid cards" markdown>

-   :material-cellphone-basic:{ .lg .middle } **USSD-First for Farmers**

    ---

    No smartphone or internet required — farmers list produce over a basic feature phone via Africa's Talking.

-   :material-handshake:{ .lg .middle } **Demand Campaigns**

    ---

    Buyers fund a specific crop/grade/quantity/province request up front via Flutterwave, before a single listing is aggregated.

-   :material-map-marker-check:{ .lg .middle } **Cooperative Verification**

    ---

    Field agents confirm produce quality and hub location (LocationIQ) before it enters the pool.

-   :material-shield-lock:{ .lg .middle } **RBAC + Argon2 + JWT**

    ---

    Role-based access for Farmer, Agent, Buyer, and Admin, with mandatory MFA for admins. See [Security](security.md).

</div>

---

Pamodzi solves this by letting:

- **Wholesale buyers** launch a *demand campaign* — a request for a specific crop, grade, quantity, and province — and fund it up front.
- **Farmers** list their produce (via USSD, so no smartphone or internet is required) against that demand.
- **Cooperative agents** verify listings, confirm hub location, and manage produce pooling on the ground.
- **The platform** aggregates individual farmer listings into a produce pool that fulfills the buyer's campaign, then handles payment disbursement.

## Who Uses It

| Role | How They Interact | Interface |
|---|---|---|
| Farmer | Lists produce, receives confirmations | USSD (feature phone) |
| Cooperative Agent | Inputs farmer produce details, verifies hub location, manages disbursal | Mobile app |
| Wholesale Buyer | Creates demand campaigns, funds them, tracks fulfillment | Mobile app |
| Administrator | Platform oversight, MFA-protected | Desktop PWA |

## Key Features

- **USSD produce listing** — farmers with basic phones can list crops without internet access, via Africa's Talking USSD gateway.
- **Demand campaigns** — buyers define what they want (crop, grade, quantity, province, moisture content) and fund it via Flutterwave.
- **Produce pooling** — individual farmer listings are aggregated against a campaign until the target quantity is met.
- **Cooperative verification** — agents confirm produce quality/status (approved/rejected) and cooperative hub location via LocationIQ.
- **Automated payments** — funds move from buyer → merchant account → farmer/cooperative payout, tracked as ledgered transactions.
- **SMS notifications** — status updates (listing confirmation, drop-off reminders, campaign changes) sent to farmers and buyers.
- **Market price reference** — historical and current commodity prices by province.

---

## System Architecture

### Narrative Walkthrough

**Farmer → USSD → Africa's Talking → Main API**
The farmer lists produce via the USSD menu. The request is sent to Africa's Talking, which relays it to the backend. A success/failure status code returns through the same path, and the farmer sees a plain-language confirmation on their phone.

**Cooperative Agent → Mobile PWA → Main API**
Agents perform three flows from the same app:

1. Disbursing produce
2. Inputting a farmer's produce details/hub location on the farmer's behalf
3. Verifying produce and cooperative hub location

Verification calls out to LocationIQ to confirm/store coordinates before the backend returns an approved/rejected status.

**Wholesale Buyer → Desktop PWA → Main API → Flutterwave**
The buyer creates a demand campaign and provides mobile money details. The backend initiates a charge via Flutterwave, which updates the ledger (merchant account balance) and returns a transfer success notification. The buyer sees updated campaign status.

**Main API → PostgreSQL**
All flows converge on a central Main API, which is the only component that reads/writes the database directly. This keeps data access centralized and auditable.

### Components

| Component | Role |
|---|---|
| USSD Interface | Entry point for farmers without smartphones; feature-phone menu system |
| Mobile App | Used by cooperative agents for field operations (listing, disbursal, verification) |
| Desktop PWA | Used by Admin to manage all ongoings on the platform |
| Main API (FastAPI) | Central backend; all business logic and database access flows through here |
| PostgreSQL | System of record for all persistent data |
| Redis | Caching / session management |
| Africa's Talking API | USSD gateway + SMS/OTP delivery |
| LocationIQ API | Geocoding — resolves and stores cooperative hub coordinates |
| Flutterwave API | Payment processing — charges buyers, updates merchant ledger |

### Data Flow Summary

Every external actor (farmer, agent, buyer) talks to the Main API through a client interface (USSD / Mobile app / Desktop PWA) — **never** directly to the database or to third-party services. The Main API is the single integration point for Africa's Talking, LocationIQ, and Flutterwave, and the single writer to PostgreSQL. This centralization is what the [RBAC and audit model](security.md) relies on.

See [Backend](backend.md), [Database](database.md), [Frontend Web](frontend_web.md), and [Mobile App](mobile-app.md) for component-level detail.


## Glossary

| Term | Definition |
|---|---|
| Demand Campaign | A wholesale buyer's funded request for a specific crop, grade, quantity, and province, within a start/end date window |
| Produce Pool | The aggregated set of farmer produce listings allocated against a single demand campaign |
| Produce Listing | A single farmer's submission of a quantity of a crop, pending or already aggregated |
| Cooperative | A regional hub that farmers belong to; represented by GPS coordinates and a province |
| Cooperative Agent | Staff member who manages field operations for a cooperative (verification, disbursal, listing on behalf of farmers) |
| USSD | Unstructured Supplementary Service Data — a protocol that lets feature phones interact with the platform via menu prompts, no internet required |
| PACRA | Patents and Companies Registration Agency (Zambia) — buyers provide a PACRA number as a business registration identifier |
| NRC | National Registration Card (Zambia) — used as a unique identity field on `user` |
| RBAC | Role-Based Access Control |
| JWT | JSON Web Token — see [Security](security.md#authentication-authorization) for the signing algorithm used |
| Argon2 | Password hashing algorithm used for all stored credentials |
| ZMW | Zambian Kwacha, the default transaction currency |
| Merchant Account / Wallet | Platform-held Flutterwave account that funds pass through before payout |
