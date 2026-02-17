# AIM Tutorial — Most Important Tasks

A local-first CLI task manager that auto-classifies tasks using BM25 and enforces SMART goal principles.

## Quick Start

```bash
# Add a task (auto-classified into Focus, Pending, or Reference)
aim add "Write quarterly report by Friday"

# See all tasks
aim list

# Complete a task
aim done 1
```

## The Problem

Task lists become chaos:
- 47 items in "Inbox"
- Vague tasks like "Work on stuff"
- No prioritization
- Procrastination city

## The Solution

AIM uses **BM25 classification** + **SMART scoring** to automatically organize tasks and keep you focused.

## Core Concepts

### Categories

| Category | Description | Examples |
|----------|-------------|----------|
| **Focus** | Most Important Tasks - actionable with deadlines | "Fix bug by tomorrow", "Write report by Friday" |
| **Backlog/Pending** | Someday/Calendar/Waiting - actionable but not urgent | "Learn Rust by Q2", "Research new framework by sprint end" |
| **Reference** | Info to keep for later - not actionable | "Store password", "Link to docs", "API reference" |
| **Inbox** | Quick capture (max 5) - unorganized tasks to process | New tasks before organizing, unclear items |

### SMART Scoring

Every task gets a SMART score (0-1), weighted toward **deadlines** (they matter most):

```
SMART = Specific×0.20 + Measurable×0.25 + Achievable×0.15 + Time_bound×0.40
        (What?)      (How measure?)     (Realistic?)   (When? — Weighted 40%)
```

| Score | Interpretation | What to do |
|-------|-----------------|-----------|
| 0.65+ | Well-defined, ready to execute | Add to Focus now |
| 0.40–0.64 | Actionable but could be clearer | Review and add deadline if possible |
| <0.40 | Too vague, needs refinement | Goes to Inbox for clarification |

**Key insight**: Adding a deadline (e.g., "by Friday") often boosts score by 0.20-0.30 alone.

Use `aim list --scores` to see SMART scores for analysis and improvement.

### Guardrails

- **Inbox limit**: Max 5 tasks (prevents pile-up)
- **Soft warning**: At 3+ tasks
- **Hard block**: At 5 tasks (use `--force` to override)

## Commands

### Adding Tasks

```bash
# Add a task
aim add "Write report by Friday"

# Show all tasks by category
aim list

# Complete a task
aim done 1

# Delete all tasks (requires confirmation)
aim clear
```

## Task Phrasing Guide

Your phrasing matters. AIM learns from it.

**✓ Good (high SMART score):**
- "Fix authentication bug by tomorrow" (verb + deadline)
- "Review code by Friday" (action + timeframe)
- "Team standup at 10am" (specific, time-blocked)
- "Deploy to production today" (clear action + urgency)
- "Complete 5 unit tests by EOD" (measurable + deadline)

**✗ Vague (low SMART score → Inbox):**
- "Work on stuff" (unclear)
- "Improve performance" (no target)
- "Think about the design" (passive)
- "Learn Python" (no deadline)
- "Board meeting" (no time specified)

**Key signals AIM looks for:**
1. **Action verb**: Fix, complete, deploy, review, implement
2. **Deadline/timeframe**: by Friday, today, at 3pm, this week
3. **Measurable target**: 5 tests, 30% improvement, user authentication
4. **Context**: who/what/where if possible

## Domain-Specific Phrasing

Different domains need different signals. Here's how to phrase tasks for your role:

### Engineers / Developers
**✓ Good:**
- "Fix race condition in database connector by Friday"
- "Review pull request #42 by EOD"
- "Deploy hotfix to production today"
- "Complete unit tests for payment module by EOD"

**Better than vague:**
- ❌ "Review code" → ✓ "Complete code review by Friday"
- ❌ "Fix that bug" → ✓ "Fix NullPointerException in UserService by tomorrow"
- ❌ "Refactor service" → ✓ "Refactor auth service (reduce complexity) by next sprint"

### Product Managers / Requirements
**✓ Good:**
- "Write user story for checkout flow by Wednesday"
- "Get stakeholder approval on feature scope by Friday"
- "Define acceptance criteria for payment processing by EOD"
- "Gather requirements from 5 customers by Friday"

