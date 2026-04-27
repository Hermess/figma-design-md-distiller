# 06 截图治理规则

## 目标

保证写入 `design.md` 的截图清晰、可追溯、可长期使用，避免过宽、过扁、缩略图模糊和短效链接失效。

## 截图来源

优先使用 Figma MCP `get_screenshot`。

不得把短效 Figma 图片 URL 作为 `design.md` 的最终图片引用。必须下载或保存为本地文件，再用相对路径引用。

## 截图阈值

满足任一条件时，必须补局部截图：

```text
节点宽度 > 1440px
节点高度 > 900px
截图比例宽高比 > 4:1
截图比例高宽比 > 3:1
总览图缩放到 1024px 后文字不可读
用户明确说看不清
```

阈值判断优先使用 Figma metadata 中的节点原始尺寸，不以本地 PNG 导出后的 `1024px` 等比缩略尺寸作为唯一依据。

本地 PNG 尺寸仅用于辅助判断：

- 文件是否缺失；
- 截图是否极端横条或竖条；
- 是否与截图清单记录明显不一致；
- 用户肉眼反馈是否看不清。

如果 Figma 原始节点本来是窄条组件，例如 Alert 单行、底部操作条、Statistic 横向行，本地截图宽高比过大不自动视为错误，但必须在用途中标记为“局部条形截图”。

## 保留数量

默认上限：

```text
每个普通组件：1 张总览
每个重要组件：1 张总览 + 最多 3 张局部
每个页面 example：1 张总览 + 最多 4 张局部
design.md 直接引用截图：最多 80 张
```

超过 80 张时：

1. 主 `design.md` 保留 P0 / P1 组件和关键页面截图；
2. 低频组件截图移到 `references/screenshots/archive/` 或只登记到 manifest；
3. 不删除原始图片，避免丢失证据。

## 命名规范

文件名必须稳定、可读、全小写、用连字符。

页面：

```text
page-login-overview.png
page-framework-header-sidebar.png
page-management-list-basic.png
page-form-left-right.png
page-approval-detail-flow.png
page-dashboard-overview.png
```

组件：

```text
components-button-overview.png
components-table-density.png
components-dialog-variants.png
components-alert-description.png
components-message-placement.png
components-statistic-segment-row.png
```

## 截图资产清单

`design.md` 或 `screenshot-inventory.md` 中必须维护截图清单。

字段：

```text
截图路径
类型
对应 Figma 节点
用途
清晰度状态
备注
```

清晰度状态：

```text
清晰
总览可用
需局部补图
已补局部
模糊需重截
```

## 局部截图选择

组件局部截图优先保留：

- 状态矩阵；
- 尺寸矩阵；
- 布局变体；
- 错误 / 禁用 / 加载状态；
- 内容较长或折叠态；
- 弹窗主体和底部按钮区。

页面局部截图优先保留：

- 登录表单；
- 框架 Header + Sidebar；
- 查询区 + 表格区；
- 表单主体；
- 底部操作栏；
- 审批流；
- 看板统计卡片；
- 水印覆盖效果。

## 自检方法

交付前必须检查：

- `design.md` 中引用的截图文件是否存在；
- 总览截图是否基于 Figma 节点原始尺寸过宽过扁；
- 登录、详情、表单、看板等重要页面是否有局部截图；
- 弹窗、提示、表格、导航等重要组件是否至少有一张清晰截图；
- 图片路径是否使用相对路径；
- 是否误用了短效外链。
