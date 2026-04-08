---
name: super-pm
description: Use when designing product interactions from scratch or optimizing existing ones — covers user profiling, design system creation, task flow mapping, multi-option interaction schemes with browser-previewed prototypes. Triggers on "交互设计", "interaction design", "设计方案", "原型", "用户体验优化", "UX redesign"
---

# Interaction Design

## Overview

核心哲学：**先懂人、再定调、后画图** — 任何交互产出必须建立在用户画像和设计系统之上，杜绝凭感觉出方案。

## Onboarding（首次触发时必须展示）

Skill 被调用时，**第一条回复必须展示以下新人指引**，然后再开始 P0：

---

**Super PM — 让产品经理拥有设计超能力**

我是你的交互设计搭档。你负责产品判断，我负责把想法变成**可体验的高保真原型 + 完整交互说明**，让你一个人就能交出专业设计团队级别的产出。

**你会得到：**
- 可在浏览器中体验的交互原型（iPhone/Android 手机外壳预览）
- 完整的交互说明文档（页面流转、状态变化、异常处理、动效描述）
- 可直接交给开发团队或自己动手实现

**流程：** P1 用户画像 → P2 设计语言（浏览器选择）→ P3 任务流 → P4 多套方案对比 → P5 原型+交互说明 → P6 交付/开发

**准备：** 有个大致想法就行，我逐步引导。版本升级可复用之前的画像和设计语言。

准备好了吗？告诉我你想设计什么。

---

展示以上内容后等待用户回复，然后进入 P0。

## Interaction Rules

- **每次只问一个问题**，不要一条消息多个问题
- **选择题优先**（3-5 项），用户只需回复序号
- **必须标注推荐**：推荐项给理由，不推荐项说原因

<HARD-GATE>

**禁止在缺少用户画像或设计系统时生成交互方案/原型。**

合法来源（二选一）：
1. 当前会话中通过 P1 + P2 新建
2. 从 `.super-pm/memory/` 复用已有记录，且用户已确认

未满足条件时，停止并引导用户先完成 P1/P2。

</HARD-GATE>

## Mode Detection

- **新建模式**（"从零设计"、"新功能"）：P1-P5 完整流程
- **优化模式**（"优化"、"改版"、"转化率低"）：优先复用 memory，P3 聚焦问题环节，P4 针对性改进 + 对照组

## Process

```dot
digraph interaction_design {
    rankdir=TB;
    node [shape=box, style="rounded,filled", fillcolor="#E8F0FE"];

    P0 [label="P0: Scan Memory"];
    has_records [shape=diamond, label="Has records?"];
    confirm_reuse [shape=diamond, label="User confirms\nreuse?"];
    P1 [label="P1: User Profiling"];
    P2 [label="P2: Design System"];
    P3 [label="P3: Task Flow Mapping"];
    P4 [label="P4: Interaction Schemes"];
    P5 [label="P5: Prototype & Verify"];
    P6_ask [shape=diamond, label="Handoff or\nSelf-dev?"];
    P6A [label="P6A: Design Handoff\n(标注+资源+说明)"];
    P6B [label="P6B: Dev Planning\n(invoke writing-plans)"];

    P0 -> has_records;
    has_records -> confirm_reuse [label="Yes"];
    has_records -> P1 [label="No"];
    confirm_reuse -> P3 [label="Reuse"];
    confirm_reuse -> P1 [label="New/Adjust"];
    P1 -> P2;
    P2 -> P3;
    P3 -> P4;
    P4 -> P5;
    P5 -> P6_ask;
    P6_ask -> P6A [label="交付团队"];
    P6_ask -> P6B [label="自己开发"];
}
```

## Checklist

- [ ] **P0 Memory Scan** — 扫描 `.super-pm/memory/`，检查可复用的画像和设计语言
- [ ] **P1 User Profiling** — 用户画像 + 核心场景 → 详见 [design-system-guide.md](references/design-system-guide.md) §用户画像
- [ ] **P2 Design System** — **必须先启动 Visual Companion 服务器**，8维度逐个在浏览器中渲染选项，禁止纯文字呈现 → 详见 [design-system-guide.md](references/design-system-guide.md)
- [ ] **P3 Task Flow** — 任务流 + 情感地图 → 参考 [interaction-patterns.md](references/interaction-patterns.md)
- [ ] **P4 Schemes** — 多套交互方案（≥2套） → 详见 [interaction-patterns.md](references/interaction-patterns.md)
- [ ] **P5 Prototype** — 原型 HTML（手机外壳预览）+ 交互说明文档（p5-interaction-spec.md），保存到 `.super-pm/artifacts/{feature}/` → 详见 [prototype-templates.md](references/prototype-templates.md)
- [ ] **P6 Landing** — 询问用户"交付团队 or 自己开发？"→ 走 P6A 或 P6B

## 项目目录结构

```
{project}/.super-pm/
  memory/user-profiles/      # P1 用户画像（跨功能复用，P0扫描）
  memory/design-systems/     # P2 设计语言（跨功能复用，P0扫描）
  artifacts/{feature}/       # P3-P6 全部物料，一个功能一个文件夹
  visual/                    # Visual Companion 会话（自动管理）
```

## P0 Memory

扫描 `.super-pm/memory/` 下 `user-profiles/` 和 `design-systems/`。有记录则展示摘要，询问用户：复用 / 微调 / 全新。P1/P2 完成后自动保存。

## P4 Scheme Dimensions

从复杂度梯度/用户群体/设计哲学/组合混搭中智能选取对比维度，需说明理由。**硬约束**：最少2套方案。

## Visual Companion（必须执行）

<HARD-GATE>
P2-P5 的可视化内容必须通过浏览器呈现。禁止用终端文字/表格替代。
</HARD-GATE>

**进入 P2 前必须执行以下启动步骤：**

1. 用 Bash 工具运行：`~/.claude/skills/super-pm/scripts/start-server.sh --project-dir {当前项目路径}`（WSL/远程环境加 `--host 0.0.0.0`）
2. 服务器返回 JSON，从中提取 `url`、`screen_dir`、`state_dir`，保存为变量
3. 告诉用户打开浏览器访问返回的 URL
4. 等用户确认已打开浏览器后，才开始 P2

**每次可视化输出：** 用 Write 工具将 HTML 片段（不要 `<!DOCTYPE>`）写入 `screen_dir`，终端只提示用户去浏览器查看，读取 `state_dir/events` 获取点击选择。详见 [design-system-guide.md](references/design-system-guide.md) 和 [prototype-templates.md](references/prototype-templates.md)。

## P6 Landing

P5 原型确认后，询问用户：**A) 交付团队** 还是 **B) 自己开发？**

### P6A：设计交付（交给团队）

输出**开发交付文档**到 `.super-pm/artifacts/{feature}/`：设计标注（间距/颜色/字号）、交互说明（状态流转/异常处理）、资源清单（Design System + 原型 HTML）、切图要求、实施优先级。

### P6B：自己开发（全栈 PM）

调用 **superpowers:writing-plans** skill，基于 P2 Design System + P5 原型 + P3 任务流生成前端实施计划。

## Red Flags

| 借口 | 真相 |
|------|------|
| "不需要画像" | 自以为清楚 ≠ 用户视角清楚 |
| "先出原型再说" | 没画像的原型是盲人摸象 |
| "跳过任务流" | 页面是任务流的投影，源头不能跳 |
| "一套方案就够" | 没对比没决策依据 |
| "文字表格展示设计" | 设计是视觉的，必须浏览器渲染 |
