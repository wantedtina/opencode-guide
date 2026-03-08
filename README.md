# opencode-guide
## Step 1：先在项目根目录启动 OpenCode

进入你的 Git 仓库，然后启动 OpenCode。

```bash
cd your-project
opencode
```

OpenCode 的 TUI 会在当前目录工作；也可以直接指定目录启动。([OpenCode][2])

## Step 2：先登录 provider，再确认模型

第一次用时先配置 provider：

```bash
opencode auth login
opencode models
```

官方说明 `opencode auth login` 会把 provider 凭证保存到本地凭证文件里，而 `opencode models` 可以列出当前可用模型。([OpenCode][3])

## Step 3：第一次进仓库，先执行 `/init`

进入 OpenCode 后，第一件事先做：

```text
/init
```

官方文档明确写了，`/init` 会扫描项目并生成或补充 `AGENTS.md`；同时官方也明确建议把项目的 `AGENTS.md` 提交到 Git，这样 OpenCode 之后每次进入这个仓库都能更好地理解项目。([OpenCode][4])

## Step 4：把 `AGENTS.md` 写成“能执行”的规则，不要写空话

`AGENTS.md` 最少写这几类内容：

* 技术栈和目录结构
* 构建 / 测试命令
* 哪些目录不要改
* 代码风格
* “优先最小改动，不要顺手重构”这类规则

因为官方说明 `AGENTS.md` 的内容会被放进模型上下文，用来定制 OpenCode 在当前项目里的行为。项目级 `AGENTS.md` 放在仓库根目录；全局规则也可以放在 `~/.config/opencode/AGENTS.md`。([OpenCode][4])

一个很实用的例子可以写成：

```md
# Project Rules

- Backend: Kotlin + Quarkus
- Run unit tests before finishing changes
- Prefer minimal changes
- Do not modify CI, Docker, Helm unless explicitly asked
- Do not add dependencies unless necessary
```

## Step 5：先分析，再修改

这是最重要的使用习惯。OpenCode 官方内置了 `plan` 和 `build` 两类 agent；其中 `plan` 是限制更严格的分析型 agent，默认对文件编辑和 bash 都是 `ask`，适合先做分析和方案设计。([OpenCode][5])

你平时第一句就这样说：

> 先不要修改代码。分析这个需求会影响哪些文件、风险点是什么、最小改动方案是什么、需要补哪些测试。

等它分析完，再说：

> 现在按刚才的最小方案修改代码。不要顺手重构别的地方。改完后运行相关测试。

这套流程通常比“上来直接做完整需求”稳定得多。([OpenCode][5])

## Step 6：权限先收紧，用熟了再放开

OpenCode 现在用 `permission` 配置管理工具权限。官方文档说明，`edit`、`bash`、`webfetch` 都可以配置成 `ask`、`allow`、`deny`，而且还能细分到某些 bash 命令。([OpenCode][6])

工作里推荐这样开始：

* `edit`: `ask`
* `bash`: `ask`
* `webfetch`: `ask` 或 `allow`

尤其是陌生仓库、公司项目、带部署脚本或数据库操作的项目，先不要一上来全放开。([OpenCode][5])

## Step 7：把任务拆小，不要让一个 session 无限变长

你前面问过上下文限制，这里直接给最实用结论：**不要把一个任务聊到很长**。OpenCode 提供了 `/new` 开新会话、`/sessions` 查看和切换会话；如果会话太长，最稳妥的办法就是做一段、总结一段、然后开新 session。([OpenCode][2])

最推荐的节奏是：

1. 一个 session 只做一个小目标
2. 做完一个阶段，让它先总结
3. 然后 `/new` 开新会话继续

常用命令：

```text
/new
/sessions
```

这些都是官方 TUI 里提供的命令。([OpenCode][2])

## Step 8：不要让它“记住”，要让它“重新读文件”

实际用 OpenCode 时，尽量少说“记住刚才那个文件”，多说“重新读取这个文件并分析”或者“搜索 userId 校验逻辑在哪些文件里”。OpenCode 的强项不是长记忆，而是它能持续读取当前仓库、搜索代码、再次分析。它本身就是面向整个项目工作的 coding agent。([OpenCode][1])

所以更好的问法是：

> 重新读取这个 resolver 和对应 service，分析最适合加校验的位置。
> 搜索当前仓库里类似的异常处理写法。
> 基于当前 diff，检查潜在回归点。

## Step 9：用 Skills 放“可复用能力”，别把所有规则都塞进对话

OpenCode 现在官方已经支持 Agent Skills。官方文档说明，skill 是通过 `SKILL.md` 定义的能力模块，OpenCode 会从 repo 或 home 目录发现它们，并且通过原生 `skill` tool **按需加载**。这正适合拿来放高频、专业化、可复用的流程。([OpenCode][7])

