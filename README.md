[README.md](https://github.com/user-attachments/files/27923300/README.md)
# athletIQ
### The Career Readiness Agent for College Athletes

> *Pronounced "athletic." Spelled with intention.*
> *Intelligence. Elevation. Legacy.*

**Fewer than 2% of college athletes ever reach a major professional league.**
554,000 are competing right now. Nobody built anything for the other 98% — until now.

athletIQ is an AI-powered career readiness platform built exclusively for college student-athletes. One Career Readiness Agent. Every athlete. Activated from day one of enrollment — building professional identity, generating LinkedIn profiles, surfacing career paths, and preparing athletes for the career they'll spend 40 years in while they're still competing.

---

## The Problem

There is a Tuesday morning coming for every athlete. Six weeks after the last game — no schedule, no team, no coach texting. The structure that defined their entire life is gone.

The institution that scheduled their 5am lifts and sold jerseys with their number had one staff member responsible for the career development of hundreds of athletes. That was never going to be enough.

- **<2%** ever reach a major professional league *(NCAA)*
- **554,298** student-athletes competing in 2024–25 — all-time high *(NCAA, 2025)*
- **26%** experience severe mental health crisis at career end *(IOC, 2023)*
- **Y4** — when most athletes first seriously think about their career. By then it's too late to build anything real.

---

## The Product

### One Career Readiness Agent. Three Integrated Capabilities.

**LinkedIn & Personal Brand**
LinkedIn headline, About section, and bio — generated from the athlete's sport, position, and interests. Ready to publish. Not a template.

**Career Opportunities**
Career paths matched to sport and interests. Résumé bullets built from athletic experience. Certifications and internships surfaced. Recruiter outreach drafted in the athlete's voice.

**Networking Intelligence**
Who to contact, what to say, and how to follow up — all drafted. Network building starts before the athlete thinks they need it.

### The Future Blueprint
Athletes complete a 4-step conversational onboarding:
1. **Identity** — name, school, sport, position, graduation year
2. **Interests** — career interests, industries, entrepreneurship (multi-select)
3. **Goals** — career goals, networking goals, internship goals, biggest concerns
4. **Digital Presence** — LinkedIn, Instagram, TikTok (optional) + employer visibility toggle

The agent activates immediately after onboarding. It sends a proactive first message — personalized to the athlete's specific sport and career interest. It does not wait to be asked.

### The Proactive Layer
Every Sunday, each athlete receives a personalized weekly briefing:
- One career action to take this week
- One observation about their progress
- One resource worth exploring

Subject: **"Your athletIQ Weekly · [First Name]"**

---

## NIL as Career Capital

Every NIL deal is a portfolio entry. Every campaign is a marketing project. Every partnership is business development experience.

| What You Have | What The Agent Calls It |
|--------------|------------------------|
| Signed a deal with Gatorade | Brand Ambassador · Gatorade · Sports Nutrition |
| Posted three sponsored videos | Produced 3 sponsored campaigns · 8K+ reach |
| Showed up to a brand event | Managed live brand activation · Fortune 500 partner |
| Local restaurant deal | Brand Partnership · F&B · Regional market |
| Negotiated your own deal | Self-directed business development · Contract negotiation |

---

## Business Model

Three tiers. Each makes the others more valuable.

| Tier | Who | Price | What They Get |
|------|-----|-------|--------------|
| **Free** | Athletes | $0 — always | Career Readiness Agent, Future Blueprint, LinkedIn generation, weekly briefing |
| **Employer Partner** | Companies | $5K–15K/year | Early access to vetted, career-ready athlete pipeline — 2–4 years before graduation |
| **Institutional** | Athletic Departments | $6K–25K/year | White-labeled platform + Athlete Development Dashboard + employer network |
| **Enterprise** | Power-4 Programs | Custom | Conference reporting, API integrations, dedicated support |

### The White-Label Advantage
The institutional tier carries the university's brand inside the athlete experience. The agent says **"Alabama State Career Agent — powered by athletIQ"** — not athletIQ. The department gets the credit. athletIQ builds the infrastructure.

### Pricing by Program Size

| Program | Annual |
|---------|--------|
| HBCU D2 | $3,000–6,000 |
| HBCU D1 / Mid-Major | $8,000–15,000 |
| Mid-Major (larger budget) | $15,000–25,000 |
| Power-4 | $40,000–80,000 |
| Employer Standard | $5,000–8,000 |
| Employer Premium | $10,000–15,000 |

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React + Tailwind + shadcn/ui (via Lovable) |
| Backend | Supabase — PostgreSQL + Auth + Edge Functions |
| AI | Anthropic Claude API — `claude-sonnet-4-20250514` |
| Email | Resend |
| Deployment | Vercel (auto-deploys from GitHub) |
| Domain | athq.tech |

---

## Database

Three tables.

### `athlete_profiles`
Central record. Every agent call reads this as system context.

| Field | Type | Notes |
|-------|------|-------|
| id | uuid | Primary key |
| user_id | uuid | FK → auth.users |
| name, school, sport, position | string | Identity |
| grad_year | integer | e.g. 2027 |
| career_interests | text[] | Multi-select from onboarding |
| industry_interests | text[] | Multi-select |
| entrepreneurship_interest | boolean | |
| goals | text | Career, networking, internship goals |
| concerns | text[] | Biggest fears — personalizes first agent message |
| linkedin_url, instagram_url, tiktok_url | string nullable | Optional |
| onboarding_complete | boolean | Gates /chat access |
| readiness_score | integer 0–100 | Calculated from profile + engagement |
| school_brand_color | string nullable | Hex — white-label theming |
| school_logo_url | string nullable | White-label chat header |
| employer_visible | boolean | Controls employer dashboard access |
| linkedin_headline_generated | boolean | Tracks key document production |

### `conversations`
Full chat history — sent to Claude as context on each message.

| Field | Type |
|-------|------|
| id | uuid |
| athlete_id | uuid → athlete_profiles |
| role | enum: user / assistant |
| content | text |
| created_at | timestamp |

### `nil_disclosures`
Manual log. Athlete submits. Compliance officer reviews.

| Field | Type | Notes |
|-------|------|-------|
| id | uuid | |
| athlete_id | uuid | FK |
| brand_name | text | |
| deal_value | decimal nullable | Optional |
| category | text | Apparel / F&B / Tech / Health / Other |
| disclosure_date | date | |
| status | enum | pending / approved / flagged |
| notes | text nullable | Staff review notes |

---

## User Roles & Access Control

All enforced via Supabase Row-Level Security.

| Role | Access |
|------|--------|
| `athlete` | Read/write own rows only |
| `compliance_officer` | Read athlete_profiles + nil_disclosures where school matches |
| `employer` | Read athlete_profiles where employer_visible = true — name, school, sport, grad_year, career_interests, readiness_score, linkedin_url ONLY. Never sees goals, conversations, nil_disclosures, or contact info. |
| `admin` | Full access |

---

## Product Screens

| Route | Who | What |
|-------|-----|------|
| `/login` `/signup` | All | Auth with role selection |
| `/onboarding` | Athlete | Future Blueprint — 4 steps + concerns |
| `/chat` | Athlete | Career Readiness Agent — proactive, white-label aware |
| `/dashboard/athlete` | Athlete | Profile, readiness score, NIL log |
| `/dashboard/compliance` | Compliance Officer | Roster, readiness scores, NIL disclosures |
| `/dashboard/employer` | Employer | Athlete search, profile cards, Express Interest |

---

## Build Sequence

### Week 0 — Validate Before Building
Run `athletiq-pilot-agent.html` with 10 real athletes. 30 days. Measure before writing production code.

**Pass criteria (4 of 5 required to proceed):**
- ≥5 of 10 athletes still active at day 7
- Agent output feels personalized — athletes say so
- ≥3 athletes say yes to paying at $10+/month
- ≥1 organic share (athlete sends it to a teammate without being asked)
- ≥3 AD meetings from 20 cold emails

### Weeks 1–2 · Prompt 1 — Shell
Auth, routing, all page shells, 3-table database, RLS enforced from day one.

### Weeks 3–4 · Prompt 2 — The Agent
Edge Function `career-agent`. Claude API. Chat UI. White-label. Weekly email via `weekly-summary` + Resend.

### Week 5 · Prompt 3 — Dashboards
Compliance dashboard. Athlete dashboard with readiness score and NIL log.

### Week 6 · Prompt 4 — Employer
Employer dashboard at `/dashboard/employer`. Search, filter, Express Interest. Strict RLS.

### Weeks 7–8 · Pilot
1 school · 10–15 athletes · 1 compliance officer · 1–2 employer partners.

---

## Pilot Success Metrics

| Metric | Target |
|--------|--------|
| Athlete DAU — week 2+ | ≥40% |
| Average session length | ≥4 minutes |
| Weekly email open rate | ≥20% |
| Employer interest expressions | ≥3 |
| Compliance officer NPS | ≥8/10 |
| First paying deal | 1 |

---

## Repository Structure

```
athletiq-website/
├── index.html                    ← Landing page (live at athq.tech)
├── athletiq-logo.png             ← Logo — same level as index.html
├── vercel.json                   ← Vercel config — serves all static files
├── athletiq-pilot-agent.html     ← Week 0 validation tool
├── athletiq-mvp-spec.jsx         ← Interactive build spec — open in browser
├── athletiq-knowledge-base.md    ← Complete product + brand knowledge base
├── athletiq-business-strategy.md ← Business model, GTM, TAM, revenue model
├── athletiq-product-roadmap.md   ← Triple Diamond framework applied to athletIQ
├── athletiq-jtbd-research-system.md ← JTBD interview script + validation system
├── athletiq-pilot-guidelines.md  ← Pilot deployment and management guide
└── README.md                     ← This file
```

> The Lovable-generated MVP application lives in a separate repository connected to Vercel. These files are the pre-build assets — landing page, pilot tool, and all strategy documentation.

---

## Pre-Build Checklist

- [ ] Week 0 pilot complete — 4+ of 5 criteria passed
- [ ] Supabase project created — URL and anon key saved
- [ ] Anthropic API key saved securely
- [ ] Resend account — API key saved
- [ ] Lovable Pro active ($25/month)
- [ ] GitHub repo connected to Vercel
- [ ] `athq.tech` domain routing confirmed on Vercel
- [ ] `api/chat.js` proxy function deployed (holds Anthropic key server-side)
- [ ] `ANTHROPIC_API_KEY` added to Vercel environment variables
- [ ] At least 1 employer verbal commitment

---

## Environment Variables

| Variable | Where It Lives |
|----------|---------------|
| `ANTHROPIC_API_KEY` | Vercel env vars + Supabase Edge Function secrets |
| `RESEND_API_KEY` | Supabase Edge Function secrets |
| `VITE_SUPABASE_URL` | Lovable / Vercel env vars |
| `VITE_SUPABASE_ANON_KEY` | Lovable / Vercel env vars |

**Rule:** API keys never go in frontend code. Always server-side.

---

## The Founder

**Fred Underwood** — Founder, athletIQ

Played at Huntington College and Jefferson Davis Community College. Graduated from Alabama State University — an HBCU. Walked off the court with no plan, no professional network, and no professional identity. Built athletIQ because that Tuesday morning was real — and it doesn't have to be for the next generation.

**Professional background:** AT&T · Amazon · Microsoft (Azure, AI, Cloud) · AWS Certified · Copilot Certified

**Speaking:** Amazon · Microsoft · USC · SDSU · Black Student-Athlete Summit · HBCU Events

---

**hello@athq.tech · [athq.tech](https://athq.tech)**

*© 2026 athletIQ, Inc. All rights reserved.*
