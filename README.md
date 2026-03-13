# todolist-agent skill

一个独立的 OpenClaw skill 仓库，用于给 Agent 提供待办事项管理能力。

## 仓库结构

```text
.
├── README.md
└── skills/
    └── todolist-agent/
        └── SKILL.md
```

## 设计原则

这个仓库只保留 **skill 本体**，不混入额外的人类说明文件、脚本模板或后端实现代码。

- `README.md`：仓库级说明，面向人
- `skills/todolist-agent/SKILL.md`：skill 定义，面向 agent

TodoList 的后端服务不在本仓库内。这个 skill 依赖外部 API 服务与对应环境变量配置。

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

本仓库只定义 skill，不包含：

- API 后端实现
- 数据库模型
- 服务启动脚本
- 凭证创建脚本

如果需要这些内容，应放在独立后端仓库中维护。
