# QA Testing Skill

---

## Routing Logic

### ✅ USE this skill when:
- Sub-agent has completed a coding task and you need to verify it works
- About to merge a feature branch to main
- Deploy is imminent and you need pre-deploy checks
- User reports something is broken and you need to diagnose
- UI change was made and you need to confirm all instances were updated

### ❌ DON'T USE this skill when:
- Just checking if an app is running (use `pm2 status` or quick curl)
- Heartbeat health checks (those have their own simpler checklist)
- Writing new code (use DELEGATION.md first, then come back here)
- Reviewing code quality/architecture (that's code review, not QA)

### ⚠️ Edge Cases:
- **"It looks fine to me"** → Not good enough. Run the grep checks anyway.
- **HTTP 200 returned** → Doesn't mean it works. Check actual content.
- **Sub-agent says "done"** → Trust but verify. Run render checks.

### Expected Outputs:
- Specific pass/fail per check
- Bug report if issues found (20 words max)
- Git commit if you fixed something

---

## ⚠️ Rules
- **CLI only** — Use `curl` to test pages, not browser/screenshots. Saves tokens.
- **Audit EVERYTHING** — grep for ALL instances before declaring done
- Report bugs in 20 words or less

## Setup
```bash
eval "$(rbenv init - bash)"
```

## Apps to Test
| App | URL | Path |
|-----|-----|------|
| Mission Control | localhost:3001 | `/Users/shiftbot/Documents/Apps/mission-control/` |
| Accounting Rails | localhost:3002 | `/Users/shiftbot/Documents/Apps/accounting-rails/` |
| Clark's Beat | localhost:3003 | `/Users/shiftbot/Documents/Apps/clarks-beat/` |

## Test Checklist

### 1. Server Health
```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:3001
curl -s -o /dev/null -w "%{http_code}" http://localhost:3002
```

### 2. Key Pages (expect 200)
**Mission Control:**
- `/` - Dashboard
- `/tasks` - Task board
- `/config` - Config viewer
- `/agents` - Agents list

**Accounting Rails:**
- `/` - Dashboard
- `/expenses` - Expenses list
- `/invoices` - Invoices list
- `/categories` - Categories

### 3. Rails Logs
```bash
tail -50 /path/to/app/log/development.log | grep -i error
```

### 4. API Tests
```bash
# Mission Control API
curl -s http://localhost:3001/api/v1/tasks | head -100

# Check JSON validity
curl -s http://localhost:3001/api/v1/tasks | jq . > /dev/null && echo "Valid JSON"
```

### 5. Form Tests
- Create new record
- Edit existing record
- Delete record (if allowed)
- Check validation errors display

### 5b. Authenticated QA (if app requires login)
- Obtain test credentials or QA bypass
- Log in before testing key flows
- For Beat: verify sub-agent click flow (`/sessions/:key`) while authenticated

### 6. Visual Audit (CLI)
When replacing patterns (emojis, icons, classes, etc.):

**Step 1: Count ALL instances BEFORE starting**
```bash
grep -rE "PATTERN" app/views --include="*.erb" | wc -l
# Save this number
```

**Step 2: Replace ALL occurrences**

**Step 3: Verify count = 0**
```bash
grep -rE "PATTERN" app/views --include="*.erb"
# Must return NOTHING. If any remain, you're not done.
```

**Step 4: Check for broken refs**
```bash
# Find fallback "?" icons (missing icon files)
curl -sL -c /tmp/c.txt -b /tmp/c.txt http://localhost:PORT/page | grep -o ">?<" | wc -l
# Should be 0
```

### 7. Responsive Audit
Flag non-responsive layouts:
```bash
# Find fixed grids without breakpoints
grep -rE "grid-cols-[0-9]" app/views --include="*.erb" | grep -v "md:\|sm:\|lg:"

# Find fixed widths
grep -rE "w-\[?[0-9]+px" app/views --include="*.erb"
```

### 8. Render Verification
HTTP 200 ≠ working. Check actual output:

```bash
# Missing icons (fallback "?" rendered)
curl -sL localhost:PORT/page | grep -c ">?<"

# Broken images
curl -sL localhost:PORT/page | grep -c 'alt=""'

# Empty containers (missing content)
curl -sL localhost:PORT/page | grep -oE '<div[^>]*></div>' | wc -l

# SVG files valid (no HTML comments breaking them)
grep -l "<!--" app/assets/icons/*.svg
```

### 9. Completeness Checklist
Before declaring ANY UI change done:
- [ ] Grep confirms 0 remaining instances of old pattern
- [ ] Render check: no fallbacks, no broken refs
- [ ] All pages return 200
- [ ] No errors in rails logs
- [ ] **Tested the actual feature, not just the page load**

## Bug Report Format
```
**Bug:** [One line description]
**Page:** [URL]
**Steps:** [How to reproduce]
**Expected:** [What should happen]
**Actual:** [What happened]
**Fix:** [If you fixed it, what changed]
```

## Common Issues
- `NameError: uninitialized constant` → Add `::` prefix for top-level constants
- `ActionController::RoutingError` → Check routes.rb
- `ActiveRecord::RecordInvalid` → Check model validations
- Blank page → Check for nil in views, add `&.` or `|| default`

## After Fixing
1. Test the fix manually
2. `git add -A && git commit -m "Fix: [description]"`
3. Report: max 20 words
