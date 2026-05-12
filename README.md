# athletIQ
### The Career Readiness Agent for College Athletes

> *Intelligence. Elevation. Legacy.*

Fewer than 2% of college athletes reach a major professional league. 554,000 are competing right now. Nobody built anything for the other 98% — until now.

athletIQ is an AI-powered career readiness platform that gives every college athlete a personal career agent from day one of enrollment. It builds professional identity, surfaces career paths, generates LinkedIn profiles, drafts networking outreach, and prepares athletes for the career they'll spend 40 years in — while they're still competing.

---

## The Problem

One athlete development staff member. Hundreds of athletes. The math has never worked.

Career development workshops deliver the same content to every athlete. Career centers are built for the average student, not the athlete with a specific sport, specific skills, and a specific transition challenge. The tools that existed before athletIQ were constrained by a ratio — one human, finite hours, finite athletes they could meaningfully serve.

AI removes the ratio. The same depth of personalized career guidance available to the athlete who walks into the office is now available to every athlete — simultaneously, continuously, from freshman year to post-graduation.

---

## The Product

**One Career Readiness Agent. Three integrated capabilities. Every athlete.**

### Career Readiness Agent
The core of the platform. A conversational AI agent that activates the moment an athlete completes their Future Blueprint. It knows their sport, their career interests, their goals, and their digital presence — and uses that context to deliver personalized guidance in every interaction.

It does not wait for the athlete to ask. It leads.

### Three Capabilities (inside one agent — not separate modules)

**1. LinkedIn & Personal Brand**
Generates LinkedIn headlines and About sections from the athlete's profile. Optimizes positioning. Translates athletic experience into professional language. Recommends content strategy based on career path.

**2. Career Opportunities**
Surfaces career paths matched to sport and interests. Recommends certifications, internships, and learning paths. Identifies skill gaps. Builds a personalized career readiness action plan.

**3. Networking Intelligence**
Recommends alumni connections and mentor matches by career interest. Drafts LinkedIn outreach and connection messages in the athlete's voice. Helps athletes build strategic professional relationships before they need them.

---

## The Business Model

Three tiers. One ecosystem. Each tier makes the others more valuable.

| Tier | Who | What They Get | Revenue |
|------|-----|---------------|---------|
| **Free** | Athletes | Career Readiness Agent — always | $0 — distribution engine |
| **Employer** | Companies | Early access to vetted, career-ready athlete pipeline | $5K–15K/year |
| **Institutional** | Athletic Departments | White-labeled platform + development dashboard | $6K–25K/year |
| **Enterprise** | Power-4 Programs | Full platform, conference reporting, dedicated support | Custom |

### Why This Model Works

Athletes get it free — removing every adoption barrier. Employers pay to access a pipeline of career-ready athletes 2–4 years before graduation — earlier than any competitor reaches them. Athletic departments pay to white-label the experience as their own recruiting differentiator.

**The flywheel:** More athlete profiles → more employer value → more employer revenue → stronger institutional pitch → more departments → more athletes → repeat.

### The White-Label Advantage

Institutional and enterprise tiers carry the university's brand inside the athlete experience. The agent doesn't say "athletIQ" — it says "Alabama State Career Agent — powered by athletIQ." The department gets the credit. athletIQ builds the infrastructure.

This is positioned as a recruiting tool, not a software subscription — and it is priced to fit within the discretionary budgets of HBCU and mid-major programs.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React + Tailwind + shadcn/ui (via Lovable) |
| Backend | Supabase (PostgreSQL + Auth + Edge Functions) |
| AI | Anthropic Claude API — `claude-sonnet-4-20250514` |
| Email | Resend |
| Deployment | Vercel (via Lovable GitHub integration) |

---

## Database Schema

Three tables. No more.

### `athlete_profiles`
Central record. Every agent call reads this as system context.

