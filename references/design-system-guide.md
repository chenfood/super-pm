# 设计语言制定指南

P2 阶段执行本文件，从用户画像推导设计方向，制定 8 维度 Design System。

## 浏览器可视化规则

<HARD-GATE>
P2 的设计维度选择 **禁止用纯文字/表格呈现**。必须启动 Visual Companion 服务器，将选项渲染为真实 HTML 页面，用户在浏览器中看到实际视觉效果后才能做选择。

如果用文字表格替代浏览器渲染，等于让用户凭想象选设计方案 — 这违背了"先懂人"的核心哲学。
</HARD-GATE>

### P2 启动步骤（进入 P2 时必须先执行）

```bash
# 1. 启动 Visual Companion 服务器
~/.claude/skills/super-pm/scripts/start-server.sh --project-dir {当前项目路径}

# 2. 服务器返回 JSON，记录 screen_dir 和 state_dir
# {"port":52341,"url":"http://localhost:52341","screen_dir":"...","state_dir":"..."}

# 3. 告诉用户打开浏览器 URL
```

如果服务器启动失败，先排查原因（端口占用、Node.js 未安装等），**不要降级为文字模式**。

### 每个维度的执行循环

```
对每个维度（1-8）：
  1. 用 Write 工具将 HTML 文件写入 screen_dir（如 dim1-colors.html）
     - HTML 必须渲染真实视觉效果（颜色方块、真实字号、实际间距等）
     - 使用 Visual Companion 的 .options + .option + data-choice 交互机制让用户点选
     - 不要写完整 HTML 文档，只写内容片段（服务器自动包裹框架）
  2. 在终端发一句话：「维度 N：xxx，请在浏览器中查看并选择偏好方案。」
     - 不要在终端重复渲染选项内容
     - 不要一次发多个维度
  3. 等待用户反馈（终端回复 + 读取 state_dir/events 的点击事件）
  4. 确认后进入下一个维度
```

### 各维度 HTML 渲染要求

| 维度 | 文件名 | 渲染内容 |
|------|--------|---------|
| 1 色彩 | `dim1-colors.html` | 每个方案渲染：色板方块（主色+辅色+语义色）+ 一组示例 UI（按钮+卡片+标签）实际应用该配色 |
| 2 字体 | `dim2-typography.html` | 用同一段真实文案，并排展示不同字号层级（H1/H2/正文/辅助）的实际效果 |
| 3 间距 | `dim3-spacing.html` | 同一组卡片列表，分别用不同基准间距排列，直观对比疏密 |
| 4 圆角 | `dim4-radius.html` | 同一组组件（按钮+卡片+输入框），分别应用不同圆角值 |
| 5 阴影 | `dim5-shadow.html` | 同一张卡片并排，分别无阴影/轻阴影/卡片阴影 |
| 6 图标 | `dim6-icons.html` | 同一组 SVG 图标（5个常用），分别线性/面性/双色 |
| 7 动效 | `dim7-motion.html` | 按钮 hover 动画，不同速度，用户可悬停体验 |
| 8 组件 | `dim8-components.html` | 综合前 7 维度，渲染完整组件库预览（按钮组/卡片/表单/导航栏）作为整合确认 |

**第 8 维度是整合确认环节** — 合成前 7 个选择的完整预览。如不满意可回退调整单个维度。

### HTML 片段示例（色彩维度）

```html
<h2>色彩体系</h2>
<p class="subtitle">选择最符合产品调性的配色方案</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>温暖橙金</h3>
      <div style="display:flex;gap:8px;margin:12px 0">
        <div style="width:48px;height:48px;border-radius:8px;background:#FF6B35"></div>
        <div style="width:48px;height:48px;border-radius:8px;background:#F5A623"></div>
        <div style="width:48px;height:48px;border-radius:8px;background:#10B981"></div>
        <div style="width:48px;height:48px;border-radius:8px;background:#EF4444"></div>
      </div>
      <button style="background:#FF6B35;color:white;border:none;padding:10px 24px;border-radius:8px;font-size:14px">示例按钮</button>
      <p style="margin-top:8px;font-size:13px;color:#666">活力/行动力/赚钱感</p>
    </div>
  </div>
  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content">
      <h3>信任蓝</h3>
      <div style="display:flex;gap:8px;margin:12px 0">
        <div style="width:48px;height:48px;border-radius:8px;background:#2563EB"></div>
        <div style="width:48px;height:48px;border-radius:8px;background:#60A5FA"></div>
        <div style="width:48px;height:48px;border-radius:8px;background:#10B981"></div>
        <div style="width:48px;height:48px;border-radius:8px;background:#EF4444"></div>
      </div>
      <button style="background:#2563EB;color:white;border:none;padding:10px 24px;border-radius:8px;font-size:14px">示例按钮</button>
      <p style="margin-top:8px;font-size:13px;color:#666">专业/可靠/安全</p>
    </div>
  </div>
</div>
```

