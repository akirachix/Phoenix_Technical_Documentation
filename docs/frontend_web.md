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
