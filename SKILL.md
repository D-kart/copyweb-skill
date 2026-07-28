---
name: copyweb-skill
description: 把 Agent 变成一名"视觉风格 skill 工厂"——输入一个参考网站 URL，输出一个符合 agentskills.io / OPC-Studio 标准、可直接被 Agent 调用的"视觉风格 skill"包。这是一个元 skill（meta-skill）：它不生成网页，而是产出一个新的"视觉风格 skill"目录（三层架构 META/ASSETS/REFERENCES），并可选发布到 GitHub + 收入 OPC-Studio 矩阵。内置六阶段工作流：命名去重 → 双通道扒站采集 → 风格分析（防翻车 checklist）→ 风格提炼 → 三层封装 → 验证 → 发布。它是 skill-creator 在"web 视觉风格复刻"场景的特化版。涵盖：网站设计语言提炼（design language extraction、配色/字体/卡片/布局/签名元素分析）、扒站数据采集（web scraping for design、curl + grep 提取 CSS 变量/字体/圆角/阴影、浏览器渲染语义采集）、风格 skill 脚手架生成（visual style skill scaffolding、三层架构模板）、GitHub 发布流程（git init + release + 矩阵收录）。触发词：复刻这个网站、扒下来做成 skill、把这个网站风格封装成 skill、网站风格提炼、把参考站变成 skill、做一个视觉风格 skill、copy this site、clone this design、copyweb、web design reference、design language extraction、visual style skill、website style to skill、scaffold a style skill。
license: MIT
compatibility: 纯 Markdown/HTML/CSS 元技能，无运行时依赖；扒站阶段依赖 curl + grep（系统自带）与可选浏览器渲染（agent-browser / playwright / WebFetch）；发布阶段依赖 git + gh CLI；兼容 Claude Skills / WorkBuddy / OpenClaw / Hermes / SkillHub 五大平台。
metadata:
  chinese-name: 扒站.skill
  author: OPC-Studio
  version: 1.0.0
  category: meta-skill
  reference_site: none (meta-skill; produces skills that reference other sites)
  style_positioning: "Visual-style skill factory: reference URL in, agentskills-compliant style skill out"
  language_support: "ZH primary / EN both"
  target-platforms:
    - claude-skills
    - workbuddy
    - openclaw
    - hermes
    - skillhub
  mcp-server: none
  tags:
    - meta-skill
    - skill-factory
    - web-design
    - design-language
    - web-scraping
    - style-extraction
    - scaffolding
    - opc-studio
agent_created: true
---

# copyweb-skill · 扒站

一个 **"视觉风格 skill 工厂"** 元 skill：输入参考网站 URL，输出一个符合 agentskills.io / OPC-Studio 标准、可直接被 Agent 调用的 **视觉风格 skill 包**。

