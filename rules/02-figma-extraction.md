# 02 Figma 抽取规则

## 目标

用 Figma MCP 从链接中抽取可写入 `design.md` 的结构、视觉、组件、页面和截图信息。

## 基本输入

每个 Figma 节点必须解析出：

```text
figmaUrl
fileKey
nodeId
nodeName
nodeType
sourceFileName
```

如果 URL 中 node-id 是 `1140-54841`，写入 manifest 时必须转换为 `1140:54841`。

## MCP 调用顺序

### 精确节点

```text
1. get_design_context(fileKey, nodeId)
2. get_screenshot(fileKey, nodeId)
3. 如果 get_design_context 失败：
   3.1 get_metadata(fileKey, nodeId)
   3.2 从 metadata 中找到主 frame、component、variant 子节点
   3.3 对关键子节点再次 get_screenshot
```

### 页面集合节点

```text
1. get_metadata(fileKey, nodeId)
2. 根据子节点名称识别页面样例
3. 对集合总览 get_screenshot
4. 对每个关键页面子节点 get_screenshot
5. 页面过宽、过高或过扁时补局部截图
```

### 组件集合节点

```text
1. get_metadata(fileKey, nodeId)
2. 识别 COMPONENT_SET、COMPONENT、FRAME demo 区域
3. 对主组件矩阵 get_screenshot
4. 对关键状态、尺寸、布局变体补局部截图
5. 写入组件契约
```

## 降级规则

`get_design_context` 可能因为选择状态、节点过大、权限或 MCP 限制失败。失败时不要停止。

只要以下任意组合成功，就可以继续蒸馏：

- `get_metadata` + `get_screenshot`
- 父节点 `get_metadata` + 子节点 `get_screenshot`
- 用户提供截图 + Figma metadata

但必须在 manifest 中记录：

```text
extractionMethod: metadata+screenshot
warnings: get_design_context failed: {简要原因}
```

## 抽取字段

### 组件节点

必须尽量抽取：

- 组件名称；
- 组件类型；
- 变体维度；
- 尺寸；
- 圆角；
- 边框；
- 背景色；
- 字号；
- 图标位置；
- 内部结构；
- 状态；
- 禁用、hover、active、loading、error 等交互态；
- 与其他组件的组合关系；
- 截图路径。

### 页面节点

必须尽量抽取：

- 页面类型；
- 页面用途；
- 整体布局；
- 顶部、侧边、内容区、底部操作区；
- 查询区；
- 表格区；
- 表单区；
- 详情信息区；
- 审批流 / 时间线 / 步骤条；
- 空状态、错误状态、水印、弹窗等；
- 依赖组件；
- 截图路径。

### 视觉 Token 节点

必须尽量抽取：

- 主色；
- 辅助色；
- 状态色；
- 文本色；
- 背景色；
- 分割线；
- 字体；
- 字号；
- 字重；
- 行高；
- 间距；
- 圆角；
- 阴影。

## 命名归一

Figma 原名可保留，但写入规范时要补充中文可读名称。

示例：

```text
Dialog / Modal → Dialog 对话框
Message → Message 全局提示
Bottom operation bar → 底部操作条
Statistic → Statistic 统计组件
```

## 页面与组件边界

- 页面样例不进入组件清单；
- 组件 demo 不进入页面 example；
- 页面中出现的组件可写入“依赖组件”；
- 组件集合中出现的示例页面只作为使用场景，不写入页面 example。

## 记录要求

每个输入 Figma 节点都必须写入 manifest，即使最后没有进入 `design.md` 正文。

manifest 中至少记录：

```text
nodeId
nodeName
category
status
sectionPath
screenshotPaths
extractionMethod
warnings
```
