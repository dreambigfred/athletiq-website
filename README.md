# athletIQ

**Intelligence. Elevation. Legacy.**

AI-powered athlete development infrastructure platform.

## What This Is

athletIQ is the operating system for athlete development. It deploys AI agents on behalf of every athlete simultaneously — covering NIL compliance, recruiting execution, financial literacy, career transition, and life after sport.

## Live Site

[athletiq.ai](https://athletiq.ai)

## Tech Stack

- **Frontend:** Single-file HTML/CSS/JS — zero dependencies, instant load
- **Hosting:** Vercel (auto-deploy on every GitHub push)
- **Forms:** Tally (waitlist capture)
- **Analytics:** Google Analytics 4
- **Email:** Zoho Mail (chris@athletiq.ai)

## Deployment

Every push to `main` branch automatically deploys to production via Vercel.

```bash
# Make a change
git add .
git commit -m "update copy"
git push origin main
# Live in ~30 seconds
```

## Local Development

```bash
# Just open the file
open index.html
```

No build step. No npm install. No dependencies.

## File Structure

```
athletiq-website/
├── index.html        ← The entire website
├── vercel.json       ← Vercel deployment config
└── README.md         ← This file
```

---

© 2025 athletIQ, Inc.
