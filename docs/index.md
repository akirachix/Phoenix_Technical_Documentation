# Welcome to Pamodzi   

<div align="center" markdown>



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

    Role-based access for Farmer, Agent, Buyer, and Admin, with mandatory MFA for admins. 

</div>

---

## Project Documentation

### Research Report
Covers the user research behind Pamodzi that is the problems farmers, cooperative agents, and wholesale buyers face today, and the findings that shaped the platform's direction.

🔗 [Research Report](https://docs.google.com/document/d/1g90fH4IchvCGz40sNaU5X0wDhD51q-UhzDgEDxRMFOE/edit?usp=sharing)

### PRD
The Product Requirements Document helps scope, users, features, and what's in vs. out of the MVP for Pamodzi.

🔗 [PRD](https://docs.google.com/document/d/1qOgH9d_-rnY6RiLk1If8aNGSY0jOBwmVg520HaBXLxI/edit?usp=sharing)

### User Flow
Walks through how each user (Farmer, Cooperative Agent, Wholesale Buyer,) moves through the platform, screen by screen.

🔗 [User Flow](https://www.figma.com/design/dgHVs6tTxpni0NgIHpLSIE/User-flows?node-id=0-1&t=1JtVKMI9rKiWwUhP-1)

In general,Pamodzi solves this by letting:
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

# Pamodzi — Brand Guidelines

## Overview

Pamodzi is an agricultural marketplace and cooperative-management platform connecting farmers, cooperatives, and buyers around produce trading, market pricing, and finance. The name "Pamodzi" (a word meaning "together" in several Southern/Central African Bantu languages, notably Bemba/Nyanja) reflects the platform's core purpose: bringing farmers, cooperatives, and agents together in one shared marketplace.

The brand identity is warm, grounded, and agricultural — built around a deep green tied to growth and land, paired with a bright lime/leaf accent that signals freshness and vitality.

---

## Logo

![Pamodzi](assets/images/logo-assets/pamodzi-mark.png){ .pamodzi-logo }

The Pamodzi mark is a stylized **leaf icon** paired with the wordmark "Pamodzi" in bold, rounded type. The leaf is rendered in a bright lime-green line-art style against the deep green brand background, symbolizing growth, agriculture, and organic produce.

- **Lockup:** Leaf icon + "Pamodzi" wordmark, left-aligned
- **Wordmark typeface:** Quicksand, Bold
- **Primary display color:** White wordmark on deep green background (`#236B21`)
- **Icon style:** Single-line, organic leaf silhouette in lime accent green

---

## Color Palette

| Role | Color | Hex |
|---|---|---|
| Primary / Brand Green | Deep forest green — sidebar, primary buttons, headings, active states | `#236B21` |
| Accent / Lime | Leaf icon, chart highlight, card borders, energetic accents | Lime-yellow-green (`#CDF31A`–style lime) |
| Background (light) | App background / canvas | `#F0F0F0` |
| Surface | Cards, panels | `#FFFFFF` |
| Text (on light) | Body copy, chart values | `#000000` / `#236B21` for labeled text |
| Text (on dark/green) | Sidebar nav labels | `#FFFFFF` |

### Data / chart color coding (produce types)
| Produce | Color |
|---|---|
| Maize | Bright green |
| Sorghum | Pink |
| Groundnuts | Brown / maroon |
| Millet | Olive / yellow-green |

This gives each crop a consistent, distinct identity across all charts and price displays.

---

## Typography

| Use | Typeface | Weight |
|---|---|---|
| Headings, wordmark, nav labels, section titles | **Quicksand** | Bold / SemiBold |


Quicksand's soft, rounded letterforms carry the brand's approachable, community-first tone, while Inter keeps dense data (prices, chart values) clean and legible.

---

## UI Patterns

- **Sidebar navigation:** Full-height deep green panel, white Quicksand SemiBold labels; active item shown as a white pill/rounded rectangle with green text (inverted state)
- **Cards:** White background, rounded corners (~20px radius), thin green or lime border — used for price summaries (e.g. "Maize: 340ZKW/50KG")
- **Charts:** Clean line charts on white cards, muted gridlines, color-coded lines per produce type, with a labeled legend ("KEY:") using colored dot markers
- **Corner radius:** Consistently rounded (16–20px) across cards and containers, reinforcing a soft, friendly feel
- **Spacing:** Generous padding, airy layout — avoids clutter despite data density

---

## Brand Voice

- **Community-oriented:** "Pamodzi" itself centers togetherness and cooperation
- **Grounded & trustworthy:** Deep green and clean data presentation signal reliability for financial/market decisions
- **Approachable:** Rounded typography and soft UI shapes avoid a cold, corporate-fintech feel despite handling pricing and transactions
- **Practical:** The product prioritizes clear, at-a-glance market data (pricing per 50kg, % change trends) over decoration

---

## Summary

Pamodzi's brand identity fuses **agricultural warmth** (green, leaf iconography, "togetherness" naming) with **clear, trustworthy data design** (clean charts, rounded cards, consistent crop color-coding). The result is a platform that feels like it belongs to a farming community, not a generic fintech dashboard.


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

Every external actor (farmer, agent, buyer) talks to the Main API through a client interface (USSD / Mobile app / Desktop PWA) — **never** directly to the database or to third-party services. The Main API is the single integration point for Africa's Talking, LocationIQ, and Flutterwave, and the single writer to PostgreSQL. This centralization is what the [RBAC and audit model] security relies on.

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
| JWT | JSON Web Token md#authentication-authorization) for the signing algorithm used |
| Argon2 | Password hashing algorithm used for all stored credentials |
| ZMW | Zambian Kwacha, the default transaction currency |
| Merchant Account / Wallet | Platform-held Flutterwave account that funds pass through before payout |

