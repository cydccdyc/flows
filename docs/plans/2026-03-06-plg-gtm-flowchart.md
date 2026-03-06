# PLG GTM Flowchart Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Create `wireframes/govdash-plg-gtm.html` — an SDR/sales-facing flowchart showing how Discover PLG leads come in and what happens in Phase 1, with a dimmed Phase 2 preview.

**Architecture:** Single self-contained HTML file. Reuses all CSS from `govdash-plg-flow.html` verbatim (same variables, classes, Firebase, click-to-edit). Adds new classes for the trigger callout, action grid, and Phase 2 dimmed section. Firebase path: `gtm-flow/fields`.

**Tech Stack:** Vanilla HTML/CSS/JS, Firebase Realtime Database (compat v9.23.0), Google Fonts (Lexend Deca + JetBrains Mono)

---

### Task 1: Create the file with head, Firebase config, and all base CSS

**Files:**
- Create: `wireframes/govdash-plg-gtm.html`

**Step 1: Write the file**

Copy the entire `<head>` block from `govdash-plg-flow.html` (Firebase scripts, font links, all CSS through line 310). Change `<title>` to `GovDash Discover — PLG Lead Generation (GTM)`. Keep `DEMO_MODE = false` and same Firebase config.

Add these NEW CSS classes after the existing styles:

```css
/* ─── GTM-SPECIFIC ─── */
/* Trigger / hot node (red) */
.node.trigger { --accent: #DC2626; --muted: #FEF2F2; }
.node.trigger .node-stripe { background: #DC2626; }

/* "Fires every time" callout */
.fires-badge {
  margin-top: 12px; padding: 10px 14px; border-radius: 8px;
  background: #FEF2F2; border: 2px solid #FECACA;
  display: flex; align-items: flex-start; gap: 10px;
}
.fires-badge-icon { font-size: 18px; flex-shrink: 0; line-height: 1; }
.fires-badge-body { flex: 1; }
.fires-badge-title {
  font-family: 'JetBrains Mono', monospace; font-size: 9px;
  font-weight: 700; letter-spacing: 0.1em; color: #DC2626;
  text-transform: uppercase; margin-bottom: 4px;
}
.fires-badge-desc { font-size: 12px; color: #7F1D1D; line-height: 1.5; }

/* Action cards (Step 5 — two parallel) */
.action-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
.action-card {
  border-radius: 10px; border: 1px solid var(--border);
  background: var(--card); overflow: hidden;
  box-shadow: var(--shadow);
}
.action-card-stripe { height: 3px; }
.action-card.email .action-card-stripe { background: #2563EB; }
.action-card.slack .action-card-stripe { background: #7C3AED; }
.action-card-hd {
  padding: 12px 14px 10px; border-bottom: 1px solid var(--border);
  display: flex; align-items: center; gap: 9px;
}
.action-card-icon { font-size: 18px; }
.action-card-title { font-weight: 600; font-size: 13px; color: var(--t1); }
.action-card-body { padding: 13px 14px; font-size: 12px; color: var(--t2); line-height: 1.6; }
.action-card-body strong { color: var(--t1); font-weight: 600; }
.action-note {
  margin-top: 10px; padding: 7px 10px; border-radius: 6px;
  font-size: 11px; line-height: 1.5;
}
.action-note.blue { background: #EFF6FF; border: 1px solid #BFDBFE; color: #1D4ED8; }
.action-note.purple { background: var(--scoring-muted); border: 1px solid #C4B5FD; color: #5B21B6; }

/* Ongoing sync badge (Last Login At) */
.sync-badge {
  display: flex; align-items: flex-start; gap: 9px;
  margin-top: 12px; padding: 9px 12px; border-radius: 7px;
  background: #FFFBEB; border: 1.5px solid #FDE68A;
  font-size: 12px; color: #78350F; line-height: 1.5;
}
.sync-badge-label {
  font-family: 'JetBrains Mono', monospace; font-size: 8px;
  font-weight: 700; letter-spacing: 0.1em; color: #D97706;
  text-transform: uppercase; margin-bottom: 3px;
}

/* ─── PHASE 2 PREVIEW ─── */
.phase2-divider {
  display: flex; align-items: center; gap: 14px;
  margin: 60px 0 32px;
}
.phase2-divider-line { flex: 1; height: 1.5px; background: var(--border); }
.phase2-divider-badge {
  font-family: 'JetBrains Mono', monospace; font-size: 9px;
  font-weight: 700; letter-spacing: 0.12em; padding: 5px 14px;
  border-radius: 20px; background: var(--scoring-muted);
  border: 1.5px solid #C4B5FD; color: var(--scoring);
  white-space: nowrap;
}
.phase2-wrapper {
  opacity: 0.62; pointer-events: none;
  position: relative;
}
.phase2-wrapper::before {
  content: ''; position: absolute; inset: -12px;
  border: 2px dashed #C4B5FD; border-radius: 16px;
  pointer-events: none;
}

/* Signals grid in Phase 2 */
.signals-grid {
  display: grid; grid-template-columns: repeat(3, 1fr);
  gap: 8px; margin-top: 10px;
}
.signal-chip {
  padding: 8px 11px; border-radius: 7px;
  background: var(--scoring-muted); border: 1px solid #C4B5FD;
  font-size: 11px; color: #5B21B6; line-height: 1.4;
}
.signal-chip-lbl {
  font-family: 'JetBrains Mono', monospace; font-size: 7.5px;
  letter-spacing: 0.08em; color: #7C3AED;
  text-transform: uppercase; margin-bottom: 3px;
}

/* Phase 2 routing preview */
.p2-routing { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-top: 12px; }
.p2-branch { padding: 11px 13px; border-radius: 8px; border: 1px solid var(--border); }
.p2-branch.high { background: var(--routing-muted); border-color: #6EE7B7; }
.p2-branch.low  { background: #FFFBEB; border-color: #FDE68A; }
.p2-branch-lbl {
  font-family: 'JetBrains Mono', monospace; font-size: 8px;
  font-weight: 700; letter-spacing: 0.08em; margin-bottom: 5px;
}
.p2-branch.high .p2-branch-lbl { color: #065F46; }
.p2-branch.low  .p2-branch-lbl { color: #D97706; }
.p2-branch-body { font-size: 12px; color: var(--t2); line-height: 1.5; }

@media (max-width: 820px) {
  .action-grid, .signals-grid { grid-template-columns: 1fr; }
  .signals-grid { grid-template-columns: 1fr 1fr; }
  .p2-routing { grid-template-columns: 1fr; }
}
```

