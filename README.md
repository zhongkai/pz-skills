# pz-skills

一个 Skill 沉淀仓库，收集围绕「心智模型 → 最简对话式 AI Skill」这一思路开发的一系列技能。

每个 Skill 自包含在 `skills/<skill-name>/` 目录下，至少含一个 `SKILL.md`，可选 `references/`（按需深读的参考材料）、`assets/`（静态资源/可视化模板）、`scripts/`（辅助脚本）。组织形式参考 [anthropics/skills](https://github.com/anthropics/skills)。

## 安装（Claude Code）

把本仓库注册为 Plugin Marketplace，再装 `pz-skills` 这个 plugin（自动包含其中所有 skill）：

```
/plugin marketplace add zhongkai/pz-skills
/plugin install pz-skills@pz-skills
```

之后在任何 Claude Code 会话里直接说「我想学 xxx」即可命中 `xuexiqu`。后续仓库新增 skill，`/plugin update` 就能拉到。

也可以手动安装（不走 marketplace）：把 `skills/xuexiqu` 软链到 `~/.claude/skills/xuexiqu` 即可。

## 目录结构

```
pz-skills/
├── README.md
├── .claude-plugin/
│   └── marketplace.json    # Claude Code Plugin Marketplace 元数据
├── template/
│   └── SKILL.md            # 新 Skill 的起始模板
└── skills/
    └── xuexiqu/            # 学习区 —— 维果茨基 ZPD 理论驱动的 AI 学习教练
        ├── SKILL.md
        ├── references/
        │   └── theory.md
        └── assets/
            ├── zone-map.svg
            └── progress.svg
```

## 已收录的 Skill

| 名称 | 心智模型 | 一句话定义 |
|---|---|---|
| [xuexiqu](./skills/xuexiqu) | 维果茨基「最近发展区 / 学习区」 | 对话式 AI 学习教练：定位你的学习区，小步教学，内化后螺旋上升。 |
| [kanjianzj](./skills/kanjianzj) | 元认知 × 情绪命名（affect labeling）× 三重大脑 | 对话式情绪与拖延陪伴教练：先接住、再命名情绪、定位是哪个脑在开车，最后给一件最小可行的下一步。 |
| [jinzita](./skills/jinzita) | 金字塔结构（结论先行）× MECE × SCQA | 对话式结构化表达教练：抓塔尖、MECE 分组、SCQA 设计开头、四问自检。让你写汇报/提案/小红书时被秒懂。 |

## 新增 Skill 的步骤

1. 复制 `template/SKILL.md` 到 `skills/<your-skill>/SKILL.md`。
2. 编辑 frontmatter 的 `name` 和 `description`（`description` 决定运行时是否会被命中，需具体描述用途和触发场景）。
3. 主体用第二人称指令体写给执行它的 Claude。
4. 复杂内容（理论详解、长篇参考）放 `references/`，由 SKILL.md 按需引用，保持 SKILL.md 精简。
5. 静态可视化模板（SVG/HTML）放 `assets/`，留参数化占位，由运行时填充。

## 设计原则

- **最简实现**：能在一个 SKILL.md 里说清的，就不拆文件。
- **关键节点才可视化**：可视化用来帮用户「看清自己在哪」，不要每轮都弹。
- **Progressive disclosure**：SKILL.md 只放运行时必须看的；理论、长例子放 `references/`，需要时再读。
