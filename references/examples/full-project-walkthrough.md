# 完整项目流程示例

以"贪吃蛇游戏"为例，展示从用户发出目标到项目完成的完整对话流程。

---

## 阶段 1：用户指派 Manager

**用户 → 会话 A：**
```
读取 company-kit/SKILL.md，你是本项目的 Manager。
项目目标：开发一个浏览器运行的贪吃蛇游戏，HTML/CSS/JS，包含移动、吃食物、计分、死亡判定，附带 README。
```

**Manager 回复（应该是这样）：**
```
规则已加载。

已创建项目结构：
  snake-game/
  ├── roles/manager.json
  ├── tasks/
  ├── messages/
  ├── board.json
  └── timeline.log

任务分解完成，已创建 5 个任务卡：

=== Ready 列表 ===
- task-001: 项目脚手架 (难度 1) — 创建 index.html + style.css + game.js 空文件
- task-002: 游戏画布与蛇渲染 (难度 2) — Canvas 初始化 + 蛇绘制
=== 等待依赖 ===
- task-003: 键盘控制与移动逻辑 (难度 3) — 依赖 task-002
- task-004: 食物生成与计分 (难度 3) — 依赖 task-003
- task-005: 死亡判定与游戏结束 (难度 3) — 依赖 task-004
- task-006: README 与运行说明 (难度 1) — 依赖 task-005
===

等待员工加入认领。
```

---

## 阶段 2：用户指派 Worker

**用户 → 会话 B：**
```
读取 company-kit/SKILL.md，你是本项目的 Worker。
项目文件夹：snake-game/
```

**Worker 回复：**
```
规则已加载。
模型 ID: claude-opus-4-20250514
员工 ID: emp-worker-001

已创建角色卡: snake-game/roles/emp-worker-001.json

查看 Ready 列表：
- task-001: 项目脚手架 (难度 1) ✓ 匹配
- task-002: 游戏画布与蛇渲染 (难度 2) ✓ 匹配

认领 task-001: 项目脚手架
已写入 messages/employee-emp-worker-001.log：审批申请
等待 Manager 审批。
```

---

## 阶段 3：推进循环

### 轮次 1：审批认领

**用户 → 会话 A：**
```
继续检查看板
```

**Manager 回复：**
```
检查 messages/employee-emp-worker-001.log...
收到 emp-worker-001 认领 task-001 的请求。

✅ 已批准 task-001 认领。
已写入 messages/manager-inbox.log：审批授予。
已更新 board.json：task-001 → In Progress。

当前状态：
- task-001: In Progress (emp-worker-001)
- task-002: Ready
- task-003~006: 等待依赖
```

### 轮次 2：Worker 执行

**用户 → 会话 B：**
```
继续
```

**Worker 回复：**
```
检查 messages/manager-inbox.log...
收到 task-001 审批授予。

执行 task-001: 项目脚手架

创建文件：
- snake-game/index.html（基础 HTML 结构 + Canvas 元素）
- snake-game/style.css（画布居中样式）
- snake-game/game.js（空文件 + 入口函数骨架）

验收标准对照：
✅ index.html 可在浏览器中打开
✅ Canvas 元素存在
✅ game.js 已引入且无报错

已交付。等待评审。
```

### 轮次 3：Manager 评审

**用户 → 会话 A：**
```
继续检查看板
```

**Manager 回复：**
```
检查 messages/employee-emp-worker-001.log...
收到 task-001 交付。

评审 task-001：
✅ 验收标准 1: index.html 结构正确
✅ 验收标准 2: Canvas 元素存在
✅ 验收标准 3: game.js 引入无误

task-001 → Done
task-002 无依赖变化，维持 Ready。

=== Ready 列表 ===
- task-002: 游戏画布与蛇渲染 (难度 2)
===

等待员工认领。
```

### 轮次 4-N：重复

Worker 继续认领 → 审批 → 执行 → 交付 → 评审，直到所有任务 Done。

---

## 阶段 4：项目完成

**Manager 回复（所有任务 Done 后）：**
```
=== 项目完成报告 ===
所有 6 个任务已完成：
- task-001: Done ✅
- task-002: Done ✅
- task-003: Done ✅
- task-004: Done ✅
- task-005: Done ✅
- task-006: Done ✅

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

**Manager 评审失败时：**
```
评审 task-003：
❌ 验收标准 2: 蛇碰到边界时未停止

task-003 → Rework
责任人: emp-worker-001
原因: 边界碰撞检测缺失

已创建 rework-task-003，限定 emp-worker-001 认领。

=== Ready 列表 ===
- rework-task-003: 修复边界碰撞检测 (难度 2, 限定认领)
===
```
