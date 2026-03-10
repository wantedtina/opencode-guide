## 推荐目录结构

```text
~/.config/opencode/
├─ opencode.json
├─ AGENTS.md
├─ skills/
│  ├─ trace-callchain/
│  │  └─ SKILL.md
│  ├─ debug-api/
│  │  └─ SKILL.md
│  ├─ test-fix/
│  │  └─ SKILL.md
│  └─ release-check/
│     └─ SKILL.md
└─ commands/
   ├─ repo-summary.md
   ├─ trace-entry.md
   ├─ review-diff.md
   └─ fix-failing-test.md
```

每个项目里再放：

```text
repo-a/
├─ opencode.json
└─ AGENTS.md

repo-b/
├─ opencode.json
└─ AGENTS.md
```

---

## 设计原则

### 1）全局只放“跨项目稳定规则”

比如：

* 默认模型分层
* 常用 agent 权限
* 通用 commands
* 通用 skills
* 你的个人编码偏好

不要把某个项目的业务背景塞进全局。

### 2）每个项目只写“最小必要覆盖”

比如：

* 技术栈
* 目录结构
* 启动 / test / build 命令
* 本项目特有禁区
* 本项目必须遵守的改动原则

### 3）把长说明做成共享 skills

比如：

* 怎么排查 API
* 怎么 trace 调用链
* 怎么处理 migration
* 怎么修测试
* 怎么做 release checklist

skills 是按需加载，比把这些内容全塞进每个项目的 `AGENTS.md` 更省 token。([opencode.ai][3])

---

# 一套更适合多项目的实际模板

## 1. 全局 `~/.config/opencode/opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",

  "model": "opencode/gpt-5.1-codex",

  "agent": {
    "plan": {
      "model": "opencode/gpt-5.1-mini",
      "permission": {
        "edit": "ask",
        "bash": "ask"
      }
    },
    "explore": {
      "model": "opencode/gpt-5.1-mini"
    },
    "build": {
      "model": "opencode/gpt-5.1-codex"
    }
  },

  "permission": {
    "bash": {
      "*": "ask",
      "git status*": "allow",
      "git diff*": "allow",
      "git log*": "allow",
      "git commit*": "deny"
    }
  },

  "command": {
    "repo-summary": {
      "description": "Summarize current repository quickly",
      "agent": "explore",
      "subtask": true,
      "model": "opencode/gpt-5.1-mini",
      "template": "Summarize this repository. Focus on architecture, entry points, major modules, test strategy, and the files I should read first."
    },
    "trace-entry": {
      "description": "Trace a function or API entry path",
      "agent": "explore",
      "subtask": true,
      "model": "opencode/gpt-5.1-mini",
      "template": "Trace the call chain for: {{args}}. Return definition, callers, downstream services, repositories, and related tests."
    },
    "review-diff": {
      "description": "Review current changes",
      "agent": "plan",
      "subtask": true,
      "model": "opencode/gpt-5.1-codex",
      "template": "Review the current git diff for correctness, edge cases, security, and maintainability. Suggest concrete fixes."
    },
    "fix-failing-test": {
      "description": "Investigate and fix failing tests",
      "agent": "build",
      "subtask": true,
      "model": "opencode/gpt-5.1-codex",
      "template": "Investigate failing tests, identify root cause, and apply the smallest safe fix. Avoid unrelated refactors."
    }
  }
}
```

### 这份全局配置为什么适合多项目

* 默认模型只设一套，避免每个 repo 重复写。OpenCode 支持在配置里设置默认模型，也支持对 agent 单独覆盖模型。([opencode.ai][5])
* `plan` 和 `explore` 用更快的模型，`build` 用更强模型，这和官方对不同 agent 用途的划分是吻合的：`plan` 偏分析，`explore` 是只读快速探索，`build` 是全工具开发。([opencode.ai][6])
* command 统一走 `subtask: true`，能把“看 repo、trace、review diff、修测试”这些高频动作扔到子任务里，减少主会话膨胀。([opencode.ai][4])

---

## 2. 全局 `~/.config/opencode/AGENTS.md`

这个文件建议非常短，只写你希望**所有项目都生效**的规则。

```markdown
# Global Working Style

- Prefer minimal, targeted changes.
- Do not refactor unrelated code.
- When exploring a repo, first identify entry points, build/test commands, and key modules.
- When information is missing, inspect the repository before making assumptions.
- Prefer explaining findings with concrete file paths.
- Before proposing a large change, outline the impact area first.

# Output Style

- Be concise.
- Use bullets only when they improve clarity.
- For code changes, explain what changed and why.
```

### 为什么要短

`AGENTS.md` 是规则文件，项目根规则会在对应项目生效；全局规则也可用。它越短，越不容易在多项目切换时一直吃上下文。官方也建议把项目 `AGENTS.md` 提交到 Git，并在项目内使用。([opencode.ai][2])

---

## 3. 全局共享 skills

### `~/.config/opencode/skills/trace-callchain/SKILL.md`

```markdown
---
name: trace-callchain
description: Trace the full call path of a function, endpoint, resolver, or job
---

