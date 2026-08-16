# Agent Coordination Protocol

Jarvis agents coordinate through shared structured state so that parallel workers can understand what is happening without relying on long conversational handoffs.

## Agent roles

### Manager

Planned model: `qwen3.8-max`

The manager owns planning, delegation, scaling decisions and final review.

### General worker

Planned model: `qwen3.7-plus`

Used for complex tasks that benefit from stronger reasoning, coding or multimodal understanding.

### Fast worker

Planned model: `qwen3.6-flash`

Used for latency-sensitive and easily parallelized work.

## Required worker behavior

When a worker claims a task it should:

1. mark the card as `running`;
2. publish a short progress summary;
3. update a heartbeat while active;
4. record blockers immediately;
5. attach produced artifacts by reference;
6. move the task to `review` when work is complete.

The manager decides whether reviewed work is accepted, retried, escalated or split into new tasks.

## Example shared card

```json
{
  "task_id": "task-42",
  "title": "Summarize log anomalies",
  "status": "running",
  "assigned_agent_id": "flash-worker-2",
  "assigned_model": "qwen3.6-flash",
  "dependencies": [],
  "progress_summary": "Parsed 8/12 log segments; grouping repeated timeout patterns.",
  "blocked_reason": null,
  "artifact_refs": [],
  "retry_count": 0
}
```

## Scale-out signal

A manager may request another worker when all of these are true:

- at least one independent task is ready;
- current workers cannot start it immediately;
- concurrency and budget limits allow another worker;
- predicted latency reduction is meaningful;
- the new task has no unresolved dependency that would keep the worker idle.

This prevents unnecessary agent spawning while still allowing burst parallelism when it helps the user.

## Handoff rule

Workers should not pass entire conversation histories between each other. A handoff should contain only:

- objective;
- task definition;
- relevant dependencies;
- required files / artifact references;
- concise progress state;
- expected output format.

This keeps context small and reduces duplicated model work.
