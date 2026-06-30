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
| className 合并 | clsx + tailwind-merge |
| 长列表 | TanStack Virtual |

当前阶段先手写少量 shadcn 风格基础组件。等项目稳定后，可以继续按需补充更多组件。

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

修改 `vite.config.ts`：

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

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
└── utils/
```

这些目录的职责如下：

| 目录 | 作用 |
|------|------|
| `components/ui` | Button、Input、Select 等通用基础组件 |
| `components/layout` | 标题栏、主布局、侧边栏等全局布局组件 |
| `features/memo` | Memo 模块自己的页面和组件 |
| `styles` | 全局样式、字体、主题变量 |
| `utils` | 通用工具函数 |

## 4. 添加 className 合并工具

先安装工具依赖：

```powershell
pnpm add clsx tailwind-merge
```

创建 `src/utils/cn.ts`：

```ts
import { type ClassValue, clsx } from "clsx";
import { twMerge } from "tailwind-merge";

/**
 * 合并 Tailwind className。
 * clsx 负责条件 class，tailwind-merge 负责处理冲突的 Tailwind 类。
 */
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

后续所有基础组件都通过 `cn` 合并样式，避免 `px-2 px-3` 这类冲突样式同时存在。

## 5. 创建全局样式

创建 `src/styles/globals.css`：

```css
@import "tailwindcss";

:root {
  color-scheme: light;

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
  color-scheme: dark;

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

* {
  box-sizing: border-box;
}

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

button,
input,
textarea,
select {
  font: inherit;
}
```

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

## 6. 添加字体文件

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

## 7. 创建 Button 组件

创建 `src/components/ui/button.tsx`：

```tsx
import type { ButtonHTMLAttributes } from "react";
import { cn } from "../../utils/cn";

type ButtonVariant = "primary" | "secondary" | "ghost" | "danger";
type ButtonSize = "sm" | "md" | "icon";

type ButtonProps = ButtonHTMLAttributes<HTMLButtonElement> & {
  variant?: ButtonVariant;
  size?: ButtonSize;
};

const variantClasses: Record<ButtonVariant, string> = {
  primary: "bg-[var(--primary)] text-[var(--primary-foreground)] hover:brightness-95",
  secondary: "bg-[var(--muted)] text-[var(--foreground)] hover:bg-[var(--border)]",
  ghost: "bg-transparent text-[var(--foreground)] hover:bg-[var(--muted)]",
  danger: "bg-[var(--danger)] text-white hover:brightness-95",
};

const sizeClasses: Record<ButtonSize, string> = {
  sm: "h-7 px-2 text-xs",
  md: "h-8 px-3 text-sm",
  icon: "h-8 w-8 p-0",
};

export function Button({
  className,
  variant = "secondary",
  size = "md",
  type = "button",
  ...props
}: ButtonProps) {
  return (
    <button
      type={type}
      className={cn(
        "inline-flex items-center justify-center gap-1.5 rounded-md border border-transparent font-medium outline-none transition",
        "focus-visible:ring-2 focus-visible:ring-[var(--ring)]",
        "disabled:cursor-not-allowed disabled:opacity-50",
        variantClasses[variant],
        sizeClasses[size],
        className,
      )}
      {...props}
    />
  );
}
```

这个按钮只保留项目当前需要的变体，后续不要一开始就堆很多未使用样式。

## 8. 创建 Input 组件

创建 `src/components/ui/input.tsx`：

```tsx
import type { InputHTMLAttributes } from "react";
import { cn } from "../../utils/cn";

type InputProps = InputHTMLAttributes<HTMLInputElement>;

export function Input({ className, ...props }: InputProps) {
  return (
    <input
      className={cn(
        "h-8 w-full rounded-md border border-[var(--border)] bg-[var(--panel)] px-2.5 text-sm text-[var(--foreground)]",
        "placeholder:text-[var(--muted-foreground)]",
        "outline-none transition focus:border-[var(--primary)] focus:ring-2 focus:ring-[var(--ring)]",
        "disabled:cursor-not-allowed disabled:opacity-50",
        className,
      )}
      {...props}
    />
  );
}
```

## 9. 创建自定义标题栏

桌面端建议使用自定义标题栏，这样 Windows 上的标题栏颜色可以和主题一致。

创建 `src/components/layout/app-title-bar.tsx`：

```tsx
import { Minus, Square, X } from "lucide-react";
import { Button } from "../ui/button";

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

这里先只做 UI。真正的窗口最小化、最大化、关闭会在 Tauri 桌面能力章节中接入。

## 10. 创建主布局

创建 `src/components/layout/app-layout.tsx`：

```tsx
import type { ReactNode } from "react";
import { Bell, FileText, Moon, Settings, Star, UserRound } from "lucide-react";
import { AppTitleBar } from "./app-title-bar";
import { Button } from "../ui/button";

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

## 11. 创建 Memo 列表占位组件

创建 `src/features/memo/components/memo-list-panel.tsx`：

```tsx
import { Search } from "lucide-react";
import { Button } from "../../../components/ui/button";
import { Input } from "../../../components/ui/input";

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
        <Button className="w-full" variant="primary">
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

## 12. 创建 Memo 编辑区占位组件

创建 `src/features/memo/components/memo-editor-panel.tsx`：

```tsx
import { Save } from "lucide-react";
import { Button } from "../../../components/ui/button";
import { Input } from "../../../components/ui/input";

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
        <Button variant="primary" size="sm">
          <Save className="h-3.5 w-3.5" />
          保存
        </Button>
      </footer>
    </section>
  );
}
```

注意这里使用 `textarea` 只是为了先验证布局。真正的富文本编辑器会在 Memo 模块章节替换为 Milkdown。

## 13. 组装 App

修改 `src/App.tsx`：

```tsx
import { AppLayout } from "./components/layout/app-layout";
import { MemoEditorPanel } from "./features/memo/components/memo-editor-panel";
import { MemoListPanel } from "./features/memo/components/memo-list-panel";

export default function App() {
  return <AppLayout list={<MemoListPanel />} detail={<MemoEditorPanel />} />;
}
```

启动前端：

```powershell
pnpm dev
```

启动 Tauri：

```powershell
pnpm tauri dev
```

## 14. 验证效果

完成本章后检查这些点：

- 应用第一屏直接进入工作界面，不出现营销页。
- 左侧导航、Memo 列表、编辑区三栏都能显示。
- 窗口高度较小时，Memo 列表内部滚动，底部用户区域不被挤走。
- 编辑区正文区域可以独立滚动。
- 浅色主题下边框、分割线、按钮、输入框对比度清楚。
- Windows 200% 缩放下，“Memo”“与我相关”“我的收藏”等文字不应该挤成竖排。
- `pnpm dev` 不应该出现 Tailwind CLI 相关错误。

## 15. 常见问题

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

## 16. 本章完成标准

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
│       ├── button.tsx
│       └── input.tsx
├── features/
│   └── memo/
│       └── components/
│           ├── memo-editor-panel.tsx
│           └── memo-list-panel.tsx
├── styles/
│   └── globals.css
└── utils/
    └── cn.ts
```

这些内容只是 UI 骨架。下一章会在这个骨架上继续实现 Memo 的数据库、接口、列表查询、保存、相关人和权限控制。
