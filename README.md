# SEMBRAMOS — Technical & Business Whitepaper
## AI-Native Agricultural Marketplace · Venezuela

**Version 2.1 — August 2026**
**Public document — prepared for judges, reviewers, and potential partners of the Build with Gemini XPRIZE**

---

> This document is intentionally split into two kinds of content, always labeled: **✅ TODAY** (live in production, verifiable right now) and **🔮 VISION** (roadmap, not yet built). Nothing in the ✅ sections is aspirational, and nothing in the 🔮 sections is presented as already working. This distinction is the whole point of the document.

---

## EXECUTIVE SUMMARY

**Sembramos** is an AI-Native digital agricultural marketplace built in Flutter Web on Firebase, serving Venezuela. It connects farmers with buyers (intermediaries, wholesale "ferieros") using Google Gemini as the system's core intelligence layer — natural-language publishing by voice, planting recommendations, and autonomous price monitoring.

### ✅ Status today (August 2026)

- Live in production at `https://sembramos.lat`
- **59 Cloud Functions** active in `us-central1`
- **~44,000 lines** of Flutter Web code (single codebase, intentional hackathon-speed monolith)
- Catalog of **58 agricultural product types**, each with real varieties/grades
- Complete flow: register → publish → negotiate → confirm → rate — all working end-to-end
- Monetization: individual unlock ($0.50) + monthly subscription ($3.00), both manually reviewed before activation
- **68 real users**, organically grown across **5 Venezuelan states** (Táchira, Mérida, Trujillo, Lara, Caracas)
- **2 real paying subscribers**, $6.00 in verified real revenue
- Full Terms of Service + Privacy Policy published, with active consent capture and a 30-day account-deletion grace period
- Submitted to **Build with Gemini XPRIZE** (Money & Financial Access category) — Devpost submission confirmed, judging period runs through September 15, 2026

### The honest number, stated first

Sembramos is early. Revenue is real but small ($6, 2 subscribers). This document does not inflate that — it explains *why* the model works at this scale, what the real constraint is (distribution, not product), and what the roadmap looks like if it's given resources to grow. Every dollar figure beyond "$6 today" in this document is a projection, clearly marked as such.

---

## PART I — THE PROBLEM

### 1. Venezuela's agricultural market has a structural information gap

In Venezuela's Andean agricultural belt (Táchira, Mérida, Trujillo, Barinas), the same crop can sell for **$35 in one state and $115 in the next** — and almost nobody involved, least of all the farmer, has visibility into that gap. Price information moves through informal WhatsApp groups with no traceability, no history, and no way to verify it's current.

- **The farmer** receives whatever the intermediary offers, with no objective reference point.
- **The buyer** has no way to know if today's price is normal, high, or low compared to last week.
- **The information gap itself** — not any single actor — is what captures value away from the people doing the actual work.

### 2. Why this isn't "disintermediation"

Sembramos does not try to remove intermediaries. It gives them a platform too: an intermediary who operates over WhatsApp today can publish demand on Sembramos, see the real reference price, and close trackable contracts. **12 of Sembramos's 68 real users are already intermediaries or wholesale buyers (10 intermediaries, 2 "ferieros"), not farmers** — they're a real user segment inside the product, not the thing the product is fighting.

### 3. Why now

Global digital agriculture was valued at USD 14.56B in 2024, projected to reach USD 43.73B by 2033 (13% CAGR). In Venezuela specifically, informal dollarization (since 2019) and mass adoption of mobile payments and USDT created the settlement infrastructure that used to be the main blocker for a platform like this.

---

## PART II — WHAT'S ACTUALLY BUILT (✅ TODAY)

### 4. Technical architecture

| Layer | Technology | Why |
|---|---|---|
| Frontend | Flutter Web | Single codebase, cross-platform later |
| Auth | Firebase Auth + Custom Claims JWT | Zero-cost verification at the Firestore layer |
| Database | Cloud Firestore | Real-time, serverless, scales without ops work |
| AI / NLP | Google Gemini 2.5 Flash via Vertex AI | Fastest production model, with Google Search grounding |
| Hosting | Firebase Hosting | Global CDN, automatic SSL |
| Backend | Cloud Functions v2, Node.js 24 | No infrastructure to maintain |
| Bot/abuse defense | Firebase App Check (reCAPTCHA v3) | Enforced on all 59 HTTP functions |

