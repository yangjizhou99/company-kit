# Message Channels

Use per-employee logs for requests and a manager inbox for replies.

## Employee channel
- File: messages/employee-<employeeId>.log
- **Who writes**: Only the employee themselves.
- **Content**: Claims, deliveries, questions, status updates.
- **Who reads**: Manager (on polling).

## Manager channel
- File: messages/manager-inbox.log
- **Who writes**: Only the Manager.
- **Content**: Approvals, Done notifications, replies, instructions.
- **Who reads**: All Workers (on "继续").

## Routing diagram
```
Worker → employee-<id>.log   (认领、交付、问题、状态更新)
                  ↓ Manager reads on "继续检查看板"
Manager → manager-inbox.log  (审批、Done通知、回复、公告)
                  ↓ Worker reads on "继续"
```

## Log entry format (MANDATORY — do not deviate)

All log entries MUST use this exact format. Free-form markdown, JSON, or plain text is NOT allowed.

```
[<ISO timestamp>] [<TAG>] <pipe-separated fields>
```

### Exact templates per tag

**Worker writes to `employee-<employeeId>.log`:**
```
[<ISO timestamp>] [CLAIM] task-<id> | title: <title> | by: <employeeId> | difficulty: <N> | deps-met: yes
[<ISO timestamp>] [DELIVERY] task-<id> | by: <employeeId> | files: <file1>, <file2> | criteria-met: all
[<ISO timestamp>] [INFO] by: <employeeId> | <free text message>
```

**Manager writes to `manager-inbox.log`:**
```
[<ISO timestamp>] [APPROVAL] task-<id> approved for <employeeId>. Status: In Progress.
[<ISO timestamp>] [DONE] task-<id> marked Done. Delivered by <employeeId>.
[<ISO timestamp>] [REPLY] To <employeeId>: <message content>
[<ISO timestamp>] [REJECT] task-<id> claim by <employeeId> rejected: <reason>.
```

> [!IMPORTANT]
> `employeeId` is the sole identifier used everywhere. Do NOT substitute model id or model name.

## ⚠️ Common mistakes

> **Manager must NEVER write to `employee-*.log`.**
> If Manager writes an approval to `employee-*.log` instead of `manager-inbox.log`,
> the Worker will never see it (Workers read `manager-inbox.log`, not their own log).
> This causes the entire project to deadlock.

> **Workers must use the EXACT format above.**
> If a Worker uses markdown headers, JSON, or any other format,
> Manager may fail to parse the claim/delivery, causing delays or missed approvals.
