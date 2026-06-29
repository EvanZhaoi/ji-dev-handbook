# 03. 创建桌面端脚手架

## 1. 创建 Tauri 项目

```powershell
pnpm create tauri-app@latest ji-desktop
cd ji-desktop
pnpm install
```

推荐选择：

- Frontend: React
- Language: TypeScript
- Package manager: pnpm

## 2. 安装前端依赖

```powershell
pnpm add zustand @tanstack/react-query @tanstack/react-virtual axios lucide-react clsx tailwind-merge
pnpm add @radix-ui/react-select @radix-ui/react-popover @radix-ui/react-tabs @radix-ui/react-context-menu @radix-ui/react-alert-dialog @radix-ui/react-tooltip
pnpm add @milkdown/crepe @milkdown/plugin-diagram @milkdown/utils mermaid
```

## 3. 安装 Tailwind

```powershell
pnpm add -D tailwindcss postcss autoprefixer
pnpm exec tailwindcss init -p
```

## 4. 增加 shadcn 风格基础组件

不一定要安装完整 shadcn CLI，可以按需复制这些组件：

- Button
- Input
- Textarea
- Select
- Tabs
- Popover
- Context Menu
- Alert Dialog
- Tooltip
- Badge
- Card

组件源码建议放在：

```text
src/components/ui/
```

## 5. 配置 Tauri 插件

需要的桌面能力：

- notification
- global-shortcut
- shell
- fs
- clipboard-manager

后续按 Tauri 2 官方方式加入插件，并在 Rust 侧暴露命令。

## 6. 第一次运行

```powershell
pnpm tauri dev
```

如果只看前端 UI：

```powershell
pnpm dev
```
