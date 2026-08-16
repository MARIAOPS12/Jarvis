# Jarvis Architecture

## Overview

Jarvis is designed as a hierarchical multi-agent system on Alibaba Cloud. A central manager model plans objectives, creates tasks, assigns workers and decides when additional parallel workers are useful.

## Core components

### Manager

The manager is planned to use `qwen3.8-max`. It owns global planning and coordination rather than executing every subtask itself.

Responsibilities:

- break user objectives into executable tasks;
- create dependencies between tasks;
- select the appropriate worker model;
- inspect the shared Kanban state;
- launch additional workers when independent work is waiting;
- review worker output and request retries or escalation;
- stop idle workers and enforce budget/concurrency limits.

### Workers

Two main worker classes are planned:

- `qwen3.6-flash` for fast, inexpensive and parallelizable subtasks;
- `qwen3.7-plus` for harder reasoning, coding and tool-heavy work.

Each worker gets an isolated runtime with its own workspace, terminal, browser and optional graphical desktop session.

## Kanban coordination model

The shared task board is the coordination backbone. Agents do not need to message each other directly for every update. Instead, each task card exposes current state to the whole system.

Suggested task schema:

```text
task_id
objective_id
parent_task_id
status
priority
assigned_agent_id
assigned_model
dependencies[]
blocked_reason
progress_summary
heartbeat_at
started_at
completed_at
artifact_refs[]
retry_count
estimated_cost
```

Typical lifecycle:

```text
backlog -> ready -> running -> review -> done
                     |
                     -> blocked
```

## Dynamic parallelism

Parallel workers are created only when there is useful independent work.

Example:

1. The manager creates tasks A, B and C.
2. C depends on A and B.
3. Worker 1 starts A.
4. B is ready and independent.
5. The scheduler has free capacity and the expected benefit outweighs the cost.
6. A second `qwen3.6-flash` worker is started for B.
7. Both workers publish progress to the Kanban board.
8. When A and B finish, C becomes ready.
9. The manager assigns C to `qwen3.7-plus` if it requires deeper reasoning.

## Isolation

A worker runtime should have:

- a unique workspace directory or volume;
- a dedicated browser profile;
- a dedicated desktop session when GUI tools are required;
- scoped temporary credentials;
- explicit CPU/memory/time limits;
- access only to the tools and files required for its task;
- an artifact upload path for OSS.

The manager coordinates workers through the orchestration layer rather than sharing one unrestricted desktop between all agents.

## Alibaba Cloud mapping

- **Model Studio**: Qwen model APIs
- **ECS**: orchestrator and worker containers
- **OSS**: generated images, files and agent artifacts
- **Managed database**: tasks, Kanban state, agent metadata and long-lived memory
- **Optional Redis-compatible layer**: queueing, locks, heartbeats and short-lived events

## Reliability and safety controls

The first implementation should include:

- maximum total workers;
- maximum workers per model class;
- task timeouts;
- lease-based task ownership;
- duplicate-work protection;
- retry limits;
- budget ceilings;
- secret isolation;
- full audit logs of model, tool and task actions.