**Step 2: Verify** — Open in browser locally. Should render the same dot-grid background and header shell.

**Step 3: Commit**
```bash
git add wireframes/govdash-plg-gtm.html
git commit -m "feat: scaffold GTM flowchart with full CSS"
```

---

### Task 2: Build the header and Step 1 — Discover Platform

**Step 1: Add header HTML**

```html
<header class="hdr">
  <div class="hdr-left">
    <span class="hdr-logo">GovDash</span>
    <span class="hdr-sep">/</span>
    <span class="hdr-title">Discover PLG — Lead Generation</span>
    <span class="hdr-tag">GTM VIEW</span>
    <span class="hdr-tag" style="background:#D1FAE5;color:#065F46;border-color:#6EE7B7">PHASE 1 · LIVE</span>
  </div>
  <div class="hdr-right">
    <div class="live-pill">
      <div class="live-dot" id="liveDot"></div>
      <span id="liveLabel">Connecting…</span>
    </div>
    <button class="save-btn" onclick="saveAll()">Save ⌘S</button>
  </div>
</header>
```

**Step 2: Add Step 1 node inside `<div class="canvas">`**

```html
<div class="step-lbl"><span class="step-num">STEP 1</span>GovDash Discover Platform</div>

<div class="node discover">
  <div class="node-stripe"></div>
  <div class="node-hd">
    <div class="node-hd-left">
      <span class="sys-badge">PRODUCT</span>
      <span class="node-title">GovDash Discover</span>
    </div>
    <span class="status-tag ok">✓ Live</span>
  </div>
  <div class="node-body">
    <div class="fg">
      <div class="fg-lbl">What is Discover?</div>
      <p style="font-size:13px;color:var(--t2);line-height:1.6;margin-bottom:12px">
        A freemium government contracting intelligence platform. Agencies and contractors can search solicitations, track opportunities, and manage their pipeline — all for free. This is GovDash's product-led growth (PLG) motion: prospects experience the product before speaking to sales.
      </p>
    </div>
    <div class="fg">
      <div class="fg-lbl">Key features (signals we track)</div>
      <div class="chips" style="--accent:var(--discover);--muted:var(--discover-muted)">
        <span class="chip"><span class="chip-dot" style="background:var(--discover)"></span>Solicitation Search</span>
        <span class="chip"><span class="chip-dot" style="background:var(--discover)"></span>Add to Pipeline</span>
        <span class="chip"><span class="chip-dot" style="background:var(--discover)"></span>Solicitation Likes / Dislikes</span>
        <span class="chip"><span class="chip-dot" style="background:var(--discover)"></span>Team Collaboration</span>
        <span class="chip"><span class="chip-dot" style="background:var(--discover)"></span>Clicks &amp; Impressions</span>
      </div>
    </div>
  </div>
</div>

<!-- Connector -->
<div class="conn"><div class="conn-line"></div><div class="conn-arrow"></div></div>
```

