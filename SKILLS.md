# 一、OpenCode 最强 Skills 架构

核心原则：

```
Global skills = 开发能力
Project skills = 项目知识
```

原因：

* **开发能力是通用的**
* **项目规则是项目专属的**

最终结构：

```
~/.config/opencode/
 ├─ skills/
 │   ├─ git-commit
 │   ├─ pr-review
 │   ├─ testing
 │   ├─ refactor
 │   ├─ security
 │   ├─ api-design
 │   ├─ debugging
 │   ├─ performance
 │   ├─ documentation
 │   └─ architecture
```

项目：

```
project/
 └─ .opencode/
     └─ skills/
         ├─ project-memory
         ├─ coding-conventions
         ├─ build-test
         ├─ project-architecture
         └─ api-contract
```

---

# 二、最推荐的 20 个 Skills

我按照 **优先级排序**。

## ⭐ 核心（必备）

```
git-commit
git-release
pr-review
testing-patterns
refactor-patterns
debugging
project-memory
project-architecture
```

---

## ⭐⭐ 强烈推荐

```
secure-coding
performance-review
api-design
error-handling
logging
documentation
```

---

## ⭐⭐⭐ 进阶

```
k8s-deploy
ci-cd
db-schema
data-migration
observability
```

---

# 三、最重要的 10 个 Skills（详细）

## 1️⃣ git-commit

作用：

自动生成 commit message。

```
~/.config/opencode/skills/git-commit/SKILL.md
```

```markdown
---
name: git-commit
description: Generate high quality conventional commit messages
---

# Goal

Generate commit messages using Conventional Commits.

# Rules

Use format:

type(scope): summary

Examples:

feat(auth): add oauth login
fix(api): handle missing token
refactor(service): simplify validation logic

# Allowed types

feat
fix
refactor
docs
test
chore
perf

# Behaviour

Analyze git diff and generate the best commit message.
```

---

# 2️⃣ pr-review

```
skills/pr-review/SKILL.md
```

```markdown
---
name: pr-review
description: Perform a comprehensive code review
---

# Review checklist

Check:

- logic bugs
- edge cases
- performance
- security issues
- readability

# Output

Return:

1. Issues
2. Suggested improvements
3. Risk level
```

---

# 3️⃣ testing-patterns

```markdown
---
name: testing-patterns
description: Generate high quality unit tests
---

# Rules

Tests must:

- cover edge cases
- have descriptive names
- avoid flaky tests
- use mocking when necessary

# Test naming

should_doSomething_whenCondition
```

---

# 4️⃣ refactor-patterns

```markdown
---
name: refactor-patterns
description: Improve code quality without changing behaviour
---

# Goals

Improve:

- readability
- maintainability
- modularity

# Techniques

- extract function
- simplify condition
- remove duplication
```

---

# 5️⃣ debugging

```markdown
---
name: debugging
description: Systematically diagnose and fix bugs
---

# Process

1 identify root cause
2 confirm failing scenario
3 propose minimal fix
4 verify with tests
```

---

# 6️⃣ secure-coding

```markdown
---
name: secure-coding
description: Identify security issues
---

Check:

- injection vulnerabilities
- auth bypass
- unsafe deserialization
- secret leakage
```

---

# 7️⃣ api-design

```markdown
---
name: api-design
description: Design clean APIs
---

Rules:

- consistent naming
- proper status codes
- idempotent operations
- clear request/response schema
```

---

# 8️⃣ performance-review

```markdown
---
name: performance-review
description: Detect performance bottlenecks
---

Check:

- unnecessary loops
- database N+1
- excessive memory usage
```

---

# 9️⃣ project-memory（最重要）

这个 skill **极其关键**。

```
project/.opencode/skills/project-memory/SKILL.md
```

```markdown
---
name: project-memory
description: Persistent knowledge about this project
---

# Stack

Backend: Kotlin + Quarkus
Database: Postgres
Infra: Kubernetes

# Rules

- GraphQL endpoints return HTTP 200
- Authentication uses JWT
- Logging via structured logs

# Coding conventions

Prefer constructor injection.
Avoid static helpers.
```

这样 AI **永远记住你的项目规则**。

---

# 10️⃣ project-architecture

```markdown
---
name: project-architecture
description: Explain system architecture
---

Services:

gateway
auth-service
user-service

Communication:

GraphQL + REST

Database:

Postgres
```

---

# 四、token 最省的 Skill 写法

很多人写 skill 很浪费 token。

❌ 不好的：

```
Write detailed tests with good quality and coverage
```

✅ 好的：

```
Rules:

- cover edge cases
- descriptive names
- avoid flakiness
```

**原则**

```
bullet > paragraph
```

---

# 五、如何让 OpenCode 按需加载 Skills

关键：

```
description 字段
```

例如：

```
description: Generate commit messages
```

AI 会在需要的时候加载。

例如：

```
commit code
```

就会自动调用 `git-commit`.

---

# 六、Pro 玩家结构（推荐）

最终结构：

```
~/.config/opencode/

agents.md

skills/
 ├─ git-commit
 ├─ git-release
 ├─ pr-review
 ├─ debugging
 ├─ testing-patterns
 ├─ refactor-patterns
 ├─ secure-coding
 ├─ api-design
 ├─ performance-review
 └─ documentation
```

项目：

```
project/

.opencode/

agents.md

skills/
 ├─ project-memory
 ├─ coding-conventions
 ├─ project-architecture
 ├─ build-test
 └─ api-contract
```

---

# 七、高手才会用的 Skill（很强）

推荐再加两个：

### observability

```
logging
metrics
tracing
```

### db-schema

```
schema design
migration
index optimization
```

---

# 八、一个关键经验（很多人踩坑）

Skill 数量不要超过：

```
20
```

否则：

* agent decision 变慢
* token 消耗增加

---

# 九、终极推荐配置（总结）

如果是 **程序员最佳组合**：

Global：

```
git-commit
git-release
pr-review
testing-patterns
refactor-patterns
debugging
secure-coding
api-design
performance-review
documentation
```

Project：

```
project-memory
project-architecture
coding-conventions
api-contract
build-test
db-schema
observability
```