# Agent Adapters

An adapter is a small discovery file for one tool. It routes the agent to the
workspace's canonical protocol.

## Canonical Target

The default canonical target is:

```text
MEMORY/01-rules/workspace-protocol.md
```

If a workspace moves that file, update every adapter.

## Codex and AGENTS.md

- Workspace entry: `AGENTS.md`
- Global skill root: `$CODEX_HOME/skills`, falling back to `~/.codex/skills`
- Project skill root supported by the installer: `.agents/skills`
- Skill folder: `agent-workspace-protocol/`

`AGENTS.md` should be short and should point to the canonical protocol and
memory index. In nested repositories, a nested `AGENTS.md` may add local
commands and path boundaries.

## Claude Code

- Workspace entry: `CLAUDE.md`
- Common user-level skill root: `~/.claude/skills`
- Common project-level skill root: `.claude/skills`

`CLAUDE.md` is an adapter, not a second copy of the protocol.

## Cursor

- Workspace rule: `.cursor/rules/000-agent-workspace.mdc`
- Legacy alternative: `.cursorrules` for older Cursor versions

The rule should use normal front matter and point to the canonical protocol.
Keep it narrowly scoped; do not create one reloaded rule per category.

## Gemini CLI

- Workspace entry: `GEMINI.md`
- Common user-level entry: `~/.gemini/GEMINI.md`

The repository-level file is the portable adapter. A user-level file can
remind Gemini to look for `AGENTS.md` and the canonical protocol, but it must
not contain project-specific memory.

## GitHub Copilot

- Workspace entry: `.github/copilot-instructions.md`

Keep the file short and link to the canonical protocol and workspace index.
Confirm the repository's Copilot setting allows repository instructions.

## DeepSeek Harness (dsh)

- Workspace entry: `AGENTS.md` (shared with Codex); `CLAUDE.md` is also read
- User-global entry: `~/.dsh/AGENTS.md`
- Skill root: `~/.dsh/skills`

DeepSeek Harness loads `AGENTS.md` and `CLAUDE.md` from each project directory,
so the cross-agent map is already the adapter. The installer's `dsh` flag
creates that shared file; it never writes the user-global `~/.dsh/AGENTS.md`.

## Qoder

- Workspace rule: `.qoder/rules/000-agent-workspace.md`
- Front matter: `trigger: always_on` (also `manual`, `model_decision`, glob)

Qoder reads its native rule directory and also recognizes `AGENTS.md`; rule
content takes precedence when the two conflict. Keep the rule short and use
plain backticked paths, because Qoder rules are natural-language context.

## Trae

- Workspace rule: `.trae/rules/000-agent-workspace.md`
- Front matter: `alwaysApply: true`
- User-level rules: `~/.trae-cn/user_rules` on Windows, `~/.trae/user_rules`
  or the equivalent per edition on macOS/Linux

Trae discovers rules recursively under `.trae/rules/`. `AGENTS.md` and
`CLAUDE.md` are read only after the corresponding import switch is enabled in
the IDE settings, so the native rule directory is the reliable adapter.

## WorkBuddy

- Workspace entry: `CODEBUDDY.md`
- Also read: `.codebuddy/CODEBUDDY.md`, `AGENTS.md`, `.codebuddy/rules/`
- Project memory: `.workbuddy/memory/` (agent-managed, not an adapter)

WorkBuddy uses the CodeBuddy project instruction loader. `CODEBUDDY.md` is the
first file it loads, and `AGENTS.md` is accepted as well. Rules under
`.codebuddy/rules/` are injected as project rules; do not place the adapter in
`.workbuddy/memory/`, which the agent rewrites and distills.

## Adding a New Adapter

1. Identify the exact discovery path and current official documentation.
2. Create a short pointer to the canonical protocol.
3. Add the adapter to the installer's agent list.
4. Add it to the adapter matrix.
5. Add the template to both `workspace/` and `workspace-zh-CN/`, and extend the
   installer test expectations.
6. Test discovery in a temporary workspace.
7. Do not copy the full protocol.

## Adapter Failure Checks

If an agent ignores the workspace rules, check:

- the file is in the discovery path expected by that tool;
- the file is tracked and present in the checkout the tool opened;
- the tool reads repository instructions only for trusted workspaces;
- nested working directories do not hide the root instructions;
- another instruction file is overriding or conflicting;
- the adapter points to a file that still exists.
