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

- The canonical shared instructions live in `~/agent-scripts/AGENTS.md` (and are mirrored in `~/AGENTS.md` for Codex’s global scope). Update this file first whenever a recurring rule changes.
- Projects that need custom guidance keep their own `AGENTS.md` (currently only `sweetistics`). When you modify repo-specific requirements, edit that repo’s file directly.
- If you adjust shared guardrail scripts (`runner`, `scripts/committer`, browser tools, etc.), apply the change in the target repo, then immediately sync the exact patch back into `~/Projects/agent-scripts` so every project can pull the canonical version.
- After syncing, re-run `rg --files -g 'AGENTS.md' ~/Projects` to verify only the intended repos carry local instruction files, then commit/push both the project change and the agent-scripts update.
- Keep `~/AGENTS.md` in sync with `agent-scripts/AGENTS.md` whenever you make substantive edits so Codex picks up the latest guidance in every workspace.
