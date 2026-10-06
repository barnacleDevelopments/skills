---
name: explain-flow
description: Trace a feature or behavior through the codebase and explain it as a real-world user scenario with numbered steps. Use when trying to understand how a system works, why something breaks, or what happens when a user takes an action.
---

# Explain Flow

Trace a feature, behavior, or bug through the codebase and explain it as a concrete, real-world scenario with named users and numbered steps. If this skill is run on the backend and the actor is a user, navigate the frontend repository to determine the user flow.

## Arguments

- `$ARGUMENTS` - A feature, behavior, or question to trace (e.g. "how does email replying work", "what happens when a booking is cancelled", "why would a sync fail for shared mailboxes")

## Instructions

### Step 1: Identify the Flow and Find Flipper Flags

From the user's question, identify:
1. **The entry point** - Where does this flow start? (API endpoint, job, webhook, user action)
2. **The key actors** - job or user etc?
3. **The exit point** - What's the end result the user sees?

Search the codebase to find the relevant code path. Follow the chain from entry to exit.

**Find all required flipper flags** — don't just grep the changed files:
1. Find the **route/page** that renders the entry-point component. Read it for flag checks — flags on parent components, layouts, or HOCs that gate whether the new code renders are just as required as flags inside the feature itself.
2. Grep the changed files for flag checks (`canUseFlag`, `useOrganizationFeatures`, `FlipperFlags`, `rollout_`).
3. Collect every flag found in both steps.

### Step 2: Trace the Code Path

Read through each step of the flow in order:
1. Start at the entry point (controller, job, webhook handler)
2. Follow each method call, service invocation, and state change
3. Note where decisions are made (conditionals, validations, error handling)

For each step, note what it does in plain terms. Do NOT include file paths or line numbers in the step explanations — those belong only in the Key Files section at the end.

### Step 3: Build the Scenario

Create a concrete scenario using:
- **Named users** with realistic roles (e.g. "Sarah, a sales manager at a hotel")
- **Numbered steps** showing exactly what happens in sequence
- **Real-world actions** (e.g. "Sarah clicks Reply" not "POST request sent to endpoint")
- **System actions** indented or annotated to show what happens behind the scenes
- **No file references in steps** — keep steps readable for non-engineers; the Key Files section covers the code

Format:

```
1. **[User] does [action]**
   → System: [what happens behind the scenes]

2. **[Result or next step]**
   → System: [what happens next]
```

### Step 4: Show the Failure Path (if applicable)

If the question involves a bug or failure:
1. Walk through the scenario again, but at the point of failure
2. Show exactly which step breaks and why
3. Contrast with what *should* happen
4. Use the same named users to keep it concrete

### Step 5: Present the Explanation

Structure the output using EXACTLY these sections — no more, no less. Do not add extra sections like "Edge Cases", "Architecture", "Notes", etc.

```markdown

## Flipper Flags Required
- `flag_name` — `file:line` — short reason
Then a single copy-pasteable Rails console command that globally enables all of them (e.g. `Flipper.enable(:flag_name)`).

## How [Feature] Works

### The Scenario
[1-2 sentence setup with named users and their roles]

### Happy Path
[Numbered steps with system annotations]

### What Goes Wrong (if applicable)
[Same scenario, showing where and why it breaks]

### Key Files
- `path/to/file.rb` - [role in the flow]
```

These four sections (Flipper Flags, The Scenario, Happy Path, What Goes Wrong, Key Files) are the ONLY sections allowed. If there are edge cases worth mentioning, fold them into the Happy Path as brief notes on individual steps, or into What Goes Wrong if they represent failure modes. Never create additional headings.

### Guidelines

- Use **real-world language**
- Keep each step to 1-2 sentences max
- **Never include file paths or line numbers in the step explanations** — the Key Files section is the only place for code references. Steps should read like a story, not a code walkthrough
- Name users with real names, not "User A" / "User B"
- If the actor is a user, make sure their system role is defined if any
- Show the user's perspective alongside the system's behavior
- When something fails, explain it the way you'd explain it to a non-engineer stakeholder
- Don't dump code blocks
- If there are multiple paths (success/failure/edge case), show each as its own scenario
- If a PR is available for the branch make sure to reference it's description to inform the explanation
