# 01 输入路由规则

## 目标

判断用户当前请求属于哪类任务，并决定要加载哪些规则文件、读取哪些已有产物、是否需要 Figma MCP。

## 任务类型

### 新建规范

触发词：

- 从 Figma 生成 design.md
- 新建设计规范
- 把组件库蒸馏成规范
- 生成完整 design.md

处理：

1. 创建 `design.md`；
2. 创建 `extraction-manifest.md`；
3. 创建 `screenshots/`；
4. 读取 Figma 节点；
5. 按 `03-design-md-schema.md` 写主规范。

### 增量更新

触发词：

- 补充这个组件
- 加入这个页面
- 继续读取这些链接
- 更新现有 design.md
- 把这里的组件加入组件清单和契约

处理：

1. 查找现有 `design.md`；
2. 查找或创建 `extraction-manifest.md`；
3. 读取新增 Figma 节点；
4. 依照 `07-incremental-merge.md` 节点级合并；
5. 不得章节级覆盖用户已修改内容。

### 截图修复

触发词：

- 截图有问题
- 看不清楚
- 模糊
- 截图太小
- 登录页截图不清晰
- 重新截图

处理：

1. 定位对应 Figma nodeId；
2. 判断是否超宽、超高、过扁；
3. 重新截总览或补局部截图；
4. 更新截图资产清单；
5. 更新 manifest 的 screenshotPaths。

### 质量检查

触发词：

- 检查一下
- 有没有问题
- 自检
- 帮我 review 这个 design.md
- 看看规范能不能给原型生成用

处理：

1. 读取 `design.md`；
2. 读取 manifest；
3. 检查截图引用；
4. 检查组件契约完整度；
5. 检查页面 example 完整度；
6. 输出 `design-md-check.md` 或直接给出检查结论。

### 方案设计

触发词：

- 先出方案
- 先整体设计
- 不要生成文件
- 等我确认

处理：

只输出方案，不写文件。

## 节点类型分类决策树

分类必须按以下顺序执行：

```text
1. 用户显式标注类型？
   是 → 直接采用用户分类。

2. 节点类型是 COMPONENT 或 COMPONENT_SET？
   是 → 组件契约。

3. 节点名包含页面类关键词？
   关键词：登录、框架、管理列表、填报、详情、申请、审批、超市、看板、水印、页面、Page
   是 → 页面 Example。

4. 节点名包含视觉规范关键词？
   关键词：Color、Typography、Font、Spacing、Shadow、Token、颜色、字体、字重、间距、圆角、阴影
   是 → 设计 Token。

5. 节点名包含常见组件关键词？
   关键词：Button、Input、Select、Table、Navigation、Menu、Tabs、Modal、Dialog、Message、Alert、Popconfirm、Statistic、Pagination
   是 → 组件规范或组件补充。

6. 节点是 FRAME 且内部包含多个页面级画板？
   是 → 页面 Example 集合。

7. 以上均不命中？
   写入 manifest，status=pending，等待用户确认。
```

## 用户输入优先级

1. 用户显式分类高于自动分类；
2. 用户后续纠正高于前一次分类；
3. Figma 节点名只作为辅助证据；
4. 不确定时不要硬写入正式章节，先放 pending；
5. 页面 example 不得写入组件契约，组件 demo 不得写成页面 example。

## 必问信息

以下内容无法稳定从 Figma 自动提取，首次生成完整规范时必须询问或使用默认值并标记待确认：

- 产品设计气质；
- 原型生成优先级；
- 业务系统类型；
- 是否偏向管理后台、运营工作台、门户、看板或移动端；
- 下游原型生成 Skill 的读取方式。

如果用户正在连续补充 Figma 链接，不要中断询问；先记录为“待用户确认”。

