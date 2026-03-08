# OpenCode

**OpenCode** (`opencode-ai`) is an open-source AI-powered coding agent — an alternative to Claude Code, Cursor, or Windsurf. It provides an interactive AI coding assistant that runs as a TUI, Web app, or Desktop app.

- **Repo:** github.com/anomalyco/opencode
- **License:** MIT
- **Runtime:** Bun 1.3.10
- **Language:** TypeScript 5.8.2
- **Default branch:** `dev`

## Monorepo Structure

Bun workspaces + Turborepo orchestration.

| Package | Name | Purpose |
|---------|------|---------|
| `packages/opencode` | `opencode` | Core engine — CLI, TUI, server, AI, tools, agents, sessions, MCP |
| `packages/app` | `@opencode-ai/app` | Web application (SolidJS + Vite + TailwindCSS) |
| `packages/desktop` | `@opencode-ai/desktop` | Desktop app (Tauri 2.0) |
| `packages/desktop-electron` | — | Desktop app (Electron) |
| `packages/ui` | `@opencode-ai/ui` | Shared UI component library (SolidJS + Kobalte) |
| `packages/web` | `@opencode-ai/web` | Landing page / docs (Astro) |
| `packages/plugin` | `@opencode-ai/plugin` | Plugin SDK with 15+ hook points |
| `packages/sdk/js` | `@opencode-ai/sdk` | Auto-generated TypeScript SDK from OpenAPI |
| `packages/console/*` | — | Hosted console (core, app, function, mail, resource) |
| `packages/slack` | — | Slack bot integration |
| `packages/enterprise` | — | Enterprise features (SolidStart) |
| `packages/function` | — | Cloudflare Worker functions |
| `packages/extensions` | — | Editor extensions (VSCode, Zed) |
| `packages/util` | `@opencode-ai/util` | Shared utilities |
| `packages/script` | `@opencode-ai/script` | Build/release scripts |
| `packages/storybook` | — | Component storybook |
| `packages/docs` | — | API documentation (Mintlify) |
| `packages/containers` | — | Docker/container configs |
| `packages/identity` | — | Brand identity assets |

## Tech Stack

- **UI Framework:** SolidJS + TailwindCSS
- **TUI:** Custom `@opentui/solid` framework (60fps, mouse support, syntax highlighting)
- **API Server:** Hono with OpenAPI specs
- **Database:** Drizzle ORM + SQLite
- **AI SDK:** Vercel AI SDK v5
- **Desktop:** Tauri 2.0 / Electron
- **Docs Site:** Astro (Starlight)
- **Infra:** SST + Cloudflare + PlanetScale + Stripe

## Core Package Modules (`packages/opencode/src/`)

| Module | Purpose |
|--------|---------|
| `provider/` | AI provider registry, transforms, model loading (20+ providers) |
| `session/` | Session management, LLM streaming, message schema, compaction |
| `agent/` | Agent definitions (build, plan, general, explore, compaction, title, summary) |
| `tool/` | Tool registry and implementations (18+ built-in tools) |
| `mcp/` | MCP client (stdio, HTTP/SSE, OAuth, tool/prompt/resource support) |
| `plugin/` | Plugin system with hooks (event, config, tool, auth, chat.*, etc.) |
| `config/` | Config loading (remote -> global -> custom -> project -> .opencode -> inline) |
| `server/` | Hono HTTP API (routes: session, project, pty, mcp, file, config, etc.) |
| `permission/` | Pattern-based permission rulesets (allow/deny/ask per tool per path) |
| `snapshot/` | Git-based snapshot system for undo/redo file change tracking |
| `bus/` | Typed pub/sub event bus |
| `command/` | Slash command system (template-based, config/MCP/skill sources) |
| `skill/` | Skill discovery (SKILL.md files) |
| `acp/` | Agent Client Protocol compliance |
| `storage/` | SQLite database layer (Drizzle ORM, JSON migrations) |
| `project/` | Project management (detection, VCS integration, instance state) |
| `cli/cmd/tui/` | TUI implementation (routes, components, context, keybinds) |
| `lsp/` | Language Server Protocol integration |
| `pty/` | Pseudo-terminal management (bun-pty) |
| `worktree/` | Git worktree management |

## AI Providers (20+)

Anthropic, OpenAI, Google (Gemini), AWS Bedrock, Google Vertex AI, Azure OpenAI, xAI (Grok), Mistral, Groq, DeepInfra, Cerebras, Cohere, Perplexity, Together AI, Vercel, OpenRouter, GitHub Copilot, GitLab Duo, SAP AI Core, Cloudflare Workers AI, any OpenAI-compatible endpoint.

