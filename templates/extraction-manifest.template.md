# extraction-manifest.md

## 说明

本文件记录 Figma 节点到 `design.md` 的抽取、截图和增量合并状态。

## 节点清单

| id | Figma 节点 | 节点名 | 类型 | 分类 | 写入位置 | 契约文件 | 截图 | 状态 | 抽取方式 | hash | 最后抽取 | 用户修改 | 备注 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| {{id}} | {{fileKey}}#{{nodeId}} | {{nodeName}} | {{nodeType}} | {{category}} | {{sectionPath}} | {{contractPath}} | {{screenshotPaths}} | {{status}} | {{extractionMethod}} | {{contentHash}} | {{lastExtractedAt}} | {{userModified}} | {{notes}} |

## 状态说明

- 未抽取：已登记但尚未读取 Figma；
- 已抽取：已写入规范；
- 需更新：Figma 或用户输入有新变化；
- 用户已修改：正文内容被用户改过，不可自动覆盖；
- 待确认分类：自动分类不可靠；
- 已拆分：契约已移至 references；
- 已废弃：节点不再作为有效规范来源；
- 抽取失败：MCP 或权限失败。

