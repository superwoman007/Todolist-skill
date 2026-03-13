# TodoList API Reference

Base URL: `${TODOLIST_API_URL}`

Authentication header for all authenticated requests:

```text
Authorization: Bearer ${TODOLIST_API_KEY}
Content-Type: application/json
```

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

Returns pending tasks where `due_at <= now`.

### Get todo details

**GET** `${TODOLIST_API_URL}/agent/todos/{id}`

### Mark a todo as done

**POST** `${TODOLIST_API_URL}/agent/todos/{id}/done`

```json
{
  "result": "Execution result"
}
```

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

## Error handling

- `401 Unauthorized`: API key missing or invalid
- `404 Not Found`: todo or subtask does not exist or is not accessible
- `422 Validation Error`: request body is invalid
