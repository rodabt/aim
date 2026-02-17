# AIM — Frequently Asked Questions

## General Questions

### What is AIM?

AIM is a **local-first CLI task manager** that automatically classifies and scores your tasks using BM25 and SMART scoring. Instead of you managing a flat list, AIM intelligently organizes your work.

```
Traditional: You add task → You prioritize → You categorize → You execute
AIM:         You add task → AIM auto-classifies & scores → You execute
```

### Why should I use AIM instead of Todoist, Things, or Taskwarrior?

**vs Todoist:**
- Todoist charges $5+/month for "real" features
- AIM is free forever and local-first
- Todoist overwhelms with 80+ buttons; AIM focuses on what matters

**vs Things/Superlist:**
- Beautiful but expensive ($20-40)
- Need a Mac
- AIM is free, CLI, works everywhere

**vs Taskwarrior:**
- Taskwarrior has 47+ commands to learn
- No automatic categorization or scoring
- You manually decide priorities
- AIM auto-classifies and scores everything

**AIM's unique advantages:**
1. **Auto-intelligent**: BM25 classification + SMART scoring = no manual organizing
2. **Guardrails**: Inbox limit (max 5) prevents procrastination
3. **Free forever**: No subscription, no cloud, no account
4. **Local-only**: Your tasks stay on your machine

### Is AIM free?

**Core AIM is free forever.** No subscription required, no paywalls, no freemium model.

The free tier includes:
- BM25 classification + SMART scoring
- Core commands (add, list, done, clear)
- Full weight customization via config.toml
- Local database

Optional paid tiers (Starter Pack, All-In Pack, Creator Pack) add **domain-specific examples** and **pre-tuned weights** for specialized use cases (startups, academics, executives, finance, etc). See "Will AIM ever charge money?" for details.

### Does AIM require an account?

No. Everything runs locally on your machine. No account, no login, no cloud sync.

### Can I use AIM on my phone?

Not currently. AIM is a CLI tool. You can use it on terminal, WSL, SSH—any place you have a command line.

Future roadmap includes a lightweight web UI for mobile access, but it's not available yet.

### Is AIM open source?

Yes. AIM is ~1500 lines of V code with zero external dependencies beyond SQLite (which is built-in).

---

## Classification & Scoring

### How does AIM decide what category to put tasks in?

AIM uses **BM25**, a proven information retrieval algorithm, to match your task against training examples.

```
Your Task: "Fix authentication bug by Friday"
                ↓
         BM25 Scoring
                ↓
Focus examples: 0.92 ← "Fix bug by tomorrow"
Pending:        0.34 ← "Research new framework"
Reference:      0.15 ← "Store API key"
                ↓
         ✅ Goes to Focus (highest score)
```

### Why BM25 and not machine learning / BERT / AI?

**Short answer:** BM25 is simple, fast, and works really well. Machine learning would be overkill.

**Long answer:**

| Aspect | BM25 | ML/BERT |
|--------|------|---------|
| **Speed** | <1ms | 50-100ms |
| **Dependencies** | None | ONNX runtime |
| **Model size** | 0KB | 4MB+ |
| **Cold start** | Works immediately | Needs training data |
| **Explainability** | Can see which examples matched | "Black box" |
| **Offline** | Perfect | Mostly |

**Why ML isn't better here:**
- BM25 already achieves 78%+ accuracy on diverse task types
- Adding complexity doesn't help much (diminishing returns)
- Users need to understand *why* their task was classified a certain way
- Speed matters for CLI—50ms is noticeable, <1ms is invisible

**We chose:** BM25 because it's the sweet spot between intelligence and simplicity.

**If accuracy drops below 75%**, we'd consider embeddings (semantic similarity without full ML).

---

### What are the four categories?

| Category | Purpose | Examples |
|----------|---------|----------|
| **Focus** | Actionable with deadlines | "Fix bug by tomorrow", "Write report by Friday" |
| **Backlog** | Actionable but not urgent | "Learn Rust by Q2", "Research new framework" |
| **Reference** | Info to keep, not actionable | "API documentation", "Password store" |
| **Inbox** | Unclear, needs clarification | "Work on stuff", "Do thing" |

### What's SMART scoring?

