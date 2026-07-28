# 采集与风格分析 · 防翻车 Checklist（阶段 1-2）

> 这是 copyweb 的核心防线。风格分析是整条链路最容易翻车的一步——panda 例就在这里翻过一次。
> **任一项"不确定"，都不能进入阶段4 封装。**

---

## 一、双通道采集（阶段1）

### 通道 A · 渲染语义采集

- 工具：`agent-browser` / `playwright` / `WebFetch`
- 目的：理解页面**语义结构**——有哪些区块、每区块内容设计、信息层级、交互模式
- 产出：区块清单 + 每区块用途说明（喂 `page-architecture.md`）

- [ ] 列出页面全部区块（按从上到下顺序）
- [ ] 每个区块标注：用途 / 内容类型 / 布局形态（单列 / 多列 / 网格）
- [ ] 标注交互模式（tab 切换 / 手风琴 / 轮播 / hover 展开）

### 通道 B · 原始资产采集（不可跳过）

> **关键坑**：WebFetch 把 HTML 转 markdown 时会**剥掉所有 `<style>` 和内联 `style=`**，配色/字体/圆角/阴影信息全丢。通道 B 是唯一能拿到这些精确数值的途径。

推荐命令：

```bash
# 拿原始 HTML
curl -sL "<url>" -o /tmp/site.html
wc -c /tmp/site.html

# 提取 CSS 变量定义
grep -oE '\-\-[a-zA-Z-]+:\s*[^;]+;' /tmp/site.html | sort -u | head -80

# 提取字体
grep -oE 'font-family:[^;}"]+' /tmp/site.html | sort -u
grep -oE '@font-face|@import[^;]+' /tmp/site.html | sort -u

# 提取圆角 / 阴影
grep -oE 'border-radius:[^;}"]+' /tmp/site.html | sort -u
grep -oE 'box-shadow:[^;}"]+' /tmp/site.html | sort -u

# 提取实际 class 的 background / color（关键！不只看 :root）
grep -oE '\.[a-zA-Z0-9_-]+\s*\{[^}]*background[^}]*\}' /tmp/site.html | head -40
```

- [ ] 拿到原始 HTML（记录字节数，确认不是被墙的空壳）
- [ ] 提取到 `:root{}` / CSS 变量块
- [ ] 提取到 `font-family` / `@font-face` / `@import` 字体来源
- [ ] 提取到 `border-radius` / `box-shadow` 数值
- [ ] **提取到实际主容器 class 的 `background` 和 `color`**（不只是 `:root` 变量定义）

### 通道 C · 截图采集

- 工具：`agent-browser` / `playwright` 截 hero / 定价 / 列表关键区块
- 已知坑：agent-browser 代理可能 `ERR_NO_SUPPORTED_PROXIES` 失败
- Fallback：失败时在 `assets/SCREENSHOTS-TODO.md` 放占位（列出应补哪些截图 + 参考站 URL），**不阻塞流程**

- [ ] 截到 hero（或放 TODO 占位）
- [ ] 截到列表 / 定价 / 核心功能区（或占位）

---

## 二、风格分析（阶段2 · 防翻车核心）

### ⚠️ 铁律：变量定义 ≠ 实际使用

**panda 例血泪案例（必读）：**

> 分析 pandaaiquant.com 时，看到 CSS 里有 `--bg-color:#000` + `--text-color:#fff`，直接判定"暗黑系 SaaS"。**完全错误。**
> 实际 `.si-page` 根容器是 `background:#fff; color:#0f0f10`——**白底为主**，只有少数重点区块（主 CTA / 能力图标块 / 软件底座 section / 场景徽章）反色为黑底白字。
> 正确定位应是"**双色 SaaS — 白底为主 + 黑色反色强调**"。
> 靠用户纠正才修正。教训：**CSS 里定义了一个变量 ≠ 页面实际大面积用它**。必须读实际 class 的 computed style。

### 配色

- [ ] 读到了**实际 class 的 background/color**（不只 `:root` 变量定义）
- [ ] 区分了**主色**（>70% 面积）与**反色锚点**（<30% 面积）
- [ ] 给出**整体调性结论**，未被反色锚点误导。四选一并写清依据：
  - 白底为主 + 反色锚点（如 panda）
  - 纯黑底 / 近单色暗黑（真暗黑，如 Vercel dark）
  - 纯白 + 衬线（如 gazette）
  - 双色 / 多色
- [ ] 列出七色：页面背景色 / 主文字色 / 次要文字色 / 边框色 / 卡片色 / 反色锚点色 / 强调色
- [ ] 标注渐变（仅 hero？全站？无？）与装饰性彩色（有？无？）
- [ ] 金融场景：确认红涨绿跌（中国区，与欧美相反）

### 字体（分清 4 角色）

| 角色 | 用途 | 记录内容 |
|---|---|---|
| 正文 / UI | 主体可读 | 字体名 + 字重 + fallback |
| 展示字 | hero / 大标题 | 字体名 + 字重 |
| 数字 / 标签 | 价格 / 数据 | 字体名 + 是否 tabular-nums |
| 装饰字 | 极少量点缀 | 字体名 + **使用场景限定** |

- [ ] 4 类角色都分清了
- [ ] 装饰字体标注了"仅 X 场景（hero/CTA），禁用正文"（装饰字体小字号常不可读）
- [ ] 给出 fallback 栈（**CJK fallback 必备**：PingFang SC / 思源系列 / 系统字体）
- [ ] 数字是否用 `tabular-nums` / 等宽

### 卡片 / 布局

- [ ] 卡片：默认**扁平**还是**带阴影**？边框色 + 圆角值(px) + hover 行为
- [ ] 布局：内容最大宽度(px)、纵向节奏（区块间距 px）、列数
- [ ] 响应式：移动端如何收窄（单列？隐藏导航？）

### 签名级视觉元素（少了就不像的标志）

- [ ] 提炼出 **2-4 个**签名元素
- [ ] 每个签名元素能写成一个 `components.md` 可运行配方

参考先例签名元素：

- **gazette**：masthead（VOL.NO. + 日期横条）、drop cap 4.5x、★ 装饰、黑白线描插画
- **panda**：5 步工作流闭环、反色底座 section、Combo `+`、VS 对比行、服务 kind 标签（TRAFFIC/DATA/SKILL/AGENT）

---

## 三、命名（阶段0 回填核查）

- [ ] 短词名，不绑参考站品牌
- [ ] 过了 GitHub / npm / Product Hunt 撞车检查
- [ ] 中文别名 `<中文名>.skill`
- [ ] 与已有风格 skill 决策规则不冲突

---

## 四、进入封装前的最终确认

- [ ] 上述所有项无"不确定"
- [ ] 配色定位没有被反色锚点误导（回头再核一次铁律）
- [ ] 若与已有 skill 高度重叠 → 已向用户提示"建议直接用已有 skill"，而非硬造冗余
