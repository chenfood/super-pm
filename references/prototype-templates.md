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
<!-- P5 原型 — 手机外壳 + 右侧交互说明 -->
<div data-device-frame>
  <!-- 原型内容 -->
  <nav style="...">导航栏</nav>
  <main style="...">主内容区</main>
  <footer style="...">底部栏</footer>

  <!-- 交互说明（隐藏，自动提取到右侧栏） -->
  <div data-interaction-spec style="display:none">
    <h3>页面流转</h3>
    <p>首页 → 详情页 → 确认页</p>
    <h3>交互动作</h3>
    <table>
      <tr><th>触发</th><th>动作</th><th>反馈</th><th>异常</th></tr>
      <tr><td>点击提交</td><td>发送请求</td><td>loading → 成功</td><td>失败提示重试</td></tr>
    </table>
    <h3>动效</h3>
    <ul><li>页面进入：右滑入 300ms ease-in-out</li></ul>
  </div>
</div>
```

- P2/P3 **不加**任何属性 → 正常展示
- P5 单原型 **`data-device-frame`** → 手机外壳 + 右侧交互说明栏
- P4 多方案 **`data-device-canvas`** → iframe 画布，点击方案显示对应说明
- 交互说明写在 `<div data-interaction-spec>` 里，frame 自动提取到右侧栏展示

### P4 多方案对比画布（data-device-canvas）

P4 阶段先把每套方案写成独立 HTML 文件（如 `scheme-a.html`、`scheme-b.html`），然后写一个画布页面汇总展示：

```html
<!-- P4 画布 — 多方案 iframe 并排对比 + 交互说明 -->
<div data-device-canvas
     data-schemes='[
       {"file":"scheme-a.html", "title":"方案 A：极简引导", "color":"#FF6B35"},
       {"file":"scheme-b.html", "title":"方案 B：信息丰富", "color":"#2563EB"}
     ]'>

  <!-- 每个方案的交互说明，用 data-spec-for 关联文件名 -->
  <div data-spec-for="scheme-a.html" style="display:none">
    <h3>方案 A 交互说明</h3>
    <p>极简三步引导流程...</p>
    <h3>交互动作</h3>
    <table><tr><th>触发</th><th>动作</th></tr><tr><td>...</td><td>...</td></tr></table>
  </div>
  <div data-spec-for="scheme-b.html" style="display:none">
    <h3>方案 B 交互说明</h3>
    <p>信息丰富的仪表盘布局...</p>
  </div>
</div>
```

**三栏布局：**
- 左侧栏：设备切换 + 方案列表 + 布局切换 + Specs 按钮
- 中间画布：手机外壳 iframe 并排
- **右侧栏：点击方案标题或列表项 → 显示该方案的交互说明**

**注意：** 方案文件在 iframe 中独立加载，交互说明写在画布页面的 `data-spec-for` 元素里（非 iframe 内部）。

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

## 交互说明文档（p5-interaction-spec.md）

P5 除了输出原型 HTML，**必须同步输出一份交互说明文档**，保存为 `artifacts/{feature}/p5-interaction-spec.md`。

### 文档结构

```markdown
# {功能名} 交互说明

## 页面清单

| 页面 | 入口 | 核心功能 |
|------|------|---------|
| 首页 | 启动默认 | ... |
| 详情页 | 首页点击卡片 | ... |

## 页面流转

用 Mermaid 或文字描述页面跳转关系：
首页 → 详情页 → 确认页 → 结果页
         ↘ 分享弹窗

## 逐页交互说明

### {页面名}

**状态列表：**
- 默认态：...
- 空状态：...
- 加载中：...
- 错误态：...

**交互动作：**

| 触发 | 动作 | 反馈 | 异常处理 |
|------|------|------|---------|
| 点击提交按钮 | 发送请求 | 按钮 loading → Toast 成功 | 网络失败 → Toast 提示重试 |
| 下拉刷新 | 重新请求列表 | 刷新动画 | 超时 → 提示检查网络 |
| 左滑卡片 | 显示删除按钮 | 卡片位移动画 | — |

**动效说明：**
- 页面进入：从右滑入，300ms ease-in-out
- 弹窗出现：底部弹起 + 背景蒙层淡入
- 列表加载：骨架屏 → 内容淡入

## 全局规则

- 手势：支持/不支持哪些手势
- 键盘：输入框聚焦时页面上推行为
- 无网络：统一兜底页/Toast
- 权限：相机/定位/通知的请求时机和拒绝处理
```

### 输出要求

- 每个页面都要覆盖：默认态、空状态、加载中、错误态
- 每个可交互元素都要写明：触发条件、执行动作、反馈效果、异常处理
- 动效要写明：触发时机、动画类型、时长、缓动函数
- 和原型 HTML 保持一致 — 文档描述的就是原型中能体验到的

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
  memory/                                   # 跨功能复用（P0 扫描）
    user-profiles/{product}-audience.md
    design-systems/{product}-design-system.md
  artifacts/{feature-name}/                 # 一个功能的全部物料
    p3-taskflow.html                        #   任务流程图
    p3-emotion-map.html                     #   情感地图
    p4-scheme-a.html                        #   方案 A
    p4-scheme-b.html                        #   方案 B
    p4-comparison.html                      #   方案对比画布
    p5-prototype.html                       #   最终原型（完整 HTML，双击可打开）
    p5-interaction-spec.md                  #   交互说明文档
    p6-handoff.md                           #   交付文档（P6A）
  visual/                                   # Visual Companion 会话（自动管理）
```

### 物料保存规则

**开发过程中（P2-P5 预览）：** HTML 片段写入 `screen_dir`（Visual Companion 临时目录）。

**每个 Phase 完成后：** 将该 Phase 的产出物保存到 `artifacts/{feature}/`：
- P3 完成 → 保存 `p3-taskflow.html` 和 `p3-emotion-map.html`
- P4 完成 → 保存各方案文件 `p4-scheme-*.html` 和对比画布 `p4-comparison.html`
- P5 确认 → 将原型拼装为**完整 HTML 文件**（加入 `<!DOCTYPE>`、CDN 引用、Design System CSS 变量），保存为 `p5-prototype.html`，确保独立双击可打开
- P6A → 保存 `p6-handoff.md`
