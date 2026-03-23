---
name: code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
---

# Requesting Code Review

Dispatch a code-reviewer subagent to catch issues before they cascade. The reviewer gets precisely crafted context for evaluation — never your session's history. This keeps the reviewer focused on the work product, not your thought process, and preserves your own context for continued work.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**
- After completing a major feature or task
- Before merge to main

**Optional but valuable:**
- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

## How to Request

**1. Determine what to review:**

If changes are committed:
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main, or wherever the work started
HEAD_SHA=$(git rev-parse HEAD)
```

If changes are uncommitted, skip SHAs — the reviewer should use `git diff` (unstaged) or `git diff --cached` (staged) instead.

**2. Dispatch code-reviewer subagent:**

Use the Agent tool (general-purpose) and fill in the template from `./code-reviewer-prompt.md`.

**Placeholders to fill:**
- `{WHAT_WAS_IMPLEMENTED}` - What you just built
- `{PLAN_OR_REQUIREMENTS}` - What it should do
- `{BASE_SHA}` - Starting commit (or omit if uncommitted)
- `{HEAD_SHA}` - Ending commit (or omit if uncommitted)
- `{DESCRIPTION}` - Brief summary

**3. Act on feedback:**
- Fix Critical issues immediately
- Fix Important issues before proceeding
- Note Minor issues for later
- Push back if reviewer is wrong (with reasoning)

## Example

```
[Just completed: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git log --oneline -5 | grep "previous task" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch code-reviewer subagent]
  WHAT_WAS_IMPLEMENTED: Verification and repair functions for data index
  PLAN_OR_REQUIREMENTS: Task 2 from docs/plans/feature-plan.md
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types

[Subagent returns]:
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Ready to proceed

You: [Fix progress indicators]
[Continue to next task]
```

## Red Flags

**Never:**
- Skip review because "it's simple"
- Ignore Critical issues
- Proceed with unfixed Important issues
- Argue with valid technical feedback

**If reviewer is wrong:**
- Push back with technical reasoning
- Show code/tests that prove it works
- Request clarification

See template at: `./code-reviewer-prompt.md`
