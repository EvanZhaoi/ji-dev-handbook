# 迹 Dev Handbook

> 中文项目名：迹  
> 目录名：`ji-dev-handbook`  
> 目标：记录如何从零开始开发一个与当前 UniComm Desktop 功能和体验相近的企业桌面应用。

## 这个项目是什么

`ji-dev-handbook` 不是设计稿项目，也不是源码项目。它是一个逐步开发手册，用来指导你在自己的电脑上按步骤做出同类应用。

文档会尽量使用开源组件和成熟依赖，减少手写基础设施代码。当前 UniComm Desktop 已经形成的 UI 风格、布局密度、Memo 编辑体验、通知中心、托盘、快捷键、权限认证等内容，都会逐步沉淀到这里。

## 推荐技术栈

| 层级 | 推荐技术 | 选择原因 |
|------|----------|----------|
| 桌面端 | Tauri 2 | 轻量、适合 Windows 桌面应用，支持托盘、快捷键、系统通知 |
| 前端 | React + TypeScript + Vite | 当前项目已验证，生态成熟 |
| UI | Tailwind CSS + shadcn/ui + Radix UI + lucide-react | 少写基础组件，保持当前漂亮 UI 风格 |
| 编辑器 | Milkdown Crepe | MIT 协议，可商用，Markdown 可视化编辑体验好 |
| 请求缓存 | TanStack Query | 请求去重、缓存、失效刷新 |
| 长列表 | TanStack Virtual | Memo 列表和通知中心虚拟滚动 |
| 状态管理 | Zustand | 简洁，适合桌面端 UI 状态和本地持久化 |
| 后端 | Java 21 + Spring Boot | 企业环境常见，易集成权限、数据库、接口 |
| ORM | MyBatis-Plus | 减少 CRUD 代码，同时保留复杂 SQL 能力 |
| 数据库 | MySQL 8.x | 当前项目已采用 |
| 实时通信 | Spring WebSocket | Memo 协作通知和通用通知推送 |
| 权限认证 | Sa-Token | 轻量，适合当前桌面 Token 认证 |
| 缓存/扩展 | Redis | 后续用于缓存、分布式会话、WebSocket 多实例广播 |

## 文档目录

```text
ji-dev-handbook/
├── README.md
├── docs/
│   ├── 00-project-overview.md
│   ├── 01-environment-setup.md
│   ├── 02-repo-structure.md
│   ├── 03-desktop-scaffold.md
│   ├── 04-ui-system.md
│   ├── 05-memo-module.md
│   ├── 06-auth-and-permission.md
│   ├── 07-realtime-and-notification.md
│   ├── 08-database-and-api.md
│   ├── 09-testing-and-release.md
│   └── 10-development-roadmap.md
├── checklists/
│   ├── daily-dev-checklist.md
│   └── feature-done-checklist.md
└── assets/
```

## 建议阅读顺序

1. 先读 [项目概览](docs/00-project-overview.md)
2. 再按 [环境搭建](docs/01-environment-setup.md) 准备电脑
3. 按 [桌面端脚手架](docs/03-desktop-scaffold.md) 创建 Tauri 项目
4. 按 [UI 系统](docs/04-ui-system.md) 搭好界面风格
5. 逐步实现 Memo、认证、通知、数据库和发布流程

## 当前第一版覆盖范围

- 从零搭建前后端与桌面端项目
- 使用现有 UniComm Desktop 的 UI 风格作为“迹”的基础界面
- Memo 模块开发路线
- Windows 桌面能力：托盘、后台运行、全局快捷键、系统通知
- 后端认证、设备信任、相关人权限
- WebSocket 实时刷新和通用通知平台
- 测试、构建、发布检查清单

