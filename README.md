# SEMBRAMOS — Technical & Business Whitepaper
## AI-Native Agricultural Marketplace · Venezuela

**Version 3.4 — August 2026**
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

### 7. Price reference — the core trust mechanic

```
reference_price_USD = MEDIAN of confirmed bilateral contracts (7-day rolling window)
```

Median, never average — resistant to a single outlier transaction. With fewer than 3 real transactions for a product, Sembramos shows it honestly as an "initial estimate," never as a real reference. **Today, 0 of 58 catalog items have enough real transaction volume to show a "real" price** — this is stated honestly in the product itself, not hidden.

This median is always computed within a single trade tier — a farmer-to-buyer transaction and a buyer-to-buyer resale transaction are never pooled into the same number (see Part IV, Section 14). A wholesale resale price and a farm-gate price mean different things; blending them would make the reference price ambiguous for the exact people it's meant to protect. Same principle every real wholesale market already follows — farm-gate and wholesale prices are always published as separate figures, never one blended average.

### 8. ✅ AI planting guidance — real today, not a roadmap item

Beyond the marketplace itself, Sembramos already runs a Gemini-powered advisory layer inside "Mis Cultivos" (My Crops), built across six iterative phases and live in production today:

- **"What should I plant?"** — honest recommendations grounded in real local transaction data, including margin-per-cycle economics and *saturation warnings* (how many other nearby farmers are already planting the same crop) — never a confident guess when the data doesn't support one.
- **Photo-based crop diagnosis** — a farmer photographs a problem, Gemini analyzes it, cross-referenced against an anonymized community knowledge base of what treatments worked for similar problems before.
- **Farm-level financial tracking** — real cost/margin tracking per planting cycle, so a farmer can see, in their own numbers, whether a crop was actually worth planting.

This is described here in more detail than a typical roadmap item precisely because it *isn't* a roadmap item — it's real, running code, used by real farmers today.

---

## PART III — BUSINESS MODEL: TODAY VS. ROADMAP

Every phase below is sequenced deliberately: each one is built on the trust or data asset the previous phase creates, rather than competing with it for attention or resources. Progression between phases is gated on real registered-user milestones, not calendar dates — a discipline this roadmap holds itself to throughout, so growth is measured the same way in every section.

### 9. ✅ Phase 1 — Today (in production)

- Individual WhatsApp unlock: $0.50
- Monthly subscription: $3.00 → full contact access + advanced filters
- Every payment is manually reviewed before activation — no auto-approval, closed as a real vulnerability earlier in development
- **Why it works at this stage:** the reference price already has value on its own (a buyer pays to avoid overpaying); WhatsApp access is the actual asset being sold

### 10. 🔮 Phase 1B — Farm-management subscription (extends Phase 1, not a separate business)

**Concept:** a paid tier of "Mis Cultivos" (My Crops), the Gemini-powered farm advisory layer already live today (Part II, Section 8). The baseline — "What should I plant?", basic crop diary, phase tracking — stays free, deliberately, for the same reason it launched free: protecting the platform's financial-inclusion positioning. The paid tier unlocks what a farmer needs once they're managing real scale: multi-plot margin/ROI comparison, season-over-season trend analysis, exportable records, priority AI response time. Same freemium logic the marketplace itself already uses — the reference price is always free, contact access is what's paid.

**Why this reinforces Phase 1 instead of competing with it:** a $3/month marketplace subscriber today only has a reason to open the app when actively buying or selling — real but sporadic usage. A farm-management subscription gives the same subscriber a reason to open the app daily, independent of that week's transactions. Higher daily engagement lowers churn on the core subscription and, just as importantly, reduces "off-platform leakage" — the standard two-sided-marketplace risk where two parties find each other once through the app and then move the relationship to WhatsApp permanently. Every contact-unlock marketplace faces this; giving users a reason to keep opening the app beyond the transaction itself is the standard mitigation.

**Price:** $2.00/month — deliberately close to the existing $3.00 marketplace subscription (Phase 1), not a premium-SaaS price point. It reflects what real paying users have already shown they're willing to pay for this platform, not a guess pulled from farm-management software pricing in wealthier markets, which would exclude the smallholder farmers this is built for.

**Milestone to unlock:** 500 registered users. Below that scale, splitting Mis Cultivos into free and paid tiers doesn't move revenue and there isn't enough usage data yet to know which advanced features farmers actually value. At 500, the farmer base is large enough for a premium tier to generate real incremental revenue and to keep refining based on real usage.

