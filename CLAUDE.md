# CLAUDE.md — NextGen Proactive Outreach

## Project Identity

**Tool:** NextGen Proactive Outreach
**Live URL:** https://nextgen-outreach.netlify.app
**Netlify Account:** drdanieltarditi@gmail.com / Project: nextgen-outreach
**Repo:** https://github.com/dtarditi51/proactive-outreach
**Local Path:** ~/Desktop/proactive-outreach/index.html
**Stack:** Single-file vanilla JS/HTML/CSS (index.html, ~1,100 lines)
**Deploy:** Local edit → Dan approves → push to GitHub → Netlify auto-deploys (~30 sec)
**Owner:** Daniel Tarditi, DO, FACC — Partner/COO, The Heart House (CADV); Chief of Cardiology, Jefferson Health NJ; Innovation Committee Chair, CVA USA

---

## Clinical Purpose

A browser-based post-procedure proactive outreach platform for cardiology. Designed for use by nurses at CADV/The Heart House managing Ava-driven automated post-discharge calls for patients following cardiac procedures. The tool supports:

- Post-procedure patient queue management with status classification
- Ava AI call transcript review and red flag escalation
- Unable-to-contact (UTC) tracking and manual nurse escalation
- Nurse disposition documentation and resolution
- Escalation handoff to the Triage Command Center (TCC)
- Weekend discharge priority management

**This is a clinical tool. All logic changes must be clinically validated before deployment.**

---

## Relationship to Triage Command Center

This tool is a **companion** to the NextGen Cardiology Command Center (https://nextgencommand.netlify.app). They share:
- The same user base (nurses at The Heart House / CADV)
- The same patient population (overlapping MRNs in demo data)
- A planned shared state layer via `TriageDataBus` (BroadcastChannel + localStorage)

The "↗ TCC" button in the header and "Open in TCC" escalation buttons are the integration seam. When both tools are open in the same browser, BroadcastChannel enables real-time state sharing. Full integration is a future milestone — current implementation uses toasts as placeholders.

---

## Current Architecture

Single-file monolith — all code lives in `index.html`. No build process, no framework, no external dependencies.

**Layout:** Two-panel SPA — Queue Panel (280px) | Record Panel (flex)

| Lines | Module | Purpose |
|-------|--------|---------|
| 1–402 | CSS | Styling, status colors, layout, animations |
| 403–988 | HTML | Header, queue items, patient record panels, modal |
| 989–1082 | JS | show(), toggle(), filter(), modal, toast, clock |

**Queue status classes:**
- `s-esc` — Escalated (red, Ava red flag fired)
- `s-utc` — Unable to Contact (amber, 3/3 attempts exhausted)
- `s-wknd` — Weekend discharge backlog (purple)
- `s-ok` — Complete (green, outreach sequence closed)
- (no class) — Active / pending

---

## Development Rules

### Surgical Edits Only
- Make the minimum change necessary to accomplish the goal
- Do not refactor, reorganize, or rename anything outside the explicit scope of the request
- Do not change CSS, layout, or visual design unless explicitly instructed
- Do not split the single-file architecture into modules unless explicitly instructed
- Always show diffs and confirm before writing changes to disk

### Clinical Logic Constraints
- Any change to escalation triggers, Ava red flag logic, or UTC thresholds requires explicit confirmation
- Do not alter existing patient record data (MRNs, procedures, dates, call transcripts) without instruction
- Nurse disposition fields are medicolegal records — changes to their structure require explicit approval

### Before Any Session
1. Read this file completely
2. Confirm understanding of the single-file architecture before making edits
3. Do not assume any external APIs, authentication, or backend exist unless explicitly stated

---

## Deploy Workflow

```bash
# Claude Code edits ~/Desktop/proactive-outreach/index.html locally only
# Dan reviews all changes before any git commands are run
# Only after Dan explicitly approves:
git -C ~/Desktop/proactive-outreach add index.html
git -C ~/Desktop/proactive-outreach commit -m "describe the specific change"
git -C ~/Desktop/proactive-outreach push origin main
# Netlify auto-deploys in ~30 seconds
# Verify at https://nextgen-outreach.netlify.app
```

### Claude Code Never Pushes Without Approval
- Claude Code edits index.html locally only
- Dan controls all git add / commit / push commands
- No deployment happens without Dan's explicit approval

---

## Current Priority Context

### Immediate — First Iteration Features
These are the highest-value additions to move beyond the prototype:

1. **Live queue stats** — footer counts (escalated, UTC, reached) should be calculated from DOM state, not hardcoded
2. **Working disposition state** — "Mark Resolved" should update queue item class and visual state
3. **TCC handoff link** — "Open in TCC" / "↗ TCC" should open `https://nextgencommand.netlify.app` in a new tab (interim until BroadcastChannel integration)
4. **CLAUDE.md committed to repo** — this file should be pushed to GitHub

### Near-Term — TriageDataBus Integration
When both tools are open in the same browser:
- Escalation events from Outreach should appear in the TCC queue
- Inbound calls resolved in TCC should update outreach record status
- Shared via BroadcastChannel + localStorage (TriageDataBus pattern from Command Center)

### Medium-Term — Ava Data Structure
Ava call transcripts are currently static HTML. Future state:
- Structured JSON per call session (sessionId, patientMRN, responses[], redFlags[], disposition)
- This is the supervised learning training signal for Basata
- isMiscalibrationSignal pattern (from Hard Stop feature in TCC) should be mirrored here

---

## Key Personnel & Relationships

| Person | Role | Relevance |
|--------|------|-----------|
| Daniel Tarditi (Dan) | Owner/developer | All decisions route through Dan |
| Ben Diestel | CVA Operations | Operational coordination for platform expansion |
| Jack Sunderman | CVA CTO | Technical leadership for CVA infrastructure |

---

## Demo Patient Roster

| Patient | MRN | Procedure | Status |
|---------|-----|-----------|--------|
| Ramirez, Carlos | 443821 | PCI/Stent · DAPT non-compliance | Escalated |
| Chen, Margaret | 227614 | ICD · Device shock | Escalated |
| Williams, Dorothy | 551209 | Fem-Pop Stent (CLI) | UTC |
| Okonkwo, James | 668431 | PVI AFib Ablation | UTC |
| Patel, Anita | 321654 | TAVR Transfemoral | UTC |
| Nguyen, Thomas | 559021 | PCI/Stent · Weekend discharge | Weekend backlog |
| Smith, Robert | 112834 | Diagnostic Cath · Radial | Calling now |
| Garcia, Luis | 883412 | Peripheral Angiography | Voicemail left |
| Johnson, Patricia | 774203 | PPM Dual Chamber | Pending T2 |
| Martino, Frank | 334872 | PVI AFib Ablation | Complete |
| Thompson, Helen | 445671 | PCI/Stent | Complete |
| Diaz, Maria | 661095 | Diagnostic Cath · Femoral | Inbound resolved |

---

## What This Tool Is Not
- Not connected to any EHR or live patient data (mock data only currently)
- Not HIPAA-compliant in current form — do not use with real PHI
- Not a replacement for clinical judgment — workflow support only
- Not a billing system