| Field | Type | Notes |
|-------|------|-------|
| `id` | uuid | Primary key |
| `user_id` | uuid | FK → auth.users |
| `name`, `school`, `sport`, `position` | string | Identity fields |
| `grad_year` | integer | e.g. 2027 |
| `career_interests` | text[] | Multi-select from onboarding |
| `industry_interests` | text[] | Multi-select from onboarding |
| `entrepreneurship_interest` | boolean | |
| `goals` | text | Free text — career, networking, internship goals |
| `linkedin_url`, `instagram_url`, `tiktok_url` | string nullable | Optional digital presence |
| `onboarding_complete` | boolean | Gates /chat access |
| `readiness_score` | integer 0–100 | Calculated from profile completeness + engagement |
| `school_brand_color` | string nullable | Hex — for white-label theming |
| `school_logo_url` | string nullable | For white-label chat header |
| `employer_visible` | boolean | Controls employer dashboard access |

### `conversations`
Full chat history. Sent to Claude as context on each message.

| Field | Type | Notes |
|-------|------|-------|
| `id` | uuid | |
| `athlete_id` | uuid | FK → athlete_profiles |
| `role` | enum | `user` or `assistant` |
| `content` | text | Full message content |
| `created_at` | timestamp | |

### `nil_disclosures`
Simple manual log. Athlete submits. Compliance officer reviews.

| Field | Type | Notes |
|-------|------|-------|
| `id` | uuid | |
| `athlete_id` | uuid | FK → athlete_profiles |
| `brand_name` | string | |
| `deal_value` | decimal nullable | Optional |
| `category` | string | Apparel / F&B / Local Business / Tech / Health / Other |
| `disclosure_date` | date | Defaults to today |
| `status` | enum | `pending` / `approved` / `flagged` |
| `notes` | text nullable | Staff review notes |

---

## User Roles & Access Control

All roles enforced via Supabase Row-Level Security.

| Role | Access |
|------|--------|
| `athlete` | Read/write own rows only |
| `compliance_officer` | Read athlete_profiles + nil_disclosures where school matches |
| `employer` | Read athlete_profiles where employer_visible = true — name, school, sport, grad_year, career_interests, readiness_score, linkedin_url only. Never sees goals, conversations, nil_disclosures, or contact info |
| `admin` | Full access |

---

## Product Screens

| Route | Who | What |
|-------|-----|------|
| `/login` `/signup` | All | Auth with role selection |
| `/onboarding` | Athlete | 4-step Future Blueprint — identity, interests, goals, digital presence |
| `/chat` | Athlete | The Career Readiness Agent — conversational, proactive, white-label aware |
| `/dashboard/athlete` | Athlete | Profile, readiness score, NIL disclosure log |
| `/dashboard/compliance` | Compliance Officer | Athlete roster, readiness scores, NIL disclosures |
| `/dashboard/employer` | Employer | Athlete search, profile cards, Express Interest |

---

## Onboarding Flow — The Future Blueprint

4 steps. Aspirational tone throughout.

> *"Build Your Athlete Future Blueprint. Sports won't last forever. Your impact can."*

- **Step 1 — Identity:** Name, school, sport, position, graduation year
- **Step 2 — Interests:** Career + industry interests (multi-select chips)
- **Step 3 — Goals:** Career goals, networking goals, internship goals (free text)
- **Step 4 — Digital Presence:** LinkedIn, Instagram, TikTok (all optional) + employer visibility toggle

**Completion:** Agent sends first proactive message referencing the athlete's specific sport and career interest. Does not wait for the athlete to speak first.

---

## The Proactive Layer

athletIQ is not a passive chatbot. The agent initiates.

**Weekly Sunday Email** (Supabase Edge Function + Resend):
Every athlete receives a personalized weekly summary generated from their profile:
- One career action to take this week
- One observation about their progress
- One resource worth exploring

Subject line: `Your athletIQ Weekly · [First Name]`

This is the mechanism that creates habit and return visits — not notification badges, not push alerts, but a genuinely useful weekly signal.

---

## Build Sequence

### Week 0 — Validate Before Building
Run five no-code tests before opening Lovable. These tests cost $0 and tell you exactly what to build.

1. **Athlete engagement:** Manually onboard 10 athletes via the pilot agent. Are 5+ still engaging at day 7?
2. **Willingness to pay:** Ask directly at day 7. Do 3+ say yes at $10+/month?
3. **AD meeting rate:** Cold email 20 athletic directors. Do 3+ respond?
4. **Employer interest:** Pitch 5 companies. Does 1 say yes at any price?
5. **White-label resonance:** In AD meetings, does the white-label pitch land? Do they use the word "recruiting"?