Every task gets a score from 0 (vague) to 1 (well-defined):

```
SMART = Specific×0.20 + Measurable×0.25 + Achievable×0.15 + Time_bound×0.40
         (What?)      (Measurable?)    (Realistic?)    (When? ← Most important)
```

| Score | Quality | What to do |
|-------|---------|-----------|
| 0.65+ | Excellent | Add to Focus now |
| 0.40-0.64 | Good | Consider adding deadline |
| <0.40 | Vague | Add to Inbox for clarification |

**Key insight:** Deadlines matter most (40% weight). Just adding "by Friday" usually boosts score by +0.20-0.30.

### Why does my task keep going to Inbox?

Usually because it's missing one of these:

1. **No deadline**: "Write report" → Add deadline: "Write report by Friday"
2. **Vague verb**: "Work on stuff" → Be specific: "Implement payment validation"
3. **Unclear target**: "Improve performance" → Add metric: "Reduce API latency by 30%"

Use `aim list --scores` to see the score breakdown. The scoring feedback helps you write better tasks.

### How do I improve my task's classification?

**Make tasks:**
- **Specific**: Use concrete action verbs (write, fix, implement, deploy)
- **Measurable**: Add numbers (5 customers, 30% reduction, all reports)
- **Achievable**: Keep scope realistic (something doable in 1 sprint)
- **Time-bound**: Add a deadline (by Friday, by March 31, at 3pm)

**Example progression:**

```
❌ Vague:     "Research ML" (score: 0.22)
⚠️  Better:   "Research ML options by Friday" (score: 0.45)
✅ Excellent: "Evaluate 3 ML approaches by Friday, deliver report" (score: 0.68)
```

---

## Inbox Management

### Why is there an Inbox limit (max 5)?

The limit forces you to prioritize. Without it:
- Tasks pile up (47 items)
- You procrastinate organizing
- Everything feels urgent
- Nothing gets done

With the limit:
- You must decide what's really important
- Inbox becomes triage (clarify) not storage
- You stay focused

### How do I deal with Inbox overflow?

```bash
# See all tasks including Inbox
aim list

# Use aim done to remove vague tasks
aim done 1

# Add clearer tasks instead
aim add "Better description by Friday"
```

**Recommendation:** Delete vague Inbox tasks and add clearer ones. The Inbox limit (max 5) helps you focus.

### What's the difference between Backlog and Inbox?

| | Inbox | Backlog |
|---|-------|---------|
| **Clarity** | Unclear, needs work | Clear, actionable |
| **Deadline** | No deadline | Has deadline |
| **Purpose** | Triage/clarification | Planning |
| **Example** | "Work on stuff" | "Research ML by Q2" |

Inbox is for fast capture that needs refinement. Backlog is for work you plan to do, just not soon.

---

## Commands & Usage

### What commands does AIM have?

```bash
aim add "Task description"            # Add a task
aim add --force "Task description"    # Force add task (bypass Inbox limit)
aim list                              # Show all tasks by category
aim done 1                            # Mark task 1 complete
aim clear                             # Delete all tasks (requires confirmation)
```

### Can I back up my tasks?

Tasks are stored in `~/.aim/aim.db` (SQLite). You can back up that file directly or copy the entire `~/.aim/` directory.

---

## Understanding Classification

### My task went to Reference, but I wanted Focus. Why?

Reference is for information, not actionable tasks. Common reasons:

```
❌ "Backup system configuration"
   → Sounds like info storage

✅ "Backup system configuration by Friday"
   → Now it has a deadline, so Focus is more likely
```

Or:

```
❌ "Learn new API"
   → Too exploratory/educational

✅ "Document new API integration by sprint end"
   → Now it's concrete, actionable work
```

### My task went to Backlog, but I thought it was urgent. Why?

Backlog is for "actionable but not urgent." If it has a deadline but sounds exploratory:

```
❌ "Research ML approaches"
   → Exploratory (Backlog)

✅ "Implement ML recommendation system by Q2"
   → Concrete delivery (Focus)
```

Add more specificity to get Focus classification.

### Why does the same task sometimes classify differently?

It shouldn't. AIM is deterministic—same input always produces same output.

