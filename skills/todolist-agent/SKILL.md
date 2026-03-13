---
name: todolist-agent
description: Manage an agent-owned todo list through a TodoList API, including creating tasks, listing tasks, completing tasks, failing tasks, checking due work, and managing subtasks. Use when the agent needs persistent task tracking, recurring reminders, or multi-step execution planning backed by an external API.
---

# Agent TodoList

Use this skill to manage the agent's own todo list through the TodoList API.

## Required environment

Provide these environment variables when enabling the skill:

- `TODOLIST_API_URL`
- `TODOLIST_API_KEY`

Use this header for all authenticated requests:

```text
Authorization: Bearer ${TODOLIST_API_KEY}
Content-Type: application/json
```

Use `${TODOLIST_API_URL}` as the base URL.

## Todo operations

### Create a todo

**POST** `${TODOLIST_API_URL}/agent/todos`

```json
{
  "title": "Task title",
  "description": "Execution instructions",
  "due_at": "2026-03-07T09:00:00",
  "priority": "normal",
  "repeat_rule": "none"
}
```

Fields:
- `title`: required
- `description`: optional
- `due_at`: optional ISO 8601 datetime
- `priority`: optional; `low` | `normal` | `high` | `urgent`
- `repeat_rule`: optional; `none` | `daily` | `weekly` | `monthly`

### List todos

**GET** `${TODOLIST_API_URL}/agent/todos?status=pending&due_before=now&priority=high&limit=50`

Query parameters:
- `status`: `pending` | `done` | `failed` | `all`
- `due_before`: ISO datetime or `now`
- `priority`: `low` | `normal` | `high` | `urgent`
- `limit`: 1-200

### Check due todos

**GET** `${TODOLIST_API_URL}/agent/todos/check`

Use this to retrieve pending tasks where `due_at <= now`.

### Get todo details

**GET** `${TODOLIST_API_URL}/agent/todos/{id}`

### Mark a todo as done

**POST** `${TODOLIST_API_URL}/agent/todos/{id}/done`

```json
{
  "result": "Execution result"
}
```

Use `result` whenever possible. Completing a repeating task may create the next occurrence automatically.

### Mark a todo as failed

**POST** `${TODOLIST_API_URL}/agent/todos/{id}/fail`

```json
{
  "result": "Error message"
}
```

### Update a todo

**PUT** `${TODOLIST_API_URL}/agent/todos/{id}`

```json
{
  "title": "New title",
  "description": "New description",
  "due_at": "2026-03-08T10:00:00",
  "priority": "high",
  "repeat_rule": "weekly",
  "status": "pending"
}
```

### Delete a todo

**DELETE** `${TODOLIST_API_URL}/agent/todos/{id}`

## Subtask operations

### List subtasks

**GET** `${TODOLIST_API_URL}/agent/todos/{id}/subtasks`

### Create a subtask

**POST** `${TODOLIST_API_URL}/agent/todos/{id}/subtasks`

```json
{
  "title": "Subtask title",
  "description": "Instructions",
  "order": 0
}
```

### Update a subtask

**PUT** `${TODOLIST_API_URL}/agent/todos/{id}/subtasks/{subtask_id}`

```json
{
  "title": "New title",
  "description": "New instructions",
  "done": true,
  "order": 1
}
```

### Mark a subtask as done

**POST** `${TODOLIST_API_URL}/agent/todos/{id}/subtasks/{subtask_id}/done`

### Delete a subtask

**DELETE** `${TODOLIST_API_URL}/agent/todos/{id}/subtasks/{subtask_id}`

## Recommended workflow

### Add a simple reminder

1. Create a todo with `POST /agent/todos`
2. Set `due_at` and `repeat_rule` if needed
3. Confirm to the user that the reminder was recorded

### Handle a complex task

1. Create the parent todo
2. Add subtasks for each execution step
3. Track progress through subtask completion
4. Mark the parent todo done when all required work is complete

### Periodically check due work

1. Call `GET /agent/todos/check`
2. For each due todo, inspect subtasks if present
3. Execute undone subtasks first
4. Mark subtasks done as they complete
5. Mark the parent todo done or failed with a `result`

## Best practices

- Break large tasks into subtasks
- Record a useful `result` when marking done or failed
- Prefer explicit priorities for urgent work
- Use recurring rules for reminders and repeated routines
- Do not silently ignore failures; mark them failed with context

## Error handling

- `401 Unauthorized`: API key missing or invalid
- `404 Not Found`: todo or subtask does not exist or is not accessible
- `422 Validation Error`: request body is invalid