**Step 3: Commit**
```bash
git add wireframes/govdash-plg-gtm.html
git commit -m "feat: add Step 1 Discover Platform node"
```

---

### Task 3: Step 2 — User Signs Up → HubSpot Contact Created

**Step 1: Add Step 2 HTML**

```html
<div class="step-lbl"><span class="step-num">STEP 2</span>User Signs Up → HubSpot Contact Created</div>

<div class="node hubspot">
  <div class="node-stripe"></div>
  <div class="node-hd">
    <div class="node-hd-left">
      <span class="sys-badge">CRM SYNC</span>
      <span class="node-title">Contact Instantly Created in HubSpot</span>
    </div>
    <span class="status-tag ok">✓ Automated</span>
  </div>
  <div class="node-body">
    <div class="fg">
      <div class="fg-lbl">Fields synced on signup</div>
      <div class="chips" style="--accent:var(--hubspot);--muted:var(--hubspot-muted)">
        <span class="chip"><span class="chip-dot" style="background:var(--hubspot)"></span>First Name</span>
        <span class="chip"><span class="chip-dot" style="background:var(--hubspot)"></span>Last Name</span>
        <span class="chip"><span class="chip-dot" style="background:var(--hubspot)"></span>Email</span>
        <span class="chip"><span class="chip-dot" style="background:var(--hubspot)"></span>Discover User ID</span>
        <span class="chip"><span class="chip-dot" style="background:var(--hubspot)"></span>Created At <span class="chip-type">datetime</span></span>
      </div>
    </div>
    <div class="sync-badge">
      <div>
        <div class="sync-badge-label">🔄 Ongoing Sync</div>
        <strong style="font-size:12px;color:var(--t1)">Last Login At</strong> — refreshed in HubSpot every time the prospect logs into Discover. Use this field to gauge how recently they've been active in the product.
      </div>
    </div>
  </div>
</div>

<!-- Connector -->
<div class="conn"><div class="conn-line"></div><div class="conn-arrow"></div></div>
```

**Step 2: Commit**
```bash
git add wireframes/govdash-plg-gtm.html
git commit -m "feat: add Step 2 HubSpot sync node"
```

---

### Task 4: Step 3 — "Talk to Sales" Trigger

**Step 1: Add Step 3 HTML**

```html
<div class="step-lbl"><span class="step-num">STEP 3</span>"Talk to Sales" — Lead Trigger</div>

<div class="node trigger">
  <div class="node-stripe"></div>
  <div class="node-hd">
    <div class="node-hd-left">
      <span class="sys-badge" style="background:#FEF2F2;color:#DC2626;border-color:#FECACA">TRIGGER</span>
      <span class="node-title">Prospect Clicks "Talk to Sales"</span>
    </div>
    <span class="status-tag ok">✓ Live</span>
  </div>
  <div class="node-body">
    <p style="font-size:13px;color:var(--t2);line-height:1.6;margin-bottom:12px">
      When a prospect clicks the <strong style="color:var(--t1)">"Talk to Sales"</strong> CTA anywhere in the Discover product, the lead workflow fires immediately. This is high-intent — the prospect is explicitly asking to speak with someone.
    </p>
    <div class="fires-badge">
      <div class="fires-badge-icon">⚡</div>
      <div class="fires-badge-body">
        <div class="fires-badge-title">Fires on every click — no deduplication</div>
        <div class="fires-badge-desc">
          Each time a prospect clicks "Talk to Sales," the full workflow triggers independently — regardless of how many times they've clicked before. A prospect clicking 3 times = 3 separate notifications and routing events. <strong>SDRs should expect repeat alerts for the same prospect.</strong>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- Connector -->
<div class="conn"><div class="conn-line"></div><div class="conn-arrow"></div></div>
```

**Step 2: Commit**
```bash
git add wireframes/govdash-plg-gtm.html
git commit -m "feat: add Step 3 Talk to Sales trigger node"
```

---

### Task 5: Step 4 — SDR Round Robin Assignment

**Step 1: Add Step 4 HTML**

