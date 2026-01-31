# MODEL-ROUTING.md - Token Cost Management

**Created:** January 31, 2026  
**Purpose:** Manage token costs by using the right model for the right task

---

## David's Preference

**For regular chat/conversation:**
- Use **OpenAI GPT-4o** or **GPT-4o-mini**
- Cheaper, faster for simple back-and-forth
- Good for: questions, status updates, planning, discussion

**For coding/building:**
- Use **Claude Opus 4.5** (latest)
- Best for: writing code, debugging, complex technical work
- Use when: building apps, fixing bugs, writing scripts

---

## Why This Matters

**Token costs add up fast:**
- This conversation so far: ~150k tokens on Sonnet
- Using GPT for chat could save 80-90% on chat portions
- Reserve expensive Opus/Sonnet for when quality really matters

---

## How to Switch Models

### Manual Override (Current Method)
In chat, David can specify:
- `/model opus` - Switch to Opus for coding
- `/model gpt` - Switch to GPT for chat
- `/model sonnet` - Balanced middle ground

### Automatic (Future Enhancement)
Ideally: Detect task type automatically
- Code blocks, file edits, technical debugging → Opus
- General questions, status, planning → GPT
- Not currently implemented

---

## OpenAI API Key

**Location:** Environment variable `OPENAI_API_KEY`  
**Status:** ✅ Already configured  
**Value:** `REDACTED`

---

## Current Configuration

**Primary model:** `anthropic/claude-sonnet-4-5` (default)  
**Available:**
- `openai/gpt-4o` (needs to be added to config)
- `openai/gpt-4o-mini`
- `anthropic/claude-opus-4-5` (alias: `opus`)
- `anthropic/claude-sonnet-4-5` (alias: `sonnet`)

---

## Recommended Workflow

1. **Start conversations in GPT** - Default for chat
2. **Switch to Opus for coding** - When building/debugging
3. **Stay in Opus for technical sessions** - Don't switch mid-coding
4. **Switch back to GPT after** - When coding done

---

## For Future Clark

When you wake up fresh:
- Remember: GPT for chat, Opus for code
- Check which model you're currently using
- Suggest switching if you're about to do heavy coding on GPT
- Or if doing light chat on expensive Opus

---

## Note to David

To make this fully automatic, we'd need to:
1. Update OpenClaw config to add OpenAI profile
2. Set GPT as default primary
3. Create routing rules (not yet supported by OpenClaw)
4. OR: Manually switch per session based on task

**For now:** Manual switching is the way. Just say "switch to opus" when we're about to code.
