# 07 增量合并规则

## 目标

更新已有 `design.md` 时保护用户手工修改，按 Figma 节点级别合并，而不是整章覆盖。

## 核心文件

必须维护：

```text
extraction-manifest.md
```

如果接入的是旧版 `design.md`，且不存在 `extraction-manifest.md`，必须先从以下信息反推创建 manifest：

1. `规范来源` 表中的 Figma 链接、fileKey、nodeId；
2. 截图资产清单中的截图路径和节点 ID；
3. 组件清单中的契约位置；
4. 页面 Example 分类和截图引用。

旧版接入时不要立即重写正文。先生成 manifest，再执行后续增量。

manifest 是节点级状态表，用来追踪：

- 哪些节点已抽取；
- 写入了哪个章节；
- 对应哪些截图；
- 上次内容 hash；
- 用户是否修改过；
- 是否需要更新。

## manifest 字段

推荐字段：

```text
id
figmaUrl
fileKey
nodeId
nodeName
nodeType
category
sectionPath
contractPath
screenshotPaths
status
extractionMethod
contentHash
lastExtractedAt
userModified
warnings
notes
```

## 状态值

```text
未抽取
已抽取
需更新
用户已修改
待确认分类
已拆分
已废弃
抽取失败
```

## 写入锚点

推荐在 `design.md` 中写节点锚点：

```markdown
<!-- figma-node: EnNxABhxlnzyZyo9Z7ZsvH#1140:54841 section: 6.17 hash: sha256:{contentHash} -->
...
<!-- /figma-node -->
```

如果用户不希望文档中出现注释，则只依赖 manifest 的 `sectionPath` 和 `contentHash`。

## 合并流程

```text
1. 读取现有 design.md
2. 读取 extraction-manifest.md
3. 对新输入节点做 Figma 抽取
4. 根据 nodeId 查找 manifest 记录
5. 若不存在 → 新增章节或新增 references 契约
6. 若存在 → 比对 contentHash
7. 若 hash 未变 → 自动替换节点段落
8. 若 hash 已变 → 判定用户可能修改过，不直接覆盖
9. 输出建议 patch 或新增“待确认更新”段落
10. 更新 manifest 状态
```

## 用户修改保护

满足任一条件时，必须视为用户已修改：

```text
design.md 中锚点段落 hash 与 manifest 不一致
同一 sectionPath 标题存在但内容明显不同
用户明确说“我改过这个段落”
```

处理：

- 不直接覆盖；
- 在 `design-md-check.md` 中列出差异；
- 可生成建议文本；
- manifest 标记 `userModified: true`、`status: 用户已修改`。

## 新增节点

新增节点按分类写入：

```text
组件 → 组件清单 + 组件契约
页面 → 页面 Example
Token → 设计 Token
截图补充 → 截图资产清单 + 对应章节截图引用
无法分类 → 待补与变更记录
```

## 废弃节点

如果 Figma 节点失效或用户要求移除：

- 不默认删除正文；
- 在 manifest 标记 `status: 已废弃`；
- 在 `design.md` 变更记录说明；
- 只有用户明确要求时才删除对应章节。

## 分卷合并

如果组件契约总数超过 20 条：

```text
P0 / P1 高频组件 → 保留在 design.md
P2 低频或长契约 → 写入 references/contracts/
design.md 只保留索引和相对路径
```

分卷文件也必须在 manifest 中记录 `contractPath`。

## 冲突处理

冲突优先级：

```text
用户明确指令 > 用户已修改正文 > manifest 记录 > Figma metadata > 模型推断
```

无法判断时不要覆盖，写入待确认。