Model metadata fetched from `models.dev` with cost/capability/context info.

## Built-in Tools (18+)

| Tool | Purpose |
|------|---------|
| `bash` | Shell command execution (tree-sitter parsed permissions) |
| `read` | File reading |
| `edit` | File editing (search/replace) |
| `write` | File creation/writing |
| `glob` | File pattern matching |
| `grep` | Content search (ripgrep-backed) |
| `task` | Spawn subagent sessions (multi-agent) |
| `batch` | Run up to 25 tools in parallel |
| `webfetch` | Fetch and process web pages |
| `websearch` | Web search (Exa-backed) |
| `codesearch` | Semantic code search |
| `skill` | Load specialized instruction sets |
| `todo` | Task tracking (TodoWrite/TodoRead) |
| `question` | Ask user clarifying questions |
| `apply_patch` | Unified diff patching (GPT models) |
| `multiedit` | Multi-file edits |
| `plan` | Plan mode operations |
| `lsp` | Language server integration |

Custom tools loaded from `{tool,tools}/*.{js,ts}` in config directories and plugins.

## Agents

| Agent | Mode | Description |
|-------|------|-------------|
| `build` | primary | Default agent — full tool access, makes changes |
| `plan` | primary | Read-only exploration, suggests without modifying |
| `general` | subagent | Multi-step task execution (spawned in parallel) |
| `explore` | subagent | Read-only codebase exploration |
| `compaction` | hidden | Context summarization when nearing token limits |
| `title` | hidden | Session title generation |
| `summary` | hidden | Session summary generation |

Custom agents definable via config. Agents can be AI-generated from descriptions.

## CLI Commands

| Command | Purpose |
|---------|---------|
| `opencode` | Start interactive TUI |
| `opencode run [msg]` | Non-interactive execution (`--format json`) |
| `opencode serve` | Headless API server |
| `opencode web` | Browser-based UI |
| `opencode attach <url>` | Connect to running server |
| `opencode auth` | Provider authentication |
| `opencode agent create/list` | Manage custom agents |
| `opencode models` | List available models |
| `opencode mcp` | MCP server management |
| `opencode pr` | Pull request operations |
| `opencode github` | GitHub integration |
| `opencode export/import` | Session data transfer |
| `opencode upgrade` | Self-update |
| `opencode session` | Session management |
| `opencode db` | Database operations |
| `opencode debug` | Diagnostics |
| `opencode stats` | Usage statistics |

## Architectural Patterns

1. **SDK-first API** — Server exposes HTTP/SSE/WS API, SDK auto-generated from OpenAPI, all frontends use SDK
2. **Plugin system** — Hooks-based extension for auth, tools, chat, permissions, events
3. **Multi-agent** — Named agent configs with permission rulesets; primary + subagents
4. **Tool registry** — Extensible with built-in + plugin/config custom tools
5. **Event bus** — Typed pub/sub (`BusEvent.define()`) for decoupled communication
6. **Instance state** — `Instance.state()` for lazy-initialized, project-scoped singletons
7. **Namespace pattern** — TypeScript namespaces for module organization (Session, Provider, Agent, Tool, Config)

## TUI Architecture

- Main thread: renders via `@opentui/solid` (SolidJS-based terminal rendering)
- Worker thread: runs Hono server, manages sessions/AI/MCP/LSP
- Communication: RPC bridge + event streaming

## Dev Commands

```sh
bun install          # install dependencies
bun dev              # start TUI (from packages/opencode, --conditions=browser)
bun dev:desktop      # start Tauri desktop app
bun dev:web          # start web UI
bun dev:storybook    # start Storybook
```

Tests run from package directories, not root. See AGENTS.md for style guide and conventions.

## Key Files

- `packages/opencode/src/provider/provider.ts` — Provider registry
- `packages/opencode/src/provider/transform.ts` — Provider-specific message transforms
- `packages/opencode/src/session/llm.ts` — LLM streaming interface
- `packages/opencode/src/session/processor.ts` — Stream event processing
- `packages/opencode/src/session/message-v2.ts` — Message/part schema definitions
- `packages/opencode/src/agent/agent.ts` — Agent definitions and generation
- `packages/opencode/src/tool/registry.ts` — Tool registration and filtering
- `packages/opencode/src/mcp/index.ts` — MCP client implementation
- `packages/opencode/src/plugin/index.ts` — Plugin system
- `packages/opencode/src/config/index.ts` — Configuration management

## Dependency Graph

```
opencode (core) -> sdk, plugin, util, script
app (web UI)    -> sdk, ui, util
desktop (Tauri) -> app, ui
desktop-electron -> app, ui
console/*       -> console-core, console-resource, ui
slack           -> sdk
```