**Better than vague:**
- ❌ "Gather requirements" → ✓ "Gather requirements from 5 customers by Friday"
- ❌ "Create spec" → ✓ "Create spec document for dark mode feature by Friday"
- ❌ "Review competitor features" → ✓ "Complete competitor analysis by EOD Thursday"

### Operations / Support
**✓ Good:**
- "Respond to P1 escalation from enterprise client by noon"
- "Investigate slow API response times by EOD"
- "Prepare incident report on 2-hour outage by tomorrow"
- "Root cause analysis meeting at 4pm"

**Better than vague:**
- ❌ "Fix production issue" → ✓ "Fix customer database sync issue - production impact by EOD"
- ❌ "Handle escalation" → ✓ "Respond to P1 escalation from enterprise client by noon"
- ❌ "Investigate errors" → ✓ "Investigate server errors affecting 20% of users by EOD"

### Researchers / Exploratory
**✓ Good:**
- "Investigate machine learning approach by Friday"
- "Prototype proof-of-concept for streaming feature by sprint end"
- "Evaluate three database solutions by next week"
- "Compile research notes into documentation by Friday"

**Better than vague:**
- ❌ "Research ML" → ✓ "Investigate machine learning approach for recommendations by Friday"
- ❌ "Explore new language" → ✓ "Evaluate Go for CLI tools (report findings by EOD)"
- ❌ "Learn about consensus" → ✓ "Read 10 papers on consensus algorithms, summarize by Friday"

### Legal / Compliance
**✓ Good:**
- "Complete GDPR data audit by quarter end - March 31"
- "Obtain legal sign-off on terms of service changes by Friday"
- "Prepare compliance report for board meeting Thursday"
- "File annual compliance certifications by April 1st"

**Better than vague:**
- ❌ "Review privacy policy" → ✓ "Review and update privacy policy for new feature by Friday"
- ❌ "Assess risks" → ✓ "Assess risk implications of data processing agreement by EOD"
- ❌ "Prepare audit" → ✓ "Prepare SOC2 compliance audit by March 31"

### Marketing / Content
**✓ Good:**
- "Launch beta sign-up campaign at 10am"
- "Finalize Q2 marketing campaign brief by EOD today"
- "Write press release for feature announcement by Friday"
- "Plan content calendar for next quarter by EOD"

**Better than vague:**
- ❌ "Design email template" → ✓ "Design email template for newsletter campaign by Friday"
- ❌ "Create content" → ✓ "Create 5 social media posts for product launch by EOD"
- ❌ "Review test results" → ✓ "Review A/B test results from last campaign by Friday"

### Managers / Leadership
**✓ Good:**
- "Team standup at 10am"
- "Schedule 1-on-1s with all reports by Friday"
- "Finalize Q2 OKRs presentation by Thursday"
- "Final approval on 2027 budget by Friday"

**Better than vague:**
- ❌ "Team meeting" → ✓ "Team standup at 10am Thursday"
- ❌ "Review reports" → ✓ "Schedule 1-on-1s with all 4 reports by Friday"
- ❌ "Plan Q2" → ✓ "Finalize Q2 roadmap presentation for stakeholder meeting by Friday"

### Key Phrase Boosters

Add these to any task to make classification stronger and improve scores:

**Time Signals** (boost SMART score +0.4, critical for classification)
- "by Friday", "by EOD", "at 3pm", "by March 31", "by sprint end", "by next Monday", "before deadline"
- Even soft deadlines help: "this week", "ASAP", "urgent" (less effective than specific dates)

**Action Words** (boost SMART score +0.2–0.4)
- Project/delivery: "complete", "deliver", "finish", "ship", "release"
- Approval/decision: "obtain", "approve", "confirm", "decide", "finalize"
- Gathering/preparation: "gather", "prepare", "compile", "document", "schedule"
- Technical: "fix", "implement", "deploy", "review", "test"

**Measurable Goals** (boost SMART score +0.2)
- Numbers: "5 customers", "30% reduction", "10 tasks", "3 options"
- Counts: "all reports", "each team member", "every section"
- Specifics: "user authentication", "database migration", "payment module"

