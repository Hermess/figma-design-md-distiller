# 压测报告：figma-design-md-distiller

压测时间：2026-04-27

## 使用素材

本次使用前期沉淀的数字浙江 PC 端 Figma 组件库规范素材：

- `design.md`：`/Users/caifeiya/Documents/Playground/figma-prototype-spec/设计规范/design.md`
- 截图目录：`/Users/caifeiya/Documents/Playground/figma-prototype-spec/设计规范/screenshots/`
- Figma 来源：数字浙江 PC 端基线版、数字资源超市系统、组件管理系统看板。

## 测试范围

本轮是技能包规则压测，不是重新生成完整 `design.md`。

覆盖：

- Skill 文件完整性；
- README 与 zip 打包完整性；
- 旧版 `design.md` 接入；
- 截图引用完整性；
- 页面 example 分类覆盖；
- 组件契约数量和文档膨胀阈值；
- Figma MCP 降级路径。

## 结果摘要

| 测试项 | 结果 | 说明 |
| --- | --- | --- |
| Skill 文件完整性 | 通过 | `SKILL.md`、`README.md`、8 个 rules、6 个 templates、4 个 checklists 均存在 |
| Figma 来源识别 | 通过 | 旧素材中识别到 20 行 Figma 来源 |
| 页面 example 覆盖 | 通过 | 登录、框架、管理列表、填报、申请详情、审批详情、水印、超市列表、超市详情、看板共 10 类均存在 |
| 截图引用完整性 | 通过 | `design.md` 中 53 个唯一截图引用全部存在 |
| 截图数量阈值 | 通过 | 主文档直接引用 70 次截图，唯一截图 53 张，低于 80 张上限 |
| Figma MCP 降级路径 | 通过 | Message 节点 `get_design_context` 失败，但 `get_metadata` + `get_screenshot` 成功，符合降级规则 |
| 旧版 manifest 接入 | 需补 | 旧素材没有 `extraction-manifest.md`，技能包应先反推生成 manifest |
| 文件膨胀阈值 | 命中 | 旧 `design.md` 约 84335 字符，超过 30000 字符分卷阈值 |
| 截图阈值规则 | 已修正 | 压测发现不能用本地 PNG 的 1024px 缩略尺寸直接判断超高，已改为优先使用 Figma 原始节点尺寸 |

## 关键数据

```text
design.md 行数：2112
design.md 字符数：84335
设计规范目录大小：3.9M
Figma 来源行：20
顶层组件契约章节：20
页面 example 类目：10
ASCII 骨架代码块：11
截图文件数：53
design.md 唯一截图引用：53
design.md 截图引用总次数：70
缺失截图：0
```

## MCP 抽取压测

测试节点：

```text
fileKey: EnNxABhxlnzyZyo9Z7ZsvH
nodeId: 1140:54841
节点：Message 全局提示
```

结果：

```text
get_design_context：失败，提示当前未选中 layer
get_metadata：成功，返回主 frame 5624:50188、主题组 5624:50271、布局定位 9955:55868 等结构
get_screenshot：成功，返回 890x1024 PNG 短效资源
```

结论：

技能包中“`get_design_context` 失败时不终止，改走 `get_metadata` + `get_screenshot`，并在 manifest 写 warning”的规则有效。

## 暴露的问题与修复

### 1. 旧素材没有 manifest

问题：

旧 `design.md` 已经有来源表、截图清单和组件章节，但没有 `extraction-manifest.md`。

处理：

已在 `rules/07-incremental-merge.md` 增加旧版接入规则：先从规范来源、截图资产清单、组件清单、页面 Example 反推 manifest，再做增量更新。

### 2. 文档超过分卷阈值

问题：

旧 `design.md` 约 84335 字符，明显超过技能包设定的 30000 字符阈值。

处理：

技能包会将其判定为需要分卷。后续正式运行时，应保留主 `design.md` 的组件索引和高频契约，把低频或超长组件契约拆到 `references/contracts/`。

### 3. 截图阈值需要区分原始节点和导出图片

问题：

本地 PNG 经 Figma MCP 等比导出后，很多总览图高度为 1024px。如果直接用本地图片高度判断，会误报超高。

处理：

已在 `rules/06-screenshot-policy.md` 明确：阈值优先使用 Figma metadata 中的节点原始尺寸；本地 PNG 尺寸只做辅助检查。对于 Alert 单行、Statistic 横条、底部操作条等条形局部截图，宽高比过大不自动视为错误，但必须标记用途。

## 结论

技能包通过基本压测，可以用于刚刚这批 Figma 组件库素材的后续增量维护。

实际投入使用前建议先跑一次“旧规范接入”：

1. 从现有 `design.md` 反推生成 `extraction-manifest.md`；
2. 按 30000 字符阈值拆分长组件契约；
3. 保留主文档的高频组件和页面 example；
4. 将低频组件或超长契约移入 `references/contracts/`；
5. 输出 `design-md-check.md`。

