# ProposalTrack

**Real-time user behavior intelligence for high-stakes proposal delivery.**

[![Deploy with Netlify](https://www.netlify.com/img/deploy/button.svg)](#deployment)
[![Firebase](https://img.shields.io/badge/Firebase-Realtime%20DB-FFCA28?logo=firebase&logoColor=black)](#)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](#license)

---

## The Problem

You spend days crafting the perfect tech proposal. You deploy it as a beautiful interactive website. You send the link to the CEO.

Then — **silence**.

- Did they open it?
- Which sections captured their attention?
- Did they skip your pricing? Your timeline?
- Did they share it with their team?
- Are they comparing you against a competitor right now?

**You're flying blind in the most critical moment of your sales cycle.**

## The Solution

ProposalTrack embeds invisible, comprehensive behavioral tracking into your proposal websites. Every mouse movement, click, scroll, and section view is captured in real-time — giving you **unprecedented visibility into prospect engagement before the follow-up call**.

```
Prospect opens your proposal
        ↓
  Every interaction captured silently
        ↓
  Real-time data flows to Firebase
        ↓
  Admin dashboard shows engagement live
        ↓
  You walk into the meeting knowing exactly
  what they care about
```

---

## Key Features

### Event Capture Engine
| Event Type | Data Captured | Sampling |
|---|---|---|
| **Mouse Movement** | x/y coordinates, viewport %, timestamp | 200ms sample, 2s batch |
| **Clicks** | Element, text, position, section context | Every click |
| **Scroll Depth** | Scroll %, direction, active section | 300ms throttle |
| **Section Views** | Enter/exit timestamps, time spent | IntersectionObserver |
| **Element Hover** | Target element, duration, section | >300ms threshold |
| **Navigation** | Nav link clicked, target section | Every nav click |
| **Tab Visibility** | Active/hidden state changes | Every change |
| **Session Meta** | User agent, screen size, viewport, active time | On session start |

### Admin Dashboard
- **Session List** — All sessions with user, start time, duration, live/ended status
- **Live Indicator** — See when a prospect is viewing your proposal right now
- **Time Per Section** — Horizontal bar chart showing where attention was spent
- **Scroll Depth Over Time** — Line chart tracking engagement progression
- **Click Map** — Visual dot map of all click positions on the viewport
- **Mouse Heatmap** — Density visualization of cursor movement patterns
- **Navigation Path** — Breadcrumb trail showing the prospect's journey
- **Event Timeline** — Chronological feed of every interaction with details

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    PROPOSAL WEBSITE                      │
│                     (index.html)                         │
│                                                          │
│  ┌──────────┐  ┌──────────────────────────────────────┐  │
│  │  Login   │→ │         Tracking Module               │  │
│  │  Gate    │  │                                        │  │
│  └──────────┘  │  Mouse ─┐                             │  │
│                │  Clicks ─┤                             │  │
│                │  Scroll ─┼→ Firebase Realtime DB ──┐   │  │
│                │  Hover  ─┤      (Cloud)            │   │  │
│                │  Tabs   ─┘                         │   │  │
│                └────────────────────────────────────│───┘  │
└─────────────────────────────────────────────────────│─────┘
                                                      │
┌─────────────────────────────────────────────────────│─────┐
│                   ADMIN DASHBOARD                    │     │
│                    (admin.html)                       │     │
│                                                      ▼     │
│  ┌──────────┐  ┌──────────────────────────────────────┐    │
│  │  Admin   │→ │  Session List │ Charts │ Heatmaps    │    │
│  │  Login   │  │  Timeline     │ Paths  │ Click Maps  │    │
│  └──────────┘  └──────────────────────────────────────┘    │
└────────────────────────────────────────────────────────────┘
```

### Firebase Data Structure
```
jawabid-tracking/
  sessions/
    {sessionId}/
      user: "prospect_name"
      startTime: 1708617600000
      endTime: 1708618200000
      activeTime: 540000
      status: "active" | "ended"
      userAgent: "Mozilla/5.0 ..."
      screenSize: "1920x1080"
      viewportSize: "1440x900"
      url: "https://your-site.netlify.app"
      events/
        {eventId}/
          type: "click" | "scroll" | "mouse_batch" | "section_enter" | ...
          timestamp: 1708617645000
          data: { ...event-specific payload }
```

### Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend | Vanilla HTML/CSS/JS | Zero build step, instant deploy |
| 3D Background | Three.js v0.160.0 | Immersive proposal experience |
| Database | Firebase Realtime DB | Real-time sync, free tier, no backend |
| Charts | Canvas 2D API | Zero dependencies, full control |
| Hosting | Netlify | One-click deploy, free SSL, custom domains |

**Total dependencies: 2** (Three.js + Firebase SDK). No build tools. No npm. No framework overhead.

---

## Installation

### Prerequisites
- A [Firebase](https://console.firebase.google.com) account (free Spark plan works)
- A [GitHub](https://github.com) account
- A [Netlify](https://netlify.com) account (free tier)

### Step 1: Clone the Repository

```bash
git clone https://github.com/siyaida/jawabbid-tracking.git
cd jawabbid-tracking
```

### Step 2: Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Click **Add Project** → Name it (e.g., `proposal-tracking`) → Create
3. Navigate to **Build → Realtime Database → Create Database**
4. Select your region → Start in **test mode** (you'll secure it later)
5. Go to **Project Settings (gear icon) → General → Your apps → Web (</> icon)**
6. Register your app → Copy the `firebaseConfig` object

### Step 3: Add Your Firebase Config

Create a `firebase-config.js` file in the project root (this file is gitignored and will never be committed):

```javascript
// firebase-config.js
window.FIREBASE_CONFIG = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT-default-rtdb.firebaseio.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.firebasestorage.app",
  messagingSenderId: "000000000000",
  appId: "YOUR_APP_ID"
};
```

Both `index.html` and `admin.html` automatically load this file. Your credentials stay local and out of version control.

### Step 4: Set Login Credentials

Default credentials are set in the source code. To change them:

**`index.html`** — Prospect login:
```javascript
// Search for VALID_CREDS
const VALID_CREDS = { user: simpleHash('ceo'), pass: simpleHash('jawabid') };
//                                   ^^^                        ^^^^^^^
//                             change these to your desired credentials
```

**`admin.html`** — Admin login:
```javascript
const ADMIN_CREDS = { user: simpleHash('admin'), pass: simpleHash('admin2026') };
```

### Step 5: Deploy to Netlify

**Option A: Git-based deploy (recommended)**
1. Push your configured repo to GitHub
2. Go to [Netlify](https://app.netlify.com) → **Add new site → Import from Git**
3. Select your repo → Deploy

**Option B: Drag & drop**
1. Go to [Netlify](https://app.netlify.com) → **Add new site → Deploy manually**
2. Drag the project folder into the browser

### Step 6: Secure Firebase (Production)

Replace your Realtime Database rules with:
```json
{
  "rules": {
    "jawabid-tracking": {
      "sessions": {
        ".write": true,
        ".read": true,
        "$sessionId": {
          ".write": true,
          ".read": true
        }
      }
    }
  }
}
```

> For tighter security, restrict `.read` to authenticated admin users only using Firebase Auth.

### Step 7: Send to Your Prospect

```
https://your-site.netlify.app          → Prospect views the proposal
https://your-site.netlify.app/admin    → You monitor their engagement
```

---

## Usage

### For Each New Proposal

1. **Fork or duplicate** this repo
2. **Replace** `index.html` content with your new proposal
3. **Keep** the Firebase SDK, login overlay, and tracking module intact
4. **Update** credentials for the new prospect
5. **Deploy** to a new Netlify site (e.g., `acme-proposal.netlify.app`)
6. **Send** the link and monitor engagement on `/admin.html`

### Reading the Dashboard

| Signal | What It Means | Action |
|---|---|---|
| Session > 5 min | High engagement | Prospect is seriously evaluating |
| Multiple sessions | Returning interest | They're comparing or sharing internally |
| Deep scroll (>80%) | Read everything | Ready for follow-up |
| Scroll < 30% | Lost interest early | Rethink your opening sections |
| Heavy time on pricing | Cost-conscious buyer | Prepare ROI justification |
| Skipped risks section | Not concerned about risk | Focus on value, not safety |
| Tab switches | Distracted or comparing | Keep proposals concise |
| Different user agents | Shared with team | Multiple stakeholders involved |

---

## Roadmap: From Tracking to Business Intelligence

> Every proposal you send feeds a growing intelligence layer. Behavioral data from prospects is crossed with the actual content they engaged with, enriched by your knowledge base, and processed by AI agents to produce actionable business development recommendations — compounding your closing odds with every deal.

### Phase 1: Behavioral Capture (Current)
> **Status: Complete**

- [x] Real-time event capture (mouse, clicks, scroll, sections, hovers, visibility)
- [x] Firebase Realtime DB storage
- [x] Login-gated proposal access
- [x] Admin dashboard with charts and heatmaps
- [x] Session management and live indicators
- [x] Canvas 2D visualizations (no external chart libs)

**Data layer established.** Every future phase builds on this foundation.

---

### Phase 2: Content-Behavior Correlation Engine
> **Goal: Cross behavioral signals with the actual proposal content to understand *what* resonates, not just *where* they looked**

- [ ] **Section Content Mapping** — Tag each proposal section with metadata (topic: `pricing`, `technical`, `timeline`, `risk`, `team`, `case-study`) so behavioral data is semantically meaningful
- [ ] **Content Engagement Matrix** — Cross time-per-section with content type:
  ```
  ┌─────────────────┬──────────┬──────────┬────────────┐
  │ Content Type     │ Avg Time │ Re-reads │ Conversion │
  ├─────────────────┼──────────┼──────────┼────────────┤
  │ AI Capabilities  │ 4m 12s   │ 2.3x     │ 72%        │
  │ Pricing/ROI      │ 2m 45s   │ 1.8x     │ 65%        │
  │ Technical Arch   │ 3m 30s   │ 1.1x     │ 58%        │
  │ Timeline         │ 1m 05s   │ 0.4x     │ —          │
  │ Risk Mitigation  │ 0m 42s   │ 0.2x     │ —          │
  └─────────────────┴──────────┴──────────┴────────────┘
  ```
- [ ] **Engagement Score** — Weighted composite score (0-100):
  - Active time spent (30%) + Scroll depth (20%) + Section coverage (20%) + Return visits (15%) + Interaction density (15%)
- [ ] **Multi-Proposal Registry** — Manage all active proposals from one admin panel, compare engagement across prospects
- [ ] **Prospect Leaderboard** — Rank pipeline by engagement score, surface deals most likely to close

---

### Phase 3: Knowledge Base & RAG Layer
> **Goal: Build a retrieval-augmented knowledge base from all past proposals, deal outcomes, and engagement data**

- [ ] **Proposal Knowledge Store** — Index all past proposals into a vector database (ChromaDB / Pinecone):
  - Section content, structure, ordering, visuals used
  - Engagement data per section per prospect
  - Deal outcome (won/lost/stalled) linked to each proposal
- [ ] **Engagement Pattern Library** — Store and retrieve behavioral archetypes:
  ```
  PATTERN: "The Deep Diver"
  BEHAVIOR: 8+ min session, >90% scroll, heavy hover on technical sections
  HISTORICAL OUTCOME: 68% close rate
  RECOMMENDED ACTION: Send technical deep-dive follow-up within 24h

  PATTERN: "The Scanner"
  BEHAVIOR: <2 min session, <40% scroll, jumps directly to pricing
  HISTORICAL OUTCOME: 12% close rate
  RECOMMENDED ACTION: Reframe value prop, lead with ROI in follow-up
  ```
- [ ] **Industry & Company Enrichment** — Cross prospect identity with external data:
  - Company size, sector, tech stack, recent funding, hiring signals
  - Enrich from LinkedIn, Crunchbase, or CRM records
  - Feed into proposal personalization and agent reasoning
- [ ] **Win/Loss Knowledge Graph** — Map relationships between proposal attributes, engagement patterns, and outcomes:
  - Which content types convert in which industries
  - Which objections appear in lost deals (inferred from skipped sections)
  - Which proposal lengths and structures perform best by company size

---

### Phase 4: AI Agent System
> **Goal: Deploy specialized agents that consume behavioral data + knowledge base to produce business development recommendations**

- [ ] **Agent 1: Engagement Analyst** — Processes raw tracking data into structured insights
  - Classifies prospect into behavioral archetype (Deep Diver, Scanner, Returner, Sharer, etc.)
  - Detects engagement decay or acceleration curves
  - Flags anomalies (sudden re-engagement, new device = shared internally, late-night viewing = personal interest)
  ```
  INPUT:  Raw Firebase session data
  OUTPUT: { archetype, engagementScore, trend, anomalies[], topSections[], skippedSections[] }
  ```

- [ ] **Agent 2: Content Strategist (RAG-powered)** — Retrieves similar past proposals from the knowledge base and recommends what to emphasize
  - Queries: "What content converted prospects with similar engagement patterns?"
  - Retrieves winning proposal structures from the vector store
  - Recommends which sections to expand, reorder, or cut for the follow-up
  ```
  INPUT:  Engagement analysis + prospect profile + knowledge base
  OUTPUT: { contentRecommendations[], sectionPriority[], talkingPoints[] }
  ```

- [ ] **Agent 3: Deal Advisor** — Synthesizes everything into a closing strategy
  - Predicts close probability based on engagement data crossed with historical outcomes
  - Recommends optimal follow-up timing, channel (call/email/meeting), and approach
  - Generates a pre-meeting brief with prospect-specific talking points
  - Surfaces objection risks based on skipped/low-engagement sections
  ```
  INPUT:  Engagement analysis + content strategy + CRM context + knowledge base
  OUTPUT: {
    closeProbability: 0.73,
    recommendedAction: "Schedule technical demo within 48h",
    talkingPoints: ["Emphasize AI pipeline — 4m 12s engagement", "Address pricing — they revisited 3x"],
    objectionRisks: ["Timeline concerns — skipped after 8s", "Team size — never viewed"],
    optimalTiming: "Tuesday 10am (their peak activity window)"
  }
  ```

- [ ] **Agent 4: Proposal Architect (RAG-powered)** — Generates new proposals informed by what historically converts
  - Retrieves top-performing section structures from past wins
  - Adapts content ordering based on prospect industry and role
  - Auto-generates first drafts with sections weighted by predicted engagement
  - Learns from every new deal outcome to refine recommendations

- [ ] **Agent Orchestrator** — Chains all agents in sequence, triggered automatically:
  - On new session → Agent 1 runs → triggers Agent 2 → feeds Agent 3
  - On deal outcome logged → updates knowledge base → improves all agents

---

### Phase 5: CRM Integration & Automated Workflows
> **Goal: Push intelligence into your existing sales tools and automate the follow-up cycle**

- [ ] **HubSpot / Salesforce Sync** — Push engagement scores, behavioral archetypes, and agent recommendations to CRM contact records
- [ ] **Automated Follow-Up Triggers**:
  - Engagement score > 70 → Notify sales team on Slack with agent-generated brief
  - Prospect returns 3+ times → Auto-create follow-up task with recommended approach
  - New device detected → Flag "internal review stage" in CRM
  - Engagement decay detected → Trigger re-engagement email sequence
- [ ] **Smart Email Sequences** — AI-drafted follow-ups based on what they engaged with:
  - Heavy time on AI capabilities → Send technical case study
  - Revisited pricing 3x → Send ROI calculator with competitive comparison
  - Deep-read everything → Send contract / next-steps / scheduling link
  - Skipped risk section → Proactively address risk in follow-up (don't wait for objection)
- [ ] **Meeting Prep Briefs** — Auto-generated before every follow-up:
  ```
  ┌─────────────────────────────────────────────────────────┐
  │  DEAL INTELLIGENCE BRIEF — Acme Corp                    │
  ├─────────────────────────────────────────────────────────┤
  │  PROSPECT:        John Smith, CEO                       │
  │  ENGAGEMENT:      82/100 (High) — "Deep Diver"          │
  │  SESSIONS:        4 visits over 3 days                  │
  │  CLOSE PROBABILITY: 73%                                 │
  │                                                         │
  │  TOP SECTIONS:    AI Pipeline (4m 12s), Features (3m 45s)│
  │  REVISITED:       Pricing (3x), Team section (2x)       │
  │  SKIPPED:         Risk Mitigation, File Matrix          │
  │                                                         │
  │  SIMILAR WINS:    Beta Corp (closed $120K, same pattern) │
  │  SIMILAR LOSSES:  Gamma Inc (stalled at pricing stage)  │
  │                                                         │
  │  RECOMMENDED APPROACH:                                  │
  │  → Lead with technical demo (AI pipeline focus)         │
  │  → Address pricing proactively — they're comparing      │
  │  → Skip risk section — low concern signal               │
  │  → Bring CTO to call — technical depth matters to them  │
  │                                                         │
  │  OPTIMAL TIMING:  Tuesday 10am (peak activity window)   │
  └─────────────────────────────────────────────────────────┘
  ```

---

### Phase 6: Continuous Optimization & Revenue Intelligence
> **Goal: Every proposal makes the next one better. Every deal outcome sharpens the model.**

- [ ] **A/B Testing Engine** — Test section order, content variants, and layouts. Measure which version drives higher engagement and close rate
- [ ] **Personalization Engine** — Dynamically adjust proposal content at load time based on prospect profile:
  - CEO → Lead with ROI, business impact, strategic vision
  - CTO → Lead with architecture, tech stack, integration depth
  - CFO → Lead with cost savings, timeline efficiency, risk mitigation
- [ ] **Competitive Intelligence Detection** — Behavioral patterns that signal competitor evaluation:
  - Short scanning sessions = comparing alternatives
  - Focus on differentiators = evaluating unique value
  - Multiple stakeholders in quick succession = formal evaluation committee
- [ ] **Revenue Forecasting** — Predict quarterly revenue from pipeline engagement data:
  ```
  Pipeline:    12 active proposals
  High intent: 5 prospects (engagement > 70)
  Predicted:   $340K weighted revenue (next 30 days)
  At risk:     2 deals showing engagement decay — trigger re-engagement
  ```
- [ ] **Closing Feedback Loop** — Log every deal outcome (won/lost/amount/reason) back into the knowledge base. The system learns:
  - Which behavioral patterns predict wins vs. losses
  - Which content types drive conversion by industry
  - Which follow-up timing and approaches close fastest
  - **Every deal you run through this system increases the intelligence available to the next one**

---

## Key Metrics & KPIs

| Metric | Formula | Target |
|---|---|---|
| **Engagement Score** | Weighted composite of all behavioral signals | > 65/100 |
| **Proposal View Rate** | Sessions / Links Sent | > 80% |
| **Deep Read Rate** | Sessions with >80% scroll / Total Sessions | > 40% |
| **Return Rate** | Prospects with 2+ sessions / Total Prospects | > 30% |
| **Time to First View** | Time between sending link and first session | < 24 hours |
| **Attention Span** | Average active time per session | > 4 minutes |
| **Section Completion** | % of sections viewed per session | > 70% |
| **Content-to-Close Correlation** | Which content types drive highest close rate | Track per industry |
| **Agent Recommendation Accuracy** | % of agent-suggested actions that led to positive outcome | > 60% |
| **Engagement-to-Close** | Close rate for high-engagement (>70) prospects | Track & improve |
| **Intelligence Compound Rate** | Improvement in close rate per 10 proposals processed | Positive trend |

---

## Project Structure

```
jawabbid-tracking/
├── index.html          # Proposal website + login gate + tracking module
├── admin.html          # Admin dashboard + session viewer + visualizations
├── firebase-config.js  # Your Firebase credentials (gitignored)
├── .gitignore          # Keeps firebase-config.js out of version control
└── README.md           # Documentation
```

No build step. No `node_modules`. No framework config. **Two HTML files + one config. That's it.**

---

## Security Considerations

| Concern | Mitigation |
|---|---|
| Firebase API key exposed in client | Firebase API keys are safe to expose — security is enforced by Realtime DB rules, not the key |
| Client-side credential check | Simple hash comparison — sufficient for gating access, not for protecting secrets |
| Tracking data privacy | Only tracks interaction patterns, no PII beyond username. Compliant with legitimate business interest |
| Admin dashboard access | Protected by separate admin credentials. For production, migrate to Firebase Auth |

For production deployments, consider:
- Firebase Auth for proper admin authentication
- Realtime DB security rules restricting read access
- Custom domain with SSL (free on Netlify)
- GDPR compliance notice if operating in EU markets

---

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/engagement-scoring`)
3. Commit your changes (`git commit -m "Add engagement scoring algorithm"`)
4. Push to the branch (`git push origin feature/engagement-scoring`)
5. Open a Pull Request

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

<p align="center">
  <strong>Stop guessing. Start knowing.</strong><br>
  <em>Every click tells a story. Every scroll reveals intent. Every second of attention is a signal.</em><br><br>
  Built for sales teams who refuse to fly blind.
</p>