### 5. What the AI actually does — with a hard limit

Gemini powers: natural-language publishing by voice or text, honest planting recommendations (with explicit "not enough data yet" messaging instead of fake confidence), photo-based crop diagnostics, and autonomous seller-risk scoring for buyers.

**The AI never moves money and never closes a deal on its own.** Every action that touches money or finalizes a contract requires an explicit human tap — this is a hard architectural rule, not a policy statement, enforced at the code level in every write path that matters.

### 6. Trust & safety already in production

- Structured dispute system (both parties present evidence, 48h response windows, admin review)
- Reputation written **only** by the backend, never the client
- WhatsApp contact only unlocked after a *confirmed* real order — closes a phishing/scraping vector that existed earlier in development and was fixed
- WhatsApp uniqueness enforced across accounts (`whatsapp_index`)
- **Account deletion with a 30-day grace period** (built this week) — no data is destroyed instantly; a user can recover their account by simply logging back in before the deadline
- **Permanent WhatsApp lock after a completed account deletion** — a deliberately stricter choice than the 30-day cooldown used by Binance (researched before building), because IP-based signals are unreliable in Venezuela's shared mobile networks (CGNAT) and WhatsApp is the platform's only real identity anchor
- A real security audit this week found and closed a gap that let any account self-assign a fake "verified" status and fake reputation counters at registration — fixed, deployed, and verified against production with live test writes before and after the fix

### 7. Price reference — the core trust mechanic

```
reference_price_USD = MEDIAN of confirmed bilateral contracts (7-day rolling window)
```

Median, never average — resistant to a single outlier transaction. With fewer than 3 real transactions for a product, Sembramos shows it honestly as an "initial estimate," never as a real reference. **Today, 0 of 58 catalog items have enough real transaction volume to show a "real" price** — this is stated honestly in the product itself, not hidden.

### 8. ✅ AI planting guidance — real today, not a roadmap item

Beyond the marketplace itself, Sembramos already runs a Gemini-powered advisory layer inside "Mis Cultivos" (My Crops), built across six iterative phases and live in production today:

- **"What should I plant?"** — honest recommendations grounded in real local transaction data, including margin-per-cycle economics and *saturation warnings* (how many other nearby farmers are already planting the same crop) — never a confident guess when the data doesn't support one.
- **Photo-based crop diagnosis** — a farmer photographs a problem, Gemini analyzes it, cross-referenced against an anonymized community knowledge base of what treatments worked for similar problems before.
- **Farm-level financial tracking** — real cost/margin tracking per planting cycle, so a farmer can see, in their own numbers, whether a crop was actually worth planting.

This is described here in more detail than a typical roadmap item precisely because it *isn't* a roadmap item — it's real, running code, used by real farmers today.

---

## PART III — BUSINESS MODEL: TODAY VS. ROADMAP

### 9. ✅ Phase 1 — Today (in production)

- Individual WhatsApp unlock: $0.50
- Monthly subscription: $3.00 → full contact access + advanced filters
- Every payment is manually reviewed before activation — no auto-approval, closed as a real vulnerability earlier in development
- **Why it works at this stage:** the reference price already has value on its own (a buyer pays to avoid overpaying); WhatsApp access is the actual asset being sold

### 10. 🔮 Phase 2 — Commission on GMV (not built, targeted Q4 2026)

**Concept:** a 1% fee on the subtotal of *bilaterally confirmed* contracts only — never charged on pending, cancelled, or failed contracts, and charged to the buyer (same model as MercadoLibre). This is money Sembramos earns for its own service, not third-party funds — it only requires a normal business bank account, not a fund-holding license.

**Why 1% specifically:** Binance charges 0.1% (high-frequency, thin margins per trade); MercadoLibre charges 2–10% (with escrow, logistics, buyer protection). At Sembramos's transaction size and frequency, 1% is competitive against the ~20–40% margin an informal intermediary already captures today, without Sembramos providing escrow yet.

### 11. 🔮 Phase 3 — Native escrow (not built, targeted Q2 2027)

