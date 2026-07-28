# Changelog

All notable changes to `copyweb-skill` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [1.0.0] - 2026-07-28

### Added

- 首个正式版本发布。
- **SKILL.md**：完整六阶段复刻工作流（命名去重 → 双通道采集 → 风格分析 → 风格提炼 → 三层封装 → 验证 → 发布），内置输入/输出契约、自检清单、反模式、与其他 skill 的路由关系。
- **assets/starter-template.html**：产物 starter 脚手架，含完整 CSS 变量、字体栈 fallback、昼夜主题切换 hook（右上角固定 + localStorage 持久化）、代表区块示例 markup。
- **assets/workflow-diagram.md**：六阶段复刻流程图（含每阶段工具/产出/坑）。
- **references/analysis-checklist.md**：阶段1-2 双通道采集 + 风格分析防翻车 checklist，含 panda 例"暗黑系"误判案例详解（变量定义 ≠ 实际使用）。
- **references/architecture-template.md**：阶段4 三层架构产物模板，含 SKILL.md / README / CHANGELOG / LICENSE / design-system.md / page-architecture.md / components.md 各文件骨架与写作要求。
- **references/publish-workflow.md**：阶段6 发布流程，含建仓、push、release、OPC-Studio 矩阵 4 处更新、`gh release edit -R` 静默失败坑。
- **references/localization-zh.md**：中文化重建 5 大坑（展示字体无 CJK、uppercase 失效、italic 失效、drop cap 过重、红涨绿跌），从 gazette 例提炼。
- 门面四件套：README.md / CHANGELOG.md / LICENSE(MIT) / .gitignore。

### Notes

- 本 skill 为元 skill（meta-skill），基于 gazette-skill / panda-skill 两例复刻经验提炼。
- 兼容 Claude Skills / WorkBuddy / OpenClaw / Hermes / SkillHub 五大平台。
