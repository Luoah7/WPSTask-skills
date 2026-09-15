# WPS 任务 Skills

[![立即体验](https://img.shields.io/badge/WPS任务-立即体验-blue?style=flat-square)](https://wpsisv-task.rishiqing.com/)
[![产品白皮书](https://img.shields.io/badge/WPS任务-产品白皮书-green?style=flat-square)](https://365.kdocs.cn/l/crbbKh7wFgwf)
[![使用指南](https://img.shields.io/badge/WPS任务-使用指南-orange?style=flat-square)](https://365.kdocs.cn/l/cmufsDDBR8Yf)

任务，是责任落地的地方。

本仓库把 WPS 任务的责任体系沉淀成可直接使用的 Agent Skill——让 WPS 生态中的 Agent WPS Comate，以及 Claude Code、Cursor、OpenClaw 等主流 AI Agent，帮你把一句话说成能推进的事，把交付物核对到能验收。

**不买 WPS 任务也能用。** 每个 Skill 在没有任何产品连接的环境下完整可用；连上之后，能力延伸到读写真实任务。

# 关于本仓库

责任体系的五个问题——目标、责任、进度、风险、结果——过去是产品里的表单字段，需要用户先理解框架、先填字段，才能体验到价值。

本仓库换了个载体：**用户照常说人话，Agent 产出结构。**

每个 Skill 独立在自己的文件夹中，包含一个 `SKILL.md` 定义文件，可单独安装、单独使用，互不依赖。

# Skill 一览

- [./skills](./skills): 全部 Skill

| Skill | 说明 |
|-------|------|
| [work-shaping](./skills/work-shaping) | 把会议纪要、聊天记录、语音转写或一句话整理成责任明确的任务清单；也能对已有任务清单做责任体检，查出缺负责人、缺完成标准、逾期与停滞 |
| [delivery-review](./skills/delivery-review) | 拿交付物逐条对照完成标准，给出可定位的证据与缺口，以及通过、退回补充或外部确认的建议 |

两个 Skill 是同一条链路的两端：`work-shaping` 定义"怎么算做完"，`delivery-review` 检验"是不是真做完了"。可以只装一个。

### 它们和别的"待办整理"工具有什么不同

**敢说"我不知道"。**

让模型把纪要变成待办，市面上做得都不差；让模型评价一份交付物，它总能说出点什么。这两件事的难处从来不是生成，是克制。

- `work-shaping` 推不出来的字段一律输出 `〔需确认〕`，不用"提交本任务的可核对结果"这类套话填满
- `delivery-review` 有「无法判断」这一级结论——完成标准写着"客户已确认"而交付物是张表格，那既不是达标也不是存疑，是需要外部事实
- 没有完成标准时，`delivery-review` 先反推候选抛回确认，不自己出题再自动判卷

一个会编内容的 Agent，比空表单更糟——它消耗信任，却不产出信息。

# 在 WPS Comate、Claude Code、Cursor 中使用

## WPS Comate

WPS Comate 是 WPS 生态中的 Agent 入口，也是本仓库优先对接的宿主。两个 Skill 可作为 Comate 公共 Skill 或团队能力使用，让任务定义与交付验收在 WPS 工作场景中直接发生。

当前仓库先提供完整的 Skill 能力与标准目录结构，WPS 任务数据连接、账号授权和生产写入能力仍按 Comate 接入协议推进。

## 直接安装

每个 `skills/<skill-name>/` 目录都是一个独立 Skill 包。把需要的目录放进 Agent 的 Skills 目录即可：

```bash
# Claude Code（项目级）
cp -r skills/work-shaping .claude/skills/
cp -r skills/delivery-review .claude/skills/

# Claude Code（用户级）
cp -r skills/work-shaping ~/.claude/skills/
```

具体安装位置以宿主产品规则为准。

## 触发方式

装好后正常说话即可，不需要记命令：

```
帮我把这段会议纪要整理成任务
看看这些任务有什么问题
这个能算做完了吗，该通过还是退回
```

也可以显式调用：

```
使用 $work-shaping 整理这份纪要
使用 $delivery-review 对照完成标准核验这份清单
```

# 与 WPS 任务连接（可选）

**连接是增量，不是前提。** 没有连接时，Skill 全程不提及产品，输出纯文本。

宿主环境提供 WPS 任务工具并经用户确认后：

| Skill | 连接后新增 |
|-------|-----------|
| work-shaping | 成型结果可写入任务；体检可直接读取任务列表 |
| delivery-review | 读取任务的完成标准与交付物；验收结论回写为过程记录；退回意见填入退回原因 |

字段映射：

| Skill 字段 | WPS 任务 API |
|---|---|
| 目标 | `title` + `description` |
| 主责人 | `assignee` |
| 交付物 | `expectedDeliverable` |
| 完成标准 | `completionCriteria` |
| 截止时间 | `due.dueOn` |
| 范围边界 | `workScope` |

**`〔需确认〕` 的字段一律不写入，留空。** 不要为了让任务"看起来完整"而填占位内容——那会把 Skill 的诚实输出变成产品里的假数据。

当前仓库提供方法规则、触发描述、示例与输出模板，不包含 MCP 实现、账号授权或生产数据写入。

# 目录结构

```
skills/<skill-name>/
├── SKILL.md              # 定义文件：触发描述 + 核心规则
├── references/           # 详细规则与正反例，按需加载
├── assets/               # 输出模板
└── agents/openai.yaml    # 宿主展示信息与默认提示词
```

# 创建 Skill

使用下面的模板快速开始：

```markdown
---
name: my-skill-name
description: 一句话描述这个 Skill 做什么，以及何时触发它
---

# Skill 名称

[在此编写 Agent 执行时遵循的指令]

## 示例
- 使用场景 1
- 使用场景 2

## 规则
- 规则 1
- 规则 2
```

frontmatter 必需字段：

- `name` — Skill 唯一标识（小写，用连字符分隔）
- `description` — 完整描述功能与触发时机（这是 Agent 判断何时使用的**唯一依据**，务必写清触发边界）

写 description 时用用户真实会说的话，不要用内部术语。"帮我把会议纪要整理成任务"能触发，"执行工作成型流程"不能。

# 设计约定

向本仓库提交 Skill 时，请遵循以下约定——它们是这两个 Skill 得以成立的前提：

1. **可独立运行**：不依赖 WPS 任务或其他产品即可完整使用
2. **宁可留空**：推断不出来就显式标记缺口，不生成没有信息增量的内容
3. **输出可直接用**：产出应能直接粘贴进飞书 / Jira / Notion，不使用工具专属语法
4. **不做人设**：Skill 的内容是规则、字段定义、正反例和输出模板，不是"你是一位资深专家"
5. **连接是增量**：环境里没有对应工具时，不提示用户"如果你有 XX 就能……"

# 社区与贡献

一套好的协作方法，只在一个团队里用太可惜了。

### 贡献步骤

1. **确认不重叠**：检查触发描述是否与已有 Skill 冲突
2. **在 SKILL.md 中注明作者**：
   ```yaml
   ---
   name: your-skill-name
   description: 一句话描述这个 Skill 做什么，以及何时触发它
   author: 你的名字 <your-github-username>
   version: 1.0.0
   ---
   ```
3. **附测试用例**：至少给出 3 个输入样例与期望输出，其中必须包含一个"应该拒绝或留空"的用例
4. **提交 PR**：在 `skills/` 目录下新建子文件夹，确保包含完整的 `SKILL.md`

有想法但没时间写？直接开一个 Issue 抛出脑洞。

## 贡献者

每一个 Skill，都是有人把自己摸索出来的工作方式，提炼成了别人也能用的方法。

<!-- 如果你贡献了 Skill，欢迎通过 PR 把自己加到这里 -->