# Trace Call Chain

When asked to trace a function or endpoint:

1. Find the definition.
2. Find direct callers.
3. Identify entry layer:
   - HTTP route
   - GraphQL resolver
   - CLI command
   - job / worker
   - event handler
4. Trace downstream dependencies:
   - service
   - repository / DAO
   - external client
5. Find related tests.
6. Return a compact path summary with file paths.
```

### `~/.config/opencode/skills/debug-api/SKILL.md`

```markdown
---
name: debug-api
description: Debug backend API failures systematically
---

# Debug API

Checklist:

1. Identify entry point.
2. Check request parsing / auth / validation.
3. Trace service path.
4. Check repository or downstream client calls.
5. Look for config/env assumptions.
6. Find logs, tests, and recent related changes.
7. Prefer smallest safe fix first.
```

### `~/.config/opencode/skills/test-fix/SKILL.md`

```markdown
---
name: test-fix
description: Investigate and fix failing tests with minimal scope
---

# Fix Failing Tests

1. Read the failing test first.
2. Confirm expected behavior.
3. Find regression source.
4. Apply minimal fix.
5. Avoid rewriting unrelated tests.
6. Explain whether the bug was in code or in test expectation.
```

skills 会被 OpenCode 发现，但只在需要时加载完整内容，所以特别适合做**多项目共享 SOP**。([opencode.ai][3])

---

## 4. 每个项目自己的最小配置

---

### repo-a：Java/Kotlin backend

#### `repo-a/opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",

  "agent": {
    "build": {
      "model": "opencode/gpt-5.1-codex"
    }
  },

  "command": {
    "run-backend-tests": {
      "description": "Run backend tests",
      "agent": "build",
      "subtask": true,
      "template": "Run the backend test suite and summarize failures. Focus on root cause, not broad refactoring."
    }
  }
}
```

#### `repo-a/AGENTS.md`

```markdown
# Project Overview

Kotlin + Quarkus backend service.

## Important Directories

- `src/main/kotlin/.../api` : entry layer
- `src/main/kotlin/.../service` : business logic
- `src/main/kotlin/.../repository` : database access
- `src/main/resources` : config and GraphQL schema

## Commands

- Dev: `./gradlew quarkusDev`
- Test: `./gradlew test`
- Build: `./gradlew build`

## Rules

- Prefer constructor injection.
- Do not modify generated sources.
- Keep GraphQL schema and resolver changes in sync.
```

---

### repo-b：前端 Web 项目

#### `repo-b/opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",

  "agent": {
    "plan": {
      "model": "opencode/gpt-5.1-mini"
    },
    "build": {
      "model": "opencode/gpt-5.1-codex"
    }
  },

  "command": {
    "review-component": {
      "description": "Review current frontend component change",
      "agent": "plan",
      "subtask": true,
      "template": "Review the changed frontend component for state management, rendering issues, performance, accessibility, and test impact."
    }
  }
}
```

#### `repo-b/AGENTS.md`

```markdown
# Project Overview

React + TypeScript frontend application.

## Important Directories

- `src/pages` : page entry points
- `src/components` : reusable UI
- `src/hooks` : custom hooks
- `src/api` : backend API integration
- `src/state` : state management

## Commands

- Dev: `pnpm dev`
- Test: `pnpm test`
- Build: `pnpm build`

## Rules

- Prefer small presentational components.
- Avoid changing shared UI primitives unless necessary.
- Keep API contract changes explicit.
```

---

# 最推荐的多项目工作流

你平时在两个项目之间切换时，可以这样用：

### 刚进入一个 repo

```bash
opencode .
```

然后先跑：

```text
/repo-summary
```

因为 OpenCode 可以直接在某个工作目录启动，也支持 `opencode [project]` 的方式进入指定项目。([opencode.ai][7])

### 想快速找调用链

```text
/trace-entry userService.login
```

### 只想安全分析，不想改代码

切到 `plan`，或者直接：

```text
@explore 这个接口从哪里进入，最终调用了哪些 repository
```

`explore` 是官方内置只读 subagent，适合这类快速仓库探索。([opencode.ai][6])

### 真要改代码时

再切 `build`。

这样会比“任何事都在一个长会话里直接让 build 干”更省 token，也更快。

---

# 这套结构最关键的 5 个点

### 第一，公共知识放全局，项目知识放本地

因为 OpenCode 原生就区分全局配置和项目配置，而且项目配置优先级更高。([opencode.ai][1])

### 第二，公共流程放 skills，不放每个项目的 AGENTS

因为 skills 按需加载。([opencode.ai][3])

### 第三，高频动作做成 commands

尤其是 `repo-summary`、`trace-entry`、`review-diff`、`fix-failing-test` 这类跨 repo 通用动作。commands 支持描述、agent、model、subtask。([opencode.ai][4])

### 第四，分析类任务用快模型，修改类任务用强模型

官方支持在 agent 和 command 级别覆盖模型。([opencode.ai][6])

### 第五，把主上下文当“指挥层”，把重活下放到 subtask

`subtask: true` 是多项目场景里最值钱的配置之一。([opencode.ai][4])

---
