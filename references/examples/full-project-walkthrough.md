# 完整项目流程示例

以"贪吃蛇游戏"为例，展示从用户发出目标到项目完成的完整对话流程。
重点演示 **并发优先分解** 策略。

---

## 阶段 1：用户指派 Manager

**用户 → 会话 A：**
```
读取 company-kit/SKILL.md，你是本项目的 Manager。
项目目标：开发一个浏览器运行的贪吃蛇游戏，HTML/CSS/JS，包含移动、吃食物、计分、死亡判定，附带 README。
```

**Manager 回复（并发优先分解）：**
```
规则已加载。

已创建项目结构：
  snake-game/
  ├── roles/manager.json
  ├── tasks/
  ├── messages/
  ├── board.json
  └── timeline.log

任务分解完成（并发优先），已创建 7 个任务卡：

=== 并发层级图 ===
Layer 0 (可立即启动, 3 个并发):
  ├── task-001: HTML/CSS 页面结构 (难度 1) — 创建 index.html + style.css + Canvas 布局
  ├── task-002: 游戏常量与工具模块 (难度 1) — 定义网格尺寸、方向枚举、颜色、碰撞检测工具函数
  └── task-003: README 编写 (难度 1) — 项目说明、运行方法、功能列表
Layer 1 (依赖 Layer 0, 3 个并发):
  ├── task-004: 蛇渲染与移动逻辑 (难度 3) ← 依赖 [task-001, task-002]
  ├── task-005: 食物生成与计分系统 (难度 2) ← 依赖 [task-002]
  └── task-006: 键盘输入处理 (难度 2) ← 依赖 [task-002]
Layer 2 (依赖 Layer 1, 1 个集成):
  └── task-007: 主游戏循环集成 (难度 3) ← 依赖 [task-004, task-005, task-006]

并发度: 3 / 3 / 1
关键路径: task-001 → task-004 → task-007
===

=== Ready 列表 ===
- task-001: HTML/CSS 页面结构 (难度 1)
- task-002: 游戏常量与工具模块 (难度 1)
- task-003: README 编写 (难度 1)
===

3 个任务可立即并发执行。等待员工加入认领。
```

> **对比旧的线性分解**：旧方案是 6 步纯串行 (1→2→3→4→5→6)，
> 新方案将并发度从 1 提升到 3，总层数只有 3 层。
> 如果有 3 个 Worker，Layer 0 和 Layer 1 都能完全并行，
> 整体完成时间 ≈ 3 层 × 单任务时间，而非 6 步串行。

---

## 阶段 2：用户指派 Worker（可同时指派多个）

**用户 → 会话 B（Worker 1）：**
```
读取 company-kit/SKILL.md，你是本项目的 Worker。
项目文件夹：snake-game/
```

**用户 → 会话 C（Worker 2）：**
```
读取 company-kit/SKILL.md，你是本项目的 Worker。
项目文件夹：snake-game/
```

**Worker 1 回复：**
```
规则已加载。
模型 ID: claude-opus-4-20250514
员工 ID: emp-worker-001

已创建角色卡: snake-game/roles/emp-worker-001.json

查看 Ready 列表（3 个并发可用）：
- task-001: HTML/CSS 页面结构 (难度 1, Layer 0) ✓ 匹配
- task-002: 游戏常量与工具模块 (难度 1, Layer 0) ✓ 匹配
- task-003: README 编写 (难度 1, Layer 0) ✓ 匹配

认领 task-001: HTML/CSS 页面结构
已写入 messages/employee-emp-worker-001.log：审批申请
等待 Manager 审批。
```

**Worker 2 回复：**
```
规则已加载。
模型 ID: gemini-2.5-pro
员工 ID: emp-worker-002

已创建角色卡: snake-game/roles/emp-worker-002.json

查看 Ready 列表（3 个并发可用）：
- task-002: 游戏常量与工具模块 (难度 1, Layer 0) ✓ 匹配
- task-003: README 编写 (难度 1, Layer 0) ✓ 匹配
（task-001 已被 emp-worker-001 认领）

认领 task-002: 游戏常量与工具模块
已写入 messages/employee-emp-worker-002.log：审批申请
等待 Manager 审批。
```

