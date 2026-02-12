# DELEGATION.md — Sub-Agent Spawning Rules

*Clark's ruleset for delegating work without blowing up context or hitting rate limits.*

---

## Routing Logic

### ✅ USE this skill when:
- About to write code (any code change = delegate to dev agent)
- Task is isolated and doesn't need conversation history
- Research would bloat context (web searches, doc reading, large file analysis)
- Multiple independent tasks can run in parallel
- Task requires >2 tool calls you'd wait on

### ❌ DON'T USE this skill when:
- Quick single tool call (just do it)
- User needs immediate response and task takes <30 seconds
- Task requires YOUR conversation context to answer correctly
- Making sensitive decisions (money transfers, external comms, deletions)
- Simple file reads or status checks

### ⚠️ Edge Cases:
- **Bug fix that seems small** → Still delegate. "Small" fixes often aren't.
- **Sub-agent failed 3 times** → Do it yourself as last resort, but note why.
- **User says "quick"** → Doesn't change the rule. Delegate if it's code.

---

## When to Delegate

**Spawn when:**
- Task is isolated (doesn't need my conversation history)
- Research that would bloat my context (web searches, doc reading)
- Coding tasks (devs don't need my full context)
- Parallel work (multiple independent tasks)
- Task > 2 tool calls that I'd need to wait on

**Do it myself when:**
- Quick single tool call
- Needs my conversation context to answer
- User is waiting for immediate response
- Task involves sensitive decisions (money, external comms)

## Task Spec Format

Keep tasks **under 50 words**. Sub-agents have tools — don't hand-hold.

```
Bad:  "Please check the expenses table in the accounting Rails app 
      at localhost:3002 and look for any records that might have 
      NULL values in the amount_gbp column, then fix them by..."

Good: "Fix NULL amount_gbp values in accounting-rails expenses table. 
      Use exchange rate from original_currency/original_amount."
```

**Include only:**
- What to do (action)
- Where (file/path/url if not obvious)
- Success criteria (how to know it's done)

**Never include:**
- Conversation history
- Why the user asked
- My reasoning process
- Multiple backup approaches

## Naming Sub-Agents (MANDATORY)

**Always use `label` parameter** with a name from the roster:
- **Devs:** Alex, Ryan, Rui, Tony
- **QA:** Stephan, Claire
- **Research:** Sam, Jordan
- **PM:** Hamster

```
sessions_spawn(
  label: "Alex",
  model: "codex",
  task: "Fix NULL amount_gbp in accounting-rails expenses"
)
```

**Never spawn unnamed agents.** UUIDs in the UI = you forgot the label.

## Model Selection

| Task Type | Model | Label Examples |
|-----------|-------|----------------|
| Coding | `codex` | Alex, Ryan, Rui, Tony |
| QA/Testing | `sonnet` | Stephan, Claire |
| Research | `gpt4o-mini` | Sam, Jordan |
| PM/Coordination | `sonnet` | Hamster |

**Hard rule:** Never spawn sub-agents on Opus. Max is Sonnet.

## Concurrency Limits

- **Max 3 concurrent sub-agents** (even if config allows 8)
- Wait for batch to complete before spawning more
- If task can be done serially in <5 tool calls, don't parallelize

## Context Isolation

Sub-agents get **clean slate**. They don't inherit my context.

**Pass via task spec:**
- File paths they need
- Specific values/IDs they need
- Success criteria

**Never pass:**
- "The user said..."
- "Earlier we discussed..."
- Summaries of my conversation
- MEMORY.md contents (they can read it themselves if needed)

## Report Format

Sub-agents report back in **max 20 words**:

```
✅ Fixed 70 NULL values. Used GBP rates from 2025.
❌ Failed: Xero API returned 401. Token expired?
```

Details go in files/logs, not the report.

## Rate Limit Awareness

**Before spawning, check:**
1. How many sub-agents currently running? (`sessions_list`)
2. Am I about to spawn 5+ in quick succession? → Stagger with 30s gaps
3. Has a sub-agent failed with 429 recently? → Back off 2 min

**Anthropic token limits:**
- Spread Opus/Sonnet calls across time
- Prefer codex/gpt4o-mini for bulk work
- If hitting limits: queue tasks, don't retry immediately

## Sub-Agent Cleanup

- Default: `cleanup: "delete"` (removes session after completion)
- Keep only if output needs follow-up inspection
- Check `sessions_list` periodically — orphaned sessions waste memory

## Escalation

Sub-agents should **not** message David directly. They report to me, I decide what's worth surfacing.

Exception: If sub-agent discovers something urgent (security issue, data loss), it can flag with `⚠️ ESCALATE:` prefix and I'll forward.

---

*Read this before any `sessions_spawn`. Cheap tokens, clean context, no rate limits.*
