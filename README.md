# pz-skills

一个 Skill 沉淀仓库，收集围绕「心智模型 → 最简对话式 AI Skill」这一思路开发的一系列技能。

每个 Skill 自包含在 `skills/<skill-name>/` 目录下，至少含一个 `SKILL.md`，可选 `references/`（按需深读的参考材料）、`assets/`（静态资源/可视化模板）、`scripts/`（辅助脚本）。组织形式参考 [anthropics/skills](https://github.com/anthropics/skills)。

## 安装（Claude Code）

把本仓库注册为 Plugin Marketplace，再装 `pz-skills` 这个 plugin（自动包含其中所有 skill）：

```
/plugin marketplace add zhongkai/pz-skills
/plugin install pz-skills@pz-skills
```

之后**新开一个 Claude Code 会话**让 skill 注册表生效。后续仓库新增 skill，跑 `/plugin marketplace update zhongkai/pz-skills` 和 `/plugin update pz-skills` 就能拉到。

也可以手动安装（不走 marketplace）：把 `skills/<name>` 软链到 `~/.claude/skills/<name>` 即可。

## 已收录的 Skill

| 名称 | 心智模型 | 一句话定义 | 类型 |
|---|---|---|---|
| [xuexiqu](./skills/xuexiqu) | 维果茨基「最近发展区 / 学习区」 | 对话式 AI 学习教练：定位你的学习区，小步教学，内化后螺旋上升。 | **独立** |
| [jinzita](./skills/jinzita) | 金字塔结构（结论先行）× MECE × SCQA | 对话式结构化表达教练：抓塔尖、MECE 分组、SCQA 设计开头、四问自检。让你写汇报/提案/小红书时被秒懂。 | **需 workspace** |
| [kanjianzj](./skills/kanjianzj) | 元认知 × 情绪命名（affect labeling）× 三重大脑 | 对话式情绪与拖延陪伴教练：先接住、再命名情绪、定位是哪个脑在开车，最后给一件最小可行的下一步。 | **需 workspace** |

## 两类 Skill 与对应的使用方式

### 「独立」型——开箱即用

`description` 里的触发短语足够独特，Claude 默认行为不会盖掉它，所以**装好后正常对话就能命中**。例如对 `xuexiqu`：

> 「我想学一下 LangChain」「带我入门金融基础」「教我基础统计」

不用任何额外配置，自然对话就会进入 skill 流程。

### 「需 workspace」型——日常化触发，需要工作目录加持

像 `kanjianzj`（情绪/拖延陪伴）和 `jinzita`（结构化表达）这两个 skill，触发场景是高度日常化的——

- "我又拖延了" / "我好焦虑" / "刷了一天手机什么都没干"
- "帮我给老板写个微信" / "这段话怎么改更清楚" / "帮我写个小红书开头"

这些话和 Claude 的默认对话行为是直接冲突的：默认它会自己共情/给建议，或者直接吐成稿。仅靠 SKILL.md 的 description 路由命中率不稳。

针对这类 skill，仓库提供一份 workspace 级 CLAUDE.md 模板：

→ **`templates/CLAUDE-router.md`**

#### 怎么用

1. 选一个你想稳定使用 pz-skills 的工作目录（比如 `~/Desktop/觉醒/` 或 `~/Documents/写作/`，专门用来和 Claude 聊情绪/写文案）。
2. 把 `templates/CLAUDE-router.md` 复制到那个目录，**重命名为 `CLAUDE.md`**：
   ```bash
   cp templates/CLAUDE-router.md ~/Desktop/觉醒/CLAUDE.md
   ```
3. 以后从这个目录启动 Claude Code（`cd ~/Desktop/觉醒/ && claude`），自然对话就能稳定命中"需 workspace"型 skill。
4. 在其他目录里启动 Claude Code 时，这份配置不生效——你的代码项目、日常对话完全不受影响。

#### 重要说明（请如实理解）

- **CLAUDE.md 是 workspace 级的，不是全局**：只在你复制到的那个目录里启动 Claude Code 时才生效。这是有意设计的——避免污染你其他所有项目。
- **不是 100% 触发，是显著提升命中率**：这是上下文注入式的强建议，能把命中率从 ~30% 提到 ~85%，但仍可能有概率跑掉。
- **永远的兜底是 `/pz-skills:<name>`**：任何场景下，输入 `/pz-skills:kanjianzj` 或 `/pz-skills:jinzita` 都能强制启动对应 skill。这是不依赖任何配置的最终保底入口。

#### 不想用 workspace 配置怎么办

完全可以——直接用 `/pz-skills:<name>` 显式调用就行。比如：

```
/pz-skills:kanjianzj
（然后说你的事）

/pz-skills:jinzita
（然后说你要写什么）
```

显式调用本身也有它的好处：是一种"刻意"，刻意才能进入觉醒模式 / 结构化模式，这跟两个 skill 的精神是对得上的。

## 目录结构

```
pz-skills/
├── README.md
├── MAINTENANCE.md             # 维护指南（给贡献者/AI 协作者看）
├── .claude-plugin/
│   └── marketplace.json       # Claude Code Plugin Marketplace 元数据
├── template/
│   └── SKILL.md               # 新 Skill 的起始模板
├── templates/
│   └── CLAUDE-router.md       # workspace 级 CLAUDE.md 模板（"需 workspace"型 skill 用）
└── skills/
    ├── xuexiqu/               # 学习区 —— 维果茨基 ZPD（独立）
    ├── jinzita/               # 金字塔 —— Pyramid + MECE + SCQA（需 workspace）
    └── kanjianzj/             # 看见自己 —— 元认知 + affect labeling + 三重大脑（需 workspace）
```

每个 skill 目录下结构：

```
skills/<name>/
├── SKILL.md                   # 主体（含 frontmatter 的 name/description/metadata）
├── references/
│   └── theory.md              # 理论详解（按需深读）
└── assets/
    └── *.svg                  # 可视化模板（占位符，运行时填充）
```

## 新增 Skill 的步骤

1. 复制 `template/SKILL.md` 到 `skills/<your-skill>/SKILL.md`。
2. 编辑 frontmatter 的 `name`、`description`、`metadata.pz_skills_type`（`standalone` 或 `workspace_assisted`）。
3. 主体用第二人称指令体写给执行它的 Claude。
4. 复杂内容（理论详解、长篇参考）放 `references/`，由 SKILL.md 按需引用，保持 SKILL.md 精简。
5. 静态可视化模板（SVG/HTML）放 `assets/`，留参数化占位，由运行时填充。
6. **如果是 `workspace_assisted` 型**，必须同步更新 `templates/CLAUDE-router.md`，按现有结构追加触发规则。
7. 在 `.claude-plugin/marketplace.json` 和 `README.md` 已收录表格中登记。

详细维护规范见 [MAINTENANCE.md](./MAINTENANCE.md)。

## 设计原则

- **最简实现**：能在一个 SKILL.md 里说清的，就不拆文件。
- **关键节点才可视化**：可视化用来帮用户「看清自己在哪」，不要每轮都弹。
- **Progressive disclosure**：SKILL.md 只放运行时必须看的；理论、长例子放 `references/`，需要时再读。
- **不污染全局**：workspace 配置只在用户主动选择的目录下生效，不写入 `~/.claude/CLAUDE.md`。
