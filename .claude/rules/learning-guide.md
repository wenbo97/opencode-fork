# OpenCode Project - Learning & Ramp-Up Guide

## Phase 1: Foundation (Start Here)

### 1. Bun (1-2 days)
Bun replaces Node.js + npm + webpack. It's the runtime, package manager, test runner, and bundler.
- Key commands: `bun install`, `bun run`, `bun dev`, `bun test`
- Key APIs: `Bun.file()`, `Bun.serve()`, `Bun.spawn()`
- Bun workspaces (replaces npm workspaces / lerna)
- Docs: https://bun.sh/docs
- Transition from npm is small: `bun install` = `npm install`, `bun run dev` = `npm run dev`

### 2. TypeScript Patterns Used in This Project (3-5 days)
Focus on what the codebase heavily uses:
- **Namespaces** — primary organizational pattern (e.g., `Session.create()`, `Provider.list()`, `Tool.define()`)
- **Zod 4 schemas** — validation and type inference everywhere (`z.object()`, `z.infer<>`)
- **Discriminated unions** — message parts, tool types, agent modes
- **Generics** — tool definitions, event bus, factory patterns
- **Type inference** — style guide says avoid explicit annotations, so understanding inference is critical
- **`const` assertions and ternaries** — preferred over `let` + reassignment
- Skip: decorators, enums, complex mapped types (not used much)

### 3. Turborepo (1 hour)
- `turbo typecheck`, `turbo build` — orchestrates tasks across packages
- `turbo.json` defines the task pipeline and caching
- Shallow knowledge is sufficient — it's just an orchestrator

## Phase 2: Core Architecture (The Heart)

### 4. `packages/opencode/src/` — Read in This Order
This is 80% of the project's logic. Everything else is a frontend or integration layer.

1. **`config/`** — configuration loading and merging (small, foundational)
2. **`storage/`** — SQLite + Drizzle ORM schemas (the data model)
3. **`bus/`** — typed event bus pattern (small module, used everywhere)
4. **`provider/provider.ts`** — how 20+ AI providers are registered and loaded
5. **`agent/agent.ts`** — agent definitions, permissions, model overrides
6. **`tool/`** — read `registry.ts` first, then a simple tool like `read.ts` or `glob.ts`
7. **`session/`** — the biggest module:
   - `message-v2.ts` — message/part data model (~915 lines)
   - `llm.ts` — LLM streaming interface (~280 lines)
   - `processor.ts` — stream event processing loop (~430 lines)
   - `compaction.ts` — context window management (~330 lines)
8. **`mcp/`** — MCP client (read after understanding tools and sessions)
9. **`plugin/`** — plugin system and hooks
10. **`server/`** — Hono HTTP API routes

### 5. Hono (1-2 hours)
- Lightweight API framework (like Express but faster/simpler)
- Learn: routing, middleware, context
- Then read `src/server/` to see route structure

### 6. Drizzle ORM (2-3 hours)
- SQLite persistence for sessions, messages, parts, config
- Learn: schema definitions, queries, migrations
- Convention: snake_case field names (see AGENTS.md)

## Phase 3: Frontend

### 7. SolidJS (2-3 days)
- Used for TUI, web app, and UI library
- Similar to React but with fine-grained reactivity (no virtual DOM)
- Key concepts: signals (`createSignal`), effects (`createEffect`), `createResource`, JSX, context providers
- The TUI uses SolidJS via custom `@opentui/solid` framework

### 8. TailwindCSS 4 (1 day)
- Utility-first CSS, used across web app and UI library
- Uses `@tailwindcss/vite` plugin

## Phase 4: Specialized (Learn As Needed)

| Area | When to learn |
|------|---------------|
| Vercel AI SDK v5 | When touching provider/streaming code |
| MCP protocol | When working on tool integrations |
| Tauri 2.0 / Rust | Only if touching the desktop app |
| SST + Cloudflare | Only if touching infrastructure/deployment |
| Playwright | Only if writing e2e tests |
| Astro | Only if touching the docs/marketing site |
| Stripe | Only if touching billing (console) |

## Priority Summary

```
Must learn first:    Bun → TypeScript patterns → packages/opencode/src/ core
Then:                Hono → Drizzle → SolidJS
As needed:           AI SDK, MCP, Tauri, SST, Playwright
```

## Key Architectural Patterns to Understand

1. **SDK-first API** — core exposes HTTP/SSE/WS API, SDK auto-generated from OpenAPI, all frontends use SDK
2. **Namespace pattern** — TypeScript namespaces for module organization (`Session`, `Provider`, `Agent`, `Tool`, `Config`)
3. **Instance state** — `Instance.state()` for lazy-initialized, project-scoped singletons
4. **Event bus** — typed pub/sub (`BusEvent.define()`) for decoupled communication
5. **Plugin hooks** — extension points for auth, tools, chat, permissions, events
6. **TUI worker architecture** — main thread renders, worker thread runs server + AI

## Dev Commands Quick Reference

```sh
bun install              # install all dependencies
bun dev                  # start TUI (packages/opencode)
bun dev:web              # start web UI only (needs backend running)
bun dev:desktop          # start Tauri desktop app
bun dev:storybook        # start component storybook
bun typecheck            # run TypeScript checks across all packages
cd packages/opencode && bun test   # run tests (must be in package dir)
```

## Windows Note

This repo uses Git symlinks. On Windows, enable them with:
```sh
git config core.symlinks true
```
Otherwise symlinks check out as plain text files containing the target path, causing typecheck errors (e.g., `custom-elements.d.ts`).