**What it sets up for later:** at real scale, this is also where Sembramos starts accumulating something most Venezuelan smallholder farmers structurally lack — verifiable, multi-cycle crop records. That asset is what Section 12 depends on.

### 11. 🔮 Real benefits from real behavior

**Concept:** Sembramos ties tangible benefits directly to the trust and data assets it already has — the same loyalty loop Cashea runs with its own users, where reliable behavior unlocks better real terms (Part VI). Sembramos's version: complete trades honestly and keep good crop records, and the platform gives back better real terms — never points or a balance to spend, always something the user can use immediately.

**Two concrete levers, both using assets that already exist:**
- **Marketplace perks tied to the existing "Verificado" badge** (Part II, Section 6 — already live: 3+ completed contracts, 85%+ reputation, 2+ distinct buyers): a subscription discount or a free period for badge holders, using the exact payment rail Phase 1 already has. Buildable without waiting for any other phase.
- **Better input-credit terms** (Section 12) for farmers with strong, verifiable crop-cycle records and reputation — the same scoring mechanism already described there (Apollo Agriculture's model), now framed explicitly as the reward for good record-keeping, not just a technical detail of how scoring works.

**Why this reinforces every phase instead of competing with them:** chasing a better badge or better credit terms means actually completing more trades honestly and keeping better crop records — the same real data every other phase in this roadmap depends on. It's not a separate business; it's the incentive that makes the existing engine turn faster.

**Milestone to unlock:** ties to the Verificado badge criteria already in production (Part II, Section 6) — this is a benefits layer on top of a qualification bar that already exists, not a new user-count gate of its own.

### 12. 🔮 Input-credit facilitation — data-gated, never Sembramos's own capital

**Concept:** once Phase 1B (Section 10) has real scale, its crop-cycle records become a verifiable track record most Venezuelan smallholder farmers structurally lack today. Sembramos could use that data to score creditworthiness for input financing (seeds, fertilizer, tools), repaid from the next harvest — a problem several real ag-fintechs have already solved profitably elsewhere.

**Why Sembramos doesn't lend directly:** lending is a regulated financial activity requiring real reserve capital and a license — not something to put on Sembramos's own balance sheet at this stage, or arguably ever, given the company's actual strength is data and software, not capital markets. The professional structure: Sembramos scores and refers; a real lender (a bank, microfinance institution, or input supplier with its own capital) originates the loan and carries the credit risk. Sembramos is the intelligence and distribution layer, not the balance sheet.

**How Sembramos earns from this, specifically:** a one-time origination fee — a percentage of the loan amount, in the 1–2% range — paid by the lending partner when a loan is actually disbursed, never by the farmer and never as a share of loan repayments. Standard economics for a scoring-and-referral intermediary; Sembramos is paid for the introduction and the data, never touches interest income, and carries no repayment risk.

**Real precedent, directly on point:** Apollo Agriculture (Kenya) uses machine-learning credit models built from farm data to give smallholder farmers instant input-financing decisions, repaid from the harvest — not from Apollo's own capital, but structured through securitization backed by real investors. Its most recent raise, closed May 2026, mobilized KES 276M in debt financing for roughly 24,000 farmers, part of a multi-year program targeting 130,000+; the company has raised $70.3M total from investors including SoftBank Vision Fund. This is the exact model — data-driven scoring, third-party capital, farmer-facing distribution — Sembramos would need to replicate, not invent from scratch.

**Milestone to unlock:** 2,000 registered users. Credit scoring needs a statistically meaningful sample of multi-cycle crop records to be trustworthy, not a handful — the same real-data requirement Apollo Agriculture's own model depends on. At 2,000 users, Phase 1B's data has had time to accumulate real cycle history across enough farmers for scoring to mean something.

### 13. 🔮 Phase 2 — Native escrow (not built — gated on a real user milestone, not a calendar date)

**Concept:** buyer pays into a Sembramos-held account before the seller ships; funds release to the seller once both sides confirm, or automatically after a fixed window if there's no dispute. If disputed, Sembramos reviews photo evidence and the in-app chat history before releasing funds. This is the point where Sembramos actually holds *someone else's* money for the first time — a materially bigger regulatory and banking obligation than anything in Phase 1 or Phase 1B, and deliberately the only other numbered monetization phase in this roadmap.

**Why the roadmap moves straight from subscription revenue to escrow:** collecting a per-transaction fee without holding funds would depend on a separate, voluntarily-paid invoice with weak enforcement — closer to hoping than charging. Real fund custody is what makes fee collection enforceable by design. Sembramos's monetization roadmap is built around that distinction: subscription revenue first, because it's collectible today with the payment rail already in production; escrow second, because it's the point where a transaction fee becomes something the platform can actually enforce rather than request.

**What it would charge, roughly:** with real custody, a fee in the 2–3% range is a reasonable planning estimate — informed by the same precedents already in Part VI (Binance's 0.1% reflects zero custody or protection; MercadoLibre's 2–10% reflects real escrow and buyer protection) — still far below the ~20–40% margin an informal intermediary captures today. Not committed; the real number gets set once the legal requirements below are actually satisfied.

**Legal requirements identified, none satisfied yet:** a registered legal entity with a USD-capable corporate bank account able to hold third-party funds, Terms of Service with an explicit escrow clause, and a basic KYC/AML policy for larger transactions.

**Jurisdiction — stated directly, technically, honestly:** this is an open question under active research, not a decision already made. The real constraint isn't "which country sounds best" — it's narrower and more concrete: **Sembramos's founder holds Venezuelan citizenship**, and the most accessible US fintech banking route for international founders, Mercury, has a documented, flat 100% account-closure policy specifically for Venezuelan passport holders — regardless of where the entity itself is incorporated. This isn't a Venezuela-wide OFAC sanction (Venezuela is not comprehensively sanctioned); it's a bank-level risk policy that excludes this specific citizenship. A US entity is not ruled out — it requires finding a specific US bank willing to serve this founder profile before committing capital to US incorporation. Colombia (SAS) and Panama (SA) remain the leading near-term candidates not because they're inherently preferable to a US entity, but because they are realistic paths that can plausibly be *opened and kept operating* — including affording the real, ongoing compliance costs that come with legally holding third-party funds — with a Venezuelan passport. The decision will be made once a specific, workable path (in any jurisdiction) is confirmed open in practice, not chosen on assumption.

**Milestone to unlock:** 5,000 registered users. At that scale, the platform's subscription revenue and real transaction volume plausibly justify the fixed cost of legal entity formation and licensed fund custody — the point where a two-sided marketplace typically graduates from listing fees to owning its own payment infrastructure. Capital for that investment is raised in parallel as the user base approaches this number, not promised on a calendar quarter.

---

## PART IV — 🔮 BEYOND THE ROADMAP: WHERE THE DATA FLYWHEEL LEADS

Everything in Part III is direct monetization of infrastructure Sembramos already has — the marketplace and, once built, the farm-data layer. The two ideas below go one step further: how the same trust and data assets could extend the platform's reach. Neither is sequenced into a numbered phase and neither has a committed timeline.

### 14. 🔮 Direct connection to end retailers and merchants

**Concept:** today, Sembramos's marketplace connects farmers to intermediaries and wholesale buyers ("ferieros"). The next logical extension downstream is connecting demand further — to supermarket chains and large-scale end buyers who today source through several layers of intermediation they don't fully see through. This doesn't remove the intermediary layer already using Sembramos; it adds a new, larger class of buyer onto the same reference-price infrastructure everyone already trusts.

**What this requires, stated precisely:** the reference price has to be segmented by trade tier, not extended from the single blended median described in Part II, Section 7 — a farmer-to-buyer price and an intermediary-to-wholesale-buyer resale price shown as two distinct, clearly labeled numbers, never pooled. The data needed already exists (every account's role is already recorded), so this is a filtering change to an existing calculation, not new infrastructure. It also clarifies what monetizes this extension: a large institutional buyer is exactly the counterparty that benefits most from Phase 2's escrow protection (Section 13) once it exists — this extension is a natural fit for that stage rather than something needing its own separate fee mechanism.

### 15. 🔮 From individual advice to national-scale agricultural planning

Part II, Section 8 described what the AI planting-guidance layer already does today, for one farmer at a time. The longer-term vision: the same aggregated, anonymized data that powers one farmer's recommendation today could, at national scale, inform real crop-diversification patterns across regions — reducing the oversupply-in-one-place, shortage-in-another whiplash that shows up in Sembramos's own price data (the exact $35-vs-$115 gap this document opens with). This is explicitly presented as an extension of something already built and running, not a new, unproven feature category.

---

## PART V — RISKS, STATED HONESTLY

| Risk | Mitigation in place today |
|---|---|
| Founder solo, unlimited personal liability (sole proprietorship structure) | Under active evaluation: forming a dedicated legal entity for Sembramos, separate from the founder's other commercial activity |
| Low current transaction volume (10 real contracts, 1 completed) | Small sample size is stated honestly everywhere in the product itself — no fabricated "real" price is ever shown |
| Regulatory risk if Venezuela regulates digital marketplaces | Roadmap already anticipates a foreign legal entity before any money-holding feature (escrow) launches |
| Founder's citizenship restricts the most accessible US banking routes (see Part III, Section 13) | Multiple jurisdictions under active, parallel research — decision gated on confirmed real-world operability, not assumption |
| Input-credit facilitation means introducing real third-party credit risk and counterparty dependence (Part III, Section 12) | Sembramos never lends directly — only scores and refers to a licensed lending partner; explicitly sequenced behind Phase 1B reaching real data scale, not built prematurely |
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
| **Apollo Agriculture** (Kenya, ongoing) — uses ML credit scoring built from farm data to finance smallholder inputs, repaid from harvest via third-party capital (KES 276M raised May 2026 alone for ~24,000 farmers, $70.3M total raised, backers include SoftBank Vision Fund) | Confirms data-driven input-credit is a real, fundable model — and that the capital comes from investors/lenders, never the platform's own balance sheet |
| **Fina** (Venezuela, 2024–2026) — B2B SaaS for Venezuelan SMBs, raised a **$1M seed round in August 2026** with 5,000+ active businesses and ~$2M ARR at the time of raising | The closest real comparable to Sembramos: same country, same early stage, proof that real capital is actively funding Venezuelan SaaS *right now* |
| **Cashea** (Venezuela, 2024–2026) — BNPL fintech, raised **$100M total** ($40M Series A March 2026 + $60M Series B June 2026), 10M+ consumer accounts | Confirms Venezuela itself is investable again in 2026, at meaningfully larger scale than a seed round |

Sources: [Fina raises $1M seed — LatamList](https://latamlist.com/venezuelan-fintech-fina-raises-1m-seed-round/) · [Cashea secures $100M — Crowdfund Insider](https://www.crowdfundinsider.com/2026/07/293776-venezuelan-fintech-cashea-secures-100m-from-global-backers-amid-credit-revival/) · [Apollo Agriculture and Kaleidofin close Kenya's first private-sector agri securitisation](https://tech-ish.com/2026/05/06/apollo-kaleidofin-agri-securitisation-kenya/) · [How Binance handles account deletion](https://www.binance.com/en/support/faq/how-to-delete-my-binance-account-f02c2640a1cd44e58de68e4a49d599f6) · [Mercury Bank Account Closures — prohibited countries/citizenships](https://www.growthhq.io/our-thinking/mercury-bank-account-closures-2024-2025-compliance-risks-regional-impacts-and-critical-steps-for-businesses-in-prohibited-countries)

---

## PART VII — WHERE SEMBRAMOS IS RIGHT NOW, HONESTLY

This project started in May 2026 — before the founder knew the Build with Gemini XPRIZE existed — as a direct response to a real problem in his own community. It has been built, largely solo, through near-daily iteration: **568 commits since June 13, 2026**, each one reviewed for correctness before shipping, with a documented history of finding and closing real security and product bugs before they reached users.

**How Sembramos grows today, stated plainly:** everything to date has been self-funded and self-managed — no government program, no accelerator, no formal grant. Distribution runs on two channels: organic word of mouth from real users who know the founder directly, and social media, where daily feedback and comments are the actual mechanism through which a Venezuelan farmer learns Sembramos exists. Direct outreach to established regional organizations is also underway — an initial conversation with Integración Nacional de Agroproductores de Venezuela, with a follow-up call planned to walk through how the app works, aimed at eventually introducing it to their own network of farmers. No formal partnership or endorsement exists yet; this is stated as exactly what it is — a real, early conversation, not a signed relationship.

The single most consequential open question right now isn't technical — it's structural: whether to formalize Sembramos as its own legal entity (separate finances, limited liability, a clean story for any future investor) versus continuing to build under the founder's existing sole-proprietorship registration, which is also used for unrelated commercial activity. Both the entity's jurisdiction (Part III, Section 13) and this broader question are being researched deliberately, not rushed — consistent with how the rest of this project has been built.

---

**Contact:** angasamocris@gmail.com
**Production app:** https://sembramos.lat
**This document:** public, hosted on GitHub

*Prepared with assistance from Claude Code (Anthropic).*
*Sembramos — August 2026*
