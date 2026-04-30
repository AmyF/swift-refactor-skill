# 前沿 Modern SwiftUI 重构 Skill

这是一个仅面向 Swift 的重构方法论，目标项目固定兼容 **iOS 18**，并优先采用 **Swift 6.2+ readiness** 的现代实践，服务 SwiftUI 应用。

它帮助 Agent 系统性分析并重构 AI 生成的 SwiftUI 代码，重点发现重复实现、未复用 Design System、遗留状态管理、不一致的 Service、SwiftData 生命周期问题、App Intents 暴露缺口、Swift 并发边界问题，以及上下文碎片化带来的架构退化。

## 适用目标

- SwiftUI 应用架构与 Feature 边界
- iOS 18 兼容与 Swift 6.2+ modern defaults
- Approachable Concurrency、strict concurrency、default actor isolation 审计
- 基于 `@Observable`、`@Bindable` 的 Observation 宏状态管理
- SwiftData、Swift Testing、App Intents、Spotlight、`Transferable` 与系统体验
- Design System 复用：View、ViewModifier、Style、Asset、颜色、字体、间距
- Xcode 与 SwiftPM target 依赖清理
- 通过 `.refactor/` 支持可恢复的增量重构

## 固定原则

- SwiftUI 优先。
- 最低平台 iOS 18。
- 最低语言模式 Swift 6，优先评估 Swift 6.2+ readiness。
- 新代码不得引入 `ObservableObject`、`@Published`、`@StateObject`、`@ObservedObject`。
- 遗留状态管理只作为迁移对象出现，目标是 Observation 宏体系。
- Domain、Services、DesignSystem、Infrastructure 不得盲目 MainActor 化。
- 必须先完成分析，再开始重构。

## 使用示例

```text
请按照 frontier-modern Swift 方法论重构这个项目。
分析这个 iOS 18 Swift 6.2-ready 项目的架构。
清理重复 SwiftUI View，并迁移遗留状态管理到 Observation。
审计 SwiftData、Swift Testing、App Intents 与 Approachable Concurrency readiness。
继续重构。
```

## 工作流

```
项目分区 -> 关键功能识别 -> 架构层分析
-> 模块深度分析 -> 执行重构 -> 最终验证
```

所有长期状态保存在 `.refactor/`，后续 Agent 可以从上次中断点继续。

## 项目结构

```
vibecoding-refactor/
├── SKILL.md
├── analysis/
├── patterns/
├── strategies/
└── workspace/
```

## 关键文档

- [SKILL.md](SKILL.md) - 主入口
- [深度分析](analysis/deep-analysis.md) - SwiftUI 功能分析
- [问题分类](patterns/vibe-coding-problems.md) - Swift 专项问题
- [重构模式](patterns/refactor-patterns.md) - Swift 标准修复模式
- [工作区规范](workspace/workspace-spec.md) - `.refactor/` 持久化格式

## 许可证

MIT