---

## 阶段 3：推进循环（并发执行）

### 轮次 1：批量审批认领

**用户 → 会话 A：**
```
继续检查看板
```

**Manager 回复：**
```
检查 messages/employee-*.log...
收到 2 个认领请求：

✅ 已批准 emp-worker-001 认领 task-001 (Layer 0)
✅ 已批准 emp-worker-002 认领 task-002 (Layer 0)

已执行三处更新（每个任务）：
  ① tasks/task-001.json → status: "In Progress", assignee: "emp-worker-001", approved: true
  ② board.json → task-001 card updated
  ③ manager-inbox.log 追加：
     [2026-02-10T11:06:00Z] [APPROVAL] task-001 approved for emp-worker-001. Status: In Progress.
     [2026-02-10T11:06:00Z] [APPROVAL] task-002 approved for emp-worker-002. Status: In Progress.

当前状态：
- task-001: In Progress (emp-worker-001) [Layer 0]
- task-002: In Progress (emp-worker-002) [Layer 0]
- task-003: Ready [Layer 0] ← 仍可认领
- task-004~007: 等待依赖 [Layer 1-2]

并发执行中: 2 个任务同时进行
```

### 轮次 2：Worker 并发执行

两个 Worker 可以**同时执行**各自的任务，互不干扰。

**用户 → 会话 B：** `继续`
**用户 → 会话 C：** `继续`

（两个 Worker 同时执行，无需等待对方完成）

### 轮次 3：Manager 批量处理交付，推进 Layer 1

**用户 → 会话 A：**
```
继续检查看板
```

**Manager 回复：**
```
检查 messages/employee-*.log...
收到 2 个交付：

task-001 → Done (emp-worker-001)
task-002 → Done (emp-worker-002)

依赖解除，以下任务升级为 Ready：
- task-004: 蛇渲染与移动逻辑 ← 依赖 [task-001✅, task-002✅] 已满足
- task-005: 食物生成与计分系统 ← 依赖 [task-002✅] 已满足
- task-006: 键盘输入处理 ← 依赖 [task-002✅] 已满足

=== Ready 列表 (Layer 1, 3 个并发可用) ===
- task-003: README 编写 (难度 1, Layer 0)
- task-004: 蛇渲染与移动逻辑 (难度 3, Layer 1)
- task-005: 食物生成与计分系统 (难度 2, Layer 1)
- task-006: 键盘输入处理 (难度 2, Layer 1)
===

4 个任务可并发执行。等待认领。
```

### 轮次 4-N：重复直到完成

Worker 继续并发认领 Layer 1 任务 → 全部完成后 → Layer 2 的 task-007 (集成) 变为 Ready。

---

## 阶段 4：项目完成

**Manager 回复（所有任务 Done 后）：**
```
=== 项目完成报告 ===
所有 7 个任务已完成：
- task-001: Done ✅ (Layer 0)
- task-002: Done ✅ (Layer 0)
- task-003: Done ✅ (Layer 0)
- task-004: Done ✅ (Layer 1)
- task-005: Done ✅ (Layer 1)
- task-006: Done ✅ (Layer 1)
- task-007: Done ✅ (Layer 2)

并发效率报告：
- 总层数: 3（而非 7 步串行）
- 最大并发度: 3（Layer 0 和 Layer 1）
- 关键路径: task-001 → task-004 → task-007

交付物清单：
- snake-game/index.html
- snake-game/style.css
- snake-game/game.js
- snake-game/README.md

请进行最终验收。
===
```

**用户：**
```
验收通过
```

---

## 如果出现返工

**Manager 创建返工任务时（最终验收失败）：**
```
用户反馈：蛇碰到边界时未停止

task-007（主游戏循环集成）→ Rework
责任人: emp-worker-003
原因: 边界碰撞检测在集成时缺失

已创建 rework-task-007，限定 emp-worker-003 认领。

=== Ready 列表 ===
- rework-task-007: 修复边界碰撞检测 (难度 2, 限定认领)
===
```
```
