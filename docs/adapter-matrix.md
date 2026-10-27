# Adapter Matrix

Adapters are deliberately small. Their job is to route an agent to the
workspace's canonical protocol.

| Agent/tool | File created by default | Discovery behavior | Canonical target |
| --- | --- | --- | --- |
| Codex / AGENTS.md tools | `AGENTS.md` | Usually discovered automatically from repository root toward the current directory | `MEMORY/01-rules/workspace-protocol.md` |
| Claude Code | `CLAUDE.md` | Read by Claude Code and compatible Claude workflows | Same canonical protocol |
| Cursor | `.cursor/rules/000-agent-workspace.mdc` | Cursor rule discovery | Same canonical protocol |
| Gemini CLI | `GEMINI.md` | Gemini CLI project instruction discovery | Same canonical protocol |
| GitHub Copilot | `.github/copilot-instructions.md` | GitHub Copilot repository instruction discovery | Same canonical protocol |
| DeepSeek Harness (`dsh`) | `AGENTS.md` (shared with Codex) | Reads `AGENTS.md` and `CLAUDE.md` from each project directory; the user-global file is `~/.dsh/AGENTS.md` | Same canonical protocol |
| Qoder | `.qoder/rules/000-agent-workspace.md` | Qoder rule discovery under `.qoder/rules/`; `AGENTS.md` is also read, and rule content wins conflicts | Same canonical protocol |
| Trae | `.trae/rules/000-agent-workspace.md` | Trae rule discovery under `.trae/rules/` (recursive); `AGENTS.md` and `CLAUDE.md` are read only when enabled in settings | Same canonical protocol |
| WorkBuddy | `CODEBUDDY.md` | WorkBuddy loads `CODEBUDDY.md`, `.codebuddy/CODEBUDDY.md`, and `AGENTS.md` as project context; `.codebuddy/rules/` is also injected | Same canonical protocol |

## Shared and Overlapping Entries

Several tools read the same file, so the installer creates each entry once even
when multiple adapters are selected.

- `codex` and `dsh` both use `AGENTS.md`. `--agents codex,dsh` creates one copy.
- WorkBuddy also reads `AGENTS.md`; `CODEBUDDY.md` exists because it is the
  first project instruction file WorkBuddy loads, and because it keeps the
  WorkBuddy-specific note out of the cross-agent map.
- Qoder and Trae can read `AGENTS.md`, but their native rule directories apply
  without an opt-in setting, so the adapter prefers the native path.
- DeepSeek Harness also has a user-global `~/.dsh/AGENTS.md`. The installer
  only writes inside the target workspace; edit the user-global file yourself
  if you want the protocol mentioned in every DSH workspace.

Rule files carry their own front matter:

- Qoder: `trigger: always_on` (also `manual`, `model_decision`, or glob rules).
- Trae: `alwaysApply: true` (plus `description`, `globs`, or `scene`).
- WorkBuddy: rules under `.codebuddy/rules/` apply always unless `alwaysApply`
  is `false` or `paths` narrows them; `CODEBUDDY.md` needs no front matter.

## Adapter Rules

1. Keep the adapter under about 40 lines.
2. Link to the canonical protocol and workspace index.
3. Include only the minimum startup path and red lines.
4. Do not paste the full protocol into multiple adapter files.
5. If an adapter cannot link to a file, include a short bootstrap instruction
   that tells the agent to read the canonical path.
6. Update the adapter when the canonical path moves; do not keep a stale
   duplicate alive.

## Nested Repositories and Monorepos

If a code repository is nested or vendored, add a nested `AGENTS.md` near the
code subtree. The nested file should explain local build/test commands and
state which parent rules still apply. Nested instructions should not fork the
authority model.
