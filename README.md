# NutriScan

AI-powered food nutrition analyser. Snap a photo of a packaged product or a
homemade/restaurant meal, and get back calories, macros, vitamins/minerals,
and a health score — grounded in real nutrition databases wherever possible,
with AI vision used only to identify the food and estimate portions.

## How it works

1. **Triage** — a vision-language model looks at the photo and decides:
   is this a **packaged/labelled product**, or a **homemade/plated meal**?
2. **Packaged food** — the app looks up the product in
   [Open Food Facts](https://world.openfoodfacts.org/) by barcode or name.
   If found, nutrition numbers come directly from that database, not from
   the AI guessing. Only if no match is found does it fall back to asking
   the model to read the printed label.
3. **Homemade/restaurant food** — the model decomposes the dish into its
   main visible components and estimates portion size using visual
   references (plate size, hand, cutlery, etc). Each component is then
   priced against, in order: a curated Indian food database, a curated
   world-cuisine database, USDA FoodData Central, Open Food Facts generic
   entries, and only as a last resort, the model's own estimate.

## Tech stack

- Vanilla HTML/CSS/JS frontend (`index.html`) — no build step
- Node.js serverless functions (`api/`), deployed on [Vercel](https://vercel.com)
- [NVIDIA NIM](https://build.nvidia.com) for vision-language model inference
- [Open Food Facts](https://world.openfoodfacts.org/) and
  [USDA FoodData Central](https://fdc.nal.usda.gov/) for nutrition data

## Getting started

### Prerequisites

- Node.js 24.x
- A [Vercel](https://vercel.com) account (or any host that runs Node
  serverless functions the same way)
- An NVIDIA NIM API key from [build.nvidia.com](https://build.nvidia.com)
- (Optional) a USDA FoodData Central API key from
  [api.data.gov/signup](https://api.data.gov/signup) — without one, USDA
  lookups fall back to the public `DEMO_KEY`, which is capped at 30
  requests/hour shared across all unauthenticated users worldwide

### Local setup

```bash
git clone <your-fork-url>
cd nutriscan
cp .env.example .env
```

Edit `.env` and fill in your keys:

```
NVIDIA_API_KEY=your-key-here
USDA_FDC_API_KEY=your-key-here   # optional
```

Run locally with the Vercel CLI (recommended, since it emulates the
serverless function environment):

```bash
npm i -g vercel
vercel dev
```

### Deploying

Push to a GitHub repo connected to a Vercel project, or deploy directly:

```bash
vercel --prod
```

Then set the same environment variables in **Vercel → Project → Settings →
Environment Variables** (not just locally in `.env`) — this is what the
production deployment actually reads.

## Security

This app is hardened against the most common risks for a public,
unauthenticated AI endpoint. Full details in [`SECURITY.md`](./SECURITY.md),
summarized here:

- **Rate limiting** on the API endpoint (per-IP burst + hourly caps),
  returning proper `429` responses before any billed AI call is made.
- **Strict input validation** — request shape, size limits, and real
  image-format verification (magic-byte sniffing, not just trusting a
  claimed content type), with unexpected fields rejected outright.
- **No hard-coded secrets** — API keys are read only from environment
  variables, never sent to or exposed in the client bundle. `.env` is
  git-ignored; `.env.example` documents the required variables without
  real values.

### Reporting a vulnerability

If you find a security issue, please **do not open a public GitHub issue**.
Instead, open a private security advisory on this repo (GitHub → Security
tab → "Report a vulnerability"), or contact the maintainer directly.

### If you fork this repo

- Never commit a real `.env` file or paste real API keys into code,
  issues, or commit messages.
- If a key is ever accidentally committed, treat it as compromised:
  rotate it at the provider immediately (see `SECURITY.md` for exact
  steps), don't just delete it from a later commit — it's still in git
  history.

## Project structure

```
.
├── index.html              # Frontend (no build step)
├── api/
│   ├── analyse.js          # Main API endpoint — the only public route
│   ├── _lib/
│   │   ├── rate-limit.js   # IP-based rate limiting
│   │   └── validate.js     # Input validation & sanitization
│   ├── indian-food-db.js   # Curated Indian food nutrition data
│   ├── world-food-db.js    # Curated world-cuisine nutrition data
│   └── usda-food-db.js     # USDA FoodData Central lookup
├── vercel.json              # Vercel routing/function config
├── .env.example              # Documents required env vars (no real values)
└── SECURITY.md              # Detailed security notes & key rotation steps
```

## License

Add a license of your choice (e.g. MIT) before making this repo public,
if you haven't already.
