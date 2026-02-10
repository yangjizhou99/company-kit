# 启动协议 (Startup Protocol)

从用户发出目标到项目完成的完整操作序列。

---

## 前置条件
- company-kit/ 已存在于工作区
- 用户有至少两个 AI 会话可用（一个当 Manager，一个或多个当 Worker）

---

## 第一步：用户指派 Manager

用户在**会话 A** 中发送：
```
读取 company-kit/SKILL.md，你是本项目的 Manager。
项目目标：<用户目标描述>
```

### Manager 收到后立即执行（无需等待）：
1. 读取 SKILL.md → rules.md → manager.md → task-contract.md → glossary.md → decomposition-scheduling.md
2. 确认已加载规则（一句话回复）
3. 创建项目根目录（如 `snake-game/`）及子目录：
   ```
   snake-game/
   ├── roles/
   ├── tasks/
   ├── messages/
   │   └── manager-inbox.log  ← 空文件，Manager 审批/回复通道
   ├── board.json         ← 从 progress-board.json 复制（含 concurrencyLayers）
   └── timeline.log       ← 空文件
   ```
4. 创建自己的角色卡 `roles/manager.json`
5. **并发优先分解**：将用户目标分解为任务卡，重点：
   - 按模块/关注点拆分，而非按时间顺序
   - 只标注真实数据依赖
   - 为每个任务标注 `concurrencyLayer` (0, 1, 2...)
   - 同层任务可并发执行
   - 如有模块交互，先提取"定义接口"任务
6. 写入 `tasks/`，更新 `board.json`（含 `concurrencyLayers` 段）
7. **输出并发层级图**（必须），格式：
   ```
   === 并发层级图 ===
   Layer 0 (可立即启动):
     ├── task-001: <标题> (难度 N)
     ├── task-002: <标题> (难度 N)
     └── task-003: <标题> (难度 N)
   Layer 1 (依赖 Layer 0):
     ├── task-004: <标题> ← 依赖 [task-001]
     └── task-005: <标题> ← 依赖 [task-002]
   Layer 2 (依赖 Layer 1):
     └── task-006: <标题> ← 依赖 [task-004, task-005]

   并发度: 3 / 2 / 1
   关键路径: task-001 → task-004 → task-006
   ===
   ```
8. **并发质量检查**：至少一个 Layer 含 2+ 任务，无虚假依赖
9. 将 Layer 0 中无依赖的任务标记为 Ready
10. 输出 **Ready 列表公告**

**关键：Manager 不等待就直接完成以上全部步骤。**

---

## 第二步：用户指派 Worker

用户在**会话 B** 中发送：
```
读取 company-kit/SKILL.md，你是本项目的 Worker。
项目文件夹：<project-root>/
```

### Worker 收到后立即执行：
1. 读取 SKILL.md → rules.md → onboarding.md → task-contract.md
2. 声明自己的模型 ID 和名称
3. 在 `<project>/roles/` 中创建自己的角色卡
4. 读取 `<project>/board.json` 查看 Ready 列表
5. 选择一个匹配自己等级的任务，发送认领公告
6. 在 `messages/employee-<id>.log` 中写入审批申请
7. 等待 Manager 审批

---

## 第三步：用户驱动循环

用户在 Manager 会话中发送轮询短语：
```
继续检查看板
```

### Manager 收到轮询后：
1. 检查所有 `messages/employee-*.log`
2. 审批待处理的认领请求（三处更新：task JSON + board.json + manager-inbox.log）
3. **主动异常检测**：检查每个 Worker 的心跳、是否有过期认领、是否有重复状态报告
4. 对检测到的异常，发送 `[REDIRECT]` / `[WAKE]` / `[INSTRUCTION]` 指令到 `manager-inbox.log`
5. 回复用户当前状态（含 Worker 健康度报告）

### 用户再到 Worker 会话中发送：
```
继续
```

### Worker 收到"继续"后：
1. 在 `employee-<id>.log` 写入 `[HEARTBEAT]`
2. 检查 `manager-inbox.log`（查看审批、指令、重定向）
3. **如果有 `[REDIRECT]` 或 `[INSTRUCTION]`**：按指令执行（而非继续等待）
4. 如果有审批 → 执行任务 → 交付
5. 如果无消息 → 认领新任务或报告等待

### 用户重复此循环：
```
用户 → Manager 会话："继续检查看板"
用户 → Worker 会话："继续"
```

直到所有任务 Done。

---

## 第四步：项目完成

当所有任务标记为 Done 后：
1. Manager 输出**项目完成报告**
2. Manager 请求用户做最终验收
3. 用户确认 → 项目关闭

---

## 简化模式（单会话）

如果用户在同一个会话中同时运行 Manager 和 Worker：

1. 用户指定模型同时担任 Manager 和 Worker
2. 发送：`读取 company-kit/SKILL.md，你同时担任 Manager 和 Worker，项目目标：<目标>`
3. 模型按以下顺序自动执行：
   - Manager 阶段：**并发优先分解** → 创建任务卡（含 concurrencyLayer） → 输出并发层级图 → 发布 Ready 列表
   - Worker 阶段：从 Ready 列表中认领（同层任务可连续执行） → 自我审批 → 执行 → 交付
   - Manager 阶段：标记 Done → 解锁下游 → 更新 Ready 列表
   - 重复直到所有任务完成
   - 请求用户最终验收
4. 每完成一个任务后暂停，等用户发送"继续"推进下一个
5. **同层任务可按任意顺序执行**，不必从左到右

**注意：** 简化模式下审批门禁仍然存在，但由模型自己的 Manager 角色审批自己的 Worker 角色。日志仍需记录完整。

---

## 常见问题

**Q: 用户在流程中需要做什么？**
A: 三件事：(1) 指派角色 (2) 在会话间传递"继续" (3) 最终验收

**Q: 可以多个 Worker 吗？**
A: 可以。每个 Worker 一个独立会话，各自认领不同的 Ready 任务。

**Q: Manager 能不能也写代码？**
A: 不能。Manager 只创建项目脚手架（目录、任务卡、看板），不写交付物代码。

**Q: 如果 Worker 卡住了怎么办？**
A: Worker 在消息日志中报告阻塞 → 用户轮询 Manager → Manager 读取消息并响应。
