# Phoenix_Technical_Documentation# Pamodzi Technical Documentation

This repository is the source for Pamodzi's technical documentation site, built with [MkDocs](https://www.mkdocs.org/) and published to GitHub Pages. It's the reference for anyone building on, integrating with, or onboarding onto the Pamodzi platform — an agricultural ecosystem connecting farmers, cooperatives, and wholesale buyers in Zambia.

## What's in here

The `docs/` folder holds one page per topic, plus the brand and image assets used across the site. Here's what you'll find when you open the published site:

| Page | What it covers |
|---|---|
| **Home** (`index.md`) | Platform overview — what Pamodzi does, who uses it, and how the pieces fit together |
| **Getting Started** (`getting-started.md`) | Prerequisites, setup, and how to get the project running locally |
| **Backend** (`backend.md`) | The FastAPI service — project structure, architecture pattern, and code organization |
| **Database** (`database.md`) | Schema reference — tables, columns, constraints, and relationships |
| **Frontend Web** (`frontend_web.md`) | The Next.js admin dashboard — stack, structure, and conventions |
| **Mobile App** (`mobile-app.md`) | The Flutter app used by cooperative agents and buyers — screens and architecture |
| **Security** (`security.md`) | Authentication, authorization, token handling, and data protection |
| **Integrations** (`integrations.md`) | Third-party services the platform depends on and how they're configured |
| **Deployment** (`deployment.md`) | How the backend and frontend are hosted, deployed, and released |
| **Brand** (`brand.md`) | Visual identity — colors, typography, and logo usage, backed by `assets/brand/pamodzi.css` |

Supporting assets live under `docs/assets/`, including `brand/` (styling) and `images/` (logos, mobile screenshots, and web screenshots used throughout the site).

## Viewing the docs

The published site is available on GitHub Pages. To preview changes locally before pushing:

```bash
pip install -r requirements.txt
mkdocs serve
```

Then open `http://127.0.0.1:8000` in your browser. The site structure and navigation are defined in `mkdocs.yml`.

## Contributing to the docs

- Each page in `docs/` maps to one nav entry in `mkdocs.yml` — add new pages there so they show up in the site navigation.
- Keep page content scoped to its topic; cross-link to other pages rather than duplicating material.
- Place new images under `docs/assets/images/` (organized by `logo-assets/`, `mobile-screens/`, or `web-screens/`) and reference them with relative paths.
- Shared visual styling lives in `docs/assets/brand/pamodzi.css` — update it there rather than overriding styles per page.