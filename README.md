# Company Kit — 一人公司多模型协作框架

一个即插即用的、纯文件驱动的多模型协作管理框架。  
无运行时依赖、无自动文件修改，所有协调通过文档和模板完成。

---

## 核心理念

| 原则 | 说明 |
|------|------|
| 引导优先 | 所有规则通过文档传达，不依赖自动化工具 |
| 文件即状态 | 任务进度、审批、消息全部通过 JSON/Markdown 文件记录 |
| 角色隔离 | Manager 只规划/审批，Worker 只执行，互不越界 |
| 审批门禁 | 任何工作/评审开始前必须获得 Manager 显式批准 |
| 项目隔离 | 项目文件与 company-kit 严格分离 |

---

## 目录结构

```
company-kit/
├── README.md                   ← 本文件（框架说明）
├── SKILL.md                    ← Skill 入口（模型加载起点）
├── task-template.json          ← 任务卡模板（所有任务基于此创建）
├── assets/                     ← 预留资源目录（图片/图表等）
│
├── references/                 ← 规则与参考文档
│   ├── rules.md                ← 核心规则总纲
│   ├── onboarding.md           ← 员工入职指南
│   ├── manager.md              ← Manager 角色职责与工作流
│   ├── manager-role.json       ← Manager 角色卡模板
│   ├── task-contract.md        ← 任务字段契约（字段定义 + 状态机）
│   ├── decomposition-scheduling.md  ← 任务分解与调度规则
│   ├── assignment-strategy.md  ← 认领匹配策略
│   ├── skills-notes.md         ← Skill 概念说明
│   ├── startup-protocol.md     ← 启动协议（完整操作序列）
│   ├── user-guide.md           ← 用户操作指南
│   ├── progress-board.json     ← 看板模板
│   ├── review-task-template.json    ← 评审任务模板
│   ├── rework-task-template.json    ← 返工任务模板
│   │
│   └── examples/               ← 操作模板与示例
│       ├── task-example.json          ← 完整任务卡示例
│       ├── role-card-template.json    ← 员工角色卡模板
│       ├── claim-announcement.md      ← 认领公告模板
│       ├── approval-request.md        ← 审批申请模板
│       ├── approval-grant.md          ← 审批授予模板
│       ├── acceptance-checklist.md    ← 验收检查清单
│       ├── review-task-announcement.md ← 评审公告模板
│       ├── blame-announcement.md      ← 责任公告模板
│       ├── rework-claim-template.md   ← 返工认领模板
│       ├── output-format.md           ← 交付物格式规范
│       ├── step-report.md             ← 单步进度报告模板
│       ├── status-report-template.md  ← 状态报告模板
│       ├── status-update-checklist.md ← 状态更新检查清单
│       ├── polling-template.md        ← 手动轮询模板
│       ├── project-timeline-log.md    ← 项目时间线日志格式
│       ├── messages-log.md            ← 消息日志格式
│       ├── message-template.md        ← 消息模板
│       ├── message-channels.md        ← 消息通道说明
│       ├── employee-message-routing.md ← 员工消息路由规则
│       └── full-project-walkthrough.md ← 完整项目流程示例
│
└── tests/                      ← 端到端流程测试用例
    ├── test-001-task.json       ← 简单任务测试
    ├── test-001-plan.md
    ├── test-001-feedback.json
    ├── test-002-task.json       ← 复杂任务测试（多子任务 + 集成）
    ├── test-002-plan.md
    └── test-002-feedback.json
```

---

## 快速上手

### 1. 加载框架
模型读取 `SKILL.md` → 按顺序加载 `rules.md`、`onboarding.md`、`task-contract.md`。

### 2. 启动项目
```
workspace/
├── company-kit/     ← 框架（只读）
└── my-project/      ← 项目文件（与 company-kit 同级）
    ├── roles/       ← 角色卡
    ├── tasks/       ← 任务卡
    ├── messages/    ← 消息日志
    └── ...          ← 项目产出
```

### 3. 核心工作流
```
用户目标 → Manager 分解为任务卡 → 发布 Ready 列表
         → Worker 认领 → 等待审批 → 执行 → 交付
         → Manager 标记 Done → 解锁下游任务
         → 循环直至全部 Done → 用户最终验收
```

### 4. 任务状态机
```
New → Decomposing → Ready → In Progress → Done
用户验收不通过 → Rework → Done
```

---

## 关键规则速查

| 规则 | 详见 |
|------|------|
| 角色职责与禁区 | references/rules.md §Roles |
| Manager 完整工作流 | references/manager.md |
| 任务字段定义与状态机 | references/task-contract.md |
| 分解与并行调度 | references/decomposition-scheduling.md |
| 认领匹配策略 | references/assignment-strategy.md |
| 入职流程 | references/onboarding.md |
| 审批门禁 | references/rules.md §Approval gate |
| 评分公式 | references/rules.md §Scoring |
| 消息通道 | references/examples/message-channels.md |

---

## 自定义扩展

- 添加新角色：复制 `references/examples/role-card-template.json` 到项目 `roles/`
- 创建新任务：复制 `task-template.json`，填写字段
- 扩展规则：在项目文件夹中添加补充规则，不修改 company-kit
- 添加资源：将图片/图表等放入 `assets/`
