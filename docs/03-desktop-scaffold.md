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
pnpm add tailwindcss @tailwindcss/vite
```

Tailwind v4 以后，Vite 项目推荐直接使用 `@tailwindcss/vite` 插件，不再执行旧版的 `tailwindcss init -p`。

修改 `vite.config.ts`：

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

在全局样式文件中引入 Tailwind，例如 `src/styles/globals.css`：

```css
@import "tailwindcss";
```

然后在 `src/main.tsx` 中引入全局样式：

```ts
import "./styles/globals.css";
```

如果 Windows 提示 `tailwindcss 不是内部或外部命令`，说明你正在执行旧版 CLI 初始化命令。当前手册按 Tailwind v4 最新方式走 Vite 插件集成，不需要全局安装 Tailwind，也不需要生成 `tailwind.config.js` 和 `postcss.config.js`。

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
