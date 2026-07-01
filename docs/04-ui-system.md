# 04. UI 系统

本章开始搭建“迹”的前端 UI 基础。目标不是一次写完全部功能，而是先把主题、字体、基础组件和主界面布局搭好，后续 Memo、通知、设置等页面都基于这套 UI 继续开发。

本章完成后，你应该能看到一个接近最终产品结构的桌面界面：

```text
┌──────────────────────────────────────────────────────────────┐
│ 自定义标题栏                                                   │
├──────────┬──────────────────┬────────────────────────────────┤
│ 左侧导航  │ Memo 列表          │ Memo 编辑区                     │
│          │ 搜索/列表/状态       │ 标题/元信息/编辑器/保存状态       │
└──────────┴──────────────────┴────────────────────────────────┘
```

## 1. UI 技术选择

“迹”的 UI 优先使用成熟开源组件，减少手写基础组件代码。

| 场景 | 推荐技术 |
|------|----------|
| 样式系统 | Tailwind CSS v4 |
| 基础组件风格 | shadcn/ui 风格组件 |
| 无障碍弹层和下拉 | Radix UI |
| 图标 | lucide-react |
| className 合并 | shadcn/ui 生成的 `cn` 工具 |
| 长列表 | TanStack Virtual |

当前阶段不要手写 Button、Input 这类基础组件，优先通过 shadcn CLI 生成。只有项目确实有特殊交互时，再在生成组件的基础上二次封装。

## 2. 安装 Tailwind v4

如果你已经按照 [03. 创建桌面端脚手架](03-desktop-scaffold.md) 安装过 Tailwind，可以跳过本节。

在 `ji-desktop` 项目根目录执行：

```powershell
pnpm add tailwindcss @tailwindcss/vite
```

Tailwind v4 的 Vite 项目不再执行旧版命令：

```powershell
pnpm exec tailwindcss init -p
```

如果 Windows 提示 `tailwindcss 不是内部或外部命令`，通常就是因为执行了旧版 CLI 初始化命令。本项目使用 Vite 插件集成，不需要全局安装 Tailwind。

Tailwind 的 Vite 插件会在第 4 节和路径别名一起配置。不要在多个地方重复改 `vite.config.ts`，否则后面复制代码时容易把前面的配置覆盖掉。

## 3. 创建目录

在前端 `src` 下创建这些目录：

```text
src/
├── components/
│   ├── layout/
│   └── ui/
├── features/
│   └── memo/
│       └── components/
├── styles/
└── lib/
```

这些目录的职责如下：

| 目录 | 作用 |
|------|------|
| `components/ui` | Button、Input、Select 等通用基础组件 |
| `components/layout` | 标题栏、主布局、侧边栏等全局布局组件 |
| `features/memo` | Memo 模块自己的页面和组件 |
| `styles` | 全局样式、字体、主题变量 |
| `lib` | `cn` 等通用工具函数，shadcn 默认会生成到这里 |

## 4. 配置 Vite 和路径别名

这里一次性配置 Tailwind v4 的 Vite 插件和 `@` 路径别名。shadcn 生成的组件通常会使用 `@/components`、`@/lib/utils` 这类导入路径，所以别名要在初始化 shadcn 前准备好。

先安装 Node path 类型：

```powershell
pnpm add -D @types/node
```

修改 `vite.config.ts`，最终保持下面这个版本：

```ts
import path from "node:path";
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
});
```

如果 `tsconfig.json` 没有配置路径别名，追加：

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

如果项目里还有 `tsconfig.app.json`，也要确认实际被 Vite/TypeScript 使用的配置中包含上面的 `baseUrl` 和 `paths`。

## 5. 初始化 shadcn/ui

在 `ji-desktop` 项目根目录执行：

```powershell
pnpm dlx shadcn@latest init
```

初始化时建议这样选择：

