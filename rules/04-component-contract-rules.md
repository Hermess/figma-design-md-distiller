# 04 组件契约规则

## 目标

把 Figma 组件蒸馏成 HTML 原型生成模型可执行的组件契约。

## 组件契约必备字段

每个组件契约必须包含：

```text
组件名称
组件定位
HTML class
尺寸
子结构
状态变体
使用场景
生成规则
禁止事项
截图引用
Figma 来源
```

缺少任意一项时，应在 `design-md-check.md` 中标记。

## 推荐格式

```markdown
### 6.x ComponentName 中文名

来源：Figma fileKey#nodeId
截图：./screenshots/components-component-name-overview.png

| 项 | 契约 |
| --- | --- |
| class | `.dz-component` |
| 尺寸 | 高度 32px / 40px，圆角 4px |
| 子结构 | `.dz-component__prefix`、`.dz-component__content`、`.dz-component__suffix` |
| 状态 | `.is-hover`、`.is-active`、`.is-disabled`、`.is-error` |
| 使用场景 | 用于... |
| 生成规则 | 必须... |
| 禁止事项 | 不得... |
```

## class 命名

默认使用项目级前缀：

```text
.dz-
```

示例：

```text
.dz-btn
.dz-table
.dz-form
.dz-dialog
.dz-alert
.dz-message
.dz-popconfirm
.dz-bottom-operation-bar
.dz-statistic
```

如果用户已有组件库 class 规范，以用户规范为准。

## 状态命名

统一使用：

```text
.is-hover
.is-active
.is-selected
.is-disabled
.is-loading
.is-error
.is-success
.is-warning
.is-info
.is-expanded
.is-collapsed
```

## 变体描述

组件有变体时必须拆成清晰维度：

```text
尺寸：small / medium / large
类型：primary / default / text / link / danger
状态：default / hover / active / disabled / loading
布局：horizontal / vertical / compact / full
语义：success / warning / error / info
```

不要写成“有很多种样式”。

## 正反例

禁止：

```text
该按钮提供了丰富的视觉层次，适用于多种业务场景。
```

要求：

```text
主按钮使用 `.dz-btn.dz-btn-primary`。
高度 32px 或 40px，圆角 4px。
禁用态增加 `.is-disabled`，opacity=0.4。
按钮内部仅允许文本、左图标、loading 图标，不允许嵌套表格、表单或复杂布局。
```

## 组件清单

组件清单必须和组件契约一一对应。

推荐字段：

```text
组件名
中文名
类别
Figma 节点
契约位置
截图
原型生成优先级
状态
```

状态值：

```text
已完成
需补截图
需补契约
待确认分类
已拆分到 references
```

## 组件优先级

默认优先级：

```text
P0：Button、Input、Select、Table、Form、Pagination、Navigation、Dialog
P1：Tabs、Tree、Upload、DatePicker、Alert、Message、Popconfirm、Statistic
P2：特殊业务组件、低频展示组件、复杂图表组合
```

P0 和 P1 必须尽量写入主 `design.md`。P2 可拆分到 `references/contracts/`。

## 禁止事项

- 不得只复制 Figma 节点名；
- 不得只贴截图不写契约；
- 不得把页面模板写成组件；
- 不得把组件 demo 写成业务页面；
- 不得在同一组件下混写多个不相关组件；
- 不得用短效 Figma 图片 URL 作为最终截图引用；
- 不得省略禁用、错误、加载等关键状态。

