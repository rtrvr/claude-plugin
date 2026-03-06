---
description: Clarify intent through questions, confirm with a concrete example.
allowed-tools: Read, Glob, Grep, AskUserQuestion
argument-hint: <task or idea to clarify>
---

## Understand Before Acting

You help users go from idea → shared understanding → confirmed plan.

### Workflow

#### Step 1: Receive the Request
User's task is in `$ARGUMENTS`. If empty, infer from prior conversation. If no context, ask what they want to accomplish.

#### Step 2: Socratic Clarification
Ask questions **ONE AT A TIME** using `AskUserQuestion`. Build on each answer.

Cover these dimensions as needed (skip what's already clear):

1. **Goal & Success**: What does "done" look like? How will you verify it works?
2. **Scope**: What's included? What's NOT? What's the minimum viable version?
3. **Context & Constraints**: What existing code/patterns should this follow? Any technical constraints?
4. **Specifics**: Can you give a concrete example? What edge cases matter?

**Rules for questioning:**
- ONE question per turn — maximum clarity per response
- Reference previous answers in follow-up questions
- Search workspace for related context if helpful (`Glob`, `Grep`, `Read`) and mention: "Found related context in: `filename`"
- **Stop when you have enough clarity** — don't over-question

#### Step 3: Restate Understanding with a Concrete Example
When the idea is clear enough, restate your understanding:

```markdown
## My Understanding

[1-2 sentence summary of what the user wants]

## Concrete Example

**Currently**: [What happens now — user-facing, observable behavior]
**After the change**: [What will happen — user-facing, observable behavior]

## Approach
[Brief strategy — NOT code, but what you plan to do]
```

The example must be **specific and observable** — something the user can see, click, or verify.

Good: "Currently, tapping 'Save' shows a toast. After the change, an inline green checkmark appears next to the button for 2 seconds."
Bad: "The save feedback mechanism will be improved."

#### Step 4: Confirm
Use `AskUserQuestion`:
- "Yes, that's correct"
- "Partially correct — let me clarify"
- "No, that's not what I mean"

If not "Yes": ask what's different, restate, confirm again.

### Rules
- **ALWAYS execute Step 3 + Step 4** — never skip the restate-and-confirm cycle, regardless of how simple the task seems
- **User-facing language** — describe behavior, not implementation details
- **One concrete example minimum** — abstract summaries are not enough
- **If ambiguous, ask** — don't guess and present a wrong understanding
