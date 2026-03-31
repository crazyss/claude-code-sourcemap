# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个**非官方**研究用仓库，通过 npm 发布包 `@anthropic-ai/claude-code@2.1.88` 内附带的 source map（`cli.js.map`）还原 TypeScript 源码。源码版权归 [Anthropic](https://www.anthropic.com) 所有，仅供技术研究使用。

## 目录结构

```
restored-src/src/
├── main.tsx              # CLI 入口（Commander.js 参数解析 + Ink/React TUI）
├── Tool.ts               # 工具类型定义基类
├── tools.ts              # 工具注册中心
├── commands.ts           # 命令注册中心
├── tools/                # 30+ 工具实现（Bash, FileEdit, Grep, Glob, MCP, Agent, Skill 等）
├── commands/             # 40+ Slash 命令（commit, review, config, mcp 等）
├── services/             # 服务层（API, MCP, 分析, LSP 等）
├── utils/                # 工具函数（git, model, auth, env, settings 等）
├── bridge/               # 远程桥接/进程间通信
├── coordinator/          # 多 Agent 协调模式
├── plugins/              # 插件系统
├── skills/               # 技能系统
├── state/                # 状态管理（AppStateStore）
├── types/                # 共享类型定义
├── migrations/           # 数据迁移脚本
└── entrypoints/          # 入口点（init 等）
```

## 关键架构

- **入口**: `main.tsx` — 使用 Commander.js 解析 CLI 参数，Ink + React 渲染终端 UI
- **工具系统**: `Tool.ts` 定义基类接口，各工具在 `tools/` 下实现，`tools.ts` 统一注册
- **命令系统**: `commands/` 下实现各 slash 命令，`commands.ts` 统一导出
- **Feature Flags**: 使用 `bun:bundle` 的 `feature()` 函数控制条件编译（如 `KAIROS`, `PROACTIVE`, `BRIDGE_MODE`, `VOICE_MODE`），以及 `process.env.USER_TYPE === 'ant'` 控制内部功能
- **状态管理**: `state/AppStateStore.js` + `state/store.js` 管理应用状态
- **MCP 集成**: `services/mcp/` 处理 Model Context Protocol 服务器连接
- **插件/技能**: `plugins/` 和 `skills/` 分别管理可扩展的插件和技能系统

## 重要文件

| 文件 | 用途 |
|------|------|
| `restored-src/src/main.tsx` | CLI 主入口，包含启动参数解析、初始化流程（~785KB，建议分段读取） |
| `restored-src/src/Tool.ts` | 工具类型定义基类 |
| `restored-src/src/tools.ts` | 工具注册中心 |
| `restored-src/src/commands.ts` | 命令注册中心 |
| `restored-src/src/QueryEngine.ts` | 查询引擎核心 |
| `restored-src/src/Task.ts` | 任务管理 |
| `extract-sources.js` | 从 source map 提取源码的脚本 |

## 注意事项

- 源码是通过 source map 还原的，可能缺少原始开发时的注释和调试信息
- 部分代码受 feature flag 控制（`USER_TYPE === 'ant'` 或 `feature('XXX')`），在公开版本中可能不可用
- `main.tsx` 文件非常大（~785KB），阅读时建议使用 `offset`/`limit` 参数分段读取
- 仓库根目录的 `package/` 包含原始 npm 包，`claude-code-2.1.88.tgz` 为压缩包备份
