# PM Agent Bundle

PM（产品经理）工作全流程智能体，基于 OpenCode（oh my opencode 插件）。

## 架构

```
pm（总指挥，primary agent）
├── 自动检测用户意图 → 加载对应 Skill
│   ├── pm-workflow        需求全流程（6阶段）
│   ├── pm-code-review     代码审查
│   └── pm-dingtalk-sync   钉钉表格解析
└── 持久记忆（pm-memory/）
```

Tab 切换时只显示 **pm** 一个选项，Skill 根据上下文自动加载。

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

## Skill 自动触发

| 你说的话 | 自动加载 |
|---------|---------|
| "我有个需求..." / "帮我写PRD" / "出个测试用例" | pm-workflow |
| "帮我看看这段代码" / "review一下" | pm-code-review |
| 粘贴钉钉表格数据 / "帮我梳理下待办" | pm-dingtalk-sync |

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

### 第二步：复制 Agent 文件

```bash
# Linux / macOS / WSL
mkdir -p ~/.config/opencode/agents
cp pm-agent-bundle/agents/*.md ~/.config/opencode/agents/

# Windows PowerShell
mkdir -Force $env:APPDATA\opencode\agents
Copy-Item pm-agent-bundle\agents\*.md $env:APPDATA\opencode\agents\
```

### 第三步：复制 Skill 文件

```bash
# Linux / macOS / WSL
mkdir -p ~/.hermes/skills/productivity/pm-workflow
mkdir -p ~/.hermes/skills/productivity/pm-code-review
mkdir -p ~/.hermes/skills/productivity/pm-dingtalk-sync
cp pm-agent-bundle/skills/pm-workflow.md ~/.hermes/skills/productivity/pm-workflow/SKILL.md
cp pm-agent-bundle/skills/pm-code-review.md ~/.hermes/skills/productivity/pm-code-review/SKILL.md
cp pm-agent-bundle/skills/pm-dingtalk-sync.md ~/.hermes/skills/productivity/pm-dingtalk-sync/SKILL.md

# Windows PowerShell
mkdir -Force $env:USERPROFILE\.hermes\skills\productivity\pm-workflow
mkdir -Force $env:USERPROFILE\.hermes\skills\productivity\pm-code-review
mkdir -Force $env:USERPROFILE\.hermes\skills\productivity\pm-dingtalk-sync
Copy-Item pm-agent-bundle\skills\pm-workflow.md $env:USERPROFILE\.hermes\skills\productivity\pm-workflow\SKILL.md
Copy-Item pm-agent-bundle\skills\pm-code-review.md $env:USERPROFILE\.hermes\skills\productivity\pm-code-review\SKILL.md
Copy-Item pm-agent-bundle\skills\pm-dingtalk-sync.md $env:USERPROFILE\.hermes\skills\productivity\pm-dingtalk-sync\SKILL.md
```

### 第四步：复制记忆模板到你的项目目录

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

### 第五步：重启 VS Code

关闭并重新打开 VS Code（或 Ctrl+Shift+P → Reload Window）。

## 目录结构

```
pm-agent-bundle/
├── README.md
├── agents/
│   └── pm.md                    # 主 agent（primary）- 人格 + 记忆 + Skill 调度
├── skills/
│   ├── pm-workflow.md           # 六阶段工作流 Skill
│   ├── pm-code-review.md        # 代码审查 Skill
│   └── pm-dingtalk-sync.md      # 钉钉表格解析 Skill
└── pm-memory/                   # 记忆模板（复制到你的项目目录使用）
    ├── user.md                  # 用户画像和偏好
    ├── projects.md              # 项目信息
    ├── conventions.md           # 团队规范
    ├── history.md               # 需求历史
    └── templates/
        ├── prd.md               # PRD 模板
        ├── requirement.md       # 需求卡片模板
        └── test-case.md         # 测试用例模板
```

## 使用示例

```
你：我有个需求，想做一个离职人员自动交接功能
pm：好的，我先确认一下核心点 [自动加载 pm-workflow]
    （输出需求卡片 + 1-2个问题）
...
pm：继续下一步？
你：继续
pm：（输出需求分析：业务价值、用户故事、影响范围等）
...
你：帮我看看这段代码
pm：（自动加载 pm-code-review）
    （输出审查报告）
...
你：（粘贴钉钉表格）
pm：（自动加载 pm-dingtalk-sync）
    （输出结构化需求列表 + 统计）
```

## 记忆系统

Agent 会自动维护 `pm-memory/` 下的文件：

- **启动时**读取记忆，了解你的偏好和历史
- **结束时**更新记忆，记住新需求和你的纠正

你也可以手动编辑这些 md 文件，比如在 projects.md 里填入你的项目模块和接口。

## 自定义

### 添加新的 Skill

在 `skills/` 下新建 md 文件：
```markdown
---
name: my-skill
description: 触发条件描述
---
你的 skill 内容
```

然后在 `agents/pm.md` 的 Skill 调度表中添加触发规则。

### 修改 Agent 行为

编辑 `agents/pm.md` 的 system prompt，修改沟通规则或 Skill 调度逻辑。

### 添加模板

在 `pm-memory/templates/` 下添加 md 文件，pm agent 会自动识别并使用。
