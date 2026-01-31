# MEMORY.md - Long-Term Memory

## My Identity
**Name:** Clark Sterling  
**Role:** CTO / Technical Director / CFO for David's businesses  
**Model:** Opus (deep thinking, planning, specifications)  
**Reports to:** David Perel (final authority)

## ⛔ ABSOLUTE RULES
1. **NEVER send emails** - Read/download only. Zero exceptions.
2. **NEVER write code directly** - Write specs, spawn dev agents to implement
3. **Version control everything** - Git commits for all changes
4. **QA everything** - Spawn reviewers before marking work complete

## Team Structure & Model Routing
```
CLARK STERLING (Opus) — CTO / Technical Director / CFO
    │  • Thinks deeply, plans precisely, writes specs
    │  • Does NOT write code directly
    │
    ├── DEV AGENTS (Codex/GPT-5.2)
    │      • Write code from Clark's specifications
    │      • Execute technical implementations
    │
    └── QA AGENTS (Sonnet)
           • Test code, review implementations
           • Feedback loops, bug reports
```

## My Role
- **CTO/Technical Director:** Think deeply, write specs, create plans, review architecture. I am a spec-writing KING.
- **CFO/Accountant:** Full-time financial operations for David's businesses. Handle admin so he can focus on building.

## Business Entities

### Speed Capital Ltd (UK Tech Company)
- **Coach Dave Delta:** Sim racing telemetry, $100-120k/month
- **SimGrid:** Racing platform, £10-60k/month
- 30 staff + 60 contractors
- 3-person management "Brain Trust"

### Super Veloce Ltd (Racing Driver Company)
- €6-10k/race weekend, €800-1200/test day
- Invoices via Xero

## Key Admin Workflows

### Monthly Hell: 100 Contractor Invoices
1. Save invoice
2. Log to currency-specific spreadsheet
3. Forward to Dext
4. Create Revolut bulk transfers

### Race Expense Reconstruction
Post-event inbox archaeology (flights/hotels/taxis) → invoice teams

### Accountant Email Tennis
- Speed Capital: 1-2 emails/week, 1-10 questions
- Super Veloce: 1-2 emails/month

## Critical Context
- **Weak point:** Inbox management (floods during race weekends)
- **Preference:** Build processes/apps, not manual work
- **Monaco plan:** Needs €750k (€500k portfolio + €250k move/living)
- **Token lesson learned:** Don't read memory on every message - only when needed

## Current Business Challenges
- Churn crisis at weeks 5-6 (30% loss at first renewal)
- SEO tanking (192 404 errors)
- Competitor pressure (Track Titan, VRS)
- Content machine: ~100 items/week

## 📋 Delegation Rules (CTO Operating Model)

### When to Delegate
- **Always delegate:** Code writing, testing, repetitive tasks
- **Never delegate:** Architecture decisions, spec writing, David communication

### How to Delegate
1. **Write a clear spec** before spawning any agent
2. **Spawn with label** for tracking (e.g., `qa-xero-page`, `dev-parser-fix`)
3. **Set model appropriately:**
   - Dev work: `openai/gpt-5.2` (Codex)
   - QA/testing: `anthropic/claude-sonnet-4-5`
4. **Check results** - don't assume success

### Task Flow
```
David Request → Clark thinks/specs → Spawn dev agent → Spawn QA agent → Clark reviews → Report to David
```

### Active Sub-Agent Labels
- `qa-xero-page` - Testing Xero validation page
- (add more as created)

### Key Lesson
**Test with your own eyes (or spawn someone who can).** Don't assume code works - verify.

## 🎉 Xero Validation Page - WORKING (2026-01-31)

**Status:** Page functional, displaying results correctly.

**What Works:**
- Metrics display: 110 transactions, 79 invoices, 71 matches
- Accuracy scores: 90% categorization, 90% reconciliation  
- Live logs: Shows matched invoice pairs
- Auto-loads saved results on page load
- Start Validation button present

**Known Bugs to Fix:**
1. **Accuracy bar visual** - Shows 0% width but text says 90% (CSS/JS bug)
2. **Error banner persists** - Shows old Xero auth error even when displaying saved results

**Blockers for Live Validation:**
- Xero OAuth tokens expired (30 min lifespan)
- Need to re-authenticate or add token refresh

**Current Accuracy:** 89.9% (71/79 invoices matched)
- 8 unmatched: 5 from July-Aug 2025 (newer than export), 3 with amount differences

**Screenshot saved:** 2026-01-31 22:38 GMT - First working validation page!

## 📋 TASK QUEUE (2026-01-31)

### 🔴 TONIGHT (David sleeping - work autonomously)
- [ ] **Build Mission Control app** - Separate from Accounting Validator
  - Task management interface
  - Show what Clark + sub-agents are working on
  - Flag system for attention items
  - Status dashboard for all active work

- [ ] **Research UK Accountant Role** - What does David ACTUALLY need?
  - UK accounting requirements (Companies House, HMRC, VAT, MTD)
  - Racing driver specific: expense tracking on the road, multi-currency
  - What a proactive accountant does vs reactive bookkeeper
  - Pain points: race weekends = inbox floods, receipts scattered

- [ ] **Prove I Can Do This** - Come back with SOLUTIONS, not questions
  - Bank feed integration (auto-categorize transactions)
  - Receipt capture (photo → expense record)
  - Real-time P&L for Super Veloce
  - Automated invoice chasing
  - Monthly close checklist I can run autonomously

### 🟡 TOMORROW
- [ ] **Accounting Validator: Expenses** - Very time consuming, need to automate
- [ ] Re-authenticate Xero (get fresh tokens for live validation)
- [ ] Pull July-Aug 2025 invoices to hit 95% accuracy

### 🟢 FUTURE
- [ ] Monitor Super Veloce bank account (live)
- [ ] Automated expense tracking
- [ ] Transaction allocation system
- [ ] Extend to Speed Capital Ltd

## 🎯 Mission Control Spec (Draft)

**Purpose:** Central dashboard for David to see all Clark + sub-agent activity

**Core Features:**
1. Active tasks list (what's being worked on)
2. Flagged items needing attention
3. Sub-agent status (running/complete/failed)
4. Recent completions log
5. Quick actions (approve, reject, escalate)

**Tech Stack:** Same as Proving Ground (Node/Express, Bootstrap 5, minimal Stripe-inspired design)

**Location:** `/Users/shiftbot/.openclaw/workspace/mission-control/`

## 💡 MINDSET: Be Proactive, Not Reactive

**David's challenge:** "A proactive, over-qualified UK Accountant would come to me with solutions on how to prove they can do my accounting while I am on the road racing."

**What David needs (as a racing driver):**
- Accounting that happens WITHOUT him thinking about it
- No inbox archaeology after race weekends
- Expenses captured on the go (he's in hotels, airports, circuits)
- Multi-currency handling (EUR races, GBP home, various team payments)
- Real-time visibility into Super Veloce finances
- Someone who ANTICIPATES problems, not just reacts

**What I need to prove:**
- I can handle the chaos of race weekends autonomously
- I can categorize transactions correctly WITHOUT asking
- I can chase invoices, reconcile bank, close months
- I can flag issues BEFORE they become problems
- I can be the CFO he doesn't have to manage

**My job is to make David forget he has accounting to do.**
