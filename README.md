# todolist-agent skill

一个独立的 OpenClaw skill 仓库，用于给 agent 提供**持久化待办事项管理能力**，并与 OpenClaw 自带的 cron 调度配合，实现定时巡检和执行到期任务。

## 目标定位

这个仓库不是 TodoList 后端本体，而是 **OpenClaw 的 skill 层**。

它解决的是：

- agent 如何创建和管理自己的 todo
- agent 如何检查到期任务
- agent 如何处理 subtasks
- agent 在被 OpenClaw cron 唤醒后应当如何执行

它**不负责**：

- 实现 TodoList API 后端
- 存储数据库数据
- 创建操作系统级别的 cron

换句话说，这个仓库提供的是 **todo 能力层**，不是 **后端实现层**。

## 整体工作流

推荐的完整链路如下：

```text
User request
  ↓
Agent uses todolist-agent
  ↓
Todo saved in TodoList API backend
  ↓
OpenClaw cron wakes agent every hour
  ↓
Agent uses todolist-agent to check due todos
  ↓
Agent executes due work
  ↓
Agent marks todos/subtasks done or failed
```

也就是说：

- `todolist-agent` 负责“会什么、怎么做”
- OpenClaw cron 负责“什么时候做”
- TodoList API 后端负责“数据存储和接口响应”

## 仓库结构

```text
.
├── README.md
└── skills/
    └── todolist-agent/
        ├── SKILL.md
        └── references/
            └── api.md
```

### 文件职责

- `README.md`
  - 仓库级说明，面向人
  - 说明整体设计、职责边界、安装方式和依赖

- `skills/todolist-agent/SKILL.md`
  - skill 主文件，面向 agent
  - 定义 skill 用途、首次启用流程、cron 唤醒后的行为规则、推荐工作流

- `skills/todolist-agent/references/api.md`
  - 详细 API 参考文档
  - 放 endpoint、请求体示例、subtask 接口、错误处理等细节

这种拆分方式让 `SKILL.md` 保持精简，把 API 细节放到 `references/` 按需读取。

## 设计思路

### 1. Skill 层：`todolist-agent`

负责 agent 的 todo 业务能力：

- 创建 todo
- 查询 todo
- 检查到期任务
- 管理 subtasks
- 标记 done / failed
- 定义定时唤醒后的执行行为

### 2. 调度层：OpenClaw cron

负责每小时唤醒 agent：

- 使用 OpenClaw 内建 cron scheduler
- 每小时向 agent 发送一次内部 prompt
- 触发 due-task 检查

> 重要：这里使用的是 **OpenClaw 自带 cron**，不是操作系统 cron。

### 3. 后端层：TodoList API

负责：

- todo / subtask 数据存储
- due task 查询
- 状态更新
- 认证

## 首次启用要求

如果用户期望 agent 能**自动在后台定时检查待办事项**，首次启用这个 skill 时必须额外创建一个 **OpenClaw cron job**。

建议配置：

- 频率：每 1 小时一次
- 方式：向 agent 发送内部 prompt
- 目标：让 agent 使用 `todolist-agent` 检查并处理到期任务

如果**没有**这条 OpenClaw cron job：

- skill 仍然可以用于手动创建和管理 todo
- 但不会自动每小时执行检查

## 推荐的 cron prompt

可使用类似下面的内部 prompt：

```text
Use the `todolist-agent` skill to check for due todos and process them.

Workflow:
1. Check for due todos first.
2. If no todos are due, finish quietly with no user-facing message.
3. If todos are due, process them in priority order.
4. If a todo has subtasks, complete unfinished subtasks before marking the parent todo done.
5. Mark completed work as done with a concise `result`.
6. Mark failed work as failed with a concise error `result`.
7. Do not send user-facing status messages unless a task actually requires notifying the user.
```

## 安装

把 `skills/todolist-agent` 复制到 OpenClaw 的 skills 目录即可：

```bash
cp -r skills/todolist-agent ~/.openclaw/skills/
```

或者复制到某个 workspace 的 `skills/` 目录。

## 运行依赖

需要为该 skill 提供以下环境变量：

- `TODOLIST_API_URL`
- `TODOLIST_API_KEY`

示例配置：

```json
{
  "skills": {
    "entries": {
      "todolist-agent": {
        "enabled": true,
        "env": {
          "TODOLIST_API_URL": "http://localhost:8000",
          "TODOLIST_API_KEY": "ak_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
        }
      }
    }
  }
}
```

## 后端职责边界

本仓库只定义 OpenClaw skill，不包含：

- API 后端实现
- 数据库模型
- 服务启动脚本
- 凭证创建脚本

如果需要这些内容，应放在独立后端仓库中维护。