| 选项 | 建议 |
|------|------|
| TypeScript | Yes |
| Style | New York |
| Base color | Neutral |
| CSS variables | Yes |
| Global CSS file | `src/styles/globals.css` |
| Components path | `src/components` |
| Utils path | `src/lib/utils` |

执行后项目中通常会生成：

```text
components.json
src/lib/utils.ts
```

`src/lib/utils.ts` 里会包含 `cn` 方法，后续组件都直接使用它，不需要自己再写一份 `cn.ts`。

## 6. 安装 Prettier 和 Tailwind class 排序插件

Tailwind class 很长，手动维护顺序没有意义。使用 `prettier-plugin-tailwindcss` 自动排序。

安装：

```powershell
pnpm add -D prettier prettier-plugin-tailwindcss
```

创建 `.prettierrc.json`：

```json
{
  "plugins": ["prettier-plugin-tailwindcss"],
  "tailwindStylesheet": "./src/styles/globals.css"
}
```

`tailwindStylesheet` 是 Tailwind v4 项目需要的配置，指向包含 `@import "tailwindcss";` 的 CSS 入口文件。

如果后续加入其他 Prettier 插件，通常把 `prettier-plugin-tailwindcss` 放在 `plugins` 数组最后，让它在最终阶段整理 Tailwind class 顺序。

创建 `.prettierignore`：

```text
dist
src-tauri/target
node_modules
```

在 `package.json` 增加脚本：

```json
{
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check ."
  }
}
```

以后提交前执行：

```powershell
pnpm format
```

这样可以统一 JSX、TS、CSS 的格式，并自动整理 Tailwind class 顺序。

## 7. 创建全局样式

创建或修改 `src/styles/globals.css`。

注意：如果 shadcn 初始化时已经写入了 `@import "tailwindcss";`、主题变量和基础样式，不要整段删除，也不要直接用下面的内容覆盖它。正确做法是：

- 保留 shadcn 生成的 `globals.css`。
- 确认文件顶部仍然有 `@import "tailwindcss";`。
- 在 shadcn 生成内容的基础上追加项目需要的字体、窗口高度、滚动控制和少量业务变量。
- 如果要改主题色，基于 shadcn 生成的 CSS 变量修改，不要重新发明一套完全不同的变量体系。

如果文件里还没有 Tailwind 入口，先保证顶部有：

```css
@import "tailwindcss";
```

然后在 shadcn 生成的变量基础上，追加“迹”自己的业务变量，例如：

```css
:root {
  --background: #f6f7fb;
  --foreground: #172033;
  --panel: #ffffff;
  --panel-subtle: #f9fafc;
  --muted: #eef1f6;
  --muted-foreground: #697386;
  --border: #dfe4ec;
  --primary: #3f63e6;
  --primary-foreground: #ffffff;
  --success: #16a34a;
  --warning: #d97706;
  --danger: #dc2626;
  --ring: rgba(63, 99, 230, 0.25);
}

.dark {
  --background: #111827;
  --foreground: #f3f4f6;
  --panel: #182131;
  --panel-subtle: #151d2b;
  --muted: #222d3f;
  --muted-foreground: #9aa4b2;
  --border: #2d3748;
  --primary: #6d8dff;
  --primary-foreground: #ffffff;
  --success: #22c55e;
  --warning: #f59e0b;
  --danger: #f87171;
  --ring: rgba(109, 141, 255, 0.35);
}
```

再追加桌面应用需要的全局布局规则：

```css
html,
body,
#root {
  width: 100%;
  height: 100%;
}

body {
  margin: 0;
  overflow: hidden;
  background: var(--background);
  color: var(--foreground);
  font-family:
    "Alibaba PuHuiTi 3.0",
    "Alibaba Sans JP",
    "Microsoft YaHei",
    "Segoe UI",
    sans-serif;
}
```

如果你选择完全使用 shadcn 默认主题，也可以先只追加 `html/body/#root` 高度、`body` 滚动控制和字体配置。后续真正做主题切换时，再统一整理变量命名。

