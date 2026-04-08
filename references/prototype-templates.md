# 原型模板与技术约定

P5 阶段执行本文件，生成可交互 HTML 原型。

## 技术栈

| 技术 | 用途 | 引入方式 |
|------|------|---------|
| Tailwind CSS | 视觉样式 | CDN `<script src="https://cdn.tailwindcss.com">` |
| Alpine.js | 交互逻辑 | CDN `<script src="https://cdn.jsdelivr.net/npm/alpinejs@3/dist/cdn.min.js" defer>` |
| 单 HTML 文件 | 零构建，直接预览 | — |

## 中保真模板结构

**重要：通过 Visual Companion 预览时，必须写内容片段（不含 `<!DOCTYPE>` 和 `<html>`），否则设备外壳和框架样式无法自动注入。**

### P5 原型片段模板（写入 screen_dir）

```html
<!-- P5 原型 — 内容片段，服务器自动包裹框架 -->
<div data-device-frame>
  <style>
    /* P2 Design System CSS 变量 */
    :root {
      --color-primary: #3B82F6;
      --color-secondary: #F59E0B;
      --spacing-unit: 8px;
      --radius-base: 8px;
      --duration-base: 250ms;
    }
  </style>

  <!-- 导航栏 -->
  <nav style="height:56px;background:#fff;box-shadow:0 1px 3px rgba(0,0,0,0.1);display:flex;align-items:center;padding:0 16px">
    <span style="font-size:18px;font-weight:700">App Name</span>
  </nav>

  <!-- 主内容区 — 使用 P2 设计语言的变量 -->
  <main style="padding:var(--spacing-unit);padding:calc(var(--spacing-unit) * 2)">
    <!-- 页面内容 -->
  </main>

  <!-- 底部标签栏（如果需要） -->
  <footer style="position:fixed;bottom:0;width:100%;height:56px;background:#fff;border-top:1px solid #eee;display:flex;align-items:center;justify-content:space-around">
    <span>首页</span><span>发现</span><span>我的</span>
  </footer>
</div>
```

**注意：** 使用 `x-ref` 而非 `id` 来引用 DOM 元素，避免 iPhone/Android 双屏渲染时 id 冲突。

## 中保真标准

- 布局和层次结构与最终产品一致
- 核心交互可操作：点击/切换状态/表单输入/页面切换
- 使用 P2 设计语言的色彩/字体/间距/圆角
- 使用真实文案（非 Lorem ipsum），可用模拟数据
- 不需要：后端通信、持久化、复杂动画

## 手机外壳（Device Frame）

**P5 原型自动包裹手机外壳**，已内置于 `scripts/frame-template.html`。

支持两种机型，用户可通过页面顶部按钮实时切换：

| 机型 | 屏幕尺寸 | 外壳特征 |
|------|---------|---------|
| iPhone 15 | 393×852 | 圆角 47px、Dynamic Island、底部横条 |
| Android（Pixel） | 412×915 | 圆角 36px、状态栏、底部三键导航 |

### 使用方式

**只需在 HTML 内容片段的根元素上加 `data-device-frame` 属性**，外壳自动激活：

```html
<!-- P5 原型 — 加 data-device-frame 即自动包裹手机外壳 -->
<div data-device-frame>
  <!-- 你的页面内容，正常写 -->
  <nav style="...">导航栏</nav>
  <main style="...">主内容区</main>
  <footer style="...">底部栏</footer>
</div>
```

- P2/P3 的内容**不加**任何属性 → 正常展示，无外壳
- P5 单原型**加** `data-device-frame` → 单手机外壳，左侧栏 + 设备切换
- P4 多方案对比**加** `data-device-canvas` → 多 iframe 画布并排，详见下方

### P4 多方案对比画布（data-device-canvas）

P4 阶段先把每套方案写成独立 HTML 文件（如 `scheme-a.html`、`scheme-b.html`），然后写一个画布页面汇总展示：

```html
<!-- P4 画布 — 多方案 iframe 并排对比 -->
<div data-device-canvas
     data-schemes='[
       {"file":"scheme-a.html", "title":"方案 A：极简引导", "color":"#FF6B35"},
       {"file":"scheme-b.html", "title":"方案 B：信息丰富", "color":"#2563EB"},
       {"file":"scheme-c.html", "title":"方案 C：游戏化", "color":"#059669"}
     ]'>
</div>
```

**自动生成的 UI：**
- 左侧栏：设备切换 + 方案列表（带颜色标识）+ 布局切换（1/2/3 列）
- 右侧画布：每套方案各一个手机外壳，内嵌 iframe 加载对应文件
- 设备切换时所有外壳同时变更
- 每个方案的 iframe 通过 `/files/{filename}` 加载，完全隔离

**注意：** 方案文件（scheme-a.html 等）可以是完整 HTML 文档或内容片段，因为它们在 iframe 中独立加载。

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

1. 启动服务器：
```bash
~/.claude/skills/super-pm/scripts/start-server.sh --project-dir /path/to/project
```

2. 将原型 HTML 写入服务器返回的 `screen_dir`
3. 用户通过浏览器 URL 查看和体验
4. P4 阶段：写入多个 HTML 文件（scheme-a.html、scheme-b.html），通过 Visual Companion 的选择机制让用户对比
5. P5 阶段：写入完整原型 HTML，用户直接在浏览器中体验交互流

## 项目目录结构

```
{project}/.super-pm/
  memory/user-profiles/                     # P1 用户画像（跨版本复用）
    {product-name}-audience.md
  memory/design-systems/                    # P2 设计语言（跨版本复用）
    {product-name}-design-system.md
  prototypes/{feature-name}/                # P5 最终确认的原型 HTML
    index.html                              #   主原型（完整 HTML，可独立打开）
    scheme-a.html                           #   P4 方案文件（如需保留）
  deliverables/                             # P6A 开发交付文档
  visual/                                   # Visual Companion 会话（自动管理，勿手动修改）
```

### P5 原型保存流程

1. 开发过程中：HTML 片段写入 `screen_dir`（Visual Companion 临时目录）用于浏览器预览
2. 用户确认满意后：将原型**另存为完整 HTML 文件**到 `.super-pm/prototypes/{feature-name}/`
3. 保存时拼装为完整文档（加入 `<!DOCTYPE>`、CDN 引用、Design System CSS 变量），确保独立双击可打开
4. 同时保留内容片段版本在 `screen_dir` 供 Visual Companion 持续预览
