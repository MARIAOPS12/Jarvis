# Alibaba Cloud Project Summary

This is the concise project description intended for the Alibaba Cloud credit application.

> I am building a multimodal personal AI assistant on Alibaba Cloud. Qwen3.8-Max will act as the central manager, planning tasks, delegating work and dynamically starting additional Qwen3.6-Flash or Qwen3.7-Plus agents when parallel execution can reduce waiting time. Each agent will receive its own isolated desktop and work environment. A shared Kanban board will let agents continuously report task status, dependencies and progress to each other. Qwen-Image-3.0-Pro and Qwen-Audio-3.0-Realtime-Plus will provide image and voice capabilities. The system will run on ECS with Docker, OSS storage and a managed database for persistent state and agent memory.

## Intended Alibaba Cloud services

- Model Studio for Qwen model access
- ECS for the orchestrator and isolated worker environments
- OSS for generated media and agent artifacts
- Managed database services for task state, Kanban data and persistent memory
- Optional Redis-compatible coordination layer for queues, locks and heartbeats

## Repository status

This repository currently contains the architecture and initial development scaffolding for the planned prototype. Implementation will be added incrementally as the Alibaba Cloud environment is provisioned.