在 `src/main.tsx` 引入全局样式：

```ts
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import "./styles/globals.css";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```

## 8. 添加字体文件

建议将字体文件放入：

```text
src/assets/fonts/
├── AlibabaPuHuiTi-3-55-Regular.woff2
├── AlibabaPuHuiTi-3-65-Medium.woff2
└── AlibabaSansJP-Regular.woff2
```

然后在 `globals.css` 顶部追加：

```css
@font-face {
  font-family: "Alibaba PuHuiTi 3.0";
  src: url("../assets/fonts/AlibabaPuHuiTi-3-55-Regular.woff2") format("woff2");
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: "Alibaba PuHuiTi 3.0";
  src: url("../assets/fonts/AlibabaPuHuiTi-3-65-Medium.woff2") format("woff2");
  font-weight: 500;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: "Alibaba Sans JP";
  src: url("../assets/fonts/AlibabaSansJP-Regular.woff2") format("woff2");
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}
```

字体切换策略：

- Windows 系统语言是中文时，默认使用中文。
- Windows 系统语言是日文时，默认使用日文。
- 未支持语言默认中文。
- 用户在设置中手动选择后，以用户设置为准。

## 9. 生成基础 UI 组件

使用 shadcn CLI 生成本章需要的基础组件：

```powershell
pnpm dlx shadcn@latest add button input textarea badge tooltip separator
```

生成后应该能看到这些文件：

```text
src/components/ui/
├── badge.tsx
├── button.tsx
├── input.tsx
├── separator.tsx
├── textarea.tsx
└── tooltip.tsx
```

如果后面需要下拉、弹窗、右键菜单，再按需执行：

```powershell
pnpm dlx shadcn@latest add select popover dialog alert-dialog context-menu tabs
```

这样做的好处是：

- 基础组件不用自己写，代码量更少。
- Radix UI 依赖由 shadcn 自动处理。
- 组件源码仍然在项目里，后续可以按企业内部规范微调。

这里要区分两类组件：

- `Button`、`Input`、`Textarea`、`Badge`、`Tooltip`、`Separator` 属于基础 UI 组件，用 shadcn CLI 生成。
- `AppTitleBar`、`AppLayout`、`MemoListPanel`、`MemoEditorPanel` 属于业务组件，需要自己写，因为它们承载的是“迹”的产品布局和业务语义。

## 10. 创建自定义标题栏

桌面端建议使用自定义标题栏，这样 Windows 上的标题栏颜色可以和主题一致。

创建 `src/components/layout/app-title-bar.tsx`：

```tsx
import { Minus, Square, X } from "lucide-react";
import { Button } from "@/components/ui/button";

export function AppTitleBar() {
  return (
    <header
      data-tauri-drag-region
      className="flex h-8 shrink-0 items-center justify-between border-b border-[var(--border)] bg-[var(--panel)] px-2"
    >
      <div data-tauri-drag-region className="flex items-center gap-2 text-xs font-medium">
        <span className="h-2.5 w-2.5 rounded-full bg-[var(--primary)]" />
        <span>迹</span>
      </div>

      <div className="flex items-center gap-1">
        <Button size="icon" variant="ghost" aria-label="最小化窗口">
          <Minus className="h-3.5 w-3.5" />
        </Button>
        <Button size="icon" variant="ghost" aria-label="最大化窗口">
          <Square className="h-3.5 w-3.5" />
        </Button>
        <Button size="icon" variant="ghost" aria-label="关闭窗口">
          <X className="h-3.5 w-3.5" />
        </Button>
      </div>
    </header>
  );
}
```

这里先只做 UI，最小化、最大化、关闭按钮暂时不会真正操作窗口。真正的窗口控制会在 Tauri 桌面能力章节中接入。

如果你希望在代码里明确标记，可以先加 TODO：

```tsx
// TODO: 在 Tauri 桌面能力章节中接入窗口最小化、最大化、关闭命令。
```