先例产物：[gazette-skill](https://github.com/D-kart/gazette-skill)（findmymoat.com，古典公报风）、[panda-skill](https://github.com/D-kart/panda-skill)（pandaaiquant.com，双色 SaaS 风）。本 skill 把这两次复刻的完整链路 + 踩过的坑，固化成可复用工作流。

## When to Use

同时满足以下两条时激活本 skill：

1. 用户给出一个**公开可访问的网站 URL**；
2. 用户意图是**把它的设计语言复刻成一个可复用 skill**（而不是一次性生成一个网页）。

触发语例：「我喜欢 xxx.com，复刻一下做成 skill」「把这个网站风格扒下来封装成 skill」「clone this design as a skill」。

**Do not activate**（路由到别处）：

- 用户只是「用某风格帮我做个页面」→ 这是**风格层 skill** 的职责（report→gazette；platform→panda），不触发 copyweb。
- 用户要做的是非 web 视觉类的通用 skill → 走 `skill-creator`。
- 用户要复刻的是品牌资产（Logo / 原创插画 / 专有字体）→ 拒绝，只复刻"设计语言"，见反模式。

## 与其他 skill 的关系

```
skill-creator（通用 skill 工厂）
   └─ copyweb-skill（web 视觉风格复刻工厂，本 skill）
          │ 产出
          ├─ gazette-skill（serif 报纸风，已存在）
          ├─ panda-skill（sans 双色 SaaS 风，已存在）
          └─ <未来更多风格 skill>
```

copyweb 产出的每个新 skill，都必须补进全局决策规则（report→gazette；platform→panda；<新场景>→<新 skill>），并在其 SKILL.md 顶部写清"何时用本 skill / 何时用 gazette / 何时用 panda"。

## 输入 / 输出契约

**输入**

| 参数 | 必填 | 说明 |
|---|---|---|
| `url` | 是 | 参考网站 URL（公开可访问） |
| `style_name` | 否 | 风格短词名（如 gazette / panda）。不填则由 copyweb 起名并过命名去重（阶段0） |
| `chinese_target` | 否 | 是否主要做中文站。默认 false。true 则强制产出 `references/localization-zh.md` |
| `platforms` | 否 | 目标平台，默认全平台（claude-skills / workbuddy / openclaw / hermes / skillhub） |
| `publish` | 否 | 是否推 GitHub + release + 收入矩阵。默认 false（仅本地产出），需用户显式确认 |

**输出**：一个完整 skill 目录（三层架构）。详见 `references/architecture-template.md`。

```
<style_name>-skill/
├── SKILL.md · CHANGELOG.md · README.md · LICENSE · .gitignore
├── assets/
│   ├── starter-template.html   # 含昼夜切换 hook 的起始模板
│   └── screenshot-*.png        # 参考站关键区块截图（或 SCREENSHOTS-TODO.md 占位）
└── references/
    ├── design-system.md · page-architecture.md · components.md
    └── localization-zh.md      # 仅 chinese_target=true
```

## 六阶段工作流

```
URL
 ├─ 阶段0 命名去重 ──────► style_name（短词、不撞车、不绑品牌）
 ├─ 阶段1 双通道采集 ────► 渲染语义 + 原始HTML/CSS + 截图
 ├─ 阶段2 风格分析 ──────► 配色/字体/卡片/布局/签名元素（最易翻车，必过 checklist）
 ├─ 阶段3 风格提炼 ──────► positioning + 签名元素 + 与已有 skill 区分
 ├─ 阶段4 三层封装 ──────► 完整 skill 目录
 ├─ 阶段5 验证 ──────────► quick_validate + 自检清单
 └─ 阶段6 发布（可选）───► GitHub + release + 矩阵
```

### 阶段 0 · 命名与去重（最先做，避免返工）

- 起**短词名**（一两个音节，如 gazette / panda），**不绑参考站品牌**（不用 findmymoat / pandaaiquant 当 skill 名）。
- **撞车检查**：在 GitHub / npm / Product Hunt / 常见 SaaS 名字空间搜索候选名，撞车即换。避免泛词（dark/clean/modern）、已知产品名、歧义词。（panda 例血泪：原名 obsidian 撞 Obsidian Notes，被迫改名。）
- 中文别名 `<中文名>.skill`（公报.skill / 熊猫.skill）。
- 写一句 **style positioning**（一句话定义风格调性）。

### 阶段 1 · 双通道采集（缺一不可）

> 只用 WebFetch 会丢 CSS/字体/圆角/阴影；只用 curl 看不到渲染语义。**必须双通道 + 截图。**

- **通道 A 渲染语义**：agent-browser / playwright / WebFetch，理解区块清单 + 每区块用途（喂 `page-architecture.md`）。
- **通道 B 原始资产**：`curl -sL <url>` 拿原始 HTML，`grep` 提取 `<style>` / 内联 `style=` / `:root{}` CSS 变量 / `@font-face` / `font-family` / `border-radius` / `box-shadow`，拿到精确数值（喂 `design-system.md`）。**这是唯一能拿到配色/字体数值的途径。**
- **通道 C 截图**：agent-browser / playwright 截 hero / 定价 / 列表。代理失败（`ERR_NO_SUPPORTED_PROXIES`）时放 `assets/SCREENSHOTS-TODO.md` 占位，不阻塞。

详见 `references/analysis-checklist.md` 采集段。

### 阶段 2 · 风格分析（最容易翻车）

> **铁律：变量定义 ≠ 实际使用。** panda 例把 `--bg-color:#000` 误判"暗黑系"，实际 `.si-page` 是 `background:#fff`——白底为主 + 黑色反色锚点。

必做三件事：

1. **读实际 class 的 computed style**，不只读 `:root` 变量定义。
2. **区分主色（>70% 面积）与反色锚点（<30% 面积）**，别把反色锚点当主调。
3. **给整体调性下定位结论**（白底为主 / 纯黑底 / 双色 / 近单色）。

字体分清 4 角色（正文-UI / 展示 / 数字-标签 / 装饰），装饰字体标注"仅 X 场景，禁用正文"，给 CJK fallback。卡片/布局分析边框/圆角/阴影/hover/最大宽度/纵向节奏。提炼 2-4 个**签名级视觉元素**（少了就不像）。

**进入封装前必须逐项过 `references/analysis-checklist.md`，任一项"不确定"都不能往下走。**

### 阶段 3 · 风格提炼

- 把结构化数据提炼成 6 条左右**人类可读设计哲学**（写入 README + SKILL.md）。
- 写一句 **style positioning**。
- **与已有 skill 区分**：新 skill 的 SKILL.md 顶部写清何时用本 skill / gazette / panda，并补入全局决策规则。
- **重叠检测**：若新风格与已有 skill 高度重叠（又一个双色 SaaS），**应建议用户直接用已有 skill**，不制造冗余（OPC-Studio 反对 skill 膨胀）。

### 阶段 4 · 三层封装

按 `references/architecture-template.md` 生成完整目录。关键原则：

- SKILL.md 只做**路由**（触发词 + 风格速览 + 工作流 + 自检 + 反模式），能力 SOP 拆到 references/ 省 token。
- `starter-template.html` 必含：完整 CSS 变量、字体栈 fallback、**昼夜主题切换 hook（右上角固定 + localStorage 持久化——用户硬性偏好）**、≥3 个代表区块 markup。
- `components.md` 所有 snippet 必须**可直接 copy-paste 运行**，含 fallback。

### 阶段 5 · 验证

- 跑 `quick_validate.py`（skill-creator 脚手架自带）：检查 frontmatter 必填字段、`agent_created: true`、文件结构完整。
- 过下方自检清单 + `references/analysis-checklist.md`。

### 阶段 6 · 发布（可选，需用户确认 publish=true）

详见 `references/publish-workflow.md`。要点：

1. 建仓 `D-kart/<style_name>-skill`（gh CLI 或用户手建）。
2. `git init` + commit + push main。
3. 设 repo description + topics。
4. 门面四件套 README / CHANGELOG / LICENSE / .gitignore。
5. 打 `v1.0.0` tag + Release。⚠️ **`gh release edit` 必须带 `-R OWNER/REPO`**，否则本地无 remote 时静默失败（panda 例踩过）。
6. **矩阵收录**：更新 OPC-Studio README 4 处（矩阵表 + Claude/WorkBuddy clone 段 + OpenClaw/Hermes clone 段 + SkillHub zip 段）。

## 交付前自检清单

**结构完整**
- [ ] 三层架构齐全（META + ASSETS + REFERENCES）
- [ ] 根目录四件套（README / CHANGELOG / LICENSE / .gitignore）
- [ ] `quick_validate.py` 通过；frontmatter 必填齐全，`agent_created: true`

**风格准确（防翻车）**
- [ ] 配色定位未被反色锚点误导（读了实际 class，不只 `:root`）
- [ ] 字体 4 角色分清，装饰字体标注禁用场景
- [ ] 2-4 个签名元素完整且都有 `components.md` 配方

**可用性**
- [ ] `starter-template.html` 含昼夜切换 hook（右上角固定 + localStorage）
- [ ] `components.md` 所有 snippet 可直接运行（含 fallback）
- [ ] 字体栈有 CJK fallback
- [ ] 与已有风格 skill 决策规则不冲突，SKILL.md 顶部写清路由

**发布（如 publish=true）**
- [ ] repo description + topics 已设
- [ ] v1.0.0 release notes 完整
- [ ] OPC-Studio 矩阵 4 处已更新

## Key Anti-Patterns（do not do）

- **只用 WebFetch 采集** —— 会丢 CSS/字体/圆角/阴影，必须双通道。
- **只读 `:root` 变量就下配色结论** —— 变量定义 ≠ 实际使用（panda 血泪）。
- **把反色锚点当主调** —— panda 例误判白底双色为"暗黑系"。
- **用参考站品牌名做 skill 名** —— 起短词名，品牌只在 `reference_site` 署名。
- **命名撞车已知产品** —— 必过撞车检查（obsidian 撞 Obsidian Notes）。
- **SKILL.md 塞满 SOP** —— 只做路由，SOP 拆到 references/。
- **装饰字体用在正文** —— 不可读。
- **省略昼夜切换 hook** —— 用户硬性偏好，所有生成 HTML 必须含。
- **与已有 skill 制造冗余** —— 高度重叠时应建议复用，不膨胀矩阵。
- **复刻品牌资产** —— Logo / 原创插画 / 专有字体不复刻，只复刻设计语言。

## File References

- `references/analysis-checklist.md` — 阶段1-2 双通道采集 + 风格分析防翻车 checklist（含 panda 误判案例详解）
- `references/architecture-template.md` — 阶段4 三层架构产物模板（各文件骨架 + 写作要求）
- `references/publish-workflow.md` — 阶段6 发布流程（建仓 / release / 矩阵 4 处 / gh -R 坑）
- `references/localization-zh.md` — 中文化重建 5 大坑（从 gazette 例提炼，chinese_target=true 时必读）
- `assets/starter-template.html` — 产物 starter 脚手架（含昼夜 hook，可作为新 skill 的 starter 基底）
- `assets/workflow-diagram.md` — 六阶段复刻流程图

## Naming Note

本 skill 名 `copyweb`（扒站.skill）取自其能力（复刻 web 视觉风格），非任何参考站品牌。它是元 skill，本身不绑定任何参考站——参考站由它产出的每个子 skill 各自在 `metadata.reference_site` 署名。
