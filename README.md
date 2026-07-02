# 🥗 NutriScan — Know What You Eat

An AI-powered food nutrition analyzer. Upload or snap a photo of a meal or
packaged product, and NutriScan identifies what it is and returns a
nutrition breakdown — grounded in real food databases wherever possible,
not just a vision model's guess.

## How It Works

NutriScan doesn't just ask an AI to guess nutrition facts from a photo. It
uses a two-step, database-grounded pipeline:

1. **Triage** — a vision model first classifies the image: is this a
   packaged/labelled product, or a homemade/plated meal?
2. **Packaged products** — the app tries to identify the product via
   barcode or name, then looks it up in
   [Open Food Facts](https://world.openfoodfacts.org/) to pull real
   nutrition data. Only if no database match is found does it fall back to
   having the vision model read the nutrition label directly from the image.
3. **Homemade / restaurant meals** — no barcode exists for a home-cooked
   dish, so the model decomposes the plate into individual ingredients and
   estimated portion sizes, then looks up nutrition per-ingredient against
   food databases, falling back to model estimation only for ingredients
   it can't find.

This grounded approach is designed to be meaningfully more accurate than
asking a vision model to output one guessed nutrition number for an entire
dish.

## Features

- 📸 **Image upload/scan** — analyze meals or packaged foods from a photo
  (JPG, PNG, WEBP)
- 🗄️ **Multi-source food data** — grounded against Open Food Facts, plus
  bundled Indian, world, and USDA food reference databases for better
  regional coverage
- 🏷️ **Barcode-aware lookups** — recognizes packaged products by barcode
  when visible, including India-specific brand/GS1 heuristics for better
  regional match accuracy
- 🍛 **Ingredient decomposition** — breaks composed/homemade dishes into
  individual components with per-ingredient nutrition estimates
- 🤖 **Multi-model fallback plan** — tries multiple vision models in
  sequence for resilience if one is unavailable or fails

## Tech Stack

- **Frontend** — static HTML/CSS/JS (`index.html`), no framework
- **Backend** — Vercel serverless functions (`/api`)
- **Vision model** — NVIDIA NIM API (vision-language models) for image
  understanding
- **Nutrition data** — [Open Food Facts](https://world.openfoodfacts.org/)
  API, plus bundled Indian, world, and USDA food databases
- **Deployment** — [Vercel](https://vercel.com)

## Getting Started

1. Clone the repo
2. Install dependencies (if any are added later) with `npm install`
3. Set the required environment variable in your Vercel project (or a
   local `.env` for `vercel dev`):
   ```
   NVIDIA_API_KEY=your_nvidia_nim_api_key
   ```
4. Deploy with [Vercel](https://vercel.com), or run locally with the
   Vercel CLI:
   ```
   vercel dev
   ```

## Project Structure

```
nutriscan-improved/
├── index.html              # Frontend UI
├── api/
│   ├── analyse.js          # Main analysis pipeline (triage → lookup/estimate)
│   ├── indian-food-db.js   # Bundled Indian food reference data
│   ├── world-food-db.js    # Bundled world food reference data
│   └── usda-food-db.js     # Bundled USDA food reference data
├── vercel.json              # Vercel routing & function config
└── package.json
```

## Security Notes

- The NVIDIA API key is read from the `NVIDIA_API_KEY` environment
  variable server-side and is never exposed to the client — do not hardcode
  it into any frontend file
- No user data or images are stored by this project's own code; review
  your Vercel function logs/retention settings if that matters for your
  deployment

## Status

This is a personal/portfolio project. Nutrition estimates, especially for
homemade or composed meals, are approximations and should not be relied on
for medical or dietary decisions.

## License

MIT License — see [LICENSE](LICENSE) for details.
