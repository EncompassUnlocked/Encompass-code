---
name: product-self-knowledge
description: >
  Use this skill when the user asks about Anthropic's products, Claude model versions,
  Claude Code features, the Anthropic API, pricing, context windows, or anything that
  requires accurate, up-to-date knowledge of the Claude ecosystem. Trigger when users
  ask "which Claude model should I use?", "what is Claude Code?", "what's the context
  window for Opus?", "how do I use the API?", "what's the difference between Sonnet and
  Haiku?", or any question about Claude's capabilities, availability, or pricing —
  even if they don't mention "Anthropic" by name.
---

# Anthropic Product & Claude Ecosystem Reference

This skill provides accurate, current information about Anthropic's products: the Claude
model family, the Anthropic API, Claude Code, and claude.ai. Use it to answer questions
about model capabilities, pricing, context windows, and features.

> **Docs source:** https://platform.claude.com/docs/en/intro.md  
> **Models reference:** https://platform.claude.com/docs/en/about-claude/models/overview.md  
> **Claude Code:** https://code.claude.com/docs/en/overview.md

---

## The Claude Model Family

Claude is a family of large language models by Anthropic. All current models support
text and image input, text output, multilingual capabilities, and vision.

### Current (Latest) Models

| Model | API ID | Context Window | Max Output | Pricing (per MTok) | Notes |
|-------|--------|---------------|------------|-------------------|-------|
| **Claude Opus 4.7** | `claude-opus-4-7` | 1M tokens | 128k tokens | $5 in / $25 out | Most capable; step-change in agentic coding |
| **Claude Sonnet 4.6** | `claude-sonnet-4-6` | 1M tokens | 64k tokens | $3 in / $15 out | Best speed/intelligence balance |
| **Claude Haiku 4.5** | `claude-haiku-4-5-20251001` | 200k tokens | 64k tokens | $1 in / $5 out | Fastest, near-frontier intelligence |

**Aliases:** `claude-haiku-4-5` → resolves to `claude-haiku-4-5-20251001`.  
Starting with the 4.6 generation, dateless model IDs are pinned snapshots, not evergreen pointers.

### Model Capabilities Comparison

| Feature | Opus 4.7 | Sonnet 4.6 | Haiku 4.5 |
|---------|----------|------------|-----------|
| Extended Thinking | No | Yes | Yes |
| Adaptive Thinking | Yes | Yes | No |
| Priority Tier | Yes | Yes | Yes |
| Latency | Moderate | Fast | Fastest |
| Reliable knowledge cutoff | Jan 2026 | Aug 2025 | Feb 2025 |
| Training data cutoff | Jan 2026 | Jan 2026 | Jul 2025 |

**Reliable knowledge cutoff** = date through which knowledge is most extensive and reliable.  
**Training data cutoff** = broader range of training data used (may include less reliable coverage).

### Knowledge Cutoff Notes

- Opus 4.7 uses a new tokenizer. Its 1M context ≈ 555k words / ~2.5M unicode characters.
- Sonnet 4.6's 1M context ≈ 750k words / ~3.4M unicode characters.
- Haiku 4.5's 200k context ≈ 150k words / ~680k unicode characters.

### Legacy Models (Still Available)

| Model | API ID | Context | Max Output |
|-------|--------|---------|------------|
| Claude Opus 4.6 | `claude-opus-4-6` | 1M tokens | 128k tokens |
| Claude Sonnet 4.5 | `claude-sonnet-4-5-20250929` | 200k tokens | 64k tokens |
| Claude Opus 4.5 | `claude-opus-4-5-20251101` | 200k tokens | 64k tokens |
| Claude Opus 4.1 | `claude-opus-4-1-20250805` | 200k tokens | 32k tokens |
| Claude Sonnet 4 ⚠️ | `claude-sonnet-4-20250514` | 200k tokens | 64k tokens |
| Claude Opus 4 ⚠️ | `claude-opus-4-20250514` | 200k tokens | 32k tokens |

