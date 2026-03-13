---
name: todolist-agent
description: Manage an agent-owned todo list through a TodoList API, including creating tasks, listing tasks, completing tasks, failing tasks, checking due work, and managing subtasks. Use when the agent needs persistent task tracking, recurring reminders, or multi-step execution planning backed by an external API.
user-invocable: true
metadata: { "openclaw": { "emoji": "✅", "requires": { "env": ["TODOLIST_API_URL", "TODOLIST_API_KEY"] } } }
---

# Agent TodoList

Use this skill to manage the agent's own todo list through the TodoList API.

## Required environment

Enable the skill with these environment variables:

- `TODOLIST_API_URL`
- `TODOLIST_API_KEY`

If you need endpoint details or request bodies, read `references/api.md`.

## Core behavior

Use this skill when the agent needs a persistent task system instead of ephemeral chat memory.

Typical uses:
- store reminders for future execution
- manage recurring tasks
- track multi-step work with subtasks
- check and execute due work
- mark work done or failed with a result

## Recommended workflow

### First-time setup

On first use of this skill, create an OpenClaw cron job that runs every hour.

The cron job should send an internal prompt to the agent telling it to use `todolist-agent` to check due todos and process them.

Important:
- Use OpenClaw's built-in cron scheduler, not the operating system cron
- This hourly cron job is required if the user expects automatic background checking of due tasks
- Without this cron job, the skill provides todo-management capability only and will not run on a schedule by itself

### Scheduled execution behavior

When invoked by an internal scheduled prompt or cron-triggered agent message:

1. Call the due-check endpoint first
2. If no tasks are due, finish quietly without verbose output
3. If tasks are due, process them in priority order
4. If a task has subtasks, complete unfinished subtasks before marking the parent task done
5. Record a useful `result` when marking tasks done or failed
6. Do not send user-facing "nothing happened" messages unless the invoking prompt explicitly asks for a status update

### Add a simple reminder

1. Create a todo
2. Set `due_at` and `repeat_rule` if needed
3. Confirm to the user that the reminder was recorded

### Handle a complex task

1. Create the parent todo
2. Add subtasks for each execution step
3. Track progress through subtask completion
4. Mark the parent todo done when all required work is complete

### Periodically check due work

1. Call the due-check endpoint
2. For each due todo, inspect subtasks if present
3. Execute undone subtasks first
4. Mark subtasks done as they complete
5. Mark the parent todo done or failed with a useful `result`

## Best practices

- Break large tasks into subtasks
- Record a useful `result` when marking done or failed
- Prefer explicit priorities for urgent work
- Use recurring rules for reminders and repeated routines
- Do not silently ignore failures; mark them failed with context
