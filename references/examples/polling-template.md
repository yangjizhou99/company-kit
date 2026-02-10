# Manual Polling Template

Use when the user sends the polling phrase.

## Checkpoint
- Poll phrase received
- Checked at (ISO time):
- Files checked: tasks + project-timeline.log + employee logs (heartbeats)

## Actions
- Update Ready list if needed
- Check employee heartbeats & idle status (send WAKE/IDLE_WARNING)
- Scan for anomalies (redirect claims, fix stuck workers)
- Approve waiting claims if valid
- Otherwise, report status & health and wait