> ⚠️ **Claude Sonnet 4** and **Claude Opus 4** are deprecated and will be **retired June 15, 2026**.
> Migrate to Sonnet 4.6 and Opus 4.7 respectively.

### Cloud Platform IDs

| Model | AWS Bedrock ID | Vertex AI ID |
|-------|---------------|-------------|
| Opus 4.7 | `anthropic.claude-opus-4-7` | `claude-opus-4-7` |
| Sonnet 4.6 | `anthropic.claude-sonnet-4-6` | `claude-sonnet-4-6` |
| Haiku 4.5 | `anthropic.claude-haiku-4-5-20251001-v1:0` | `claude-haiku-4-5@20251001` |

Claude is also available on **Microsoft Foundry** and **Claude Platform on AWS**
(which uses the same model IDs as the Claude API, not Bedrock-style IDs).

---

## The Anthropic API

Anthropic offers two development paths:

| | Messages API | Claude Managed Agents |
|---|---|---|
| **What it is** | Direct model prompting | Pre-built configurable agent harness on managed infrastructure |
| **Best for** | Custom agent loops, fine-grained control | Long-running tasks, async work |
| **Docs** | platform.claude.com/docs | platform.claude.com/docs/en/managed-agents |

### Key API Features

- **Extended Thinking** — lets Claude show its reasoning before answering (Sonnet 4.6, Haiku 4.5, and most legacy 4.x models)
- **Adaptive Thinking** — dynamically allocates reasoning budget (Opus 4.7, Sonnet 4.6)
- **Prompt Caching** — cache frequently used context to reduce cost and latency
- **Batch Processing** — async batch API with up to 300k output tokens per message (Opus 4.7, Opus 4.6, Sonnet 4.6 with `output-300k-2026-03-24` beta header)
- **Streaming** — stream responses token by token
- **Vision** — analyze images, PDFs, and documents
- **Files API** (beta) — upload and reuse files across requests
- **Tool Use** — give Claude access to external tools (web search, code execution, browser, file system, etc.)
- **Structured Outputs** — force JSON or other structured formats
- **Embeddings** — generate text embeddings
- **Token Counting** — count tokens before sending a request

### Available SDKs

Python, TypeScript, Go, Java, C#, Ruby, PHP, CLI, Terraform

### Developer Console

- **Workbench** at platform.claude.com — prototype and test prompts in browser
- **API Reference** at platform.claude.com/docs/en/api/overview

---

## Claude Code

Claude Code is an AI-powered agentic coding assistant. It reads your codebase, edits
files, runs commands, and integrates with development tools. It is not just a chat
interface — it is an autonomous agent that works across your entire project.

### How to Install

**macOS/Linux/WSL:**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**
```powershell
irm https://claude.ai/install.ps1 | iex
```

**Homebrew:** `brew install --cask claude-code`  
**WinGet:** `winget install Anthropic.ClaudeCode`

Also available via `apt`, `dnf`, or `apk` on Debian, Fedora, RHEL, and Alpine.

### Environments / Interfaces

| Interface | Description |
|-----------|-------------|
| **Terminal (CLI)** | Full-featured CLI; run `claude` in any project directory |
| **VS Code** | Extension with inline diffs, @-mentions, plan review, conversation history |
| **JetBrains** | Plugin for IntelliJ, PyCharm, WebStorm, and other JetBrains IDEs |
| **Desktop App** | Standalone app; visual diff review, multiple sessions, scheduled tasks; available on macOS and Windows |
| **Web (claude.ai/code)** | Browser-based; no local setup; long-running cloud tasks |
| **Remote Control** | Control your local machine from a browser or another device |
| **Slack** | Mention `@Claude` in Slack; get pull requests back |
| **GitHub Actions / GitLab CI** | Automate PR reviews and issue triage in CI/CD |
| **Chrome** | Debug live web applications with Claude in Chrome |

### The Agentic Loop

