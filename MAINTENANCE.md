# 维护指南（给 pz-skills 仓库的贡献者/AI 协作者看）

> 本文档仅供仓库维护者阅读。普通用户看 `README.md` 即可。

## 1. Skill 的两种类型

每个 skill 在 `SKILL.md` frontmatter 的 `metadata.pz_skills_type` 字段标注自己属于哪一类：

### `standalone`（独立型）

- **特征**：触发语义足够独特、不会和 Claude 的默认对话行为冲突。
- **代表**：`xuexiqu`——"我想学 X" 是非常明确的请求，Claude 默认不会自己开课，所以 skill description 路由就够稳。
- **判断标准**：触发短语在日常对话里**不容易自然出现**，或者出现时用户的意图就是明确想被 skill 流程接住。
- **维护要求**：写好 `description` 即可，不需要额外的 workspace 配置。

### `workspace_assisted`（需 workspace 加持型）

- **特征**：触发语义高度日常化，与 Claude 的默认行为冲突，仅靠 description 不能稳定命中。
- **代表**：
  - `kanjianzj`——"我又拖延了" / "我好焦虑" 是日常情绪表达，Claude 默认会自己共情或给建议；
  - `jinzita`——"帮我写个邮件" 是日常写作请求，Claude 默认会直接吐成稿。
- **判断标准**：你预期触发短语会和 Claude 的某种"自己就能搞定"的默认行为冲突时。
- **维护要求**：
  1. `SKILL.md` frontmatter 加 `metadata.pz_skills_type: workspace_assisted`。
  2. **必须同步更新 `templates/CLAUDE-router.md`**——按现有结构追加一段「触发 / → 调用 / 例外」。
  3. 在 `README.md` 已收录 skill 表格的"类型"列写「需 workspace」。
  4. commit message 里说明已同步更新模板。

## 2. 加新 skill 的检查清单

不论新 skill 是哪一类，按下面顺序操作：

- [ ] `skills/<name>/SKILL.md` —— 至少含 frontmatter 的 `name` / `description` / `metadata.pz_skills_type`。
- [ ] 必要时 `skills/<name>/references/theory.md`、`skills/<name>/assets/*.svg`。
- [ ] **`.claude-plugin/marketplace.json`** —— 在 `plugins[0].skills` 数组里登记 `./skills/<name>`，并更新 `plugins[0].description` 简介。
- [ ] **`README.md`** —— 在「已收录的 Skill」表格里加一行，含名称、心智模型、一句话定义、类型（独立 / 需 workspace）。
- [ ] **如果是 `workspace_assisted` 型**：同步更新 `templates/CLAUDE-router.md`，按现有「触发 / → 调用 / 例外」结构追加。
- [ ] commit message 中明确列出上面动了哪些文件。

## 3. 反模式（不要做的事）

### ❌ 不要写全局型 CLAUDE.md（指 `~/.claude/CLAUDE.md`）

理由：会污染用户所有项目和会话。强路由规则只能写到 workspace 级 CLAUDE.md（用户自己选目录复制），不能写到全局。

### ❌ 不要在 SKILL.md 里 cue 任何具体作者或书

理由：skill 是客观工具，不是读后感。所有理论锚定用学术原始来源（Lieberman 2007、MacLean 1990、Minto 1987 这种），不要写"胖总书里说"或"《半小时讲透 X》第 N 章"。书可以是灵感来源，但不能进 skill 产物。

### ❌ 不要为了让"自然对话 100% 触发"而过度堆砌 description 触发词

理由：description 字段过长 / 触发词过宽，会增加错误命中其他 skill 的概率。日常化的触发场景应该走 workspace CLAUDE.md 强路由路径，而不是把所有信号都堆进 description。

### ❌ 不要 commit Claude 的署名

理由：用户明确要求过。所有 commit 的 author / committer 必须是用户本人（`Stefan（胖总） <kaizhongfan@gmail.com>`），commit message 里**不要**加 `Co-Authored-By: Claude ...`。

## 4. 模板文件结构稳定性约定

`templates/CLAUDE-router.md` 是给用户复制的。一旦这个文件结构变了（小节命名、规则块格式），用户**不会自动同步**——他们之前复制走的版本还停在旧格式。所以：

- **新增 skill 规则**：用 append 方式，不破坏现有结构。
- **修改既有 skill 规则**：仅在确实必要时（比如某个触发词被证实导致严重误命中），改完在 README 中明确说明"建议老用户重新复制最新模板"。
- **整体重构**：避免。如果不得不重构，写 migration note。

## 5. 命名约定

- skill 目录：拼音全小写连写，无连字符。例：`xuexiqu` / `kanjianzj` / `jinzita`。
- frontmatter `name`：与目录名严格一致。
- assets 文件名：英文小写连字符。例：`emotion-map.svg` / `scqa-hook.svg`。
