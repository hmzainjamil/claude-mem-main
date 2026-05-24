# claude-mem-main

> **Claude remembers everything** — persistent cross-session memory layer for Claude Code: facts, preferences, decisions, and context that survive every conversation

<div align="center">

[![Stars](https://img.shields.io/github/stars/hmzainjamil/claude-mem-main?style=flat&color=FFD700&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/stargazers)
[![Forks](https://img.shields.io/github/forks/hmzainjamil/claude-mem-main?style=flat&color=00BFFF&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/network)
[![Issues](https://img.shields.io/github/issues/hmzainjamil/claude-mem-main?style=flat&color=FF6347&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/issues)
[![PRs](https://img.shields.io/github/issues-pr/hmzainjamil/claude-mem-main?style=flat&color=32CD32&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/pulls)
[![Last Commit](https://img.shields.io/github/last-commit/hmzainjamil/claude-mem-main?style=flat&color=9370DB&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/commits)

</div>

<div align="center">

![Claude Code](https://img.shields.io/badge/Claude_Code-Memory-FF6B35?labelColor=555&style=flat)
![Persistent](https://img.shields.io/badge/Memory-Persistent-2196F3?labelColor=555&style=flat)
![Cross Session](https://img.shields.io/badge/Sessions-Cross--Session-9C27B0?labelColor=555&style=flat)
![JSONL](https://img.shields.io/badge/Storage-JSONL%2FMarkdown-607D8B?labelColor=555&style=flat)
![Hooks](https://img.shields.io/badge/Claude_Hooks-Stop%2FStart-4CAF50?labelColor=555&style=flat)
![Auto Index](https://img.shields.io/badge/Index-Auto--Build-FF5722?labelColor=555&style=flat)
![Zero Config](https://img.shields.io/badge/Setup-Zero_Config-00BCD4?labelColor=555&style=flat)

</div>

---

## Why This Exists

Claude forgets everything when a session ends. Every new conversation starts from zero — no knowledge of your preferences, your project context, your past decisions, or your established patterns. This is fine for one-off tasks. It's a productivity killer for ongoing work.

`claude-mem` is a persistent memory layer that runs alongside Claude Code. It captures important facts, decisions, preferences, and learnings during sessions and makes them available in future sessions — automatically.

No more explaining your tech stack every session. No more re-establishing preferences. No more losing hard-won context.

---

## ☠️ STARTUPS / BUSINESSES

Knowledge loss between AI sessions is a hidden productivity tax. Every time your team member re-explains your tech stack, your client rules, or your naming conventions — that's 5–15 minutes gone. Multiply by 10 sessions/day × 10 developers = 500–1500 minutes/day of context re-establishment. Claude-mem eliminates that.

- **Solo founders** — Claude knows your entire product without re-explaining
- **Agency owners** — per-client memory: rules, brand voice, stack, blacklists
- **Dev teams** — shared memory across sessions, preserved architectural decisions
- **Power users** — preferences, shortcuts, and personal rules persist forever

---

## At a Glance

| Property | Detail |
|---|---|
| **System type** | Persistent memory layer for Claude Code |
| **Storage format** | JSONL queue + Markdown memory files |
| **Persistence mechanism** | Stop hook → processes queue → writes to MEMORY.md |
| **Load mechanism** | SessionStart hook → injects MEMORY.md into context |
| **Memory location** | `~/.claude/projects/[project]/memory/MEMORY.md` |
| **Queue location** | `~/.claude/session-queue.jsonl` |
| **Index file** | `MEMORY.md` — master index with links to topic files |
| **Topic files** | Individual `.md` files per memory category |
| **Auto-write** | Claude writes learnings to queue during session |
| **Auto-process** | Stop hook processes queue into permanent memory |
| **Search** | Full-text search across all memory files |
| **Scope** | Per-project and global memory supported |

---

## 🧠 CONCEPTS

| Concept | Definition |
|---|---|
| **Session Queue** | `~/.claude/session-queue.jsonl` — append-only log of learnings |
| **Stop Hook** | Claude Code hook that runs when session ends |
| **SessionStart Hook** | Hook that runs at session start — injects memory |
| **MEMORY.md** | Master index file loaded into every session's context |
| **Topic File** | `feedback_X.md` or `project_X.md` — categorized memory |
| **Memory Category** | Type: feedback, project, user profile, reference |
| **Context Injection** | Memory file content included in system prompt |
| **Memory Compaction** | Dedup + summarize old entries to save context tokens |
| **Global Memory** | `~/.claude/CLAUDE.md` — applies to all projects |
| **Project Memory** | `~/.claude/projects/[hash]/memory/` — project-scoped |
| **Auto-learn** | Claude writes learnings without explicit user instruction |
| **Memory Index** | MEMORY.md table: [topic](file.md) — description |
| **Feedback Pattern** | `feedback_X.md` — what went wrong and how to fix it |
| **Profile Pattern** | `user_X.md` — person profiles with context |
| **Reference Pattern** | `reference_X.md` — stable factual references |

---

### 🔥 Hot

```bash
# Install memory system
git clone https://github.com/hmzainjamil/claude-mem-main ~/.claude/mem-system
cd ~/.claude/mem-system && python3 install.py

# Write a learning to queue mid-session
echo '{"type":"feedback","topic":"api_auth","content":"Always use Bearer token not Basic auth for this API"}' >> ~/.claude/session-queue.jsonl

# Process queue manually (also runs automatically on Stop hook)
python3 ~/.claude/bin/process-memory-queue.py

# Search memory
grep -r "Bearer token" ~/.claude/projects/*/memory/

# View current memory index
cat ~/.claude/projects/$(python3 -c "import hashlib,os; print(hashlib.md5(os.getcwd().encode()).hexdigest())")/memory/MEMORY.md

# Add global rule
echo "\n- [API Auth Rule](reference_api_auth.md) — Always Bearer token for client X API" >> ~/.claude/CLAUDE.md

# Compact old memory (deduplicate)
python3 ~/.claude/bin/compact-memory.py --project current
```

---

## Installation

```bash
# 1. Clone repository
git clone https://github.com/hmzainjamil/claude-mem-main ~/.claude/mem-system

# 2. Run installer
python3 ~/.claude/mem-system/install.py

# What installer does:
# - Creates ~/.claude/projects/ directory structure
# - Installs Stop hook → process-memory-queue.py
# - Installs SessionStart hook → inject-memory.py
# - Creates initial MEMORY.md template
# - Adds auto-learn instructions to CLAUDE.md

# 3. Verify hooks installed
cat ~/.claude/settings.json | python3 -m json.tool | grep -A5 hooks

# 4. Start a session — memory activates automatically
claude "let's work on the auth module"
```

---

## Memory File Structure

```
~/.claude/
├── CLAUDE.md                          # Global memory + rules
├── session-queue.jsonl                # Append-only learning queue
├── bin/
│   ├── process-memory-queue.py        # Stop hook processor
│   └── inject-memory.py              # SessionStart hook
└── projects/
    └── [project-hash]/
        └── memory/
            ├── MEMORY.md              # Master index
            ├── feedback_auth.md       # Auth-related learnings
            ├── feedback_api_client.md # Client-specific rules
            ├── project_myapp.md       # Project context
            ├── user_mc.md             # User profile
            └── reference_stack.md    # Tech stack reference
```

---

## Memory Categories

| Category | Filename pattern | Contains | Example |
|---|---|---|---|
| Feedback | `feedback_X.md` | What went wrong + fix | "Never use `rm -rf` without confirm" |
| Project | `project_X.md` | Project context + decisions | "Auth uses JWT RS256, not HS256" |
| User Profile | `user_X.md` | Person context + preferences | "User mc: prefers short responses" |
| Reference | `reference_X.md` | Stable factual data | "Client API endpoints + auth" |
| Rules | Inline in MEMORY.md | Hard behavioral rules | "Always check test exists before refactor" |

---

## MEMORY.md Template

```markdown
# Memory Index

- [Auth Rules](feedback_auth.md) — JWT RS256 only, refresh token in httpOnly cookie
- [Client API](reference_client_api.md) — Base URL, endpoints, Bearer token auth
- [Tech Stack](reference_stack.md) — Next.js 14, Prisma, Postgres, Tailwind, Vercel
- [User Profile](user_mc.md) — Caveman mode, max 120 words, no filler
- [Project Context](project_myapp.md) — SaaS CRM, B2B, 3 engineers, 50 clients
- [Naming Conventions](reference_naming.md) — camelCase vars, PascalCase components
```

---

## Hook Configuration

```json
// ~/.claude/settings.json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [{
          "type": "command",
          "command": "python3 ~/.claude/bin/process-memory-queue.py"
        }]
      }
    ],
    "SessionStart": [
      {
        "matcher": "",
        "hooks": [{
          "type": "command",
          "command": "python3 ~/.claude/bin/inject-memory.py"
        }]
      }
    ]
  }
}
```

---

## session-queue.jsonl Format

```jsonl
{"type":"feedback","topic":"postgres","content":"Always use transactions for multi-table writes","session":"2026-05-25","project":"myapp"}
{"type":"rule","topic":"testing","content":"Never delete tests without checking if feature is live","session":"2026-05-25","project":"myapp"}
{"type":"project","topic":"auth","content":"OAuth2 PKCE flow implemented, refresh token in httpOnly cookie path /api/auth","session":"2026-05-25","project":"myapp"}
{"type":"preference","topic":"output","content":"User prefers numbered lists over bullets for task breakdowns","session":"2026-05-25","project":"global"}
```

---

## Auto-Learn Patterns

Claude writes to queue automatically when it detects:

| Trigger | What gets written | Category |
|---|---|---|
| Error + fix discovered | The fix + root cause | feedback |
| Decision made | Decision + rationale | project |
| User preference stated | Preference + context | preference |
| New API/integration | Endpoints + auth method | reference |
| Naming convention used | Pattern + scope | reference |
| Rule established | Rule + enforcement | rule |

---

## Memory Compaction

```bash
# View memory size
du -sh ~/.claude/projects/*/memory/

# Compact (deduplicate + summarize)
python3 ~/.claude/bin/compact-memory.py

# What compaction does:
# 1. Deduplicates entries with >85% semantic similarity
# 2. Merges related short entries into single entry
# 3. Archives entries older than 90 days
# 4. Rebuilds MEMORY.md index
# 5. Reports tokens saved
```

---

## Comparison: Memory Approaches

| Approach | Persistence | Context cost | Setup | Search |
|---|---|---|---|---|
| **claude-mem (this)** | ✅ Forever | Low (indexed) | 5 min | ✅ Full-text |
| Manual CLAUDE.md edits | ✅ Forever | High (full file) | Manual | ❌ |
| Session notes | ❌ Session only | N/A | None | ❌ |
| Mem0 MCP | ✅ | Medium | Account needed | ✅ |
| External DB | ✅ | High (retrieval) | Complex | ✅ |
| Brain/memory skills | ✅ | Medium | Skill install | ✅ |

---

## ■ Tips

> **Memory files should be decision logs, not information dumps**
> Bad memory: "We use React"
> Good memory: "We use React 18 with Suspense — do NOT use class components, team decision 2025-03"
> Source: [Architecture Decision Records](https://adr.github.io/)

> **Feedback files are the most valuable**
> Every time Claude makes an error and you fix it: that fix belongs in memory.
> After 30 sessions, you'll have a perfect rule set trained on your actual mistakes.

> **Per-project memory >> global memory for clients**
> Client A has totally different rules than Client B.
> Use project-scoped memory to avoid cross-contamination.

> **Memory injection costs tokens — keep MEMORY.md tight**
> Target: <2000 tokens for the MEMORY.md index.
> Full detail lives in topic files — MEMORY.md is just the index.

---

## Troubleshooting

| Issue | Cause | Fix |
|---|---|---|
| Memory not loading | SessionStart hook not installed | Re-run `install.py` or manually add hook |
| Queue not processed | Stop hook not triggered | Check `~/.claude/settings.json` hooks section |
| Memory growing too large | No compaction | Run `compact-memory.py` weekly |
| Wrong project memory | Hash mismatch | Check cwd when session started |
| Outdated rules persisting | Stale topic file | Delete `feedback_X.md` and let Claude relearn |
| Injection too slow | MEMORY.md too large | Run compaction, target <100 lines in index |

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/claude-mem-main&type=Date)](https://star-history.com/#hmzainjamil/claude-mem-main&Date)

---

<div align="center">

Built by [hmzainjamil](https://github.com/hmzainjamil) · Part of the [DigiMinds AI Agency](https://github.com/hmzainjamil) toolkit

</div>

---

## Memory Schema Examples

### Feedback Memory File

```markdown
# feedback_postgres.md

## Rule: Transaction boundaries

Always wrap multi-table writes in explicit transactions.

```python
# WRONG — partial writes if second fails
await db.users.create(user_data)
await db.profiles.create(profile_data)

# CORRECT — atomic
async with db.transaction():
    user = await db.users.create(user_data)
    await db.profiles.create({**profile_data, "user_id": user.id})
```

**Learned:** 2026-01-15 — production incident where user created without profile
**Source:** Actual bug in session abc123
```

### Project Memory File

```markdown
# project_saas_crm.md

## Architecture

- **Stack:** Next.js 14 App Router, Prisma, PostgreSQL, Tailwind, Vercel
- **Auth:** Clerk — JWT RS256, refresh token in httpOnly cookie
- **State:** Zustand for client, React Query for server state
- **Payments:** Stripe — subscription billing, webhook at /api/webhooks/stripe

## Key Decisions

- **Decision:** RSC for data fetching, not tRPC
  Reason: Simpler architecture, colocated fetching, better caching
  Date: 2026-01-10

- **Decision:** Prisma over Drizzle
  Reason: Better type safety, team familiarity
  Date: 2026-01-08

## Conventions

- Components: PascalCase, colocated with feature module
- API routes: kebab-case, RESTful
- DB tables: snake_case, plural
- Env vars: SCREAMING_SNAKE_CASE

## Gotchas

- Stripe webhook must receive raw body — don't parse before sig verification
- Clerk user ID ≠ DB user ID — always join via `clerkId` field
```

---

## Global vs Project Memory

| Scope | Location | Applied to | Use for |
|---|---|---|---|
| **Global** | `~/.claude/CLAUDE.md` | All sessions | Preferences, personal rules, output format |
| **Global Memory** | `~/.claude/projects/-Users-*/memory/` | All sessions | Cross-project learnings |
| **Project** | `~/.claude/projects/[hash]/memory/` | Current project | Tech stack, client rules, decisions |
| **In-session** | Conversation context | Current session only | Temporary working memory |

---

## Auto-Learn Triggers

Claude writes to session queue when it detects these patterns in conversation:

```python
TRIGGER_PATTERNS = [
    "the fix is",          → feedback category
    "turns out",           → feedback category  
    "never do this",       → rule category
    "we decided",          → project category
    "going forward",       → rule category
    "preference:",         → preference category
    "always use",          → rule category
    "note for next time",  → feedback category
    "the convention is",   → reference category
    "client requires",     → project category
]
```

---

## Memory Compaction Script

```python
# ~/.claude/bin/compact-memory.py
import os, json, hashlib
from pathlib import Path

def compact_project_memory(project_hash: str):
    mem_dir = Path.home() / '.claude' / 'projects' / project_hash / 'memory'
    
    for md_file in mem_dir.glob('feedback_*.md'):
        content = md_file.read_text()
        lines = content.split('\n')
        
        # Remove duplicate rules (semantic similarity > 85%)
        unique_lines = deduplicate(lines, threshold=0.85)
        
        # Archive entries older than 90 days
        recent = filter_by_date(unique_lines, days=90)
        
        # Rewrite file
        md_file.write_text('\n'.join(recent))
    
    # Rebuild MEMORY.md index
    rebuild_index(mem_dir)
    
    print(f"Compacted {project_hash}: {before} → {after} lines")
```

---

## Integration with Claude Code Hooks

### Full Hook Configuration

```json
{
  "hooks": {
    "Stop": [{
      "matcher": "",
      "hooks": [{
        "type": "command",
        "command": "python3 ~/.claude/bin/process-memory-queue.py --project=$(pwd | md5 -q)"
      }]
    }],
    "SessionStart": [{
      "matcher": "",
      "hooks": [{
        "type": "command", 
        "command": "python3 ~/.claude/bin/inject-memory.py --project=$(pwd | md5 -q)"
      }]
    }],
    "PreToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "python3 ~/.claude/bin/check-memory-rules.py"
      }]
    }]
  }
}
```

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/claude-mem-main&type=Date)](https://star-history.com/#hmzainjamil/claude-mem-main&Date)

---

<div align="center">

Built by [hmzainjamil](https://github.com/hmzainjamil) · Part of the [DigiMinds AI Agency](https://github.com/hmzainjamil) toolkit

</div>
