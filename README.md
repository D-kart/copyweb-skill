# 扒站.skill · copyweb-skill

> 一个"视觉风格 skill 工厂"——输入参考网站 URL，输出一个符合规范、可直接被 Agent 调用的"视觉风格 skill"包。

![made by](https://img.shields.io/badge/made%20by-OPC--Studio-1a1a4e) ![license](https://img.shields.io/badge/license-MIT-green) ![type](https://img.shields.io/badge/type-meta--skill-purple) ![compat](https://img.shields.io/badge/compat-Claude%20%7C%20WorkBuddy%20%7C%20OpenClaw%20%7C%20Hermes%20%7C%20SkillHub-orange)

---

## 这是什么

`copyweb-skill` 是一个符合 [agentskills.io](https://agentskills.io) 开放规范的 **元 skill（meta-skill）**。

它不生成网页——它 **产出一个新的"视觉风格 skill"**。你给它一个参考网站 URL，它扒站、分析配色/字体/布局、提炼设计语言，然后封装成一个完整的三层架构 skill 目录，并可选发布到 GitHub、收入 OPC-Studio 矩阵。

它是 [`skill-creator`](https://agentskills.io) 在 **"web 视觉风格复刻"** 场景的特化版：内置扒站双通道采集、配色/字体分析防翻车 checklist、三层架构模板、OPC-Studio 发布流程。

先例产物：

- 📰 [gazette-skill](https://github.com/D-kart/gazette-skill)（复刻 findmymoat.com，古典公报 + 金融大报风）
- 🐼 [panda-skill](https://github.com/D-kart/panda-skill)（复刻 pandaaiquant.com，双色 SaaS 白底 + 黑色反色风）

copyweb 把这两次复刻走过的**同一条链路**和**踩过的坑**，固化成一个可复用工作流。下次说"我喜欢 xxx.com，复刻一下"，Agent 调用 copyweb 就能稳定产出符合标准的新 skill 包。

---

## 它能做什么

| # | 能力 | 产出 |
|---|---|---|
| 1 | **命名去重** | 短词风格名 + 撞车检查 + 中文别名 + style positioning |
| 2 | **双通道扒站采集** | 渲染语义（区块清单）+ 原始 HTML/CSS（配色/字体/圆角/阴影精确值）+ 截图 |
| 3 | **风格分析（防翻车）** | 主色 vs 反色锚点、字体 4 角色、卡片/布局、2-4 个签名元素结构化数据 |
| 4 | **风格提炼** | 6 条设计哲学 + 一句 positioning + 与已有 skill 区分决策规则 |
| 5 | **三层封装** | 完整 skill 目录（SKILL.md + assets/ + references/），含昼夜 hook starter |
| 6 | **发布（可选）** | GitHub 建仓 + push + v1.0.0 release + OPC-Studio 矩阵 4 处收录 |

---

## 何时触发

> 复刻这个网站、扒下来做成 skill、把这个网站风格封装成 skill、网站风格提炼、把参考站变成 skill、做一个视觉风格 skill、copy this site、clone this design、copyweb、web design reference、design language extraction、visual style skill、website style to skill、scaffold a style skill……

**触发条件（AND）**：① 用户给出公开可访问的 URL；② 用户意图是复刻其设计语言为可复用 skill。

**不触发**：用户只是"用某风格做个页面"（→ 路由到 gazette / panda 风格层 skill）；非 web 视觉类通用 skill（→ skill-creator）。

---

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

---

## 快速上手

### 在 Claude / WorkBuddy 中使用

1. 下载 `copyweb-skill/` 文件夹，放入 `~/.claude/skills/` 或 `~/.workbuddy/skills/`。
2. 重启客户端，向 Agent 说：**"我喜欢 stripe.com 的风格，帮我扒下来做成 skill"**，技能自动触发。

### 在 OpenClaw / Hermes 中使用

1. 将 `copyweb-skill/` 放入 `skills/` 目录。
2. 通过 `skill_view` 查看 SKILL.md 加载。

### 在 SkillHub 中使用

1. 打包：`zip -r copyweb-skill.zip copyweb-skill/`
2. 登录 SkillHub，点击"上传 Skill"，选择 zip 文件。

---

## 目录结构（三层架构）

```
copyweb-skill/
├── SKILL.md                          # 主路由（六阶段工作流 + 自检 + 反模式）
├── CHANGELOG.md                      # 版本日志
├── README.md                         # GitHub 门面
├── LICENSE                           # MIT
├── .gitignore
├── assets/                           # 🅰️ ASSETS 资产真源
│   ├── starter-template.html         # 产物 starter 脚手架（含昼夜切换 hook）
│   └── workflow-diagram.md           # 六阶段复刻流程图
└── references/                       # 📚 REFERENCES 具体规范
    ├── analysis-checklist.md         # 双通道采集 + 风格分析防翻车 checklist（含 panda 误判案例）
    ├── architecture-template.md      # 三层架构产物模板（各文件骨架）
    ├── publish-workflow.md           # 发布流程（建仓 / release / 矩阵 4 处 / gh -R 坑）
    └── localization-zh.md            # 中文化重建 5 大坑
```

> 三层架构：**META**（SKILL.md frontmatter）· **ASSETS**（assets/ 资产真源）· **REFERENCES**（references/ 具体规范）。SKILL.md 只做路由，能力 SOP 拆到 references/ 省 token。

---

## 六条设计哲学

1. **双通道扒站，缺一不可**：WebFetch 转 markdown 会剥掉所有 `<style>` 和内联样式——配色/字体/圆角/阴影全丢。必须并用 curl + grep 拿原始数值。
2. **变量定义 ≠ 实际使用**：绝不只读 `:root` 变量就下配色结论。必须读实际 class 的 computed style。panda 例的"暗黑系"误判正是死在这里。
3. **区分主色与反色锚点**：主色占页面 >70% 面积决定调性，反色锚点占 <30% 是节奏锚点。别把锚点当主调。
4. **短词命名，不绑品牌**：skill 名是可迁移的设计语言标识（gazette / panda），不是参考站品牌（findmymoat / pandaaiquant）。必过撞车检查。
5. **只复刻设计语言，不复刻品牌资产**：配色规律、字体栈类别、布局骨架、组件模式可复刻；Logo、原创插画、专有字体文件不复刻。参考站只在 `reference_site` 署名。
6. **反对矩阵膨胀**：新风格与已有 skill 高度重叠时，应建议用户直接复用，而不是制造又一个几乎一样的 skill。

---

## 兼容性

| 平台 | 状态 | 备注 |
|---|---|---|
| Claude Skills | ✅ | 遵循 agentskills.io 规范，frontmatter 覆盖触发词 |
| WorkBuddy | ✅ | 原生支持，含 `agent_created: true` 标记 |
| OpenClaw | ✅ | 通过 clawhub 兼容 |
| Hermes Agent | ✅ | 通过 agentskills.io 标准 |
| SkillHub | ✅ | zip 上传 |

---

## 与 OPC-Studio 其他 skill 协同

`copyweb-skill` 是 **元 skill（工厂层）**，位于风格层 skill 之上：

- 它 **产出** 风格层 skill（gazette / panda / 未来更多）。
- 风格层 skill 负责生成具体风格的网页；copyweb 负责生产这些风格层 skill 本身。
- 每产出一个新风格 skill，copyweb 都会把它补进全局**决策规则**（report→gazette；platform→panda；<新场景>→<新 skill>），保证互斥不冲突。

---

## 边界（不是什么）

- **不生成网页**：生成某风格 HTML 是 gazette/panda 等风格层 skill 的职责，copyweb 的产物是 skill 本身。
- **不做像素级 1:1 还原**：目标是提炼"可迁移的设计语言"，不是 forensic 级像素拷贝。
- **不复刻品牌资产**：Logo / 原创插画 / 商标 / 专有字体文件不复刻。
- **不替代 skill-creator**：非 web 风格类通用 skill 仍走 skill-creator。

---

## 许可证

MIT License · © 2026 OPC-Studio

---

## 反馈与贡献

- Issue / PR：欢迎在本仓库提交
- 兼容问题：请附上平台名与 Agent 版本号
- 能力扩展：期待更多采集通道 fallback、配色分析自动化脚本、命名去重脚本的贡献

---

_OPC-Studio 出品 · 让每个 AI Agent 都能成为行业专家。_
