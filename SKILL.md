---
name: figma-design-md-distiller
description: 将 Figma 组件库、页面样例、视觉规范和截图资产蒸馏为中文 design.md，供 HTML 原型生成技能读取。适用于新建设计规范、增量补充 Figma 节点、补截图、修复模糊截图、检查 design.md 规范质量。
---

# Figma Design MD Distiller

## 目标

把 Figma 组件库、页面 example、视觉 token、截图和用户补充说明，蒸馏成面向模型执行的中文 `design.md`。

本 Skill 的产物不是设计师说明书，也不是 Figma 转代码结果，而是供后续 HTML 原型生成 Skill 读取的可执行 UI 规范。

## 触发场景

当用户出现以下意图时使用本 Skill：

- “把 Figma 组件库整理成 design.md”
- “从 Figma 组件库蒸馏设计规范”
- “补充这些 Figma 组件到组件清单和契约”
- “这个页面 example 加入规范”
- “截图看不清，重新处理”
- “检查这个 design.md 有没有问题”
- “把组件库转成 HTML 原型生成可用的规范”

## 输入

### 必须输入

- 至少一个 Figma 文件链接、节点链接、fileKey + nodeId，或已有 `design.md`。

### 可选输入

- 用户指定的页面分类；
- 用户指定的组件分类；
- 产品设计气质；
- 原型生成优先级；
- 截图命名偏好；
- 已存在的 `design.md`、`extraction-manifest.md`、`screenshots/`；
- 后续原型生成 Skill 的使用约束。

## 输出

标准输出目录由用户指定；若用户未指定，优先放在当前工作区的设计规范目录中。

标准产物包括：

- `design.md`：主规范，给下游原型生成模型读取；
- `extraction-manifest.md`：Figma 节点级抽取状态与合并记录；
- `screenshots/`：保留的清晰截图；
- `references/contracts/`：当组件契约过多时拆分出的长契约；
- `design-md-check.md`：质量检查报告。

## 工作流索引

执行时按任务类型加载对应规则：

1. 先读 `rules/01-input-routing.md` 判断任务类型；
2. 涉及 Figma 链接时读 `rules/02-figma-extraction.md`；
3. 新建或改写 `design.md` 时读 `rules/03-design-md-schema.md`；
4. 写组件清单或组件契约时读 `rules/04-component-contract-rules.md`；
5. 写页面 example 时读 `rules/05-page-example-rules.md`；
6. 处理截图时读 `rules/06-screenshot-policy.md`；
7. 更新已有规范时读 `rules/07-incremental-merge.md`；
8. 交付前必须读 `rules/08-quality-check.md`。

## Figma 依赖

涉及 Figma 链接、nodeId、截图、metadata、组件上下文时，必须使用 Figma MCP。

推荐顺序：

1. 读取本地 `figma` Skill 的 MCP 约定；
2. 对精确节点优先调用 `get_design_context`；
3. 若失败或过大，调用 `get_metadata` 获取结构；
4. 对主节点和关键子节点调用 `get_screenshot`；
5. 用截图和 metadata 共同蒸馏规范。

`get_design_context` 失败不代表任务失败。只要 `get_metadata` 和 `get_screenshot` 成功，仍可继续生成 `design.md`，但必须在 manifest 中记录降级原因。

## 核心原则

- 全文默认中文；
- 面向 HTML 原型生成，不写空泛审美描述；
- 组件契约必须可执行，包含 class、尺寸、子结构、状态、禁止事项；
- 页面 example 必须和组件清单分离；
- 超宽、超高、过扁截图必须补局部截图；
- 已存在 `design.md` 时默认增量合并，保护用户手工修改；
- 组件契约超过 20 条时启动分卷，避免主 `design.md` 过大；
- 所有 Figma 节点必须进入 manifest，不能只凭聊天记录记忆。

## 目录建议

```text
设计规范/
  design.md
  extraction-manifest.md
  design-md-check.md
  screenshots/
  references/
    contracts/
    screenshots/
      archive/
```

