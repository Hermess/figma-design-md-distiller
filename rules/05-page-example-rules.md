# 05 页面 Example 规则

## 目标

把 Figma 页面样例整理成可供 HTML 原型生成复用的页面模板规范。

页面 Example 是布局和组合模式，不是组件。

## 页面分类

默认分类：

```text
登录页面
框架页面
管理列表
填报页面
申请详情页面
审批详情页面
超市列表
超市详情
水印页面
看板页面
```

如用户新增分类，以用户分类为准。

## 页面 Example 必备字段

每个页面 example 必须包含：

```text
页面类型
适用场景
Figma 来源
总览截图
必要局部截图
ASCII 骨架
关键区域说明
依赖组件
布局规则
交互状态
原型生成注意事项
禁止事项
```

## 推荐格式

```markdown
### 7.x 页面类型

来源：Figma fileKey#nodeId
截图：./screenshots/page-example-overview.png

用途：...

ASCII 骨架：

```text
+--------------------------------------------------+
| Header                                           |
+---------+----------------------------------------+
| Sidebar | Breadcrumb / Toolbar                   |
|         +----------------------------------------+
|         | Content                                |
|         | Search / Form / Table / Detail         |
+---------+----------------------------------------+
```

依赖组件：Navigation、Button、Table、Form、Pagination

生成规则：
- 必须...
- 优先...
- 不得...
```

## ASCII 骨架规则

ASCII 骨架必须表达：

- 顶部区；
- 侧边区；
- 主内容区；
- 查询区；
- 操作区；
- 表格区；
- 表单区；
- 详情区；
- 底部操作区；
- 弹窗或水印等覆盖层。

不要把 ASCII 骨架写成自然语言段落。

## 截图规则

页面总览截图必须保留。

如果页面宽度大于 1440px、高度大于 900px，或截图缩放后细节不可读，必须补局部截图。

常见局部截图：

```text
page-login-form.png
page-framework-header-sidebar.png
page-management-list-search-table.png
page-form-left-right.png
page-detail-approval-flow.png
page-dashboard-statistics.png
```

## 页面依赖组件

每个页面 example 必须列出依赖组件。

示例：

```text
管理列表：Search Form、Button、Table、Pagination、Dialog、Message
填报页面：Form、Input、Select、DatePicker、Upload、Alert、Bottom operation bar
审批详情：Descriptions、Steps、Timeline、Button、Dialog、Popconfirm
看板页面：Statistic、Chart、Card、Filter、Table
```

## 页面生成规则

写法必须是执行规则。

示例：

```text
管理列表页面必须包含查询区、操作按钮区、表格区和分页区。
查询区默认单行展示，字段过多时使用“展开/收起”。
表格主操作放在右上角，行操作放在最右列。
不得把整个页面包成一个大卡片。
```

## 页面与组件边界

- “填报页面-左右”属于页面 example，不属于组件；
- “框架页面”属于页面 example，不属于 Navigation 组件；
- 页面中出现的 Table 可以引用 Table 契约，但不要在页面章节重复 Table 全部规则；
- 页面 example 用于告诉模型如何组合组件。

