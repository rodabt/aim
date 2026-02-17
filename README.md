# AIM — Auto-Intelligent Task Manager

<div align="center">

[![Language](https://img.shields.io/badge/Language-V-blue)](https://vlang.io)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)]()

**A local-first CLI task manager that auto-classifies and scores your tasks. Focus on what matters.**

[Features](#-features) • [Quick Start](#-quick-start) • [Usage](#-usage) • [FAQ](FAQ.md) • [Tutorial](TUTORIAL.md)

</div>

---

## 🎯 Overview

`aim` is a **local-first CLI task manager** built on intelligent classification and SMART goal principles. Instead of maintaining a flat list, AIM automatically routes tasks to the right category while scoring their actionability.

- ✅ **Auto-classification** — Tasks routed to Focus, Pending, Reference, or Inbox using BM25
- ✅ **SMART scoring** — 0-1 score measuring goal quality (deadline is 40% of the weight)
- ✅ **Guardrails** — Max 5 tasks in Inbox, prevents procrastination
- ✅ **Local-first** — Zero cloud, zero accounts, SQLite database
- ✅ **Fast** — Classified in <1ms, no dependencies beyond stdlib

## ❓ Why AIM?

**The problem:**
Most task managers force *you* to organize. You add a task, choose a category, set priority, add a deadline. That's friction—especially when tasks pile up. You end up with a bloated inbox and decision fatigue.

**Why AIM is different:**

| Problem | How AIM Solves It |
|---------|-------------------|
| **Inbox pile-up** | Max 5 tasks. Forces you to clarify vague tasks before adding. |
| **Manual prioritization** | BM25 auto-routes Focus vs. Pending. You don't decide—the system does. |
| **Vague goals** | SMART scoring penalizes unclear tasks. Add a deadline and watch your score jump. |
| **Feature bloat** | 5 commands. That's it. No notifications, no syncing, no premium features. |
| **Data lock-in** | Local SQLite. Copy your DB anywhere. Not locked to a service. |
| **Slow classification** | <1ms per task. Faster than you can think. |

**When AIM shines:**
- You're an engineer, PM, or maker who captures tasks quickly
- You value privacy and offline-first tools
- You're tired of complex task managers with 50+ features
- You want your system to *encourage* clear thinking, not enable procrastination

**Who might prefer something else:**
- You need team collaboration/sharing → Todoist, Asana
- You want drag-and-drop UI → Things, Notion
- You need time tracking → Harvest, Clockify

---

## ⚡ Features

### Auto-Intelligent Classification

Your tasks are automatically routed using **BM25**, a proven information retrieval algorithm:

```
Input:  "Fix authentication bug by Friday"
        ↓
        BM25 Scoring (similarity to examples)
        ↓
Output: Focus (92% confidence)
```

### SMART Goal Scoring

Every task gets a score (0-1) encouraging clear, actionable goals:

```
Score = Specific(0.2) + Measurable(0.25) + Achievable(0.15) + Time_bound(0.4)
```

High weight on deadlines—just adding "by Friday" boosts most scores by +0.20.

### Task Categories

| Category | Purpose | Examples |
|----------|---------|----------|
| **Focus** | Urgent & actionable | Fix bug by tomorrow, Write report by Friday |
| **Pending** | Someday/waiting | Learn Rust by Q2, Schedule review |
| **Reference** | Info to keep | API docs, password reminder |
| **Inbox** | Unclear, needs review | Vague tasks or things to organize |

### Inbox Guardrails

- **Max 5 tasks** in Inbox (soft warning at 3)
- Forces prioritization, prevents pile-up
- Use `--force` to override when needed

## 🚀 Quick Start

### Install

Clone and build:

```bash
git clone https://github.com/yourusername/aim
cd aim
make build
```

The binary is created as `./aim`. Add to your PATH or create a symlink:

```bash
ln -s $(pwd)/aim /usr/local/bin/aim
```

### Basic Usage

```bash
# Add tasks (auto-classified and scored)
aim add "Fix authentication bug by Friday"
aim add "Research machine learning options"
aim add "Review pull request"

# List all tasks by category
aim list

# See SMART scores
aim list --scores

# Mark task as done
aim done 1

# Soft delete task (kept for analytics)
aim rm 2

# Permanently delete task (requires confirmation)
aim rm --force 3

# Show completed tasks
aim list --show-completed

# Clear everything
aim clear
```

## 📖 Usage

### All Commands

```bash
aim add "<task text>"              Add a new task (auto-classified)
aim add "<task>" --force           Force add to Focus even if over limit
aim list                            Show all tasks by category
aim list --scores                  Show SMART scores for each task
aim list --show-completed          Include completed tasks with strikethrough
aim done <task_id>                 Mark task as completed
aim rm <task_id>                   Mark task as deleted (soft delete)
aim rm --force <task_id>           Permanently delete task (irreversible)
aim clear                           Delete all tasks (requires confirmation)
```

### Example Workflow

```bash
$ aim add "Write quarterly report by Friday"
✓ Task 1 added to Focus

$ aim add "research ML frameworks"
✓ Task 2 added to Pending

$ aim list
📋 Tasks by Category

▸ Focus (1)
  [1] Write quarterly report by Friday

▸ Pending (1)
  [2] research ML frameworks

$ aim done 1
Completed task 1: "Write quarterly report by Friday"

$ aim list
📋 Tasks by Category

▸ Pending (1)
  [2] research ML frameworks
```

## 🔍 How Classification Works

AIM uses **BM25** (Okapi Best Matching) to match incoming tasks against category examples:

1. **Training examples** — Embedded patterns for each category (Focus, Pending, Reference)
2. **BM25 scoring** — Ranks task similarity to each category
3. **Confidence** — Calculates confidence as ratio of best/second-best scores
4. **SMART filter** — Vague tasks (<0.4) forced to Inbox for review

**Why BM25?**
- ✅ Fast: <1ms classification
- ✅ Explainable: See which patterns matched
- ✅ Accurate: 78%+ on diverse tasks
- ✅ No dependencies: Pure algorithm, no ML models

### SMART Scoring Details

Each task is rated 0-1 on four dimensions:

| Dimension | Weight | Measures | Examples |
|-----------|--------|----------|----------|
| **Specific** | 20% | Clear subject + action verb | write, fix, implement |
| **Measurable** | 25% | Numbers or completion criteria | 3 reports, 30% reduction |
| **Achievable** | 15% | Realistic scope | 1 sprint worth of work |
| **Time-bound** | 40% | Has deadline or timeframe | by Friday, at 3pm |

**Boost your scores:**
- Add a deadline: +0.20-0.30
- Use action verb: +0.15
- Add numbers: +0.15

## 📊 Data & Storage

**Location:** `~/.aim/aim.db` (SQLite)

Created automatically on first run. Back it up with:

```bash
cp ~/.aim/aim.db ~/.aim/aim.db.backup
```

**Schema:** Single `tasks` table with columns:
- `id`, `text`, `category`, `smart_score`, `is_completed`, `is_deleted`, etc.

## 🛠️ Architecture

| Component | Purpose |
|-----------|---------|
| **main.v** | CLI app entry point |
| **cli.v** | Command definitions & execution |
| **calc.v** | BM25 & SMART scoring logic |
| **db.v** | SQLite database operations |
| **utils.v** | Helper functions |

**Stats:**
- ~800 lines of V code
- Zero external dependencies
- Single file per module

## 🏗️ Tech Stack

- **Language:** V — Fast, compiled, simple syntax
- **Database:** SQLite — Local-first, no setup
- **Algorithm:** BM25 (Okapi) — Industry-standard text ranking
- **SMART Framework** — Custom goal quality scoring

## 📚 Documentation

- **[FAQ](FAQ.md)** — Common questions and troubleshooting
- **[Tutorial](TUTORIAL.md)** — Guided walkthrough with examples
- **[Dev Docs](dev_docs/)** — Architecture, design decisions, improvements

## 🎯 Design Principles

1. **Local-first** — Your data stays on your machine. No cloud, no accounts, no sync.
2. **Minimal** — Few commands, clear behavior, no unnecessary complexity.
3. **Actionable** — Vague tasks are discouraged; SMART scoring rewards clarity.
4. **Guarded** — Inbox limits prevent task pile-up and forced procrastination.
5. **Explainable** — Scores are transparent; you can see why tasks scored the way they did.

## 🔄 Workflow Philosophy

```
Traditional task manager:
  You add → You prioritize → You categorize → You execute

AIM:
  You add (clear) → AIM auto-classifies + scores → You execute
```

The system's job is classification. Your job is clarity.

## ❓ FAQ

**Is AIM free?**  
Yes. Core features (BM25, SMART scoring, all commands) are free forever. No subscriptions, no paywalls.

**Does it sync to cloud?**  
No. Everything is local on your machine. Great for privacy, offline-first.

**Can I export my tasks?**  
Directly: Copy `~/.aim/aim.db`. Future: Export/import commands planned.

**What about recurring tasks?**  
Not yet. Planned for Phase 2.

**How accurate is classification?**  
~78% on diverse task types. Better with clear task descriptions.

For more FAQs, see [FAQ.md](FAQ.md).

## 📝 Contributing

Contributions welcome! This is a learning project—help us improve:

1. Add more category examples to `mits.txt`, `backlog.txt`, `reference.txt`
2. Improve SMART scoring heuristics in `calc.v`
3. Test edge cases and file issues
4. Expand docs and tutorials

See [AGENTS.md](AGENTS.md) for build/test commands.

## 📄 License

MIT License — Use freely in personal and commercial projects.

---

<div align="center">

**[Documentation](TUTORIAL.md) • [FAQ](FAQ.md) • [Issues](https://github.com/yourusername/aim/issues)**

Made with ❤️ for people who want to focus.

</div>