**Concept:** buyer pays into a Sembramos-held account before the seller ships; funds release to the seller once both sides confirm, or automatically after a fixed window if there's no dispute. If disputed, Sembramos reviews photo evidence and the in-app chat history before releasing funds. Unlike Phase 2, this means Sembramos temporarily holds *someone else's* money — a materially bigger regulatory and banking obligation, not just "more commission."

**Legal requirements identified, none satisfied yet:** a registered legal entity with a USD-capable corporate bank account able to hold third-party funds, Terms of Service with an explicit escrow clause, and a basic KYC/AML policy for larger transactions.

**Jurisdiction — stated directly, technically, honestly:** this is an open question under active research, not a decision already made. The real constraint isn't "which country sounds best" — it's narrower and more concrete: **Sembramos's founder holds Venezuelan citizenship**, and the most accessible US fintech banking route for international founders, Mercury, has a documented, flat 100% account-closure policy specifically for Venezuelan passport holders — regardless of where the entity itself is incorporated. This isn't a Venezuela-wide OFAC sanction (Venezuela is not comprehensively sanctioned); it's a bank-level risk policy that excludes this specific citizenship. A US entity is not ruled out — it requires finding a specific US bank willing to serve this founder profile before committing capital to US incorporation. Colombia (SAS) and Panama (SA) remain the leading near-term candidates not because they're inherently preferable to a US entity, but because they are realistic paths that can plausibly be *opened and kept operating* — including affording the real, ongoing compliance costs that come with legally holding third-party funds — with a Venezuelan passport. The decision will be made once a specific, workable path (in any jurisdiction) is confirmed open in practice, not chosen on assumption.

### 12. 🔮 Phase 4 — SIEMBRA utility token (not built, 2028+, lowest-confidence part of the roadmap)

A utility token (not an investment instrument) inspired by Binance's BNB model: paying platform fees with SIEMBRA earns a discount, and verified farmers earn SIEMBRA for maintaining real crop-cycle records with the app. This phase has no committed timeline and is included for completeness, not because it's a near-term priority.

---

## PART IV — 🔮 THE FULLER VISION: FROM DIGITAL LAYER TO PHYSICAL INFRASTRUCTURE

Everything in Part III is financial and software infrastructure. The founder's longer-term ambition for Sembramos goes further, into physical infrastructure that the digital layer makes possible — none of this is sequenced into Phases 1–4 above, and none of it has a committed timeline. It's included because a document about "how ambitious is this project" is incomplete without it.

### 13. 🔮 Collection centers ("centros de acopio")

**Concept:** regional physical points where farmers could consolidate and store harvests before sale, directly attacking the 15–30% post-harvest waste that CEPAL estimates happens today when a farmer can't find a buyer fast enough (see Part V). Sembramos's own transaction data — which zone, which crop, which timing patterns repeat — would inform *where* a center makes sense before any physical capital is spent, rather than guessing.

**The honest tension, stated directly:** operating a physical collection center would make Sembramos a direct participant in the market it currently only connects — a real strategic shift from the neutral-platform positioning the live product is built on today (Part I, Section 2). This is exactly why it's presented here as a distant, unsequenced vision rather than folded into the numbered roadmap: it needs to be positioned carefully so it never contradicts "Sembramos doesn't compete with intermediaries," not rushed into.

### 14. 🔮 Direct connection to end retailers and merchants

**Concept:** today, Sembramos's marketplace connects farmers to intermediaries and wholesale buyers ("ferieros"). The next logical extension downstream is connecting demand further — to supermarket chains and large-scale end buyers who today source through several layers of intermediation they don't fully see through. This doesn't remove the intermediary layer already using Sembramos; it adds a new, larger class of buyer onto the same reference-price infrastructure everyone already trusts.

### 15. 🔮 From individual advice to national-scale agricultural planning

Part II, Section 8 described what the AI planting-guidance layer already does today, for one farmer at a time. The longer-term vision: the same aggregated, anonymized data that powers one farmer's recommendation today could, at national scale, inform real crop-diversification patterns across regions — reducing the oversupply-in-one-place, shortage-in-another whiplash that shows up in Sembramos's own price data (the exact $35-vs-$115 gap this document opens with). This is explicitly presented as an extension of something already built and running, not a new, unproven feature category.