## 11. 创建主布局

创建 `src/components/layout/app-layout.tsx`：

```tsx
import type { ReactNode } from "react";
import { Bell, FileText, Moon, Settings, Star, UserRound } from "lucide-react";
import { AppTitleBar } from "@/components/layout/app-title-bar";
import { Button } from "@/components/ui/button";

type AppLayoutProps = {
  list: ReactNode;
  detail: ReactNode;
};

const groups = [
  { id: 1, name: "工作记录", color: "#3f63e6" },
  { id: 2, name: "会议纪要", color: "#16a34a" },
  { id: 3, name: "项目跟进", color: "#d97706" },
];

export function AppLayout({ list, detail }: AppLayoutProps) {
  return (
    <div className="flex h-full flex-col bg-[var(--background)] text-[var(--foreground)]">
      <AppTitleBar />

      <div className="flex min-h-0 flex-1">
        <aside className="flex w-[190px] shrink-0 flex-col border-r border-[var(--border)] bg-[var(--panel)]">
          <nav className="space-y-1 p-2">
            <button className="flex h-8 w-full items-center gap-2 rounded-md bg-[var(--muted)] px-2 text-left text-sm font-medium">
              <FileText className="h-4 w-4" />
              <span>Memo</span>
            </button>
            <button className="flex h-8 w-full items-center gap-2 rounded-md px-2 text-left text-sm text-[var(--muted-foreground)] hover:bg-[var(--muted)]">
              <UserRound className="h-4 w-4" />
              <span>与我相关</span>
            </button>
          </nav>

          <div className="mx-2 my-1 rounded-md bg-[var(--panel-subtle)] p-2">
            <div className="mb-1.5 flex items-center justify-between text-xs text-[var(--muted-foreground)]">
              <span>分组</span>
              <button className="rounded px-1 hover:bg-[var(--muted)]">+</button>
            </div>
            <div className="max-h-[220px] space-y-1 overflow-y-auto">
              {groups.map((group) => (
                <button
                  key={group.id}
                  className="flex h-7 w-full items-center gap-2 rounded px-2 text-left text-xs hover:bg-[var(--muted)]"
                >
                  <span className="h-2.5 w-2.5 rounded-full" style={{ background: group.color }} />
                  <span className="truncate">{group.name}</span>
                </button>
              ))}
            </div>
          </div>

          <div className="mx-2 mt-1 space-y-1 rounded-md bg-[var(--panel-subtle)] p-2 text-xs">
            <button className="flex h-7 w-full items-center gap-2 rounded px-2 hover:bg-[var(--muted)]">
              <Star className="h-3.5 w-3.5" />
              <span>我的收藏</span>
            </button>
          </div>

          <div className="mt-auto border-t border-[var(--border)] p-2">
            <div className="mb-2 flex items-center justify-between">
              <div className="flex items-center gap-2">
                <div className="flex h-7 w-7 items-center justify-center rounded-full bg-[var(--primary)] text-xs text-white">
                  迹
                </div>
                <div className="min-w-0">
                  <div className="truncate text-xs font-medium">当前用户</div>
                  <div className="truncate text-[10px] text-[var(--muted-foreground)]">Windows 认证</div>
                </div>
              </div>
              <Button size="icon" variant="ghost" aria-label="通知">
                <Bell className="h-4 w-4" />
              </Button>
            </div>
            <div className="grid grid-cols-2 gap-1">
              <Button size="icon" variant="ghost" aria-label="设置">
                <Settings className="h-4 w-4" />
              </Button>
              <Button size="icon" variant="ghost" aria-label="切换主题">
                <Moon className="h-4 w-4" />
              </Button>
            </div>
          </div>
        </aside>

        <main className="grid min-w-0 flex-1 grid-cols-[280px_minmax(0,1fr)]">
          {list}
          {detail}
        </main>
      </div>
    </div>
  );
}
```

