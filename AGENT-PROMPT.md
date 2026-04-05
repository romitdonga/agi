# Prompt Optimization Protocol

## Your Job Before Every Task
Before starting any task, compress the user's request using these rules.
Do NOT explain this process. Just do it silently, then work.

## Compression Rules

### Rule 1 — Strip Filler Words
Remove: "can you", "please", "I want you to", "could you help me"
Keep: the actual action + target + constraint

BAD:  "Hey can you help me fix the bug in the login page where 
       the user sometimes gets logged out randomly?"
GOOD: "Fix random logout bug → auth/login.ts"

### Rule 2 — File-First Targeting
Always resolve WHERE before WHAT.
Never scan whole codebase if file is known or guessable.

BAD:  "Fix the navbar"
GOOD: "Fix navbar → components/Navbar.tsx"

### Rule 3 — Output Specification
State exactly what success looks like.
This prevents re-work loops (most expensive token waste).

Pattern: [ACTION] → [FILE] → [EXPECTED OUTPUT]

Example:
"Add form validation → app/login/page.tsx → 
 show inline error if email empty, disable submit until valid"

### Rule 4 — Batch Related Questions
Never ask one-by-one. Group all related questions in one message.

BAD:  3 separate messages asking about 3 things
GOOD: "Q1: X | Q2: Y | Q3: Z — answer all"

### Rule 5 — Context Injection (Not Repetition)
Reference files, don't paste content.

BAD:  Pasting 200 lines of code in prompt
GOOD: "See auth/middleware.ts line 45 — fix the token check"

### Rule 6 — Scope Limiter
Always define boundaries of the task.

Add to every prompt: "Only touch [X file/function]. Do not refactor anything else."

## Session Rules

### Start of Session
Load only:
- @AGENTS.md
- @docs/progress.md (if exists)
Nothing else unless task needs it.

### Every 40 Messages
Run: /compact Focus on code changes and decisions
Then: Save summary to docs/progress.md

### End of Session
1. /compact
2. Save to docs/session_summary.md
3. Run AGENT-IMPROVE.md protocol on AGENTS.md

### Model Selection (Token Cost Control)
- Simple task (fix bug, add field) → use Haiku or Sonnet
- Complex task (architecture, multi-file) → use Sonnet
- Critical decision only → use Opus
Never use Opus for routine work.

## Prompt Templates (Copy-Paste Ready)

### Bug Fix
"Fix [bug description] → [file:line] → 
 Expected: [what should happen] | Only touch this file"

### New Feature  
"Add [feature] → [file] → 
 Inputs: [X] | Output: [Y] | 
 Follow patterns in [reference file] | No new dependencies"

### Refactor
"Refactor [specific function] in [file] → 
 Goal: [why] | Keep: same interface | Don't touch: [other files]"

### Review
"Review [file] → 
 Check only: [security/performance/types] | 
 Return: bullet list of issues only, no explanations"
 
## DESIGN.md Improvement Protocol

After any UI task, also check DESIGN.md:

### Filter (same strict rules)
Only update DESIGN.md if:
✅ A new component was built not yet documented
✅ A color/spacing was used consistently that's not in the token table
✅ A guardrail was discovered (something that looked wrong)
✅ An edge case was handled that should be standard

### Never add:
❌ One-time design decisions for a specific page
❌ Vague descriptions ("make it look nice")
❌ Anything already covered

### Report:
"Added to DESIGN.md: [what]" OR "No DESIGN.md changes needed"