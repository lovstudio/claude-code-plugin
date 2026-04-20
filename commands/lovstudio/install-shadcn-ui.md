---
allowed-tools: [Bash, Read, Write, Edit, Glob, Grep]
description: Install and configure shadcn/ui with Lovstudio Warm Academic theme
version: "1.0.0"
author: "公众号：手工川"
---
# Install shadcn/ui

为前端项目安装 shadcn/ui 并适配 Lovstudio 暖学术风格（陶土色 #CC785C / 暖米 #F9F9F7 / 炭灰 #181818），自动处理 shadcn CLI 的常见踩坑（preset 覆盖品牌色、global border reset 冲突、Button hover bug 等）。

## Prerequisites

- Vite + React + TypeScript（其它框架走 `pnpm dlx shadcn@latest init -t <next|react-router|astro|...>`，本命令默认 vite）
- Tailwind CSS v4（`@tailwindcss/vite` 已装）；如未装，先执行 Step 0
- 包管理器 pnpm（npm/yarn 自行替换）
- 网络：境外源走代理 `export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7891`

## Process

### Step 0: 环境检查

```bash
# 检测包管理器
PM="pnpm"
[ -f "yarn.lock" ] && PM="yarn"
[ -f "package-lock.json" ] && PM="npm"

# 检测 Tailwind v4
if ! grep -q "@tailwindcss/vite\|tailwindcss.*\"\\^4\"" package.json; then
  echo "需要先安装 Tailwind v4"
  $PM add -D tailwindcss @tailwindcss/vite
  # 同时在 vite.config.ts 加上 plugin（手动确认）
fi

# 检测已存在
[ -f "components.json" ] && echo "shadcn 已初始化，跳到 Step 3 仅安装组件"
```

### Step 1: shadcn init

**关键**：必须显式指定 `-t vite -b radix -p nova`，否则会进入交互式询问。

```bash
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7891
$PM dlx shadcn@latest init -t vite -b radix -p nova --yes
```

**会发生**：
- 生成 `components.json`、`src/lib/utils.ts`
- 装依赖：`clsx`、`tailwind-merge`、`radix-ui`(umbrella)、`lucide-react`、`tw-animate-css`、`@fontsource-variable/geist`
- 在 `src/index.css` 末尾追加 OKLCH 单色 `:root` / `.dark` 块（**会和你的品牌色冲突，Step 2 必须重写**）

### Step 2: 重写 index.css —— 把 shadcn token 映射到 Lovstudio 暖学术色

⚠️ **不要直接删 nova 追加的内容然后保留旧的 `@theme` 块**——shadcn v4 的组件依赖 `@theme inline { --color-primary: var(--primary); ... }` 桥接，必须按以下结构重写整个文件：

```css
@import 'tailwindcss';
@import "tw-animate-css";

@custom-variant dark (&:is(.dark *));

/* 字体 */
@import url('https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;400;500;600;700&display=swap');

/*
 * Lovstudio Warm Academic Theme (暖学术风格)
 * 主色陶土 #CC785C · 暖米背景 #F9F9F7 · 炭灰文字 #181818
 */
:root {
  --background: #F9F9F7;
  --foreground: #181818;
  --card: #FFFFFF;
  --card-foreground: #181818;
  --popover: #FFFFFF;
  --popover-foreground: #181818;
  --primary: #CC785C;
  --primary-foreground: #FFFFFF;
  --secondary: #F0EEE6;
  --secondary-foreground: #181818;
  --muted: #E8E6DC;
  --muted-foreground: #87867F;
  --accent: #F0EEE6;
  --accent-foreground: #181818;
  --destructive: #DC2626;
  --destructive-foreground: #FFFFFF;
  --border: #D5D3CB;
  --input: #D5D3CB;
  --ring: #CC785C;

  --sidebar: #F0EEE6;
  --sidebar-foreground: #181818;
  --sidebar-primary: #CC785C;
  --sidebar-primary-foreground: #FFFFFF;
  --sidebar-accent: rgba(0, 0, 0, 0.04);
  --sidebar-accent-foreground: #181818;
  --sidebar-border: #D5D3CB;
  --sidebar-ring: #CC785C;

  --chart-1: #CC785C;
  --chart-2: #87867F;
  --chart-3: #629A90;
  --chart-4: #97B5D5;
  --chart-5: #D2BEDF;

  --radius: 0.625rem;

  /* Legacy aliases —— 保留给项目里旧的 *.css 文件 */
  --color-text-main: #181818;
  --color-text-faded: #87867F;
  --color-bg-main: #F9F9F7;
  --color-bg-ivory-medium: #F0EEE6;
  --color-bg-clay: #CC785C;
  --color-bg-dark: #141413;
  --color-bg-faded: #3D3D3A;
  --color-border-default: #D5D3CB;
  --color-separator: rgba(135, 134, 127, 0.1);
  --color-swatch-cloud-light: rgba(232, 230, 220, 0.3);

  --font-family-sans: 'Fira Code', ui-sans-serif, system-ui, sans-serif;
  --font-family-serif: Georgia, 'Times New Roman', serif;

  --spacing-xs: 0.25rem;
  --spacing-s: 0.5rem;
  --spacing-m: 1rem;
  --spacing-l: 1.5rem;
  --spacing-xl: 2rem;
  --radius-s: 0.25rem;
  --radius-m: 0.5rem;
  --radius-l: 1rem;
}

.dark {
  --background: #141413;
  --foreground: #F9F9F7;
  --card: #1F1F1D;
  --card-foreground: #F9F9F7;
  --popover: #1F1F1D;
  --popover-foreground: #F9F9F7;
  --primary: #CC785C;
  --primary-foreground: #FFFFFF;
  --secondary: #3D3D3A;
  --secondary-foreground: #F9F9F7;
  --muted: #3D3D3A;
  --muted-foreground: #A5A39B;
  --accent: #3D3D3A;
  --accent-foreground: #F9F9F7;
  --destructive: #EF4444;
  --destructive-foreground: #FFFFFF;
  --border: rgba(255, 255, 255, 0.1);
  --input: rgba(255, 255, 255, 0.15);
  --ring: #CC785C;

  --sidebar: #1F1F1D;
  --sidebar-foreground: #F9F9F7;
  --sidebar-primary: #CC785C;
  --sidebar-primary-foreground: #FFFFFF;
  --sidebar-accent: rgba(255, 255, 255, 0.06);
  --sidebar-accent-foreground: #F9F9F7;
  --sidebar-border: rgba(255, 255, 255, 0.1);
  --sidebar-ring: #CC785C;
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);
  --color-destructive: var(--destructive);
  --color-destructive-foreground: var(--destructive-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);
  --color-chart-1: var(--chart-1);
  --color-chart-2: var(--chart-2);
  --color-chart-3: var(--chart-3);
  --color-chart-4: var(--chart-4);
  --color-chart-5: var(--chart-5);
  --color-sidebar: var(--sidebar);
  --color-sidebar-foreground: var(--sidebar-foreground);
  --color-sidebar-primary: var(--sidebar-primary);
  --color-sidebar-primary-foreground: var(--sidebar-primary-foreground);
  --color-sidebar-accent: var(--sidebar-accent);
  --color-sidebar-accent-foreground: var(--sidebar-accent-foreground);
  --color-sidebar-border: var(--sidebar-border);
  --color-sidebar-ring: var(--sidebar-ring);

  --font-sans: 'Fira Code', ui-sans-serif, system-ui, sans-serif;
  --font-heading: var(--font-sans);

  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
}

@layer base {
  * { box-sizing: border-box; }
  html, body {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    font-family: var(--font-family-sans);
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
  body { @apply text-foreground; }
}
```

