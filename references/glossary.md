# 术语表 (Glossary)

框架中使用的核心术语定义。

## 角色

| 术语 | 定义 |
|------|------|
| **Manager** | 负责分解任务、发布 Ready 列表、审批认领、标记 Done。不执行任何 Worker 任务，不做质量评审。 |
| **Worker** | 执行具体任务并交付产出。不修改看板或任务状态文件。 |
| **User（用户）** | 项目老板，只在最后做整体验收。 |
| **Employee** | Worker 的统称，即所有非 Manager 的参与者。 |

## 任务状态

| 状态 | 含义 |
|------|------|
| **New** | 刚创建，尚未分解或准备就绪 |
| **Decomposing** | 正在被 Manager 拆分为子任务 |
| **Ready** | 所有依赖已完成，可被 Worker 认领 |
| **In Progress** | 已认领且已获审批，正在执行 |
| **Done** | Worker 已交付，Manager 已标记完成 |
| **Rework** | 用户最终验收不通过时，由 Manager 创建的修复任务 |

## 流程术语

| 术语 | 定义 |
|------|------|
| **Claim（认领）** | Worker 宣布将执行某个 Ready 任务。认领后需等待 Manager 审批。 |
| **Approval Gate（审批门禁）** | 任何工作开始前必须获得 Manager 的显式批准。 |
| **Acceptance Criteria（验收标准）** | 任务卡中定义的、可测试的通过条件。 |
| **Ready List（Ready 列表）** | 当前所有可认领任务的清单，每次状态变更后由 Manager 公告。 |
| **Polling（轮询）** | 用户发送固定短语（如"继续检查看板"）触发 Manager 检查状态并响应。 |
| **Board（看板）** | `progress-board.json` 中记录的任务全局视图。 |
| **Timeline Log（时间线日志）** | 项目文件夹中记录每次认领、交付、评审事件的日志。 |
| **Messages Log（消息日志）** | 员工与 Manager 之间的异步通信记录。 |
| **Score（评分）** | 员工的累积表现分，影响任务匹配。公式：`score + success_weight - rework_weight - delay_weight` |
| **Blame（责任归属）** | 确认缺陷根因后，记录责任人和原因。对应的返工任务限定该责任人认领。 |
| **Restricted Assignee（限定认领人）** | 返工任务专用字段，仅指定的责任人可认领。 |

## 文件术语

| 术语 | 定义 |
|------|------|
| **Task Card（任务卡）** | 基于 `task-template.json` 创建的单个任务文件。 |
| **Role Card（角色卡）** | 基于 `role-card-template.json` 创建的员工身份文件。 |
| **Review Task（评审任务）** | 已废弃 — 不再做中间评审，只有最终用户验收。 |
| **Rework Task（返工任务）** | 基于 `rework-task-template.json` 创建的修复任务。 |
| **Skill** | 可复用的知识包，通过 `SKILL.md` 加载。 |
| **Company Kit** | 本框架本身，作为只读参考，项目运行时不修改。 |
| **manager-inbox.log** | Manager 向所有 Worker 发布审批、Done 通知、指令和回复的**唯一通道**。Manager 是唯一写入者，所有 Worker 在收到"继续"后读取此文件。项目初始化时由 Manager 创建为空文件。 |
| **employee-\<id\>.log** | 每个 Worker 的**专属通道**，用于写入认领、交付、问题和状态更新。Worker 是唯一写入者，Manager 在轮询时读取。**Manager 禁止向此文件写入任何内容。** |