Claude Code works through three phases: **gather context → take action → verify results**.
The loop adapts to the task and repeats until complete. You can interrupt at any point.

Built-in tool categories:

| Category | What Claude Can Do |
|----------|--------------------|
| File operations | Read, edit, create, rename files |
| Search | Find files by pattern, regex content search |
| Execution | Run shell commands, start servers, run tests, use git |
| Web | Search the web, fetch documentation |
| Code intelligence | Type errors, warnings, jump-to-definition (requires plugins) |

### Key Concepts

- **CLAUDE.md** — markdown file in your project root; Claude reads it every session for persistent instructions, coding standards, and conventions.
- **Auto Memory** — Claude automatically saves learnings (build commands, patterns, preferences) to `MEMORY.md`; the first 200 lines or 25KB load at session start.
- **Skills** — packaged repeatable workflows (e.g., `/review-pr`, `/deploy-staging`). Create with `/init` or manually.
- **MCP (Model Context Protocol)** — open standard for connecting Claude to external services (Google Drive, Jira, Slack, custom tools).
- **Hooks** — shell commands that run before/after Claude actions (e.g., auto-format after file edit, lint before commit).
- **Subagents** — spawn multiple Claude agents that work in parallel on different subtasks; each gets its own fresh context.
- **Routines** — scheduled tasks running on Anthropic-managed infrastructure; persist even when your computer is off.
- **Checkpoints** — every file edit is reversible; press `Esc` twice to rewind to a previous state.

### Permission Modes (Shift+Tab to cycle)

| Mode | Behavior |
|------|----------|
| Default | Asks before file edits and shell commands |
| Auto-accept edits | Edits files and runs common filesystem commands without asking |
| Plan mode | Read-only tools only; creates a plan for you to approve |
| Auto mode | Evaluates all actions with background safety checks (research preview) |

### Models in Claude Code

- Default model: **Claude Sonnet 4.6** (handles most coding tasks well)
- Switch during session: `/model`
- Start with specific model: `claude --model <name>`
- Opus provides stronger reasoning for complex architectural decisions

### Sessions

- Sessions saved locally as JSONL under `~/.claude/projects/`
- Sessions are independent — each starts with a fresh context window
- Resume: `claude --continue` or `claude --resume`
- Fork: `--fork-session` or `/branch`
- Work across branches: use `git worktrees` to run parallel Claude sessions

### Subscription / Pricing

- A [paid Claude subscription](https://claude.com/pricing) or [Anthropic Console](https://console.anthropic.com) account is required.
- The Desktop app requires a paid subscription.
- Third-party API providers are also supported (Terminal CLI and VS Code).

---

## claude.ai (Consumer Product)

- **claude.ai** is the consumer chat interface for Claude — not the API.
- Different from the Anthropic API (platform.claude.com), which is for developers.
- For account/billing questions, the Help Center is at: https://support.claude.com
- Service status: https://status.claude.com
- iOS app available: can run Claude Code sessions from your phone.

---

## Quick Decision Guide

| Need | Recommendation |
|------|---------------|
| Most complex reasoning / agentic coding | Claude Opus 4.7 |
| Balanced speed + intelligence (coding, agents, enterprise) | Claude Sonnet 4.6 |
| Fastest responses, high volume | Claude Haiku 4.5 |
| Long-running agentic tasks, async | Claude Managed Agents |
| Direct API integration, custom loops | Messages API |
| Coding assistant in terminal/IDE | Claude Code CLI or extension |
| No local setup, browser-based coding | claude.ai/code (web) |

---

## Reference Links

- API docs: https://platform.claude.com/docs
- Models overview: https://platform.claude.com/docs/en/about-claude/models/overview.md
- Claude Code docs: https://code.claude.com/docs
- Claude Code overview: https://code.claude.com/docs/en/overview.md
- Support: https://support.claude.com
- Pricing: https://claude.com/pricing
- Status: https://status.claude.com
- Discord: https://www.anthropic.com/discord
