# PM Agent Bundle

面向产品经理的 OpenCode 智能体工具包。安装后，在 OpenCode 中用 `@pm` 即可启动产品全流程助手。

## 功能

| Agent | 用途 |
|-------|------|
| `pm` | 全流程助手：需求承接 → 分析 → 设计 → 边界推敲 → 开发交接 → 测试用例 |
| `pm-review` | 代码审查：安全、性能、边界、业务逻辑 |

### 核心特性

- **全流程覆盖**：从需求承接到测试用例，一个 Agent 搞定
- **持久记忆**：越用越懂你，记住你的偏好、项目信息、需求历史
- **业务语言**：输出给 PM 看的，不用代码术语
- **代码审查**：粘贴代码就能找边界问题和安全隐患

## 安装

### 前置条件

- [OpenCode](https://opencode.ai) 已安装并配置好大模型
- 项目目录（任意项目根目录下操作）

### 步骤一：安装 Agent 文件

```bash
# 全局安装（所有项目都能用）
mkdir -p ~/.config/opencode/agents
cp agents/pm.md ~/.config/opencode/agents/
cp agents/pm-review.md ~/.config/opencode/agents/
```

### 步骤二：初始化记忆文件

在你的项目根目录下创建记忆目录：

```bash
cd /你的项目目录
mkdir -p pm-memory
cp /path/to/pm-agent-bundle/pm-memory/*.md pm-memory/
```

安装后项目结构：

```
your-project/
├── src/
├── ...
└── pm-memory/          ← Agent 的记忆目录
    ├── user.md         ← 用户画像
    ├── projects.md     ← 项目信息
    ├── conventions.md  ← 工作规范
    └── history.md      ← 需求历史
```

### 步骤三：启动

1. 在项目目录下启动 OpenCode
2. 按 `Tab` 键切换到 `pm` Agent
3. 开始使用

## 使用方法

### 需求全流程

```
你: @pm 我有个新需求
你: [粘贴需求原文]
AI: [输出需求卡片，带澄清问题]
你: [回答问题]
AI: [输出完整需求卡片] 需要继续分析吗？
你: 继续
AI: [输出需求分析报告] 需要继续做设计吗？
你: 继续
AI: [输出PRD] 需要推敲边界吗？
你: 帮我整理给开发
AI: [输出开发任务清单]
你: 帮我写测试用例
AI: [输出测试用例]
```

### 代码审查

```
@pm-review 帮我看看这段代码
[粘贴代码]
```

审查维度：
- 业务逻辑是否符合需求
- 边界场景是否遗漏
- 安全问题（权限、注入、数据暴露）
- 性能隐患（N+1查询、大数据量）

### 直接跳到某一步

```
@pm 帮我分析这个需求
@pm 帮我推敲边界
@pm 帮我写测试用例
@pm 帮我整理给开发
```

## 记忆系统

Agent 会自动学习和记忆，不需要你手动管理。

### 记忆规则

| 会记住的 | 不会记住的 |
|----------|-----------|
| 新需求及其决策 | 闲聊、寒暄 |
| 用户的纠正和偏好 | "好的""没问题" |
| 项目模块/接口/数据表 | 没有结论的讨论 |
| 业务决策和原因 | 重复信息 |
| 踩过的坑 | |

**判断标准**：三个月后还有用就记，否则不记。

### 记忆文件

| 文件 | 内容 |
|------|------|
| `pm-memory/user.md` | 你是谁、喜欢什么沟通方式 |
| `pm-memory/projects.md` | 项目模块、接口、数据表 |
| `pm-memory/conventions.md` | 命名规范、审批流程、上线规范 |
| `pm-memory/history.md` | 所有做过的需求数记录 |

你可以直接打开这些文件看 Agent 记住了什么。

### 多项目隔离

每个项目有自己的 `pm-memory/` 目录，记忆互不干扰。

## 目录结构

```
pm-agent-bundle/
├── README.md
├── agents/
│   ├── pm.md              主Agent（全流程 + 记忆）
│   └── pm-review.md       子Agent（代码审查）
└── pm-memory/             记忆模板（复制到你的项目目录）
    ├── user.md
    ├── projects.md
    ├── conventions.md
    └── history.md
```

## 自定义

### 修改 Agent 行为

编辑 `agents/pm.md`，修改 prompt 部分。重启 OpenCode 生效。

### 添加新 Agent

在 `~/.config/opencode/agents/` 下新建 `.md` 文件：

```markdown
---
description: 你的Agent描述
mode: subagent
---

你的提示词...
```

文件名就是 Agent 名。比如 `pm-sprint.md` 创建一个 `pm-sprint` Agent。

## 适用场景

- 管理工作台、OA、CRM 等企业内部系统的产品经理
- 前后端分离架构（Vue/React + Java/Spring）
- 使用钉钉/飞书等工具管理需求
- 有 OpenCode + 大模型环境（内网可用）

## License

MIT