常见放法是：

* 项目级：`.opencode/skills/<name>/SKILL.md`
* 全局级：`~/.config/opencode/skills/<name>/SKILL.md`

官方也兼容 `.claude/skills/` 和 `.agents/skills/` 这些目录约定。([OpenCode][7])

你可以把下面这类东西做成 skill：

* Git commit / PR 规范
* 测试补全流程
* GraphQL 代码规范
* 文档查询流程
* 项目初始化模板

## Step 10：找 Skills 时，优先挑少数高价值的

SkillsMP 现在是一个社区技能市场，聚合 GitHub 上的 agent skills；它也明确说明 skills 是模块化的，可以组合使用，而且 skill 与 slash commands 的区别在于：skill 是模型按上下文自动选用的。([SkillsMP][8])

但实战上，不建议一次装很多。更实用的方式是先装少量高价值 skill，比如：

* 文档查询类
* Git workflow 类
* 代码库理解类
* 项目初始化类

如果只是先上手 OpenCode，先把 `AGENTS.md` 用好，skills 控制在 3 到 5 个就够了。OpenCode 官方本身也是按需加载 skills，而不是一次全塞进上下文。([OpenCode][7])

## Step 11：改完一定看 diff，不满意就 `/undo`

OpenCode 官方 TUI 提供 `/undo` 和 `/redo`，并且说明文件改动也会一起回滚或恢复；这一机制内部依赖 Git，所以项目最好是 Git 仓库。([OpenCode][2])

最稳的工作流是：

1. 让它先做一个小改动
2. 你看 diff
3. 不满意就 `/undo`
4. 满意再继续下一步

对应命令：

```text
/undo
/redo
```

这一步非常关键，它能把 OpenCode 从“黑箱代写”变成“可控的 coding agent”。([OpenCode][2])

## Step 12：需要外部工具时，再接 MCP

如果你想让 OpenCode 连外部工具，比如文档系统、内部平台、Jira 或别的服务，官方支持通过 MCP 接入；文档写得很明确，MCP server 加进去之后，工具会自动和内置工具一起提供给模型使用。([OpenCode][9])

常用命令是：

```bash
opencode mcp add
opencode mcp list
opencode mcp auth
```

但建议顺序是：**先把本地仓库工作流跑顺，再接 MCP**。不然一开始复杂度会太高。([OpenCode][9])

## Step 13：最后再让它做收尾工作

当代码改完后，再让 OpenCode 帮你做这些：

* 总结本次改动
* 列出风险点
* 起草 commit message
* 起草 PR 描述

这一步非常适合交给 OpenCode，因为它已经看过你当前仓库、当前 diff 和当前会话内容。OpenCode 官方定位就是可以规划、修改和自动化开发任务的 coding agent。([OpenCode][1])

---

## 一套最实用的日常模板

你平时几乎可以固定按这个顺序用：

1. `cd your-project && opencode`
2. `opencode auth login`，再 `opencode models`
3. `/init`
4. 补好并提交 `AGENTS.md`
5. 先让它分析，不要直接改
6. 再让它按最小方案执行
7. 改完跑测试
8. 自己看 diff
9. 不满意就 `/undo`
10. 会话太长就 `/new`
11. 高频流程再沉淀成 Skills
12. 最后让它写改动总结和 commit / PR 文案

---

## 最终一句话版

**OpenCode 最稳、最好用的方式，就是：先用 `AGENTS.md` 固化项目规则，再用分析优先的小步工作流驱动它，必要时用 Skills 扩展高频能力，用权限和 Git 回滚控制风险，用新 session 避免长上下文变乱。** 这些能力都已经在当前官方文档里明确支持。([OpenCode][4])

你要的话，我下一条可以直接给你一版**“适合程序员上班直接照抄的 OpenCode 提示词模板”**。

[1]: https://opencode.ai/docs/?utm_source=chatgpt.com "Intro | AI coding agent built for the terminal"
[2]: https://opencode.ai/docs/tui/?utm_source=chatgpt.com "TUI"
[3]: https://opencode.ai/docs/cli/?utm_source=chatgpt.com "CLI"
[4]: https://opencode.ai/docs/rules/?utm_source=chatgpt.com "Rules"
[5]: https://opencode.ai/docs/agents/?utm_source=chatgpt.com "Agents"
[6]: https://opencode.ai/docs/permissions/?utm_source=chatgpt.com "Permissions"
[7]: https://opencode.ai/docs/skills/?utm_source=chatgpt.com "Agent Skills"
[8]: https://skillsmp.com/?utm_source=chatgpt.com "SkillsMP: Agent Skills Marketplace - Claude, Codex ..."
[9]: https://opencode.ai/docs/mcp-servers/?utm_source=chatgpt.com "MCP servers"
