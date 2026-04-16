# PM Agent Bundle

PM（产品经理）工作全流程智能体，基于 OpenCode（oh my opencode 插件）。

## 架构

```
pm（总指挥，primary agent）
├── 自己处理：需求承接 → 分析 → 设计 → 边界 → 交接 → 测试
├── 需要审查代码时 → 自动调度 @pm-review
└── 收到钉钉表格时 → 自动调度 @pm-sync
```

| Agent | 角色 | 模式 |
|-------|------|------|
| pm | PM 全流程助手，统管六阶段工作流 | primary（主入口） |
| pm-review | 代码审查专家，找业务逻辑/边界/安全问题 | subagent（被调度） |
| pm-sync | 钉钉表格解析，结构化为需求列表 | subagent（被调度） |

## 六阶段工作流

| 阶段 | 输出物 |
|------|--------|
| 1. 需求承接 | 需求卡片（名称/来源/优先级/待确认点） |
| 2. 需求分析 | 业务价值、用户故事、影响范围、风险点、MVP 定义 |
| 3. 需求设计 | PRD（流程、字段规格、异常处理、验收标准） |
| 4. 边界推敲 | 输入/业务/并发/数据边界 + 防御方案 |
| 5. 开发交接 | 前后端任务清单 + 接口建议（业务语言） |
| 6. 测试用例 | 功能/边界/异常测试用例 + 回归清单 |

每步完成后会问"继续下一步？"，你可以随时跳到任意阶段。

## 沟通规则

- 优先表格，不要大段文字
- 选项不超过 4 个
- 不给 PM 看代码/SQL/接口定义
- 输出分两层：PM 看业务语言，技术细节给开发
- 默认全量上线，不提灰度

## 安装配置

### 前置要求

- VS Code + [OpenCode 插件](https://marketplace.visualstudio.com/items?itemName=opencode.opencode)（oh my opencode）

### 第一步：下载

方式一：直接下载 zip
- 点击 GitHub 页面右上角 Code → Download ZIP
- 解压到任意目录

方式二：git clone
```bash
git clone https://github.com/Yookkku/pm-agent-bundle.git
```

### 第二步：复制 Agent 文件到全局目录

```bash
# Linux / macOS / WSL
mkdir -p ~/.config/opencode/agents
cp pm-agent-bundle/agents/*.md ~/.config/opencode/agents/

# Windows PowerShell
mkdir -Force $env:APPDATA\opencode\agents
Copy-Item pm-agent-bundle\agents\*.md $env:APPDATA\opencode\agents\
```

### 第三步：复制记忆模板到你的项目目录

在你日常工作的项目根目录下：

```bash
# Linux / macOS / WSL
mkdir -p pm-memory/templates
cp pm-agent-bundle/pm-memory/*.md pm-memory/
cp pm-agent-bundle/pm-memory/templates/*.md pm-memory/templates/

# Windows PowerShell
mkdir -Force pm-memory\templates
Copy-Item pm-agent-bundle\pm-memory\*.md pm-memory\
Copy-Item pm-agent-bundle\pm-memory\templates\*.md pm-memory\templates\
```

### 第四步：重启 VS Code

关闭并重新打开 VS Code（或 Ctrl+Shift+P → Reload Window）。

### 第五步：切换到 pm Agent

在 OpenCode 面板中按 **Tab 键**切换 agent，选择 **pm**。

## 目录结构

```
pm-agent-bundle/
├── README.md
├── agents/
│   ├── pm.md            # 主 agent - PM 全流程 + 调度入口
│   ├── pm-review.md     # 子 agent - 代码审查
│   └── pm-sync.md       # 子 agent - 钉钉表格解析
└── pm-memory/           # 记忆模板（复制到你的项目目录使用）
    ├── user.md          # 用户画像和偏好
    ├── projects.md      # 项目信息
    ├── conventions.md   # 团队规范
    ├── history.md       # 需求历史
    └── templates/
        ├── prd.md       # PRD 模板
        ├── requirement.md  # 需求卡片模板
        └── test-case.md # 测试用例模板
```

## 使用示例

```
你：我有个需求，想做一个离职人员自动交接功能
pm：好的，我先确认一下核心点（输出需求卡片 + 1-2个问题）
...
pm：继续下一步？
你：继续
pm：（输出需求分析：业务价值、用户故事、影响范围等）
...
你：帮我看看这段代码
pm：我帮你请代码审查专家看看（自动调度 @pm-review）
...
你：（粘贴钉钉表格）
pm：我来帮你梳理一下（自动调度 @pm-sync，输出结构化表格）
```

## 记忆系统

Agent 会自动维护 `pm-memory/` 下的文件：

- **启动时**读取记忆，了解你的偏好和历史
- **结束时**更新记忆，记住新需求和你的纠正

你也可以手动编辑这些 md 文件，比如在 projects.md 里填入你的项目模块和接口。

## 自定义

### 修改 Agent 行为

编辑 `agents/pm.md` 的 system prompt，修改沟通规则或工作流。

### 添加子 Agent

在 `agents/` 下新建 md 文件：
```markdown
---
description: 你的 agent 描述
mode: subagent
---
你的 system prompt
```

然后在 `pm.md` 的权限配置和调度指南中添加对新子 agent 的引用。

### 添加模板

在 `pm-memory/templates/` 下添加 md 文件，pm agent 会自动识别并使用。
