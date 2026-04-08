# 原型模板与技术约定

P5 阶段执行本文件，生成可交互 HTML 原型。

## 技术栈

| 技术 | 用途 | 引入方式 |
|------|------|---------|
| Tailwind CSS | 视觉样式 | CDN `<script src="https://cdn.tailwindcss.com">` |
| Alpine.js | 交互逻辑 | CDN `<script src="https://cdn.jsdelivr.net/npm/alpinejs@3/dist/cdn.min.js" defer>` |
| 单 HTML 文件 | 零构建，直接预览 | — |

## 中保真模板结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{页面标题}}</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3/dist/cdn.min.js"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          // 从 P2 Design System 注入
          colors: { primary: 'var(--color-primary)', secondary: 'var(--color-secondary)' },
        }
      }
    }
  </script>
  <style>
    /* P2 Design System CSS 变量注入 */
    :root {
      --color-primary: #3B82F6;
      --color-secondary: #F59E0B;
      --spacing-unit: 8px;
      --radius-base: 8px;
      --duration-base: 250ms;
    }
  </style>
</head>
<body class="bg-gray-50 min-h-screen" x-data="app()">

  <!-- 页面内容 -->

  <script>
    function app() {
      return {
        // Alpine.js 状态和交互逻辑
      }
    }
  </script>
</body>
</html>
```

## 中保真标准

- 布局和层次结构与最终产品一致
- 核心交互可操作：点击/切换状态/表单输入/页面切换
- 使用 P2 设计语言的色彩/字体/间距/圆角
- 使用真实文案（非 Lorem ipsum），可用模拟数据
- 不需要：后端通信、持久化、复杂动画

## 高保真升级项

在用户确认中保真方案后，可选择性添加：

| 升级项 | 实现方式 |
|--------|---------|
| 页面转场 | Alpine.js `x-transition` + CSS transition |
| 微交互 | hover scale/opacity、focus ring、active 按下效果 |
| 加载动画 | CSS @keyframes skeleton shimmer |
| 滚动触发 | Intersection Observer + Alpine.js |
| 响应式 | Tailwind 断点 `sm:` `md:` `lg:` |
| 暗色模式 | Tailwind `dark:` + 媒体查询或手动切换 |

## Visual Companion 集成

原型文件通过 Visual Companion 服务器预览：

1. 启动服务器（复用 brainstorming 脚本）：
```bash
~/.claude/skills/super-pm/scripts/start-server.sh --project-dir /path/to/project
```

2. 将原型 HTML 写入服务器返回的 `screen_dir`
3. 用户通过浏览器 URL 查看和体验
4. P4 阶段：写入多个 HTML 文件（scheme-a.html、scheme-b.html），通过 Visual Companion 的选择机制让用户对比
5. P5 阶段：写入完整原型 HTML，用户直接在浏览器中体验交互流

## Memory 目录结构

```
{project}/.super-pm/memory/
  user-profiles/
    {product-name}-audience.md    # 用户画像
  design-systems/
    {product-name}-design-system.md  # 设计语言（含 CSS 变量和 Tailwind 配置）
```
