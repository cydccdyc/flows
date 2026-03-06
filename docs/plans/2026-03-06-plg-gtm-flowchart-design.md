# PLG GTM Flowchart — Design Doc
Date: 2026-03-06

## Purpose
A second flowchart for the GTM team (SDRs and Sales) that explains how Discover PLG leads come in and what happens to them. Companion to `govdash-plg-flow.html` (engineering/ops view). This file is the SDR/sales-facing view.

## File
`wireframes/govdash-plg-gtm.html` — same repo, same Netlify deploy.

## Audience
SDRs and Sales. Language should be plain and action-oriented. No scoring math, no routing logic complexity.

## Visual Format
- Identical design language to `govdash-plg-flow.html` (CSS variables, fonts, Firebase sync, click-to-edit)
- Firebase path: `gtm-flow/fields` (separate from PLG flow's `pls-flow/fields`)
- Full-width linear layout, Approach A

---

## Phase 1 — Main Flow (5 Steps)

### STEP 1 · GovDash Discover Platform
- Node color: Blue (discover)
- Badge: PRODUCT
- Content: What Discover is — freemium gov contracting intelligence platform
- Feature chips: Solicitation Search, Pipeline Tracking, Team Collaboration, Add to Pipeline

### STEP 2 · User Signs Up → HubSpot Contact Created
- Node color: Orange (hubspot)
- Badge: CRM SYNC
- Content: On signup, contact is instantly created in HubSpot
- Fields synced as chips: First Name, Last Name, Email, Discover User ID, Created At
- Ongoing sync callout: **Last Login At** — refreshed on every login, SDRs can use for engagement recency

### STEP 3 · "Talk to Sales" Trigger
- Node color: Red/hot (custom — #DC2626 accent)
- Badge: TRIGGER
- Content: User explicitly clicks "Talk to Sales" CTA in the product
- Critical callout box: **Every click fires the workflow independently** — no deduplication. Same prospect clicking 3× = 3 notifications. SDRs should expect repeat alerts.

### STEP 4 · SDR Round Robin Assignment
- Node color: Green (routing)
- Badge: ROUTING
- Content: Next available SDR assigned via Round Robin. Ownership set on contact + company in HubSpot.

### STEP 5 · Automated Actions (parallel)
- Two side-by-side cards:
  - **📧 Automated Email** — sent immediately from the assigned SDR's name/email address. Prospect sees it as a personal outreach from their SDR.
  - **💬 Slack Notification** — fires to `#sdr-inbound-leads` on every "Talk to Sales" click. SDRs should monitor this channel actively.

---

## Phase 2 — Preview Section (dimmed, 65% opacity)

- Visual treatment: 65% opacity overlay, "PHASE 2 · COMING SOON" badge, dashed border
- Not interactive — just informational

### Content
- **Weekly Activity Signal Sync** — Discover → HubSpot on a weekly cadence
- Signals tracked (Weekly + Total counts):
  1. Add to Pipeline
  2. Solicitation Likes
  3. Solicitation Dislikes
  4. Clicks
  5. Search Impressions
  6. Invitees added to team
- **Lead Intent Score** — weighted sum of signals
- **Threshold check** → Route to SDR/AE (high intent) or Nurture sequence (low intent)
- Footer note: "Full rollout presentation coming when Phase 2 is ready"

---

## Technical Notes
- Copy all base CSS from `govdash-plg-flow.html`
- Firebase: same config, path = `gtm-flow/fields`
- Include universal click-to-edit (same JS pattern)
- No loop-back SVG lines needed (linear flow only)
- Header tags: "GTM VIEW" + "PHASE 1 · LIVE"
