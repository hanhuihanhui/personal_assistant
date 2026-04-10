# Frontmatter Schema

本仓库受管理文档建议使用 YAML frontmatter。

## Required Fields

- `id`: 唯一标识
- `title`: 文档标题
- `type`: 文档类型
- `status`: 当前状态
- `updated_at`: 最近更新时间，格式 `YYYY-MM-DD`

## Optional Fields

- `domain`: 所属主题域
- `tags`: 标签数组
- `source`: 来源，如 `manual`、`review`、`conversation`
- `review_after`: 下次建议复查日期
- `related`: 关联文件路径数组
- `owner`: 预留字段，当前可固定为个人

## Allowed Types

- `memory`
- `review`
- `okr`
- `knowledge`
- `capture`
- `reference`

## Status Lifecycle

推荐状态：

- `draft`
- `active`
- `archived`

原始采集信息也可以短暂使用：

- `inbox`