If it changes:
1. Task wording might be slightly different (typo?)
2. Training data changed (unlikely—would need recompile)
3. SMART score dropped below 0.40, triggering Inbox override

Use `aim list` to see task classifications.

---

## SMART Scoring Details

### How is Specific scoring calculated?

A task scores high on Specific if it:
- Contains an action verb (write, fix, implement, deploy, review, etc)
- Is longer than 10 characters (shows thought)
- Contains code-related words (bug, API, endpoint, pipeline)

Low if it:
- Contains question marks (uncertainty)
- Contains "maybe" or "think" (wishy-washy)

```
"Fix bug in UserService" → High (verb + specificity)
"Do stuff" → Low (no clear action)
```

### How is Measurable scoring calculated?

A task scores high on Measurable if it:
- Contains numbers (5 tasks, 30% improvement, 10 customers)
- Contains completion verbs (deliver, finish, submit, publish, release)

Low if it:
- Contains vague words (maybe, think, consider, explore, look at)

```
"Complete 5 unit tests" → High (number + completion verb)
"Explore options" → Low (vague verb)
```

### How is Achievable scoring calculated?

A task scores high on Achievable if it:
- Is concise (<50 chars = more focused)
- Uses realistic language (complete, finish, submit, fix, update)
- Doesn't contain grandiose language (revolutionize, become an expert)

```
"Fix bug in database" → High (realistic, focused)
"Master machine learning forever" → Low (unrealistic)
```

### How is Time_bound scoring calculated?

A task scores high on Time_bound (40% of total score!) if it:
- Contains deadline keywords (by, before, due, Friday, March 31, at 3pm)
- Contains specific dates or times (2024-03-15, 10am)
- Contains soft deadlines (this week, ASAP, urgent)

```
"Write report by Friday" → High (explicit deadline)
"Learn Python" → Low (no time constraint)
```

**Key insight:** Time_bound is weighted 40% because deadlines drive execution more than anything else.

---

## Performance & Storage

### How fast is AIM?

- Adding a task: <1ms
- Listing tasks: <10ms
- Classification: <1ms (BM25 is very fast)
- Full database scan: 50-100ms

AIM is compiled to a single binary and runs locally—speed is excellent.

### How much disk space does AIM use?

Very little. A SQLite database with 1000 tasks is ~100KB. The binary is ~1MB.

### Does AIM sync across devices?

Not automatically. But you can:

Cloud sync (via Dropbox/Drive/Nextcloud folders) is planned for future versions.

---

## Privacy & Security

### Is my data private?

Completely. All tasks are stored locally in `~/.aim/aim.db` on your machine. Nothing is sent anywhere.

- No cloud storage
- No analytics
- No telemetry
- No account
- No authentication

**You own your data.**

### Can I trust AIM with sensitive tasks?

Yes. It's local-only, open-source code, no network access. But:

- Database file is **not encrypted** (it's SQLite)
- If someone accesses your `~/.aim/` folder, they can read your tasks
- Consider file permissions: `chmod 700 ~/.aim/aim.db`

### What about backups?



---

## Customization & Advanced

### Can I customize categories?

Currently: No (categories are fixed to GTD model: Focus/Backlog/Reference/Inbox)

Future: Yes—Phase 2 roadmap includes custom categories with user-defined examples.

### Can I change the SMART weights?

Currently: No (40% time_bound, 25% measurable, 20% specific, 15% achievable)

Future: Yes—Phase 2 roadmap includes per-user weight customization via config file.

### Can I add my own category examples?

Currently: No (examples are embedded in the binary at compile time)

Future: Yes—Phase 1 roadmap includes runtime example management.

### Can AIM learn from my corrections?

Currently: No (BM25 weights are fixed)

Future: Yes—Phase 2 roadmap includes learning from user reclassifications to adjust examples.

---

## Troubleshooting

### Task went to the wrong category. How do I fix it?

Edit the task to be clearer:

```bash
# Before (went to Inbox)
aim add "Fix bug"

# Delete the vague task and add a better one
aim done 1
aim add "Fix authentication bug by tomorrow"

# Now it will be categorized correctly as Focus
```

### Inbox is full (5 tasks) and I can't add more

The solution is to delete vague Inbox tasks and add clearer ones:

```bash
aim list              # See what's in Inbox
aim done 1            # Delete a vague task
aim add "Better task with deadline by Friday"
```

The limit is intentional—it forces you to stay focused. Embrace it!

### Database got corrupted. How do I recover?

If `~/.aim/aim.db` is corrupted:

```bash
# Start fresh
rm ~/.aim/aim.db
aim add "Your first task"
```

AIM automatically creates the database on first run. Backup your `~/.aim/` directory regularly to prevent data loss.

### AIM is slow (slower than 100ms per command)

Unlikely. If it is:

1. Check database size: `ls -lh ~/.aim/aim.db`
    - If >10MB, you have 10,000+ tasks—consider cleaning up old completed tasks
2. Report issue if problem persists

### A task's score seems wrong

Use `aim list --scores` to debug. SMART scoring is transparent:

```
$ aim list --scores

▸ Focus (1)
  [1] 0.75 │ Write report by Friday

Score breakdown:
  Specific: ✓ (has "write" verb)
  Measurable: ✗ (no numbers, but has "report")
  Achievable: ✓ (realistic)
  Time_bound: ✓ (has "Friday")
```

If score seems wrong, it's usually because a key signal is missing (deadline, verb, specificity).

---

## Getting Help

### Where's the tutorial?

Read `TUTORIAL.md` in the repo. It covers:
- Quick start
- Core concepts (categories, SMART, guardrails)
- All commands
- Domain-specific phrasing (Engineers, PMs, Researchers, Managers, etc)
- Task phrasing guide
- Example workflow
- Troubleshooting

### Where's the source code?

GitHub: `https://github.com/yourrepo/aim`

Structure:
- `main.v` — CLI interface
- `calc.v` — SMART scoring & BM25 classification
- `db.v` — Database management
- `cli.v` — Command parsing
- `utils.v` — Utilities

### I found a bug. How do I report it?

1. Check TUTORIAL.md troubleshooting section
2. Run `mit status` and note the output
3. Check if it's expected behavior (see FAQ above)
4. If it's a real bug, open an issue on GitHub with:
    - Exact command run
    - Expected behavior
    - Actual behavior
    - Output of `aim list --scores`

### AIM isn't classifying my task correctly. Help!

**First:** Make sure task follows SMART principles. Use task phrasing guide in TUTORIAL.md.

**Examples by domain:**

- **Engineers**: "Fix race condition in UserService by Friday"
- **Product Managers**: "Gather requirements from 5 customers by Friday"
- **Researchers**: "Prototype ML approach by sprint end"
- **Managers**: "Team standup at 10am Friday"
- **Legal/Compliance**: "Complete GDPR audit by March 31"

**If still wrong:** Report issue on GitHub with:
1. Your task text
2. Current classification
3. Expected classification
4. SMART score breakdown

---

## Quick Reference

### Most Common Commands

```bash
aim add "Task description"              # Add task
aim list                                # Show all tasks
aim done 1                              # Mark done
aim clear                               # Delete all tasks
```

### Task Phrasing Checklist

Before adding a task, check:

- [ ] Has a **verb** (write, fix, implement, deploy, review, etc)
- [ ] Has a **deadline** (by Friday, by EOD, at 3pm, by March 31)
- [ ] Has a **target** (what are you doing it on/for?)
- [ ] Is **specific** (not "work on stuff")
- [ ] Is **measurable** (numbers help: 5 tasks, 30% reduction, all)

```
✅ "Fix authentication bug by tomorrow"
❌ "Work on stuff"
```

### SMART Score Interpretation

```
0.65+  → Excellent, add to Focus now
0.40-0.64 → Good, consider adding deadline
<0.40 → Vague, goes to Inbox for clarification
```

**Boost tips:**
- Add deadline: +0.20-0.30
- Add number: +0.15
- Add specific noun: +0.10

---

## Final Thoughts

AIM is built on a simple philosophy:

> **Don't organize tasks. Let the system help you write better tasks.**

The Inbox limit, SMART scoring, and auto-classification aren't meant to constrain you—they're guardrails to keep you focused and productive. Embrace them.

Questions? Check the TUTORIAL.md or open an issue on GitHub.

**Happy task managing!**
