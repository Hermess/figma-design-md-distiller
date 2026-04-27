# figma-design-md-distiller

`figma-design-md-distiller` 是一个 Codex Skill，用于把 Figma 组件库、页面样例、视觉规范和截图资产蒸馏成中文 `design.md`，供后续 HTML 原型生成 Skill 读取。

它不直接生成业务页面代码，而是生成一份模型可执行的 UI 规范。

## 适用场景

- 从 Figma 组件库生成 `design.md`；
- 将新增 Figma 组件补入组件清单和组件契约；
- 将页面 example 归类为登录页、框架页、管理列表、填报页、详情页、看板页等模板；
- 处理截图模糊、页面过宽过扁、局部细节看不清的问题；
- 检查现有 `design.md` 是否适合给 HTML 原型生成 Skill 使用；
- 维护 Figma 节点到规范章节的增量合并记录。

## 安装

把整个目录放到 Codex skills 目录：

```bash
~/.codex/skills/figma-design-md-distiller/
```

目录中必须包含入口文件：

```text
SKILL.md
```

安装后，在新的 Codex 会话中可以通过自然语言触发，例如：

```text
用 figma-design-md-distiller 把这些 Figma 链接整理成 design.md
```

## 目录结构

```text
figma-design-md-distiller/
  SKILL.md
  README.md
  PRESSURE_TEST.md
  rules/
    01-input-routing.md
    02-figma-extraction.md
    03-design-md-schema.md
    04-component-contract-rules.md
    05-page-example-rules.md
    06-screenshot-policy.md
    07-incremental-merge.md
    08-quality-check.md
  templates/
    design.md.template
    extraction-manifest.template.md
    component-contract.template.md
    page-example.template.md
    screenshot-inventory.template.md
    design-md-check.template.md
  checklists/
    component-coverage.md
    page-example-coverage.md
    screenshot-quality.md
    final-review.md
```

## 核心产物

默认产物建议放在项目的设计规范目录：

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

主要文件说明：

| 文件 | 作用 |
| --- | --- |
| `design.md` | 主规范，供 HTML 原型生成模型读取 |
| `extraction-manifest.md` | 记录 Figma 节点、章节、截图、hash 和合并状态 |
| `screenshots/` | 本地清晰截图资产 |
| `references/contracts/` | 当组件契约过多时存放分卷契约 |
| `design-md-check.md` | 质量检查报告 |

技能包根目录中的 `PRESSURE_TEST.md` 记录了使用真实数字浙江 Figma 组件库素材进行的压测结果。

## 工作流

1. 判断用户任务类型：新建、增量、补截图、质量检查；
2. 使用 Figma MCP 读取 `get_design_context`、`get_metadata` 和 `get_screenshot`；
3. 将节点分类为组件契约、页面 example、视觉 token、截图补充或待确认；
4. 按截图治理规则保存总览图和局部图；
5. 写入或增量合并 `design.md`；
6. 更新 `extraction-manifest.md`；
7. 执行质量检查，输出风险项和待确认项。

## 关键规则

### 组件契约

每个组件必须包含：

- class；
- 尺寸；
- 子结构；
- 状态变体；
- 使用场景；
- 生成规则；
- 禁止事项；
- Figma 来源和截图。

### 页面 Example

页面 example 不属于组件契约。默认分类包括：

- 登录页面；
- 框架页面；
- 管理列表；
- 填报页面；
- 申请详情页面；
- 审批详情页面；
- 超市列表；
- 超市详情；
- 水印页面；
- 看板页面。

每个页面 example 必须包含 ASCII 骨架、依赖组件、布局规则和截图引用。

### 截图治理

满足以下条件之一时必须补局部截图：

- 节点宽度大于 `1440px`；
- 节点高度大于 `900px`；
- 截图宽高比大于 `4:1`；
- 截图高宽比大于 `3:1`；
- 用户明确反馈看不清。

截图阈值优先依据 Figma metadata 中的原始节点尺寸。本地 PNG 经 MCP 导出后可能被等比缩放到 `1024px`，不能只用导出图片尺寸判断是否超宽或超高。

主 `design.md` 直接引用截图上限为 `80` 张，超过后低频截图应移入 `references/screenshots/archive/` 或仅登记到 manifest。

### 增量合并

已有 `design.md` 时默认增量更新，不做全量覆盖。

`extraction-manifest.md` 负责记录：

- Figma 节点；
- 写入章节；
- 截图路径；
- 内容 hash；
- 用户是否手工修改；
- 节点状态。

如果发现用户手工改过对应段落，Skill 必须生成建议 patch，不得直接覆盖。

如果接入的是旧版 `design.md`，且没有 `extraction-manifest.md`，Skill 必须先从规范来源表、截图资产清单、组件清单和页面 Example 反推 manifest，再执行增量更新。

## 压测记录

已使用前期沉淀的数字浙江 PC 端 Figma 组件库素材做过规则压测。

关键结果：

- 旧 `design.md` 约 `84335` 字符，命中分卷阈值；
- 识别到 `20` 行 Figma 来源；
- 页面 example 覆盖 `10` 类；
- `53` 个唯一截图引用全部存在；
- `Message` 节点复现 `get_design_context` 失败，但 `get_metadata` + `get_screenshot` 成功，验证了 MCP 降级路径；
- 压测后修正了截图阈值规则和旧规范 manifest 接入规则。

详见 `PRESSURE_TEST.md`。

## 常用提示词

新建规范：

```text
用 figma-design-md-distiller 从这些 Figma 链接生成中文 design.md，输出到 ./设计规范。
```

增量补组件：

```text
用 figma-design-md-distiller 把这个 Figma 节点加入组件清单和组件契约，保护我已有的 design.md 修改。
```

补页面样例：

```text
用 figma-design-md-distiller 把这个页面作为“填报页面”example 加入 design.md。
```

修复截图：

```text
用 figma-design-md-distiller 检查登录页截图是否清晰，不清晰就补局部截图并更新截图清单。
```

质量检查：

```text
用 figma-design-md-distiller 检查这个 design.md 是否适合给 HTML 原型生成 skill 使用。
```

## 依赖

本 Skill 依赖 Codex 的 Figma MCP 能力。

推荐 MCP 调用顺序：

1. `get_design_context`
2. `get_metadata`
3. `get_screenshot`

当 `get_design_context` 失败时，只要 `get_metadata` 和 `get_screenshot` 成功，仍可继续蒸馏规范，但需要在 manifest 中记录降级原因。

## 版本说明

当前版本聚焦 PC 端管理后台、数字政务系统、组件库规范、页面模板和 HTML 原型生成场景。
