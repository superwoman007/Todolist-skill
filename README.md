# Todolist Skill

这是从 `TodolistForAgent` 仓库中抽取出来的独立 OpenClaw Skill 仓库。

当前包含：

- `skills/todolist-agent/SKILL.md` — Skill 定义与接口说明
- `skills/todolist-agent/README.md` — Skill 使用说明

## 仓库用途

这个仓库只负责 **Skill 部分**，用于让 OpenClaw Agent 拥有自己的待办清单能力。

它依赖一个单独运行的 TodoList API 后端。后端代码仍然来自原始项目（`TodolistForAgent`）中的 `apps/api` 部分。

## 目录结构

```text
.
├── README.md
└── skills
    └── todolist-agent
        ├── README.md
        └── SKILL.md
```

## 安装

将 `skills/todolist-agent` 复制到你的 OpenClaw skills 目录：

```bash
cp -r skills/todolist-agent ~/.openclaw/skills/
```

或者复制到某个 workspace 的 `skills/` 目录中。

## 后端依赖

Skill 需要以下环境变量：

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

## 来源

抽取自：`git@github.com:superwoman007/TodolistForAgent.git`
