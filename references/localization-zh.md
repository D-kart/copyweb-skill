# 中文化重建规则（chinese_target=true 时必读）

> 从 gazette 例复刻 findmymoat.com 时提炼。**英文站的视觉规则直接套到中文站会失败**——字体、大小写、斜体、首字下沉在中文语境全都要重建。
> copyweb 产出中文向 skill 时，把本文件的通用规则**特化**成该风格专属的 `references/localization-zh.md`。

---

## 坑 1 · 展示字体无 CJK 字形

英文展示字体（Playfair Display / Dream BraveGB / 各种 display font）**几乎都没有中文字形**，直接用会 fallback 成难看的系统字或方框。

**解法**：

| 英文展示字 | 中文替换 |
|---|---|
| 衬线 display（Playfair 等） | **思源宋体 Heavy** / Noto Serif SC 900 |
| 无衬线 display（sans display） | **思源黑体 Heavy** / Noto Sans SC 900 |
| 装饰/故障字体 | 保留仅用于极少量英文点缀，中文另配粗黑体 |

正文同理：英文正文字体配 CJK fallback（`PingFang SC` / `Microsoft YaHei` / 思源系列）。

---

## 坑 2 · `text-transform: uppercase` 中文无效

英文小标常用全大写 + 宽字距做 section divider。**中文没有大小写**，`uppercase` 完全无效。

**解法**：改用 **宽字距（`letter-spacing: 0.3em`）+ 加粗** 制造同等的"标签感"。

```css
.section-label {
  /* text-transform: uppercase;  ← 中文删掉 */
  letter-spacing: 0.3em;
  font-weight: 700;
  font-size: 13px;
}
```

---

## 坑 3 · `font-style: italic` 中文极丑

中文没有真正的斜体字形，`italic` 会强制倾斜方块字，非常难看。

**解法**：副标题/引文改用 **浅灰色 + 字号变化 + 字重区分**，不用倾斜。

```css
.subtitle {
  /* font-style: italic;  ← 中文删掉 */
  color: var(--color-fg-muted);
  font-size: 1.1em;
  font-weight: 400;
}
```

---

## 坑 4 · Drop cap 首字下沉在汉字上过重

英文 drop cap 可放大 4-5x（单个字母瘦，视觉平衡）。**汉字笔画密，放 4.5x 会像一坨墨块**。

**解法**：中文 drop cap 缩到 **3x** 左右，或改用其他强调手法（首句加粗 / 竖排引号）。

---

## 坑 5 · 金融场景红涨绿跌（与欧美相反）

中国金融惯例：**涨 = 红，跌 = 绿**，与欧美完全相反。做中文金融/投研产品时必须遵守。

```css
:root {
  --color-up:   #d4341f;  /* 涨 = 红 */
  --color-down: #1f9d55;  /* 跌 = 绿 */
}
```

货币符号用 `¥`，大额用"万元 / 亿元"。

---

## 坑 6 · 中文笔画密，需要更松的排版

同样字号下，中文比英文视觉更"满"。直接套英文的紧凑排版会压抑。

**解法**：

- 正文字号 **≥16-18px**
- 行高 **≥1.7**
- 每屏元素比英文版 **少 20%** 左右，留白更多
- 避免过窄的多列（中文短句在窄列里断行难看）

---

## 中文化定位两条路线（参考 gazette）

不同风格可对应不同的中文文化定位，产出 skill 时可据风格调性择一说明：

- **A. 现代大报路线**（《申报》《大公报》风）—— 金融 / 投研 / 严肃内容产品
- **B. 清雅留白路线**（宋韵刻书 / 极简）—— 文化 / 内容 / 生活方式产品

具体路线由参考站调性决定，写入该风格 skill 的 localization-zh.md。
