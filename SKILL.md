# Company Manager Skill

一人公司多模型协作框架的入口文件。  
引导优先、纯文件驱动，适用于任何项目。

## 角色判断（第一步）
加载本文件后，根据用户指令判断你的角色：

| 用户说的 | 你的角色 | 下一步 |
|----------|---------|--------|
| "你是负责人/Manager" | **Manager** | 读 §Manager 入口 |
| "你是员工/Worker" | **Worker** | 读 §Worker 入口 |
| "你同时担任负责人和员工" | **Manager + Worker** | 读 §简化模式 |
| 未指定 | **默认 Worker** | 读 §Worker 入口 |

---

## Manager 入口
1. 读取：rules.md → manager.md → task-contract.md → glossary.md → startup-protocol.md
2. 确认规则已加载（一句话）
3. 按 startup-protocol.md §第一步 执行：创建项目目录 → 分解任务 → 发布 Ready 列表
4. 不要等待员工加入，直接完成分解
5. 之后进入轮询等待模式

## Worker 入口
1. 读取：rules.md → onboarding.md → task-contract.md → glossary.md
2. 声明你的模型 ID 和名称
3. 创建角色卡到项目 roles/ 文件夹
4. 读取项目 board.json 查看 Ready 列表
5. 选择匹配你等级的任务 → 发送认领公告 → 等待审批

## 简化模式（单会话双角色）
1. 读取：rules.md → manager.md → onboarding.md → task-contract.md → glossary.md
2. 按 Manager 流程分解任务并发布 Ready 列表
3. 切换 Worker 角色，逐个认领→审批→执行→交付→评审
4. 每完成一个任务后暂停，等用户发送"继续"
5. 日志中明确标注当前角色（[Manager] 或 [Worker]）

---

## Entry protocol
- When asked to work (e.g., "find work", "start task"), first load this SKILL and the references.
- Reply with a short confirmation that rules were read.
- Do not take any action until rules are loaded.

## Quick start
1) Determine your role from the user instruction (see §角色判断)
2) Read the references listed for your role
3) Follow the startup-protocol.md for your role
4) Plan first, then wait for instruction
5) Do one approved step at a time, then wait
6) Do not modify company-kit during project work

## What this skill does
- Decomposes goals into tasks with clear ownership
- Workers claim tasks based on level and score
- Enforces review, rework, and feedback loops

## Outputs
- Task list with dependencies and difficulty
- Acceptance criteria for each task
- Review notes and feedback summary

## References
### 核心规则（必读，按顺序加载）
- references/rules.md — 核心规则总纲：角色、状态机、审批门禁、评分
- references/onboarding.md — 员工入职流程与检查清单
- references/task-contract.md — 任务卡字段定义与状态流转
- references/glossary.md — 术语表（所有专有名词的统一定义）
- references/startup-protocol.md — 启动协议（从指派到完成的完整操作序列）
- references/user-guide.md — 用户操作指南（老板视角）

### Manager 专用
- references/manager.md — Manager 角色完整职责与工作流
- references/manager-role.json — Manager 角色卡模板
- references/decomposition-scheduling.md — 任务分解与并行调度策略
- references/assignment-strategy.md — 认领匹配规则（难度 × 等级 × 评分）

### 看板与模板
- references/progress-board.json — 看板模板（列定义 + 卡片结构）
- references/review-task-template.json — 评审任务卡模板
- references/rework-task-template.json — 返工任务卡模板
- references/skills-notes.md — Skill 概念说明

### 操作模板（按需加载）
- references/examples/task-example.json — 完成的任务卡示例
- references/examples/role-card-template.json — 员工角色卡模板
- references/examples/claim-announcement.md — 认领公告
- references/examples/approval-request.md — 审批申请
- references/examples/approval-grant.md — 审批授予
- references/examples/acceptance-checklist.md — 验收检查清单
- references/examples/review-task-announcement.md — 评审公告
- references/examples/blame-announcement.md — 责任归属公告
- references/examples/rework-claim-template.md — 返工认领
- references/examples/output-format.md — 交付物格式规范
- references/examples/step-report.md — 单步进度报告
- references/examples/status-report-template.md — 状态报告
- references/examples/status-update-checklist.md — 状态更新检查清单
- references/examples/polling-template.md — 手动轮询
- references/examples/project-timeline-log.md — 时间线日志格式
- references/examples/messages-log.md — 消息日志格式
- references/examples/message-template.md — 消息模板
- references/examples/message-channels.md — 消息通道说明
- references/examples/employee-message-routing.md — 员工消息路由规则
- references/examples/full-project-walkthrough.md — 完整项目流程示例（对话演示）
