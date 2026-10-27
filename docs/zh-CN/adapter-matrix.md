# 适配矩阵

适配器刻意保持简短，职责是让智能体找到工作区规则正本。

| 智能体或工具 | 默认创建文件 | 发现方式 | 正本目标 |
| --- | --- | --- | --- |
| Codex / AGENTS.md 工具 | `AGENTS.md` | 通常从仓库根目录向当前目录自动发现 | `MEMORY/01-rules/workspace-protocol.md` |
| Claude Code | `CLAUDE.md` | Claude Code 和兼容工作流读取 | 同一规则正本 |
| Cursor | `.cursor/rules/000-agent-workspace.mdc` | Cursor 规则发现机制 | 同一规则正本 |
| Gemini CLI | `GEMINI.md` | Gemini CLI 项目说明发现机制 | 同一规则正本 |
| GitHub Copilot | `.github/copilot-instructions.md` | GitHub Copilot 仓库说明发现机制 | 同一规则正本 |
| DeepSeek Harness（`dsh`） | `AGENTS.md`（与 Codex 共用） | 每个项目目录读取 `AGENTS.md` 和 `CLAUDE.md`；用户级文件为 `~/.dsh/AGENTS.md` | 同一规则正本 |
| Qoder | `.qoder/rules/000-agent-workspace.md` | Qoder 从 `.qoder/rules/` 发现规则；同时读取 `AGENTS.md`，冲突时规则内容优先 | 同一规则正本 |
| Trae | `.trae/rules/000-agent-workspace.md` | Trae 从 `.trae/rules/` 递归发现规则；`AGENTS.md`、`CLAUDE.md` 需在设置中开启后读取 | 同一规则正本 |
| WorkBuddy | `CODEBUDDY.md` | WorkBuddy 将 `CODEBUDDY.md`、`.codebuddy/CODEBUDDY.md` 和 `AGENTS.md` 作为项目上下文加载；`.codebuddy/rules/` 同样注入 | 同一规则正本 |

## 共用与重叠的入口

多个工具会读取同一个文件，因此即使同时选择多个适配器，安装器也只为每个入口创建
一份。

- `codex` 和 `dsh` 都使用 `AGENTS.md`；`--agents codex,dsh` 只创建一份。
- WorkBuddy 也读取 `AGENTS.md`。`CODEBUDDY.md` 单独存在，是因为它是 WorkBuddy
  最先加载的项目说明文件，同时可以把 WorkBuddy 专属说明留在跨智能体地图之外。
- Qoder 和 Trae 都能读取 `AGENTS.md`，但它们的原生规则目录无需额外开关即可生效，
  因此适配器优先使用原生路径。
- DeepSeek Harness 另有用户级 `~/.dsh/AGENTS.md`。安装器只写目标工作区；如果希望
  每个 DSH 工作区都提到本协议，需要自行编辑该用户级文件。

规则文件各自带有前置元数据：

- Qoder：`trigger: always_on`（也支持 `manual`、`model_decision` 和 glob 规则）。
- Trae：`alwaysApply: true`（可配合 `description`、`globs` 或 `scene`）。
- WorkBuddy：`.codebuddy/rules/` 下的规则默认始终生效，除非 `alwaysApply` 为
  `false` 或由 `paths` 限定范围；`CODEBUDDY.md` 不需要前置元数据。

## 适配器规则

1. 适配器保持在约 40 行以内。
2. 指向规则正本和工作区索引。
3. 只包含最小启动路径和红线。
4. 不要把完整协议粘贴到多个适配器。
5. 如果适配器不能链接文件，写一句最短指令，让智能体读取正本路径。
6. 正本路径移动时同步更新适配器，不能保留过期副本。

## 嵌套仓库和 Monorepo

如果代码仓库嵌套或 vendor 在工作区中，在代码子树附近增加嵌套 `AGENTS.md`。嵌套
文件说明本地构建和测试命令，以及哪些父级规则仍然适用。嵌套说明不得分叉权威模型。
