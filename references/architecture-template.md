# 三层架构产物模板（阶段 4）

> copyweb 产出的每个新风格 skill 都长这样。本文件给出各文件的**骨架 + 写作要求**。
> 复制骨架 → 用阶段2/3 的分析结果填充 → 得到该风格专属的 skill 包。

## 目录结构

```
<style_name>-skill/
├── SKILL.md                    # META · 主路由
├── CHANGELOG.md                # 版本日志
├── README.md                   # GitHub 门面
├── LICENSE                     # MIT · © 2026 OPC-Studio
├── .gitignore                  # OS / editor / build / logs / env
├── assets/
│   ├── starter-template.html   # 可直接复用起始模板（含昼夜切换 hook）
│   └── screenshot-*.png        # 参考站关键区块截图（截不到则 SCREENSHOTS-TODO.md）
└── references/
    ├── design-system.md        # 完整设计系统规范
    ├── page-architecture.md    # 区块骨架 + 内容原则
    ├── components.md           # HTML/CSS 组件配方
    └── localization-zh.md      # 仅 chinese_target=true
```

> 文件数量参照 gazette（7 件）/ panda（含 SCREENSHOTS-TODO 的 8 件）。不强求件数，但 META + ASSETS + REFERENCES 三层必须齐全。

---

## META · SKILL.md

### frontmatter（OPC-Studio 标准，ASCII 符号，禁尖括号/全角冒号）

```yaml
---
name: <style_name>-skill
description: <一段话，塞满触发词。含中英触发词 + 风格定位 + 覆盖能力 + 触发词列表。agentskills 允许到 1024 字符，塞满它。>
license: MIT
compatibility: 纯 HTML/CSS 技能，无运行时依赖；可选 CDN/本地字体…；兼容 Claude Skills / WorkBuddy / OpenClaw / Hermes / SkillHub。
metadata:
  chinese-name: <中文名>.skill
  author: OPC-Studio
  version: 1.0.0
  category: web-design
  reference_site: <参考站 URL>
  style_positioning: "<一句话定位>"
  language_support: "ZH primary / EN primary / both"
  target-platforms:
    - claude-skills
    - workbuddy
    - openclaw
    - hermes
    - skillhub
  mcp-server: none
  tags: [<风格相关 tag>]
agent_created: true
---
```

### 正文段落（按 gazette/panda 既定结构）

1. **顶部一句话定位** + 与已有 skill 关系（report→gazette；platform→panda；`<新场景>`→本 skill）
2. **When to Use / Do not activate**
3. **Style Snapshot**（配色 / 字体 / 卡片 / 布局 / 签名元素速览，load before designing）
4. **Workflow**：
   1. 确认语言（中文站先读 localization-zh.md）
   2. load `design-system.md`
   3. load `components.md`（建真页面时）
   4. 用 `starter-template.html` 起步
   5. 看截图
   6. 自检
5. **Key Anti-Patterns（do not do）**
6. **File References**
7. **Adaptation Notes**
8. **Naming Note**（署名参考站，不绑品牌）

> **原则：SKILL.md 只做路由。** 能力 SOP 全部拆到 references/ 省 token。

---

## ASSETS · assets/

### starter-template.html

必须含：

- [ ] 完整 CSS 变量表（palette / fonts / spacing / radius / shadow）—— 填阶段2 精确值
- [ ] 字体栈 + fallback（**CJK fallback 必备**）
- [ ] **昼夜主题切换 hook**：右上角固定定位 + `localStorage` 持久化（用户硬性偏好，不可省）
- [ ] ≥3 个代表区块示例 markup（nav / hero / card grid 等）

> 可直接复用 copyweb 的 `assets/starter-template.html` 作为基底，把 `{{占位}}` 替换成本风格数值。

### screenshot-*.png

- 参考站关键区块截图（hero / 列表 / 定价）
- 截不到 → 放 `SCREENSHOTS-TODO.md` 占位（列出应补哪些 + 参考站 URL），不阻塞

---

## REFERENCES · references/

### design-system.md（完整设计系统）

- **配色**：精确十六进制 + 用途（主色七件 + 反色锚点 + 强调色）。明确标注主色 vs 反色锚点。
- **字体**：4 角色（正文-UI / 展示 / 数字-标签 / 装饰）+ 权重 + fallback。装饰字体标使用场景限定。
- **卡片**：边框 / 圆角 / 阴影 / hover 行为
- **按钮系统** + **标签系统**
- **布局栅格**：最大宽度 / 纵向节奏 / 列数
- **签名元素**：逐个规格

### page-architecture.md（区块骨架）

- N 个区块按序列出
- 每区块：目的 + 内容设计原则 + 类名约定（可沿用参考站命名空间便于溯源）

### components.md（组件配方）

- 每个组件一段可**直接 copy-paste 运行**的 HTML/CSS（含 fallback）
- 必须覆盖全部签名元素
- 建议每个 snippet 自带最小样式，不依赖外部 CSS

### localization-zh.md（仅 chinese_target=true）

- 从 copyweb 的 `references/localization-zh.md` 提炼本风格专属中文化规则
- 至少覆盖：展示字体 CJK 替换、uppercase/italic 失效替代、drop cap 缩放、红涨绿跌、CJK 行高/字号

---

## 门面文件（根目录）

- **README.md**：镜像 gazette README 结构 —— 这是什么 / 能做什么(表格) / 何时触发 / 核心输出风格 / 快速上手(四平台) / 目录结构 / 设计哲学(6条) / 兼容性(表格) / 与其他 skill 协同 / 中文化说明 / 许可证 / 反馈
- **CHANGELOG.md**：Keep a Changelog 格式，`[1.0.0] - YYYY-MM-DD`
- **LICENSE**：MIT · © 2026 OPC-Studio
- **.gitignore**：OS / editor / build / logs / env（可直接复用 copyweb 的）