### Step 3: 移除全局 border reset（关键）

很多老项目在 `index.css` 或全局样式里有 `* { border: none; outline: none }`。**这会让 shadcn 的 Input/Select/Card 等组件全部丢边框**，必须删掉。

```bash
# 检查
grep -rn "border:\s*none\|outline:\s*none" src/ --include="*.css" | grep -v "^.*ui/"
```

如果在 `* { ... }` 选择器里发现这两条，删除。需要某些组件去边框时改成在该组件上局部声明。

### Step 4: 安装常用组件

```bash
$PM dlx shadcn@latest add button switch select input label separator card dialog dropdown-menu tabs --yes
```

按需增减。生成在 `src/components/ui/`，**这是源码不是 npm 包**，可以直接改。

### Step 5: 修 nova preset 的 Button hover bug

shadcn nova preset 生成的 `src/components/ui/button.tsx` 里 default 变体的 hover 写成 `[a]:hover:bg-primary/80`——只对 `<a>` 标签生效，普通 `<button>` 没有 hover 效果。改：

```bash
# 在 src/components/ui/button.tsx 里
# default: "bg-primary text-primary-foreground [a]:hover:bg-primary/80"
# →
# default: "bg-primary text-primary-foreground hover:bg-primary/90"
```

### Step 6: 验证

```bash
$PM type-check 2>&1 | tail
$PM build 2>&1 | tail
```

build 通过即可。

## 使用示例

```tsx
import { Button } from '@/components/ui/button'
import { Switch } from '@/components/ui/switch'
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select'

<Button variant="default">主操作</Button>
<Button variant="secondary">次要</Button>
<Button variant="destructive">危险</Button>

<Switch checked={v} onCheckedChange={setV} />

<Select value={x} onValueChange={setX}>
  <SelectTrigger className="w-36"><SelectValue /></SelectTrigger>
  <SelectContent>
    <SelectItem value="a">A</SelectItem>
  </SelectContent>
</Select>
```

## Arguments

- `--components <list>`: 自定义安装的组件列表（默认 `button switch select input label separator card dialog dropdown-menu tabs`）
- `--no-theme`: 跳过 Step 2，保留 nova 默认 OKLCH 单色配色
- `--keep-border-reset`: 跳过 Step 3，保留 `* { border: none }`（不推荐）
- `--skip-button-fix`: 跳过 Step 5

## Notes

- **alias 路径**：默认 `@/components`、`@/lib/utils`，要求 `tsconfig.json` 已配 `"paths": { "@/*": ["src/*"] }` 且 `vite.config.ts` 已配 `resolve.alias`。
- **Tailwind v4**：所有 token 通过 `@theme inline` 桥接，**不要再写 `tailwind.config.js`**，shadcn v4 已不依赖。
- **品牌迁移**：如果项目用其它品牌色，只改 Step 2 里的 `:root` / `.dark` 颜色值，shadcn token 名不变。
- **新增组件**：随时 `pnpm dlx shadcn@latest add <name>`，会读取 `components.json` 配置自动生成。
- **icon 库**：本命令默认 `lucide-react`（nova preset 自带）；如全局禁用 emoji，用 lucide 替代 Unicode 符号。

ARGUMENTS: $ARGUMENTS
