# todolist-agent skill

一个独立的 OpenClaw skill 仓库，用于给 Agent 提供待办事项管理能力。

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

## 设计原则

这个仓库按 skill 的常见组织方式拆分：

- `README.md`：仓库级说明，面向人
- `skills/todolist-agent/SKILL.md`：skill 主说明，面向 agent
- `skills/todolist-agent/references/api.md`：详细 API 参考，按需读取

这样可以让 `SKILL.md` 保持精简，把重细节放进 `references/`。

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


