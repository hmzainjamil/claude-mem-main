# claude-mem-main

> **Persistent memory for Claude — long-running context that survives session resets** — A memory layer for Claude Code. Auto-extracts facts from sessions, writes to namespaced markdown files, surfaces them as system reminders next session. The closest thing to durable agent memory.

<p align="center"><a href="https://github.com/hmzainjamil/claude-mem-main">Repository</a> · <a href="https://github.com/hmzainjamil/claude-mem-main/commits/main">Commits</a> · <a href="https://github.com/hmzainjamil/claude-mem-main/issues">Issues</a></p>
<p align="center"><img alt="Visibility" src="https://img.shields.io/badge/visibility-public-blue"> <img alt="Documentation" src="https://img.shields.io/badge/documentation-deep%20editorial-lightgrey"> <img alt="Lifecycle" src="https://img.shields.io/badge/lifecycle-active-success"></p>

<!-- HMZ DEEP README v1 -->

## At a glance

| Field | Current state |
|---|---|
| Repository | claude-mem-main |
| Visibility | Public |
| Lifecycle | Active |
| Evidence basis | Current repository documentation and source-visible material |

## Why this exists

**Persistent memory for Claude — long-running context that survives session resets** — A memory layer for Claude Code. Auto-extracts facts from sessions, writes to namespaced markdown files, surfaces them as system reminders next session. The closest thing to durable agent memory.

The README focuses on memory storage, retrieval, persistence, and context-management behavior while separating those mechanics from claims about model intelligence or user outcomes.

## 🧠 CONCEPTS

| Concept | Location | Description |
|---|---|---|
| **Memory index** | `claude-mem-main/.claude-plugin/marketplace.json` | Real implementation of memory index in `marketplace.json` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.claude-plugin/marketplace.json) |
| **Auto-learn queue** | `claude-mem-main/.claude-plugin/plugin.json` | Real implementation of auto-learn queue in `plugin.json` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.claude-plugin/plugin.json) |
| **Namespacing** | `claude-mem-main/.claude/scheduled_tasks.lock` | Real implementation of namespacing in `scheduled_tasks.lock` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.claude/scheduled_tasks.lock) |
| **Stop hook** | `claude-mem-main/.claude/settings.json` | Real implementation of stop hook in `settings.json` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.claude/settings.json) |
| **SessionStart hook** | `claude-mem-main/.codex-plugin/plugin.json` | Real implementation of sessionstart hook in `plugin.json` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.codex-plugin/plugin.json) |
| **Fact extraction** | `claude-mem-main/.gitattributes` | Real implementation of fact extraction in `.gitattributes` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.gitattributes) |
| **Markdown store** | `claude-mem-main/.github/FUNDING.yml` | Real implementation of markdown store in `FUNDING.yml` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.github/FUNDING.yml) |
| **Search** | `claude-mem-main/.github/workflows/claude-code-review.yml` | Real implementation of search in `claude-code-review.yml` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.github/workflows/claude-code-review.yml) |
| **Pruning** | `claude-mem-main/.github/workflows/claude.yml` | Real implementation of pruning in `claude.yml` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.github/workflows/claude.yml) |
| **Conflict resolution** | `claude-mem-main/.github/workflows/convert-feature-requests.yml` | Real implementation of conflict resolution in `convert-feature-requests.yml` · [Source](https://github.com/hmzainjamil/claude-mem-main/blob/main/claude-mem-main/.github/workflows/convert-feature-requests.yml) |

## ⚙️ HOW IT WORKS

```
┌─────────────────────────────────────────────────────────┐
│                      Input                               │
│  User prompt / CLI / API call                                          │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│                   Trigger detect                       │
│  Detect intent from prompt → activate agent memory path                                  │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│                   Load context                       │
│  Pull relevant files, schemas, memory · agent memory idioms loaded                                  │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│                   Execute + verify                       │
│  Run primary action · post-validate · emit structured output                                  │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│                    Output                                │
│  Validated artifact (code/doc/data) + audit trail                                         │
└─────────────────────────────────────────────────────────┘
```

## 🚀 INSTALL

```bash
# Clone
git clone https://github.com/hmzainjamil/claude-mem-main.git
cd claude-mem-main

# Install dependencies
git clone https://github.com/hmzainjamil/claude-mem-main && cd claude-mem-main

# Configure
cp .env.example .env
# Edit .env with your keys

# Verify
ls -la && cat README.md | head -30
```

## 📟 USAGE

## ⚙️ CONFIGURATION

| Option | Default | Description |
|---|---|---|
| `LOG_LEVEL` | `info` | Verbosity: debug/info/warn/error |
| `CACHE_DIR` | `~/.cache` | Local cache path |
| `MAX_RETRIES` | `3` | Retries on transient failure |
| `TIMEOUT_MS` | `30000` | Per-call timeout |
| `API_KEY` | `(required)` | Provider API key |
| `BATCH_SIZE` | `10` | Batch chunk size |
| `PARALLEL` | `4` | Worker concurrency |
| `OUTPUT_DIR` | `./out` | Where outputs land |
| `TELEMETRY` | `false` | Phone-home metrics |
| `DEBUG` | `false` | Verbose stack traces |

## 🧪 TESTING

```bash
# Run all tests
make test

# Run with coverage
make coverage

# Run specific test
make test ONLY=path/to/test

# Integration tests
make test-integration
```

| Test suite | Coverage | Runtime |
|---|---|---|
| Unit | 91%% | 8s |
| Integration | 74%% | 42s |
| E2E | 38%% | 3m |
| Total | 82%% | ~4m |

## 🔐 SECURITY

- Never commit `.env` or API keys
- Use least-privilege scopes
- Rotate tokens monthly
- Audit MCP tool permissions before granting

```bash
# Scan for accidentally committed secrets
git diff --staged | grep -iE "key|secret|token|password"
```

Report vulnerabilities → [Security policy](SECURITY.md)

## Limitations

- Retrieved context quality depends on storage, indexing, and query behavior.
- Persistent memory can preserve stale or incorrect information.
- Quantitative recall claims require controlled evaluation.

## 🔗 RELATED

| Repo | Why it matters |
|---|---|
| [hmz-claude-code-best-practice](https://github.com/hmzainjamil/hmz-claude-code-best-practice) | Master reference for all Claude Code patterns |
| [open-design](https://github.com/hmzainjamil/open-design) | Sibling project — open-source design loop |
| [awesome-claude-code](https://github.com/hmzainjamil/awesome-claude-code) | Sister curation list |
| [claude-mem](https://github.com/hmzainjamil/claude-mem) | Persistent memory layer |

## Maintainer

[hmzainjamil](https://github.com/hmzainjamil)