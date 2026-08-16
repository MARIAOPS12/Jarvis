# Planned Services

This directory will contain the implementation services for Jarvis.

Planned layout:

```text
services/
├── orchestrator/      # API, task graph, model routing, scheduler
├── worker/            # isolated agent runtime
├── kanban-api/        # task-state and heartbeat endpoints
└── web/               # user interface and Kanban dashboard
```

The initial architecture is documented in [`../docs/architecture.md`](../docs/architecture.md).