```html
<div class="step-lbl"><span class="step-num">STEP 4</span>SDR Round Robin Assignment</div>

<div class="node routing">
  <div class="node-stripe"></div>
  <div class="node-hd">
    <div class="node-hd-left">
      <span class="sys-badge">ROUTING</span>
      <span class="node-title">Next Available SDR Assigned</span>
    </div>
    <span class="status-tag ok">✓ Automated</span>
  </div>
  <div class="node-body">
    <p style="font-size:13px;color:var(--t2);line-height:1.6;margin-bottom:14px">
      The lead is automatically assigned to the next available SDR via Round Robin rotation in HubSpot. Ownership is set on both the <strong style="color:var(--t1)">contact</strong> and <strong style="color:var(--t1)">company</strong> records simultaneously.
    </p>
    <div class="assign-grid">
      <div class="assign-card found">
        <div class="ac-lbl found">Contact already exists</div>
        <div class="ac-body">Previous Discover user signs up again, or contact was in HubSpot from another source. <strong>Ownership updated</strong> to new assigned SDR.</div>
      </div>
      <div class="assign-card">
        <div class="ac-lbl none">Net new contact</div>
        <div class="ac-body">Brand new to HubSpot. <strong>Contact + company created</strong>, ownership assigned to next SDR in rotation.</div>
      </div>
    </div>
  </div>
</div>

<!-- Fork connector → two parallel actions -->
<div class="fork-stem"><div class="fork-stem-line"></div></div>
<div class="fork-bar"></div>
<div class="fork-legs"><div class="fork-leg"></div><div class="fork-leg"></div></div>
```

**Step 2: Commit**
```bash
git add wireframes/govdash-plg-gtm.html
git commit -m "feat: add Step 4 SDR Round Robin assignment node"
```

---

### Task 6: Step 5 — Automated Actions (Email + Slack, parallel)

**Step 1: Add Step 5 HTML**

```html
<div class="step-lbl"><span class="step-num">STEP 5</span>Automated Actions — Fires Simultaneously</div>

<div class="action-grid">

  <!-- Email -->
  <div class="action-card email">
    <div class="action-card-stripe"></div>
    <div class="action-card-hd">
      <div class="action-card-icon">📧</div>
      <div class="action-card-title">Automated Email — From SDR's Name</div>
    </div>
    <div class="action-card-body">
      An email is sent <strong>immediately</strong> upon SDR assignment, appearing to come directly from the assigned SDR's name and email address. The prospect sees a personal outreach, not an automated message.
      <div class="action-note blue" style="margin-top:10px">
        <strong>SDR tip:</strong> This email goes out automatically — you don't need to send it manually. Watch for replies landing in your inbox.
      </div>
    </div>
  </div>

  <!-- Slack -->
  <div class="action-card slack">
    <div class="action-card-stripe"></div>
    <div class="action-card-hd">
      <div class="action-card-icon">💬</div>
      <div class="action-card-title">Slack Notification → #sdr-inbound-leads</div>
    </div>
    <div class="action-card-body">
      A notification fires to <strong>#sdr-inbound-leads</strong> alerting the team to the new inbound lead from Discover. Includes prospect name, company, and a link to the HubSpot contact.
      <div class="action-note purple" style="margin-top:10px">
        <strong>Reminder:</strong> Fires on <em>every</em> "Talk to Sales" click. If you see the same prospect appear multiple times in the channel, that's intentional — they clicked again.
      </div>
    </div>
  </div>

</div>

<!-- Merge connector -->
<div class="merge-legs"><div class="merge-leg"></div><div class="merge-leg"></div></div>
<div class="merge-bar"></div>
<div class="merge-stem"><div class="merge-stem-line"></div></div>

<!-- End of Phase 1 summary -->
<div style="margin-top:16px;padding:14px 18px;border-radius:10px;background:var(--routing-muted);border:1.5px solid #6EE7B7;font-size:13px;color:#065F46;line-height:1.6">
  <strong style="display:block;margin-bottom:4px;font-size:14px">✓ Phase 1 complete</strong>
  SDR is assigned, automated email is sent, and the team is notified in Slack. The lead is now live in HubSpot and ready for follow-up.
</div>
```

**Step 2: Commit**
```bash
git add wireframes/govdash-plg-gtm.html
git commit -m "feat: add Step 5 parallel action cards (email + Slack)"
```

---

### Task 7: Phase 2 preview section (dimmed)

**Step 1: Add Phase 2 HTML**

