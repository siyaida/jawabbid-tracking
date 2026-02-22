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

### Phase 1: Foundation (Current)
> **Status: Complete**

- [x] Real-time event capture (mouse, clicks, scroll, sections, hovers, visibility)
- [x] Firebase Realtime DB storage
- [x] Login-gated proposal access
- [x] Admin dashboard with charts and heatmaps
- [x] Session management and live indicators
- [x] Canvas 2D visualizations (no external chart libs)

---

### Phase 2: Multi-Proposal Analytics
> **Goal: Compare engagement across multiple prospects and proposals**

- [ ] **Proposal Registry** — Dashboard to manage multiple proposal deployments from one admin panel
- [ ] **Cross-Proposal Metrics** — Compare avg. time, scroll depth, click density across different prospects
- [ ] **Engagement Score** — Weighted composite score (0-100) based on:
  - Time spent (30%)
  - Scroll depth (20%)
  - Section coverage (20%)
  - Return visits (15%)
  - Interaction density (15%)
- [ ] **Prospect Leaderboard** — Rank all active prospects by engagement score
- [ ] **Funnel View** — Visualize how many prospects reach each section of your proposal

---

### Phase 3: Predictive Intelligence
> **Goal: Use historical data to predict close probability**

- [ ] **Close Probability Model** — Train on historical engagement data + deal outcomes:
  ```
  Input:  { avgTimeOnSite, scrollDepth, returnVisits, sectionCoverage, hoverDensity }
  Output: { closeProbability: 0.73, confidence: "high" }
  ```
- [ ] **Engagement Decay Curve** — Detect when prospect interest is fading (decreasing session lengths, longer gaps between visits)
- [ ] **Optimal Follow-Up Timing** — Analyze when prospects are most active to recommend the best time to call
- [ ] **Section Impact Analysis** — Correlate which sections high-converters spend time on vs. lost deals
- [ ] **Anomaly Detection** — Alert when a prospect shows unusual behavior (e.g., sudden deep engagement after weeks of silence = competitor comparison)

---

### Phase 4: CRM & Workflow Integration
> **Goal: Turn insights into automated actions**

- [ ] **HubSpot / Salesforce Sync** — Push engagement scores and events to CRM contact records
- [ ] **Automated Follow-Up Triggers**:
  - Prospect views proposal 3+ times → Auto-create follow-up task
  - Engagement score > 70 → Notify sales team on Slack
  - Prospect shares with new device → Flag "internal review stage"
- [ ] **Email Sequences** — Trigger personalized emails based on which sections they focused on:
  - Heavy time on AI Pipeline → Send AI capabilities case study
  - Skipped pricing → Send ROI calculator
  - Deep-read everything → Send contract/next-steps
- [ ] **Meeting Prep Briefs** — Auto-generate a one-pager before your follow-up call:
  ```
  PROSPECT: Acme Corp (CEO: John)
  ENGAGEMENT SCORE: 82/100 (High)
  SESSIONS: 4 visits over 3 days
  TOP SECTIONS: AI Pipeline (4m 12s), Features (3m 45s)
  SKIPPED: Risks, File Matrix
  INSIGHT: Prospect is technically focused. Emphasize AI capabilities.
  RECOMMENDED APPROACH: Technical deep-dive, skip risk mitigation talk.
  ```

---

### Phase 5: A/B Testing & Optimization
> **Goal: Continuously improve proposal effectiveness**

- [ ] **Section Reordering** — Test different section orders and measure which flow retains attention longer
- [ ] **Content Variants** — A/B test different headlines, visuals, or data presentations
- [ ] **Layout Experiments** — Compare single-page scroll vs. multi-page navigation
- [ ] **Personalization Engine** — Dynamically adjust proposal content based on prospect industry/role:
  - CEO → Lead with ROI and business impact
  - CTO → Lead with architecture and tech stack
  - CFO → Lead with cost savings and timeline
- [ ] **Conversion Benchmarks** — Establish baseline metrics and track improvement over time:
  ```
  Avg. Engagement Score:  45 → 68 (+51%)
  Avg. Time on Proposal:  2m 30s → 6m 15s (+150%)
  Close Rate:             18% → 34% (+89%)
  ```

---

### Phase 6: AI-Powered Deal Intelligence
> **Goal: AI copilot for proposal strategy**

- [ ] **Natural Language Insights** — Ask questions about your prospects:
  ```
  "Which prospects showed the most engagement this week?"
  "What sections should I emphasize for enterprise clients?"
  "Compare engagement between Acme Corp and Beta Inc proposals"
  ```
- [ ] **Proposal Generation** — AI drafts new proposals based on what historically converts:
  - Analyze top-performing proposal structures
  - Auto-suggest section ordering
  - Generate content tailored to prospect profile
- [ ] **Competitive Intelligence** — Detect patterns that indicate competitor comparison:
  - Short, scanning sessions = comparing alternatives
  - Focus on differentiators = evaluating your unique value
  - Multiple stakeholders = formal evaluation process
- [ ] **Revenue Forecasting** — Predict quarterly revenue based on pipeline engagement data

---

## Key Metrics & KPIs

Once you accumulate data across multiple proposals, track these metrics:

| Metric | Formula | Target |
|---|---|---|
| **Engagement Score** | Weighted composite of all signals | > 65/100 |
| **Proposal View Rate** | Sessions / Links Sent | > 80% |
| **Deep Read Rate** | Sessions with >80% scroll / Total Sessions | > 40% |
| **Return Rate** | Prospects with 2+ sessions / Total Prospects | > 30% |
| **Time to First View** | Time between sending link and first session | < 24 hours |
| **Attention Span** | Average active time per session | > 4 minutes |
| **Section Completion** | % of sections viewed per session | > 70% |
| **Engagement-to-Close** | Close rate for high-engagement prospects | Track & improve |

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
