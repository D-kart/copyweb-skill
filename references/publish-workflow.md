# 发布流程（阶段 6 · 可选，需 publish=true + 用户确认）

> 仅当用户显式确认发布后执行。发布 = 推 GitHub + 打 release + 收入 OPC-Studio 矩阵。

---

## Step 1 · 建仓

用 gh CLI 建（推荐）：

```bash
gh repo create D-kart/<style_name>-skill --public \
  --description "<style_name>-skill，<中文名>.skill · <一句话风格定位>。OPC-Studio 出品。"
```

或用户在 GitHub 手建空仓库 `D-kart/<style_name>-skill`。

---

## Step 2 · 本地 git init + push

```bash
cd <style_name>-skill
git init
git add -A
git commit -m "feat: <style_name>-skill v1.0.0 — <一句话定位>"
git branch -M main
git remote add origin https://github.com/D-kart/<style_name>-skill.git
git push -u origin main
```

---

## Step 3 · 设 repo description + topics

```bash
gh repo edit D-kart/<style_name>-skill \
  --add-topic opc-studio --add-topic agentskills --add-topic ai-skill \
  --add-topic web-design --add-topic <风格相关 tag>
```

---

## Step 4 · 门面文件核查

确认根目录四件套齐全：`README.md` / `CHANGELOG.md` / `LICENSE`(MIT) / `.gitignore`。

---

## Step 5 · 打 release

```bash
gh release create v1.0.0 -R D-kart/<style_name>-skill \
  --title "<style_name>-skill v1.0.0 — <一句话定位>" \
  --notes-file /tmp/release-notes.md
```

> ⚠️ **血泪坑（panda 例）**：`gh release edit`（以及 create）**必须带 `-R OWNER/REPO`**。
> 本地目录若没配 git remote，不带 `-R` 会**静默失败**（报 `no git remotes found` 或什么都不发生）。永远显式带 `-R D-kart/<style_name>-skill`。

release notes 建议结构：

```markdown
## What's in the box
- 三层架构：META（SKILL.md）+ ASSETS（starter + 截图）+ REFERENCES（design-system / page-architecture / components）

## 组件清单
- <签名元素1> / <签名元素2> / ...

## 区块架构
- <N 个区块>

## 风格签名
- <positioning 一句话> + 2-4 个签名元素

## 安装
git clone https://github.com/D-kart/<style_name>-skill.git ~/.claude/skills/<style_name>-skill

## 与已有 skill 协同
- report → gazette；platform → panda；<新场景> → <style_name>
```

---

## Step 6 · 收入 OPC-Studio 矩阵（README 4 处）

矩阵仓库：`D-kart/OPC-Studio`，文件 `README.md`。**4 处都要加**，否则矩阵不一致。

用 GitHub Contents API PUT，或 clone → 改 → commit → push。推荐后者更直观：

```bash
git clone https://github.com/D-kart/OPC-Studio.git /tmp/opc && cd /tmp/opc
```

### 处 1 · Skill 矩阵表

在 `### Skill 矩阵` 的表格里加一行（表头：`| Skill | 中文名 | 主题 | 状态 | 仓库 |`）：

```
| <emoji> **<style_name>-skill** | <中文名>.skill | <主题一句话> | ✅ v1.0.0 已发布 | **[D-kart/<style_name>-skill](https://github.com/D-kart/<style_name>-skill)** |
```

### 处 2 · Claude Skills / WorkBuddy clone 段

```
git clone git@github.com:D-kart/<style_name>-skill.git ~/.claude/skills/<style_name>-skill
```

### 处 3 · OpenClaw / Hermes clone 段

```
git clone git@github.com:D-kart/<style_name>-skill.git ~/path/to/skills/
```

### 处 4 · SkillHub zip 段

```
zip -r <style_name>-skill.zip <style_name>-skill/
```

提交：

```bash
cd /tmp/opc && git add README.md
git commit -m "docs: 矩阵收录 <style_name>-skill v1.0.0"
git push
```

---

## 发布自检

- [ ] 仓库 public，description + topics 已设
- [ ] main 分支有全部文件
- [ ] v1.0.0 release 存在且 notes 完整（用 `gh release view v1.0.0 -R D-kart/<style_name>-skill` 核对）
- [ ] OPC-Studio README 4 处都已更新（矩阵表 + 三段 clone/zip）
- [ ] 新 skill SKILL.md 顶部路由段 + 全局决策规则已补入本 skill