以上示例展示了正确的渲染方式 — 用真实颜色方块和实际 UI 组件，而非文字描述颜色值。每个维度都应按此标准渲染。

## 用户画像 → 设计方向推导

| 用户特征 | 设计方向 | 调性关键词 |
|---------|---------|-----------|
| 年轻人群（18-25） | 活泼、高对比、大胆用色 | 潮流/活力/个性 |
| 职场人群（25-40） | 克制、专业、信息密度高 | 效率/专业/可信 |
| 银发族（55+） | 大字体、高对比、少层级 | 简洁/清晰/安心 |
| 高端消费 | 留白多、深色系、精致细节 | 奢华/品质/从容 |
| 儿童/亲子 | 圆润、明亮、大触控区 | 趣味/安全/直觉 |
| 技术/开发者 | 暗色主题、等宽字体、信息密集 | 极客/高效/掌控 |

## 8 维度规范

### 1. 色彩体系

为每个维度提供 2-3 个预设方向，用户选择或自定义：

| 方向 | 主色 | 辅色 | 语义色 | 适用 |
|------|------|------|--------|------|
| 活力 | 亮蓝 #3B82F6 / 珊瑚 #FF6B6B | 互补暖色 | 绿成功/红错误/黄警告 | 年轻/社交 |
| 专业 | 深蓝 #1E3A5F / 石墨 #374151 | 同色系浅色 | 同上 | 商务/工具 |
| 自然 | 森绿 #059669 / 大地 #92400E | 米白/沙色 | 同上 | 健康/户外 |

### 2. 字体层级

| 层级 | 紧凑型 | 舒适型 | 宽松型 |
|------|--------|--------|--------|
| H1 标题 | 24px/700 | 32px/700 | 40px/700 |
| H2 副标题 | 18px/600 | 24px/600 | 28px/600 |
| 正文 | 14px/400 | 16px/400 | 18px/400 |
| 辅助文字 | 12px/400 | 14px/400 | 14px/400 |

### 3. 间距系统

| 基准 | 节奏 | 适用 |
|------|------|------|
| 4px | 4/8/12/16/24/32 | 信息密集型（管理后台/数据面板） |
| 8px | 8/16/24/32/48/64 | 通用平衡型（大多数产品） |
| 12px | 12/24/36/48/72 | 呼吸感重（品牌官网/内容阅读） |

### 4. 圆角策略

| 风格 | 值 | 适用 |
|------|-----|------|
| 锐利 | 0-2px | 专业/技术/新闻 |
| 柔和 | 6-8px | 通用/友好 |
| 圆润 | 12-16px | 年轻/活泼/亲子 |
| 胶囊 | 全圆角 | 按钮/标签/芯片 |

### 5. 阴影层级

| 层级 | 用途 | 值 |
|------|------|-----|
| 无阴影 | 扁平风格，靠边框区分 | none |
| 轻阴影 | 悬浮提示、轻微层次 | 0 1px 3px rgba(0,0,0,0.1) |
| 卡片阴影 | 卡片、弹窗、下拉 | 0 4px 12px rgba(0,0,0,0.15) |

### 6. 图标风格

| 风格 | 特征 | 适用 |
|------|------|------|
| 线性 | 1.5-2px 描边，不填充 | 工具/专业/极简 |
| 面性 | 实心填充 | 导航/强调/移动端 |
| 双色 | 主色描边 + 辅色局部填充 | 活泼/品牌强化 |

### 7. 动效节奏

| 节奏 | 时长 | 缓动 | 适用 |
|------|------|------|------|
| 快速 | 150-200ms | ease-out | 工具/高频操作 |
| 舒适 | 250-350ms | ease-in-out | 通用 |
| 从容 | 400-600ms | cubic-bezier(0.4,0,0.2,1) | 品牌/内容/转场 |

### 8. 组件基础样式

定义以下核心组件的默认外观（基于上述 7 个维度）：

- **按钮**：主按钮/次按钮/文字按钮/危险按钮 — 含 hover/active/disabled 状态
- **卡片**：内边距/阴影/圆角/hover 效果
- **表单**：输入框高度/边框/聚焦态/错误态
- **导航**：高度/背景/选中态/响应式断点

## 输出格式

Design System 产出为两部分：

1. **CSS 变量**（供原型直接使用）：
```css
:root {
  --color-primary: #3B82F6;
  --color-secondary: #F59E0B;
  --font-size-base: 16px;
  --spacing-unit: 8px;
  --radius-base: 8px;
  --shadow-card: 0 4px 12px rgba(0,0,0,0.15);
  --duration-base: 250ms;
}
```

2. **Tailwind 配置片段**（供高保真原型扩展）：
```js
// tailwind.config extend
colors: { primary: '#3B82F6', secondary: '#F59E0B' },
borderRadius: { base: '8px' },
spacing: { unit: '8px' }
```