**Context/Specificity** (boost SMART score +0.1)
- Add names, numbers, or clear targets: "with Sarah" (vs. "with team"), "Q2 budget" (vs. "budget"), "#42 PR" (vs. "code review")

## How AIM Classifies Tasks

AIM automatically routes tasks based on keywords and deadlines:

- **→ Focus**: Has action verb + deadline/time (e.g., "Fix bug by tomorrow")
- **→ Backlog**: Has deadline but exploratory (e.g., "Research ML by Friday")
- **→ Reference**: No deadline, informational (e.g., "API documentation")
- **→ Inbox**: Too vague or unclear (e.g., "Work on stuff")

The system learns from ~842 training examples, so similar phrasing gets routed correctly. See "Domain-Specific Phrasing" section below for your domain.

## Example Workflow

### Morning: Capture

```bash
$ aim add "Write quarterly report by Friday"
✓ Task 1 added to Focus (Deadline + action verb)

$ aim add "Fix critical bug"
⚠ Task is too vague (0.25)
  Tip: Add a deadline or specify what to fix
  Better: "Fix authentication bug by tomorrow"

$ aim add "Research ML options by end of Q1"
✓ Task 2 added to Backlog (Deadline but exploratory)
```

### During Day: Review

```bash
$ aim list
▸ Focus (1)
  [1] Write quarterly report by Friday

▸ Backlog (1)
  [2] Research ML options by end of Q1

▸ Inbox (1)
  [3] Fix critical bug (too vague)
```

**Note**: Tasks in Backlog are actionable (have deadlines) but exploratory—good for later planning. Inbox is for quick capture of unclear items.

### Complete Tasks

```bash
$ aim done 1
Completed task 1
```

## Inbox Management Tips

The Inbox is your "triage area"—tasks here need clarification before moving to actionable categories:

**What goes to Inbox (and why):**
- Too vague: "Work on stuff" → clarify to "Implement payment validation by Friday"
- Missing deadline: "Fix bug" → add deadline to "Fix authentication bug by tomorrow"
- Unclear context: "Update docs" → specify to "Update API docs for v2.0 by EOD Friday"

**How to escape Inbox:**
1. Use `aim list` to see all tasks including those in Inbox
2. Use `aim done <id>` to complete Inbox tasks that are no longer relevant
3. Use `aim add "better description by deadline"` to add clearer tasks

**Inbox limit (max 5):**
- Soft warning at 3 tasks
- Hard block at 5 tasks
- Delete vague tasks and add clearer ones instead

## Philosophy

1. **Capture quickly** — Just dump thoughts (can be vague)
2. **Auto-classify** — BM25 + SMART scores route to category
3. **Refine in Inbox** — Add deadlines and context
4. **Execute from Focus** — Focus on urgent, actionable work
5. **Someday Backlog** — Plan future, exploratory work

## Configuration

Database: `~/.aim/aim.db` (created automatically on first run)

## Troubleshooting

**"My task keeps going to Inbox"**
- Add a deadline: "by Friday", "by EOD", "at 3pm"
- Make it more specific: add who/what/where
- Check example in your domain section above

**"Task went to wrong category"**
- Check SMART score: `aim list --scores`
- Rephrase to match examples in Domain-Specific Phrasing
- (This is actually helping you clarify—Inbox is designed to catch unclear items)

**"I want to organize my Inbox"**
- Use `aim list` to see all tasks including Inbox
- Use `aim done <id>` to delete vague tasks
- Use `aim add "clearer description by deadline"` to add better tasks

## Why This Works

- **BM25 training** learns from 800+ carefully chosen examples in your domain
- **SMART scoring** enforces actionable tasks (40% weight on deadline for good reason)
- **Inbox limit** prevents procrastination—forces prioritization
- **Local-only** = fast, private, no sync delays, full data control
- **GTD-inspired** = aligned with proven productivity methodology (Capture → Clarify → Organize → Reflect → Execute)

---

**AIM** is built on simple principles: don't organize tasks—let the system help you write better tasks.
