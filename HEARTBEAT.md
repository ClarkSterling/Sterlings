# HEARTBEAT.md - Overnight Work Checklist

## 🚨 Prioritise process above speed
**Dev flow lives in MEMORY.md — read it before ANY code change.**

## Priority Order
1. **Mission Control** — Check for outstanding tasks. This is the source of truth.
2. **Test apps are running** — `pm2 status`, curl each endpoint
3. **Sub-agents** — Check sessions_list for completed work. Process results.
4. **Blocked tasks** — Continue if unblocked.
5. **New builds** — Only after above checks pass

## QA Checklist (Run Before Every Deploy)
```bash
# Bean Counter (localhost:3002)
curl -s http://localhost:3002/login | grep "Bean Counter"  # Login page
curl -s -X POST http://localhost:3002/dev_login -c /tmp/bc.cookie -L | grep -i dashboard
curl -s -b /tmp/bc.cookie http://localhost:3002/ | grep -i expenses

# Clark's Beat (localhost:3003)
curl -s http://localhost:3003/ | grep -i "cron\|agents"
```

## Rules
- **DO NOT message David unless critical** (let him sleep)
- If sub-agent completed: process result, spawn follow-up if needed
- If stuck after 3 attempts: note blocker in memory, move on
- Compact context if >80k tokens before doing work
- Log progress to `memory/YYYY-MM-DD.md`

## What Counts as Critical (message David)
- Security issue or breach
- Production system down
- Something that will cost money if not addressed

## Otherwise
If nothing needs attention: HEARTBEAT_OK
