# PM Agent Bundle

面向产品经理的 OpenCode 智能体工具包。安装后，在 OpenCode 中用 `@pm` 即可启动产品全流程助手。

## 功能

| Agent | 用途 |
|-------|------|
| `pm` | 全流程助手：需求承接 → 分析 → 设计 → 边界推敲 → 开发交接 → 测试用例 |
| `pm-review` | 代码审查：安全、性能、边界、业务逻辑 |
| `pm-sync` | 钉钉表格解析：粘贴表格 → 结构化需求列表 |

### 核心特性

- **全流程覆盖**：从需求承接到测试用例，一个 Agent 搞定
- **持久记忆**：越用越懂你，记住你的偏好、项目信息、需求历史
- **子Agent调度**：自动判断何时调用专业子Agent（代码审查、表格解析）
- **PRD模板**：输出格式统一，可直接用于评审
- **Skill集成**：自动发现并使用已安装的 Skills
- **业务语言**：输出给 PM 看的，不用代码术语

## 安装

### 前置条件

- [OpenCode](https://opencode.ai) 已安装并配置好大模型
- 项目目录（任意项目根目录下操作）

### 步骤一：安装 Agent 文件

```bash
# 全局安装（所有项目都能用）
mkdir -p ~/.config/opencode/agents
cp agents/*.md ~/.config/opencode/agents/
```

### 步骤二：初始化记忆文件

在你的项目根目录下创建记忆目录：

```bash
cd /你的项目目录
mkdir -p pm-memory/templates
cp ~/pm-agent-bundle/pm-memory/*.md pm-memory/
cp ~/pm-agent-bundle/pm-memory/templates/*.md pm-memory/templates/
```

安装后项目结构：

```
your-project/
├── src/
├── ...
└── pm-memory/
    ├── user.md               用户画像
    ├── projects.md           项目信息
    ├── conventions.md        工作规范
    ├── history.md            需求历史
    └── templates/
        ├── prd.md            PRD模板
        ├── requirement.md    需求卡片模板
        └── test-case.md      测试用例模板
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
AI: [按需求卡片模板输出，带澄清问题]
你: [回答问题]
AI: [完整需求卡片] 需要继续分析吗？
你: 继续
AI: [按PRD模板输出分析报告]
你: 继续
AI: [按PRD模板输出设计方案]
你: 帮我写测试用例
AI: [按测试用例模板输出]
你: 帮我整理给开发
AI: [输出开发任务清单]
```

### 代码审查

```
你: @pm 帮我审查这段代码
[粘贴代码]
AI: 我让代码审查助手来看看...
    [自动调用 @pm-review 输出审查报告]
```

或者直接调用：

```
@pm-review 帮我看看这段代码
[粘贴代码]
```

### 钉钉表格解析

```
你: @pm 帮我看看钉钉表格里有什么需求
[从钉钉复制粘贴表格数据]
AI: 我让表格解析助手来看看...
    [自动调用 @pm-sync 输出结构化需求列表]
```

或者直接调用：

```
@pm-sync 帮我解析这个表格
[粘贴表格数据]
```

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
| `pm-memory/templates/*.md` | PRD、需求卡片、测试用例模板 |

你可以直接打开这些文件看 Agent 记住了什么。

### 多项目隔离

每个项目有自己的 `pm-memory/` 目录，记忆互不干扰。

## 目录结构

```
pm-agent-bundle/
├── README.md
├── agents/
│   ├── pm.md              主Agent（全流程 + 记忆 + 子Agent调度）
│   ├── pm-review.md       子Agent（代码审查）
│   └── pm-sync.md         子Agent（钉钉表格解析）
└── pm-memory/             记忆模板（复制到你的项目目录）
    ├── user.md
    ├── projects.md
    ├── conventions.md
    ├── history.md
    └── templates/
        ├── prd.md
        ├── requirement.md
        └── test-case.md
```

## 自定义

### 修改 Agent 行为

编辑 `agents/pm.md`，修改 prompt 部分。重启 OpenCode 生效。

### 修改模板

编辑 `pm-memory/templates/` 下的 `.md` 文件。Agent 输出时会按模板格式填充。

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