---

## PART V — RISKS, STATED HONESTLY

| Risk | Mitigation in place today |
|---|---|
| Founder solo, unlimited personal liability (sole proprietorship structure) | Under active evaluation: forming a dedicated legal entity for Sembramos, separate from the founder's other commercial activity |
| Low current transaction volume (10 real contracts, 1 completed) | Small sample size is stated honestly everywhere in the product itself — no fabricated "real" price is ever shown |
| Regulatory risk if Venezuela regulates digital marketplaces | Roadmap already anticipates a foreign legal entity before any money-holding feature (escrow) launches |
| Founder's citizenship restricts the most accessible US banking routes (see Part III, Section 11) | Multiple jurisdictions under active, parallel research — decision gated on confirmed real-world operability, not assumption |
| Low rural internet penetration | Voice-first design + offline-tolerant data writes already in production |
| Distrust of digital platforms in an informal cash economy | Free, no-login public price reference; real farmer testimonials; voice mode for users who can't read or write |

---

## PART VI — REAL-WORLD PRECEDENTS THIS MODEL IS BUILT ON

Every comparison below is a real, sourced case — not a generic "other platforms do X."

| Precedent | What it proves for Sembramos |
|---|---|
| **Binance** (2017) — launched with no formal entity, built compliance in year two, not year one | Product-and-network first, formal compliance layered in deliberately, not skipped |
| **MercadoLibre / MercadoPago** (1999→2003) — marketplace first, escrow four years later | Trust has to be earned by the marketplace before a payments layer makes sense |
| **Twiga Foods** (Kenya, 2014) — B2B agri platform paying farmers 20–40% more than informal market rate, in 24h via mobile money | Farmers adopt a platform because of the direct economic gain, not the technology story |
| **Fina** (Venezuela, 2024–2026) — B2B SaaS for Venezuelan SMBs, raised a **$1M seed round in August 2026** with 5,000+ active businesses and ~$2M ARR at the time of raising | The closest real comparable to Sembramos: same country, same early stage, proof that real capital is actively funding Venezuelan SaaS *right now* |
| **Cashea** (Venezuela, 2024–2026) — BNPL fintech, raised **$100M total** ($40M Series A March 2026 + $60M Series B June 2026), 10M+ consumer accounts | Confirms Venezuela itself is investable again in 2026, at meaningfully larger scale than a seed round |

Sources: [Fina raises $1M seed — LatamList](https://latamlist.com/venezuelan-fintech-fina-raises-1m-seed-round/) · [Cashea secures $100M — Crowdfund Insider](https://www.crowdfundinsider.com/2026/07/293776-venezuelan-fintech-cashea-secures-100m-from-global-backers-amid-credit-revival/) · [How Binance handles account deletion](https://www.binance.com/en/support/faq/how-to-delete-my-binance-account-f02c2640a1cd44e58de68e4a49d599f6) · [Mercury Bank Account Closures — prohibited countries/citizenships](https://www.growthhq.io/our-thinking/mercury-bank-account-closures-2024-2025-compliance-risks-regional-impacts-and-critical-steps-for-businesses-in-prohibited-countries)

---

## PART VII — WHERE SEMBRAMOS IS RIGHT NOW, HONESTLY

This project started in May 2026 — before the founder knew the Build with Gemini XPRIZE existed — as a direct response to a real problem in his own community. It has been built, largely solo, through near-daily iteration: **568 commits since June 13, 2026**, each one reviewed for correctness before shipping, with a documented history of finding and closing real security and product bugs before they reached users.

The single most consequential open question right now isn't technical — it's structural: whether to formalize Sembramos as its own legal entity (separate finances, limited liability, a clean story for any future investor) versus continuing to build under the founder's existing sole-proprietorship registration, which is also used for unrelated commercial activity. Both the entity's jurisdiction (Part III, Section 11) and this broader question are being researched deliberately, not rushed — consistent with how the rest of this project has been built.

---

**Contact:** angasamocris@gmail.com
**Production app:** https://sembramos.lat
**This document:** public, hosted on GitHub

*Prepared with assistance from Claude Code (Anthropic).*
*Sembramos — August 2026*
