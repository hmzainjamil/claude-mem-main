# claude-mem-main

> **Claude remembers everything** — persistent cross-session memory layer for Claude Code: facts, preferences, decisions, and context that survive every conversation

<div align="center">

[![Stars](https://img.shields.io/github/stars/hmzainjamil/claude-mem-main?style=for-the-badge&color=FFD700&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/stargazers)
[![Forks](https://img.shields.io/github/forks/hmzainjamil/claude-mem-main?style=for-the-badge&color=00BFFF&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/network)
[![Issues](https://img.shields.io/github/issues/hmzainjamil/claude-mem-main?style=for-the-badge&color=FF6347&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/issues)
[![PRs](https://img.shields.io/github/issues-pr/hmzainjamil/claude-mem-main?style=for-the-badge&color=32CD32&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/pulls)
[![Last Commit](https://img.shields.io/github/last-commit/hmzainjamil/claude-mem-main?style=for-the-badge&color=9370DB&labelColor=555)](https://github.com/hmzainjamil/claude-mem-main/commits)

</div>

<div align="center">

![Claude Code](https://img.shields.io/badge/Claude_Code-Memory-FF6B35?labelColor=555&style=flat)
![Persistent](https://img.shields.io/badge/Memory-Persistent-2196F3?labelColor=555&style=flat)
![Cross Session](https://img.shields.io/badge/Sessions-Cross--Session-9C27B0?labelColor=555&style=flat)
![JSONL](https://img.shields.io/badge/Storage-JSONL%2FMarkdown-607D8B?labelColor=555&style=flat)
![Hooks](https://img.shields.io/badge/Claude_Hooks-Stop%2FStart-4CAF50?labelColor=555&style=flat)
![Auto Index](https://img.shields.io/badge/Index-Auto--Build-FF5722?labelColor=555&style=flat)

</div>

---

## Why This Exists

Claude forgets everything when a session ends. Every new conversation starts from zero — no knowledge of your preferences, your project context, your past decisions, or your established patterns. This is fine for one-off tasks. It's a productivity killer for ongoing work.

`claude-mem` is a persistent memory layer that runs alongside Claude Code. It captures important facts, decisions, preferences, and learnings during sessions and makes them available in future sessions — automatically.

No more explaining your tech stack every session. No more re-establishing preferences. No more losing hard-won context.

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
| **Session Queue** | `~/.claude/session-queue.jsonl` — learnings written during session |
| **Stop Hook** | Script run when Claude Code session ends — processes queue |
| **Start Hook** | Script run when session starts — injects memory into context |
| **MEMORY.md** | Master index file listing all memory topics with links |
| **Topic File** | Per-category memory file (e.g., `feedback_model_routing.md`) |
| **Memory Entry** | Structured fact: what was learned, when, why it matters |
| **Auto-learn** | Claude writes to queue without being explicitly asked |
| **Context injection** | Memory content added to system prompt at session start |
| **Memory scope** | Global (`~/.claude/`) vs project (`~/.claude/projects/[id]/`) |
| **Consolidation** | Merging duplicate/outdated entries — runs on `Stop` |
| **Recall** | Retrieving specific memories by topic or keyword |
| **Feedback loop** | Error → learned → written → never repeated |

### 🔥 Hot

- **Auto-learn from every error** — Claude writes learnings to queue automatically at session end
- **Blockchain-style manifest** — each memory file is versioned and linked from the master index
- **Cross-project global memory** — preferences and patterns shared across all projects
- **Instant context injection** — MEMORY.md loaded at session start with zero extra prompting

---

## ⚙️ HOW IT WORKS

```
Session start → Stop hook reads MEMORY.md → injects into Claude context
       ↓
Claude works with full memory context (preferences, past decisions, patterns)
       ↓
During session: Claude writes learnings → ~/.claude/session-queue.jsonl
       ↓
Session ends → Stop hook fires → processes session-queue.jsonl
       ↓
New entries → written to topic .md files → MEMORY.md index updated
       ↓
Next session → full updated context available
```

**Memory anatomy:**
```jsonl
// session-queue.jsonl entry
{
  "timestamp": "2025-05-24T10:30:00Z",
  "topic": "feedback_model_routing",
  "content": "Always use Groq for sub-tasks; never Claude for internal processing",
  "category": "preference",
  "project": "global"
}
```

**MEMORY.md structure:**
```markdown
# Memory Index
- [Model routing preference](feedback_model_routing.md) — description
- [Never kill Ollama](feedback_never_kill_ollama.md) — description
- [Project X context](project_x.md) — description
```

---

## 🚀 INSTALL

### Quick install

```bash
git clone https://github.com/hmzainjamil/claude-mem-main.git ~/.claude/mem
cd ~/.claude/mem && ./install.sh
```

### Manual setup

```bash
# 1. Create memory directories
mkdir -p ~/.claude/projects/$(pwd | md5sum | head -c8)/memory
mkdir -p ~/.claude/memory

# 2. Create MEMORY.md index
cat > ~/.claude/memory/MEMORY.md << 'EOF'
# Memory Index
EOF

# 3. Create session queue
touch ~/.claude/session-queue.jsonl

# 4. Install Stop hook (add to ~/.claude/settings.json)
# See CONFIGURATION section below
```

### Hook configuration

Add to `~/.claude/settings.json`:

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.claude/mem/scripts/process-queue.py"
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.claude/mem/scripts/inject-memory.py"
          }
        ]
      }
    ]
  }
}
```

---

## 📟 USAGE

### Write a memory manually

```bash
# Add to session queue (processed at session end)
echo '{"topic":"my_preference","content":"Always use tabs not spaces","category":"code_style"}' \
  >> ~/.claude/session-queue.jsonl
```

### Ask Claude to remember

```
Remember: I prefer TypeScript over JavaScript for all new projects
```

```
Save this decision: we chose PostgreSQL over MongoDB because of complex relational queries
```

### Retrieve memory

```
What do you know about my model routing preferences?
```

```
Recall everything about the Elementec project
```

### Process queue manually

```bash
python3 ~/.claude/mem/scripts/process-queue.py
```

### View all memories

```bash
cat ~/.claude/memory/MEMORY.md
# or
ls ~/.claude/memory/*.md
```

### Search memories

```bash
grep -r "model routing" ~/.claude/memory/
grep -r "postgres" ~/.claude/projects/*/memory/
```

---

## ⚙️ CONFIGURATION

| Config key | Default | Options | Description |
|---|---|---|---|
| `memory_dir` | `~/.claude/memory/` | any path | Global memory directory |
| `project_memory` | `true` | `true`, `false` | Enable per-project memory |
| `queue_file` | `~/.claude/session-queue.jsonl` | any path | Session queue location |
| `auto_learn` | `true` | `true`, `false` | Auto-write learnings to queue |
| `consolidate_on_stop` | `true` | `true`, `false` | Merge duplicates on session end |
| `max_context_tokens` | `4000` | `1000-16000` | Max tokens injected into context |
| `index_format` | `markdown` | `markdown`, `json` | MEMORY.md format |
| `topic_per_file` | `true` | `true`, `false` | One file per memory topic |
| `include_timestamps` | `true` | `true`, `false` | Timestamp each memory entry |
| `global_scope` | `true` | `true`, `false` | Share memories across projects |
| `encrypt` | `false` | `true`, `false` | Encrypt sensitive memory files |
| `backup_on_write` | `true` | `true`, `false` | Backup before overwriting |

---

## 💡 TIPS AND TRICKS

### Writing Good Memories

> **Be specific, not vague** — "Use Groq llama-3.3-70b for all summarization tasks" beats "use fast models".

> **Include the why** — "PostgreSQL chosen over MongoDB — complex relational joins required" survives longer than "use Postgres".

> **One fact per entry** — don't bundle multiple learnings into one memory entry. They'll be searched and retrieved independently.

### Memory Organization

> **Use consistent topic names** — prefix with category: `feedback_`, `project_`, `reference_`, `user_`. Makes grep fast.

> **Date important decisions** — "2025-05-24: switched from Vercel to Fly.io — cold start latency issue". Dated memories are auditable.

> **Archive don't delete** — move outdated memories to `archive/` directory. Context about why something was changed is valuable.

### Performance

> **Cap injection size** — set `max_context_tokens: 4000`. Loading all memories kills context window. Index file + lazy load topic files.

> **Topic files over mega-file** — one file per topic makes search fast and prevents context bloat on injection.

> **Weekly consolidation** — run `process-queue.py --consolidate` weekly to merge duplicates and prune outdated entries.

---

## 🔧 TROUBLESHOOTING

| Issue | Cause | Fix |
|---|---|---|
| Memories not persisting | Stop hook not configured | Add hook to `~/.claude/settings.json` |
| Queue not processing | Script not executable | `chmod +x ~/.claude/mem/scripts/*.py` |
| Context not injected | Start hook misconfigured | Check PreToolUse hook path |
| Memory file corrupted | Write interrupted | Restore from `*.backup` file in memory dir |
| Too much context | All memories loaded | Set `max_context_tokens` lower; use index-only injection |
| Duplicate entries | Auto-learn writing twice | Set `consolidate_on_stop: true` |
| Project memory mixing | Wrong project ID | Check project memory dir: `~/.claude/projects/[id]/memory/` |
| MEMORY.md index stale | Not rebuilt after topic add | Run `process-queue.py --rebuild-index` |
| Encrypted files unreadable | Key mismatch | Check `~/.claude/mem/.key` exists and matches |
| Old memories not expiring | No TTL set | Add `ttl_days` field to entries you want to expire |

---

## 📊 ARCHITECTURE

```
claude-mem-main/
├── SKILL.md                          # Claude Code skill definition
├── scripts/
│   ├── process-queue.py              # Stop hook: queue → memory files
│   ├── inject-memory.py              # Start hook: memory → context
│   ├── consolidate.py                # Deduplicate and merge entries
│   └── search-memory.py             # Full-text search utility
├── templates/
│   ├── MEMORY.md.tpl                 # Master index template
│   ├── topic-file.md.tpl             # Per-topic file template
│   └── entry.jsonl.tpl              # Queue entry template
├── config/
│   └── mem-config.json              # Configuration file
├── install.sh                        # One-command installer
└── README.md

Memory storage:
~/.claude/
├── session-queue.jsonl               # Active session writes
├── memory/
│   ├── MEMORY.md                     # Master index
│   ├── feedback_*.md                 # Feedback/preference memories
│   ├── project_*.md                  # Project-specific memories
│   ├── reference_*.md                # Reference data
│   └── user_*.md                     # User profile memories
└── projects/
    └── [project-id]/
        └── memory/
            └── MEMORY.md             # Project-scoped index
```

---

## 🗺️ ROADMAP

| Feature | Status | ETA |
|---|---|---|
| JSONL queue + Stop hook processor | ✅ Done | — |
| MEMORY.md index system | ✅ Done | — |
| Per-project memory scope | ✅ Done | — |
| Auto-learn from session | ✅ Done | — |
| Cross-project global memory | ✅ Done | — |
| Encrypted sensitive memories | 🔄 In progress | Q3 2025 |
| Memory expiry (TTL) | 🔄 In progress | Q3 2025 |
| Vector search across memories | 📋 Planned | Q4 2025 |
| Memory export/import | 📋 Planned | Q4 2025 |
| Web UI for memory browser | 📋 Planned | Q1 2026 |
| MCP server mode | 📋 Planned | Q1 2026 |
| Memory sharing across team | 📋 Planned | Q2 2026 |

---

## ☠️ STARTUPS / BUSINESSES

**What persistent memory actually means for productivity:**

| Scenario | Without memory | With claude-mem |
|---|---|---|
| Daily Claude Code usage | Re-explain context every session | Full context from session 1 |
| Team preferences | Lost when conversation ends | Persisted globally |
| Architecture decisions | Re-discussed in every PR | Recorded with rationale |
| Bug patterns | Same bugs fixed multiple times | Pattern recognized and avoided |
| Client project context | Brief Claude every call | Auto-loaded from project memory |
| Model routing preferences | Override needed every session | Learned once, applied always |

**Power user ROI:**
- 5 min/session saved on context re-establishment
- 20 sessions/week = 100 min/week = 1.6 hours saved
- Per month: 6+ hours of reclaimed productivity

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/claude-mem-main&type=Date)](https://star-history.com/#hmzainjamil/claude-mem-main&Date)

---

<div align="center">

Built by [HMZ](https://github.com/hmzainjamil) · Persistent memory for Claude Code · Never repeat yourself

</div>
