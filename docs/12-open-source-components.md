# 12. 开源组件选型清单

目标：能用成熟开源组件解决的，不自己从零写。这样代码量更少，UI 一致性更好，也更容易维护。

## 前端 UI

| 场景 | 推荐组件 | 用途 |
|------|----------|------|
| 按钮 | shadcn/ui Button | 普通按钮、图标按钮、危险操作按钮 |
| 输入框 | shadcn/ui Input | 搜索、标题、快捷键输入 |
| 多行输入 | shadcn/ui Textarea | Markdown 源码、快速 Memo |
| 下拉选择 | Radix Select | 语言、分组、状态等 |
| 弹层 | Radix Popover | 远程搜索、选择面板 |
| 右键菜单 | Radix Context Menu | Memo 列表右键操作 |
| 确认弹窗 | Radix Alert Dialog | 删除 Memo、删除分组 |
| 标签页 | Radix Tabs | 通知筛选、编辑模式切换 |
| Tooltip | Radix Tooltip | 只有图标的按钮说明 |
| 图标 | lucide-react | 全应用统一图标来源 |

## 编辑器

| 场景 | 推荐组件 | 用途 |
|------|----------|------|
| 可视化 Markdown | Milkdown Crepe | Memo 主编辑器 |
| Markdown 图形 | Milkdown diagram + mermaid | 流程图、甘特图、时序图 |
| 图片插入 | Crepe ImageBlock upload hook | 第一阶段转 base64，后续替换上传接口 |

## 数据和状态

| 场景 | 推荐组件 | 用途 |
|------|----------|------|
| HTTP | Axios | 统一 baseURL、token、错误处理 |
| 服务端缓存 | TanStack Query | 请求去重、缓存、失效刷新 |
| 长列表 | TanStack Virtual | Memo 列表、通知中心 |
| UI 状态 | Zustand | 侧边栏、设置、草稿状态、通知中心本地状态 |
| 本地持久化 | Zustand persist | 语言、主题、快捷键、通知缓存 |

## 桌面能力

| 场景 | 推荐组件 | 用途 |
|------|----------|------|
| 桌面壳 | Tauri 2 | Windows 桌面应用 |
| 系统通知 | Tauri notification plugin | Windows 通知中心 |
| 快捷键 | Tauri global-shortcut plugin | 唤出主窗口和快速 Memo |
| 文件能力 | Tauri fs plugin | 后续文件上传、本地缓存 |
| 剪贴板 | Tauri clipboard-manager plugin | 后续剪贴板功能 |

## 后端

| 场景 | 推荐组件 | 用途 |
|------|----------|------|
| Web API | Spring Boot Web | REST API |
| 实时通信 | Spring WebSocket | Memo 和通知事件推送 |
| ORM | MyBatis-Plus | Entity、Mapper、简单 CRUD |
| 复杂 SQL | MyBatis 注解 SQL | 列表聚合、权限查询 |
| Token | Sa-Token | 桌面端认证 token |
| API 文档 | SpringDoc OpenAPI | Swagger UI |
| 缓存 | Redis | 后续缓存、分布式会话、多实例广播 |
| 数据库 | MySQL | 主数据存储 |

## 不建议自己写的东西

- 基础 Button/Input/Select。
- 右键菜单定位和焦点管理。
- 弹窗焦点陷阱。
- 虚拟列表。
- Markdown 编辑器。
- WebSocket 心跳以外的大型实时框架。
- Token 认证框架。

## 可以自己写的东西

- 业务 Store。
- API adapter。
- 权限判断和保存顺序。
- Memo 草稿保护策略。
- 通知规则。
- Tauri command 的业务封装。