**Gate:** Pass all five before opening Lovable.

### Week 1–2 — Lovable Prompt 1: Shell
Auth, routing, all page shells, database structure, RLS. No AI calls yet.

### Week 3–4 — Lovable Prompt 2: The Agent
Supabase Edge Function `career-agent`. Claude API integration. Chat UI with white-label support. Weekly email Edge Function.

### Week 5 — Lovable Prompt 3: Dashboards
Compliance dashboard (3 sections). Athlete dashboard (profile, readiness score, NIL log).

### Week 6 — Lovable Prompt 4: Employer
Employer dashboard at `/dashboard/employer`. Search, filter, athlete cards, Express Interest. Strict RLS enforced.

### Week 7–8 — Pilot
1 school. 1 compliance officer. 10–15 athletes. 1–2 employer partners.

---

## Pilot Success Metrics

Defined before the pilot launches.

| Metric | Target |
|--------|--------|
| Athlete DAU — week 2+ | ≥ 40% |
| Average session length | ≥ 4 minutes |
| Weekly email open rate | ≥ 20% |
| Employer interest expressions | ≥ 3 |
| Compliance officer NPS | ≥ 8/10 |
| First paying institutional deal | 1 |

### Early Warning Signs
- Athletes open once and don't return — agent output isn't landing. Tune the system prompt.
- Athletes describe it as "like ChatGPT" — personalization isn't specific enough to their profile.
- Weekly email open rate below 15% after week 3 — A/B test subject lines.
- Zero employer interest expressions after 30 days — athlete profiles need more depth or employer UX is broken.
- Compliance officer hasn't logged in after onboarding — ask directly what would make it worth opening weekly.

---

## Repository Structure

```
athletiq/
├── index.html                  # Landing page (rename from athletiq-landing-v2.html)
├── athletiq-logo.png           # Logo — same directory as index.html
├── athletiq-pilot-agent.html   # Week 0 pilot agent — standalone, no backend required
├── athletiq-mvp-spec.jsx       # Interactive MVP spec — open in browser
└── README.md                   # This file
```

> **Note:** The Lovable-generated application will live in a separate repository created by Lovable and connected to Vercel. The files above are pre-build assets — the landing page, the pilot validation tool, and the build spec.

---

## Pre-Build Checklist

Before running Lovable Prompt 1:

- [ ] Supabase project created — Project URL and anon key saved
- [ ] Anthropic API key obtained and saved securely
- [ ] Resend account created — API key saved
- [ ] Lovable Pro plan active ($25/month)
- [ ] GitHub account connected to Lovable
- [ ] `athq.tech` domain connected to Vercel
- [ ] Week 0 validation complete — all five tests passed
- [ ] Email confirmation disabled in Supabase Auth (for development)

---

## Environment Variables

Set these as Supabase Edge Function secrets — never in the frontend.

```
ANTHROPIC_API_KEY=sk-ant-...
RESEND_API_KEY=re_...
```

Set these in Lovable as Supabase connection variables:

```
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
```

---

## Who Built This

athletIQ was founded by a former college athlete who played at Huntington College and Jefferson Davis Community College before graduating from Alabama State University — an HBCU — and who lived this gap firsthand. Years of elite athletic preparation. Zero systematic preparation for the career that came after.

**Background:** Played at Huntington College + Jefferson Davis CC · Graduated Alabama State University (HBCU) · AT&T · Amazon · Microsoft (Azure, AI, Cloud) · AWS · Copilot certified

**Speaking:** Amazon · Microsoft · USC · SDSU · Black Student-Athlete Summit · HBCU Events

athletIQ is built for the programs where the gap is widest first — HBCUs and mid-major programs where the athlete talent is world-class, the development mandate is identical to Power-4 schools, and the staff resources are not.

---

## Contact

**hello@athq.tech**

[athq.tech](https://athq.tech)

---

*© 2026 athletIQ, Inc. All rights reserved.*
*Intelligence. Elevation. Legacy.*