```html
<!-- ══ PHASE 2 PREVIEW ══ -->
<div class="phase2-divider">
  <div class="phase2-divider-line"></div>
  <div class="phase2-divider-badge">⚗ PHASE 2 · COMING SOON</div>
  <div class="phase2-divider-line"></div>
</div>

<div class="phase2-wrapper">

  <div class="step-lbl" style="margin-top:0"><span class="step-num" style="background:#7C3AED">PHASE 2</span>Weekly Activity Signal Sync</div>

  <div class="node scoring">
    <div class="node-stripe"></div>
    <div class="node-hd">
      <div class="node-hd-left">
        <span class="sys-badge">DATA SYNC</span>
        <span class="node-title">Discover → HubSpot — Weekly Cadence</span>
      </div>
      <span class="status-tag warn">⚗ In Design</span>
    </div>
    <div class="node-body">
      <div class="fg">
        <div class="fg-lbl">Activity signals synced (Weekly count + Total count per prospect)</div>
        <div class="signals-grid">
          <div class="signal-chip"><div class="signal-chip-lbl">Add to Pipeline</div>Weekly + Total</div>
          <div class="signal-chip"><div class="signal-chip-lbl">Solicitation Likes</div>Weekly + Total</div>
          <div class="signal-chip"><div class="signal-chip-lbl">Solicitation Dislikes</div>Weekly + Total</div>
          <div class="signal-chip"><div class="signal-chip-lbl">Clicks</div>Weekly + Total</div>
          <div class="signal-chip"><div class="signal-chip-lbl">Search Impressions</div>Weekly + Total</div>
          <div class="signal-chip"><div class="signal-chip-lbl">Invitees Added</div>Weekly + Total</div>
        </div>
      </div>
    </div>
  </div>

  <div class="conn"><div class="conn-line"></div><div class="conn-arrow"></div></div>

  <div class="step-lbl"><span class="step-num" style="background:#7C3AED">PHASE 2</span>Lead Intent Score → Routing Decision</div>

  <div class="node scoring">
    <div class="node-stripe"></div>
    <div class="node-hd">
      <div class="node-hd-left">
        <span class="sys-badge">SCORING</span>
        <span class="node-title">Intent Score = Weighted Sum of Activity Signals</span>
      </div>
      <span class="status-tag warn">⚗ Weights TBD</span>
    </div>
    <div class="node-body">
      <p style="font-size:13px;color:var(--t2);line-height:1.6;margin-bottom:14px">
        Each signal is assigned a weight. The intent score is calculated weekly for every Discover prospect and written back to HubSpot. A threshold determines whether a lead is ready for sales outreach or should continue in a nurture sequence.
      </p>
      <div class="p2-routing">
        <div class="p2-branch high">
          <div class="p2-branch-lbl">↑ Score ≥ Threshold</div>
          <div class="p2-branch-body">Route to SDR or AE — high-intent prospect is ready for outreach.</div>
        </div>
        <div class="p2-branch low">
          <div class="p2-branch-lbl">↓ Score &lt; Threshold</div>
          <div class="p2-branch-body">Enter nurture email sequence. Re-evaluated weekly.</div>
        </div>
      </div>
      <div style="margin-top:14px;padding:9px 12px;border-radius:6px;background:#F3F4F6;border:1px solid var(--border);font-size:11px;color:var(--t3);font-family:'JetBrains Mono',monospace">
        Full rollout presentation will be shared with the team when Phase 2 is ready. More signals likely to be added.
      </div>
    </div>
  </div>

</div><!-- /phase2-wrapper -->
```

**Step 2: Commit**
```bash
git add wireframes/govdash-plg-gtm.html
git commit -m "feat: add Phase 2 dimmed preview section"
```

---

### Task 8: Add Firebase + click-to-edit scripts, close HTML, deploy

**Step 1: Copy the full `<script>` block from `govdash-plg-flow.html`** — the entire block from `<script>` through `</script>` (Firebase init, DEMO_MODE branch, universal click-to-edit). Change only the Firebase path:

```js
const REF = db.ref('gtm-flow/fields');  // was: pls-flow/fields
```

Add `<div class="toast" id="toast">✓ Saved</div>` before the script.

Close with `</body></html>`.

**Step 2: Update `wireframes/index.html`** — add a card for the new GTM flowchart alongside the existing PLG flow card.

**Step 3: Push to deploy**
```bash
cd "/Users/cydc/GTM Agent/flows"
git add wireframes/govdash-plg-gtm.html wireframes/index.html
git commit -m "feat: complete PLG GTM flowchart for SDR/sales audience"
git push origin main
```

**Step 4: Verify** — Visit Netlify URL `/govdash-plg-gtm.html`. Confirm:
- All 5 steps render correctly
- Phase 2 section is visibly dimmed
- Firebase live dot goes green
- Click on any text → amber edit outline appears
- Cmd+S triggers save toast
