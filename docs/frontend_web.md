

# Phoenix_Dashboard — Frontend Getting Started Guide


## 1. Overview

`Phoenix_Dashboard` is the **Desktop PWA used by Administrators** to manage every ongoing process on the Pamodzi platform — cooperatives, demand campaigns, finances, market prices, and user onboarding. It talks exclusively to the `Phoenix_Backend` FastAPI service; it never touches the database or third-party services (Flutterwave, LocationIQ, Africa's Talking) directly.

### Design reference #
🔗 [Phoenix Figma file](https://www.figma.com/design/mesXrW3dt9qWCyJqgbcakJ/Phoenix?node-id=1459-4)
Walks through how each user (Farmer, Cooperative Agent, Wholesale Buyer,) moves through the platform, screen by screen.


### Live deployment 
🔗 [phoenixdashboard.vercel.app](https://phoenixdashboard.vercel.app/) 

## 2. Tech Stack

<div class="grid cards" markdown>

-   **Framework**
    ---
    Next.js 16.3.0 (App Router)
-   **UI Library**

    ---
    React 19.2.8 / React DOM 19.2.8

-   **Language**

    ---
    TypeScript configured, strict mode on

-   **Styling**

    ---
    Tailwind CSS v4 (configured) + CSS Modules 

-   **Forms**

    ---
    `react-hook-form` 

-   **Charts**

    ---
    `recharts`

-   **Icons**

    ---
    Custom inline SVG components in `icons.jsx`

-   **Font**

    ---
    Quicksand

-   **Linting**

    ---
    ESLint 9 flat config (`eslint-config-next` + `eslint-config-prettier`) + Prettier

-   **Hosting**

    ---
    Vercel

</div>



## 3. Prerequisites

Install these before cloning:

- **Node.js** — use the version in ` Node LTS 
- **npm** (comes with Node) — the repo uses `package-lock.json`, 
- Access to the `Phoenix_Backend` repo, or at least a running instance of it (local or the shared dev deployment) — the dashboard is unusable without an API to hit
- A GitHub account with access to the `akirachix` org repos
- Figma access for  UI work: `https://www.figma.com/design/mesXrW3dt9qWCyJqgbcakJ/Phoenix`

---

## 4. Clone & Install

```bash
git clone https://github.com/akirachix/Phoenix_Dashboard.git
cd Phoenix_Dashboard
npm install
```

---

## 5. Environment Variables

Create a `.env.local` file in the project root (never commit this):

```bash
NEXT_PUBLIC_API_URL=http://localhost:8000
```

- In local dev, point this at your local `Phoenix_Backend` instance (default `uvicorn` port is 8000).
- In production, this points at the deployed backend on Heroku (`NEXT_PUBLIC_API_URL` is set in Vercel's project settings, not in a committed file — it's what backs `phoenixdashboard.vercel.app`).
- Because it's prefixed `NEXT_PUBLIC_`, this value is exposed to the browser bundle — never put secrets in a `NEXT_PUBLIC_` variable.

---

## 6. Running It Locally

```bash
npm run dev
```

This starts the Next.js dev server (default `http://localhost:3000`) with hot reload. You'll need `Phoenix_Backend` running concurrently (see its own Getting Started section) for any page beyond the static shell to actually load data — login, dashboard cards, market prices, etc. all fetch from the API.

Quick sanity check after starting both:
1. Open `http://localhost:3000/login`
2. Log in with an admin account (seeded via `seed_cooperative.py` on the backend, or ask a teammate for dev credentials)
3. Confirm the dashboard loads cooperative/campaign data without console errors

Other scripts you'll use:

```bash
npm run build      # production build — run this before opening a PR that touches many files
npm run lint        # ESLint, per the flat config in eslint.config.mjs
npm run start       # serve the production build locally
```

---

## 7. Project Structure

```
app/
├── api/
├── components/
│   └── produce_pool/
├── cooperative-info/
├── dashboard/
│   ├── onboard-user/
│   ├── layout.jsx
│   └── page.jsx
├── Finance/
├── FinanceCards/[id]/
├── Home/
├── login/
├── market-price/
└── profile/

components/
├── dashboard/
│   ├── Sidebar.jsx / .module.css
│   ├── TopBar.jsx / .module.css
│   └── icons.jsx
├── App.jsx
├── CooperativeInformationDashboard.jsx
└── MarketPricesDashboard.jsx

lib/
├── admin.js
└── api.js

libs/
└── api.js
```

---


<!-- ### 8. Screens in detail

A closer look at the three screens most people touch first:

**Home**

Live counts (cooperatives, buyers, farmers, campaigns, listings), escrow/phase status chips, an active-campaigns table with per-row refund/extend actions, an activity feed, and a 30-day aggregation trend chart.

**Onboard User**

Single form, three roles via radio select — Cooperative, Cooperative Agent, Admin — routing to `POST /cooperatives/`, `POST /cooperative-agents/onboard`, or `POST /auth/admin/onboard` depending on which is picked.

**Market Price**

Per-crop price cards (ZMW/50kg) above a multi-line trend chart with a color-coded legend — sourced from `GET /market-prices/current` and `/market-prices/history`.

--- -->

## 9. Code Snippets — Common Patterns

### 9.1 The dashboard shell (`layout.jsx`)

Every route nested under `app/dashboard/` gets wrapped by this layout.

```jsx
// app/dashboard/layout.jsx
import Sidebar from "@/components/dashboard/Sidebar";
import TopBar from "@/components/dashboard/TopBar";
import styles from "./layout.module.css";

export default function DashboardLayout({ children }) {
  return (
    <div className={styles.shell}>
      <Sidebar />
      <div className={styles.main}>
        <TopBar />
        <main className={styles.content}>{children}</main>
      </div>
    </div>
  );
}
```

If you build a new page *outside* `app/dashboard/` (like `Home` or `login`) it will **not** get this shell automatically — that's why `/login` and `/Home` have their own standalone styling. Decide early whether a new screen belongs inside the admin shell or not.

### 9.2 A new dashboard page

```jsx
// app/dashboard/example-feature/page.jsx
import styles from "./page.module.css";

export default function ExampleFeaturePage() {
  return (
    <div className={styles.container}>
      <h1 className={styles.title}>Example Feature</h1>
      {/* page content */}
    </div>
  );
}
```

### 9.3 Fetching from the backend

```js
const API_URL = process.env.NEXT_PUBLIC_API_URL;

export async function apiGet(path, token) {
  const res = await fetch(`${API_URL}${path}`, {
    headers: token ? { Authorization: `Bearer ${token}` } : {},
  });
  if (!res.ok) {
    throw new Error(`API error ${res.status}: ${await res.text()}`);
  }
  return res.json();
}

export async function apiPost(path, body, token) {
  const res = await fetch(`${API_URL}${path}`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      ...(token ? { Authorization: `Bearer ${token}` } : {}),
    },
    body: JSON.stringify(body),
  });
  if (!res.ok) {
    throw new Error(`API error ${res.status}: ${await res.text()}`);
  }
  return res.json();
}
```

```jsx
import { apiGet } from "@/lib/api";
import { useEffect, useState } from "react";

export default function MarketPriceWidget() {
  const [prices, setPrices] = useState(null);

  useEffect(() => {
    apiGet("/market-prices/current").then(setPrices).catch(console.error);
  }, []);

  if (!prices) return <p>Loading...</p>;
  return <pre>{JSON.stringify(prices, null, 2)}</pre>;
}
```

### 9.4 A dynamic route (`FinanceCards/[id]`)

```jsx
import { apiGet } from "@/lib/api";
import styles from "./financecard.css";

export default async function FinanceCardPage({ params }) {
  const { id } = await params;
  const record = await apiGet(`/finance/${id}`); // confirm actual endpoint shape with backend team

  return (
    <div className={styles.card}>
      <h1>{record.type}</h1>
      <p>Amount: {record.amount} {record.currency}</p>
      <p>Status: {record.status}</p>
    </div>
  );
}
```

Note `params` is a `Promise` you have to `await` in current Next.js — a common mistake porting patterns from older Next.js tutorials is treating `params` as a plain object.

### 9.5 A chart with recharts (matches `MarketPricesDashboard.jsx`)

```jsx
import { LineChart, Line, XAxis, YAxis, Tooltip, ResponsiveContainer } from "recharts";

export default function PriceTrendChart({ data }) {
  return (
    <ResponsiveContainer width="100%" height={300}>
      <LineChart data={data}>
        <XAxis dataKey="date" />
        <YAxis />
        <Tooltip />
        <Line type="monotone" dataKey="price" stroke="#236b21" strokeWidth={2} />
      </LineChart>
    </ResponsiveContainer>
  );
}
```

### 9.6 Using an existing icon

```jsx
import { LeafIcon } from "@/components/dashboard/icons";

<button className={styles.primaryButton}>
  <LeafIcon /> Approve Produce
</button>
```

Add new icons to `icons.jsx` rather than pulling in an icon library — that's the established pattern.

---

## 10. Deployment (Vercel)

The frontend deploys to **Vercel**, connected to the `Phoenix_Dashboard` repo, and serves from `phoenixdashboard.vercel.app`.

- Required env var in Vercel project settings: `NEXT_PUBLIC_API_URL` → pointing at the production `Phoenix_Backend` URL on Heroku.
- Pushes to `main`  trigger an automatic Vercel deploy — there's no manual deploy step for the frontend.
- Preview deployments: opening a PR against `main` should give you a Vercel preview URL — use this to sanity-check UI changes before merge. 

**Pre-merge checklist** (mirrors the platform-wide release checklist):

- [ ] `npm run build` succeeds locally with no errors
- [ ] `npm run lint` is clean
- [ ] Confirm `NEXT_PUBLIC_API_URL` in your `.env.local` points at the backend you actually tested against (don't accidentally test against prod)
- [ ] PR description follows `.github/pull_request_template.md`
- [ ] After merge, verify `phoenixdashboard.vercel.app` loads inventory/dashboard data without console errors against the current production backend

---

## 11. Troubleshooting

| Symptom | Likely cause | Check |
|---|---|---|
| Dashboard loads but every data-driven section is blank/errors | `NEXT_PUBLIC_API_URL` isn't pointing at a reachable backend | Confirm `.env.local` and that `Phoenix_Backend` is actually running on that port |
| Login succeeds but immediately redirects back to `/login` | Token isn't being stored/attached, or it's a 401 from an expired/blacklisted JWT | Check how the token is persisted after `/auth/login/admin/form` and that it's sent as a Bearer token on subsequent calls |
| CORS error in the browser console | Your frontend origin isn't in the backend's `CORS_ORIGINS` | This is a **backend** config fix (Heroku config var), not something you can patch from the frontend — flag it to the backend team with your exact origin (e.g. a Vercel preview URL) |
| A page you expect to have the sidebar/topbar doesn't | The route isn't nested under `app/dashboard/` | Move it under `app/dashboard/` if it's meant to be an authenticated admin screen, or confirm with the team if it's intentionally standalone like `/login`/`/Home` |
| Styling looks broken only in production, not `npm run dev` | Mixing global CSS (`home.css`-style) with CSS Modules can behave differently once bundled/minified | Prefer CSS Modules for anything new; treat the plain-CSS files as legacy, not a pattern to extend |
| Import error on `api.js` | You imported from `libs/` instead of `lib/` (or vice versa) | Check which one the rest of the file's neighbors import from before adding a new call — don't assume |




# Frontend Web

The admin-facing web dashboard ("Phoenix Dashboard") is a Next.js application used by Administrators to manage all ongoing activity on the platform. For the page-by-page breakdown of the dashboard, see [Web Dashboard](web-dashboard.md).

## Tech Stack

| Layer | Choice | Notes |
|---|---|---|
| Framework | Next.js 16.3.0 | App Router — confirmed by `app/` paths and real page files |
| UI Library | React 19.2.8 / React DOM 19.2.8 | |
| Language | TypeScript (strict mode) | Configured, but the large majority of actual files are `.jsx`, not `.tsx` — see inconsistency note below |
| Styling | Tailwind CSS v4 | Configured, but in practice most components use CSS Modules instead |
| Forms | react-hook-form | Declared as a dependency but not yet seen in use in any shared component — the Profile form uses plain `useState` instead |
| Charts | recharts | Confirmed in active use in `MarketPricesDashboard.jsx` (line/trend charts per crop) |
| Icons | Custom inline SVG components | `icons.jsx` — `UserIcon`, `MailIcon`, `BadgeIcon`, `LockIcon`, `LeafIcon`, `SearchIcon`, `PhoneIcon` |
| Fonts | Quicksand | |
| Linting/Formatting | ESLint 9 (flat config) + Prettier | Extends `eslint-config-next` + `eslint-config-prettier` |



## Environment

| Variable | Value |
|---|---|
| `NEXT_PUBLIC_API_URL` | Points at the deployed backend, e.g. `https://phoenixdashboard.vercel.app/` |

See [Deployment](deployment.md#frontend-vercel) for the full Vercel deployment configuration.