布局重点：

- 最外层用 `h-full flex flex-col` 固定桌面窗口高度。
- 中间区域用 `min-h-0 flex-1`，否则子元素滚动容易失效。
- Memo 列表和编辑区用 `grid-cols-[280px_minmax(0,1fr)]`，避免编辑区被内容撑爆。
- 左侧分组区域单独滚动，底部用户和设置区域固定。

## 12. 创建 Memo 列表占位组件

创建 `src/features/memo/components/memo-list-panel.tsx`：

```tsx
import { Search } from "lucide-react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";

const memoItems = [
  { id: 1, title: "项目会议纪要", status: "待办", time: "09:30" },
  { id: 2, title: "客户需求整理", status: "普通", time: "昨天" },
  { id: 3, title: "上线检查清单", status: "完成", time: "06/28" },
];

export function MemoListPanel() {
  return (
    <section className="flex min-w-0 flex-col border-r border-[var(--border)] bg-[var(--panel-subtle)]">
      <div className="space-y-2 border-b border-[var(--border)] p-3">
        <div className="relative">
          <Search className="pointer-events-none absolute left-2.5 top-2 h-4 w-4 text-[var(--muted-foreground)]" />
          <Input className="pl-8" placeholder="搜索标题或内容" />
        </div>
        <Button className="w-full">
          新建 Memo
        </Button>
      </div>

      <div className="min-h-0 flex-1 overflow-y-auto">
        {memoItems.map((memo) => (
          <button
            key={memo.id}
            className="block w-full border-b border-[var(--border)] bg-[var(--panel)] px-3 py-2 text-left hover:bg-[var(--muted)]"
          >
            <div className="flex items-center justify-between gap-2">
              <span className="truncate text-sm font-medium">{memo.title}</span>
              <span className="shrink-0 text-[10px] text-[var(--muted-foreground)]">{memo.time}</span>
            </div>
            <div className="mt-1 flex items-center gap-1.5 text-xs text-[var(--muted-foreground)]">
              <span className="h-1.5 w-1.5 rounded-full bg-[var(--primary)]" />
              <span>{memo.status}</span>
            </div>
          </button>
        ))}
      </div>
    </section>
  );
}
```

## 13. 创建 Memo 编辑区占位组件

创建 `src/features/memo/components/memo-editor-panel.tsx`：

```tsx
import { Save } from "lucide-react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";

export function MemoEditorPanel() {
  return (
    <section className="flex min-w-0 flex-col bg-[var(--panel)]">
      <div className="border-b border-[var(--border)] p-3">
        <Input className="border-transparent px-0 text-base font-medium focus:border-transparent focus:ring-0" placeholder="请输入标题" />
        <div className="mt-2 flex flex-wrap items-center gap-2 text-xs text-[var(--muted-foreground)]">
          <span>状态：待办</span>
          <span>分组：工作记录</span>
          <span>最后更新：09:30</span>
        </div>
      </div>

      <div className="min-h-0 flex-1 p-3">
        <textarea
          className="h-full w-full resize-none rounded-md border border-[var(--border)] bg-[var(--panel-subtle)] p-3 text-sm outline-none focus:border-[var(--primary)] focus:ring-2 focus:ring-[var(--ring)]"
          placeholder="这里后续会替换为 Milkdown 可视化编辑器"
        />
      </div>

      <footer className="flex h-9 shrink-0 items-center justify-between border-t border-[var(--border)] px-3">
        <span className="text-xs text-[var(--muted-foreground)]">已保存</span>
        <Button size="sm">
          <Save className="h-3.5 w-3.5" />
          保存
        </Button>
      </footer>
    </section>
  );
}
```

注意这里使用 `textarea` 只是为了先验证布局。真正的富文本编辑器会在 Memo 模块章节替换为 Milkdown。

## 14. 组装 App

修改 `src/App.tsx`：

