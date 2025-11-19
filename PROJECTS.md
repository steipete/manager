# Active Projects (November 15, 2025)

The repositories below currently live under `~/Projects` and receive ongoing attention. Use this list as the jumping-off point when choosing where to work or when verifying guardrails.

1. Poltergeist – Bun-based build/watch daemon.
2. oracle – TypeScript CLI + browser automation.
3. mcporter – MCP runtime + tooling.
4. tmuxwatch – Go TUI for tmux snapshots.
5. pi-mono – Multi-package monorepo (fork; upstream rules apply).
6. Peekaboo – Swift client + Tachikoma framework.
7. Peekaboo/Tachikoma – Standalone Tachikoma repo inside Peekaboo.
8. wingman – Bun full-stack app with Convex backend.
9. sweetistics – PNPM monorepo (primary). AGENTS.md stays here.
10. sweetlink – Companion CLI/runtime repo.
11. gemini-cli – Gemini-focused CLI helpers.
12. iterm-mcp – MCP tooling for iTerm automation.
13. TauTUI – Swift/SwiftUI terminal UI experiments.
14. poltergeist-pitui – Assets and scripts for Poltergeist panel/TUI.
15. agent-scripts – Canonical guardrail helpers (runner, committer, etc.).

## Guardrail Sync Process

- Canonical shared instructions now live **only** in `~/Projects/agent-scripts/AGENTS.md` and the tool list in `~/Projects/agent-scripts/TOOLS.md`. Every other repo’s `AGENTS.md` is just the pointer line `READ ~/Projects/agent-scripts/{AGENTS.md,TOOLS.md} BEFORE ANYTHING (skip if files missing).`
- When a project truly needs repo-local guidance, place it **after** the pointer line so we keep one source of truth for the shared portion. Delete any lingering `[shared]` or `<tools>` blocks when cleaning up older repos.
- If you edit shared guardrail scripts (`runner`, `scripts/committer`, browser tools, etc.), change them in the active repo, immediately sync the exact patch into `~/Projects/agent-scripts`, and only then fan it back out. The canonical docs should explain the new behavior before you touch downstream repos.
- When someone asks to “sync agent scripts,” the sweep is now: pull latest `agent-scripts`, ensure the pointer line exists in the target repo’s `AGENTS.md`, copy over updated helper scripts if needed, and leave repo-local notes untouched below the pointer.
- Keep `~/AGENTS.md` (Codex global) identical to `agent-scripts/AGENTS.md`. Update both whenever you change recurring rules or the pointer text.
