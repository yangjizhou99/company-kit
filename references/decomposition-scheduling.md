# Decomposition and Scheduling Rules

This defines how to split work and maximize parallel execution.

---

## Core principle: concurrency-first decomposition

> **默认思路应是"哪些能同时做"，而不是"先做什么再做什么"。**
> 线性链式分解是反模式。只有真正存在数据/接口依赖时才设置 dependency。

---

## Decomposition steps

1) **Identify deliverables** — 列出所有最终交付物和验收标准。
2) **按模块/关注点拆分** — 将目标拆为功能独立的模块（UI / 逻辑 / 数据 / 文档等），而不是按时间顺序。
3) **接口先行** — 如果两个模块有交互，先提取一个"定义接口/契约"的前置任务，让两个实现可以并行。
4) **标注依赖** — 只标注 **真实的数据依赖**。判断标准：
   - ❌ "一般习惯先做 A 再做 B" → 不算依赖
   - ✅ "B 的代码必须 import A 的输出才能编译/运行" → 真依赖
5) **划分并发层级 (concurrencyLayer)** — 按 DAG（有向无环图）拓扑排序，将任务分到层级：
   - Layer 0: 无依赖，可立即并发启动
   - Layer 1: 依赖 Layer 0 中的任务
   - Layer N: 依赖 Layer N-1 中的任务
6) **验证并发度** — 如果任何 Layer 只有 1 个任务且不是最终集成任务，考虑是否能进一步拆分。
7) **Assign difficulty** — 给每个任务标注难度 1-5。

---

## Concurrency-aware decomposition strategies

### Strategy 1: 模块并行
将系统按功能模块拆分，每个模块独立开发。

```
示例（Web App）:
Layer 0: [前端脚手架] [后端脚手架] [数据库 Schema]  ← 3 个并发
Layer 1: [前端页面组件] [API 路由实现] [测试数据]     ← 3 个并发
Layer 2: [前后端联调] [README]                        ← 2 个并发
```

### Strategy 2: 接口契约先行
当模块有依赖时，先提取"定义接口"任务，然后实现可以并行。

```
示例：
Layer 0: [定义 API 接口规范]
Layer 1: [前端按接口开发(mock数据)] [后端按接口实现]  ← 并发
Layer 2: [集成联调]
```

### Strategy 3: 横向切片
按功能特性（feature）水平切片，每个 feature 包含完整的前后端。

```
示例（电商应用）:
Layer 0: [项目脚手架 + 公共组件]
Layer 1: [商品列表 feature] [购物车 feature] [用户登录 feature]  ← 3 个并发
Layer 2: [集成测试] [部署配置]                                    ← 2 个并发
```

### Strategy 4: 独立产出物并行
文档、测试、配置等与主代码无强依赖的任务应尽早并行。

```
示例:
Layer 0: [核心逻辑开发] [README 编写] [CI 配置]  ← 3 个并发
Layer 1: [单元测试(依赖核心逻辑)]
```

---

## DAG visualization (Manager must produce)

分解完成后，Manager **必须** 输出并发层级图：

```
=== 并发层级图 ===
Layer 0 (可立即启动):
  ├── task-001: <标题>
  ├── task-002: <标题>
  └── task-003: <标题>
Layer 1 (依赖 Layer 0):
  ├── task-004: <标题>  ← 依赖 [task-001]
  └── task-005: <标题>  ← 依赖 [task-001]
Layer 2 (依赖 Layer 1):
  └── task-006: <标题>  ← 依赖 [task-004, task-005]

并发度: 3 / 2 / 1
关键路径: task-001 → task-004 → task-006
===
```

---

## Anti-patterns (must avoid)

| 反模式 | 问题 | 修正 |
|--------|------|------|
| 纯线性链 A→B→C→D→E | 并发度为 1，效率最低 | 找出无真实依赖的步骤并行化 |
| "先搭架子再写逻辑" | 不一定有真依赖 | 架子和独立逻辑模块可并行 |
| 所有任务依赖于一个巨型前置任务 | 瓶颈，阻塞所有后续 | 将前置任务拆小，只暴露必要接口 |
| 把文档/测试放在最后 | 浪费并发机会 | 文档/测试与开发并行 |
| 用 parallelizable=true 但不减少 dependencies | 字段矛盾，实际仍串行 | 减少不必要的依赖才是真并行 |

---

## Parallelization rules
- Prefer tasks with no shared state.
- Avoid parallel tasks that require the same artifact at the same time.
- If two tasks can start after the same dependency, mark both with the same `concurrencyLayer`.
- Tasks in the same `concurrencyLayer` **must** be independently executable.
- A task's `concurrencyLayer` = max(layer of each dependency) + 1; if no dependencies, layer = 0.

---

## Scheduling heuristics
- Start **all** tasks in Layer 0 simultaneously.
- When a Layer N task completes, check if any Layer N+1 tasks have all dependencies met → mark Ready.
- Prioritize tasks on the **critical path** (longest dependency chain).
- If a task unblocks multiple others, schedule it earlier.
- **Maximize concurrent Ready tasks** — if the Ready list has only 1 task and other tasks are blocked, re-examine dependencies.

---

## Decomposition quality checklist (Manager must verify)

- [ ] 并发度 ≥ 2（至少有一个 Layer 包含 2+ 任务）
- [ ] 无虚假依赖（每条依赖都有真实的数据/接口理由）
- [ ] 文档/测试/配置类任务已尽可能前置并行
- [ ] 关键路径已识别
- [ ] 每个任务的 inputs/outputs 明确（支撑并行开发）
- [ ] 跨层任务的 inputs/outputs 已具体到文件和接口级别（不仅仅是"代码"）
- [ ] 接口/契约任务已提取（如需要）

---

## Ownership clarity
- One task, one owner.
- If unclear ownership, split further.
- Parallel tasks should have clear, non-overlapping output artifacts.