```tsx
import { AppLayout } from "@/components/layout/app-layout";
import { MemoEditorPanel } from "@/features/memo/components/memo-editor-panel";
import { MemoListPanel } from "@/features/memo/components/memo-list-panel";

export default function App() {
  return <AppLayout list={<MemoListPanel />} detail={<MemoEditorPanel />} />;
}
```

启动前端：

```powershell
pnpm format:check
pnpm dev
```

如果项目已经配置 `typecheck` 脚本，也执行：

```powershell
pnpm typecheck
```

启动 Tauri 桌面窗口：

```powershell
pnpm tauri dev
```

## 15. 验证效果

完成本章后检查这些点：

- 应用第一屏直接进入工作界面，不出现营销页。
- 左侧导航、Memo 列表、编辑区三栏都能显示。
- 窗口高度较小时，Memo 列表内部滚动，底部用户区域不被挤走。
- 编辑区正文区域可以独立滚动。
- 浅色主题下边框、分割线、按钮、输入框对比度清楚。
- Windows 200% 缩放下，“Memo”“与我相关”“我的收藏”等文字不应该挤成竖排。
- `pnpm format:check` 能通过。
- 如果配置了 `typecheck`，`pnpm typecheck` 能通过。
- `pnpm dev` 不应该出现 Tailwind CLI 相关错误。

## 16. 常见问题

### `tailwindcss 不是内部或外部命令`

不要执行旧版命令：

```powershell
pnpm exec tailwindcss init -p
```

Tailwind v4 的 Vite 项目使用：

```powershell
pnpm add tailwindcss @tailwindcss/vite
```

然后在 `vite.config.ts` 中添加 `tailwindcss()` 插件。

### 页面高度不对，滚动区域失效

检查这些位置是否有 `min-h-0`：

- 主体区域：`flex min-h-0 flex-1`
- Memo 列表：`min-h-0 flex-1 overflow-y-auto`
- 编辑区正文：`min-h-0 flex-1`

Flex 布局里如果缺少 `min-h-0`，子元素经常会把父元素撑开，导致滚动条出现在错误的位置。

### 字体没有生效

先确认字体文件路径是否正确，再确认 `globals.css` 已经被 `main.tsx` 引入。

如果字体文件暂时没有准备好，可以先使用系统字体，不影响后续功能开发。

### shadcn 组件导入路径报错

如果看到类似 `Cannot find module "@/components/ui/button"` 的错误，检查两处：

- `vite.config.ts` 是否配置了 `@` alias。
- `tsconfig.json` 或 `tsconfig.app.json` 是否配置了 `baseUrl` 和 `paths`。

### Tailwind class 顺序每个人都不一样

确认已经安装并配置：

```powershell
pnpm add -D prettier prettier-plugin-tailwindcss
```

Tailwind v4 项目还需要确认 `.prettierrc.json` 里有 CSS 入口：

```json
{
  "plugins": ["prettier-plugin-tailwindcss"],
  "tailwindStylesheet": "./src/styles/globals.css"
}
```

然后执行：

```powershell
pnpm format
```

格式化后 Tailwind class 会自动按插件规则排序。

## 17. 本章完成标准

本章完成后，代码中至少应该有这些文件：

```text
src/
├── App.tsx
├── main.tsx
├── components/
│   ├── layout/
│   │   ├── app-layout.tsx
│   │   └── app-title-bar.tsx
│   └── ui/
│       ├── badge.tsx
│       ├── button.tsx
│       ├── input.tsx
│       ├── separator.tsx
│       ├── textarea.tsx
│       └── tooltip.tsx
├── features/
│   └── memo/
│       └── components/
│           ├── memo-editor-panel.tsx
│           └── memo-list-panel.tsx
├── styles/
│   └── globals.css
└── lib/
    └── utils.ts
```

这些内容只是 UI 骨架。下一章会在这个骨架上继续实现 Memo 的数据库、接口、列表查询、保存、相关人和权限控制。
