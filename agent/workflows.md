# Workflows

## Capture Workflow

### Input

- 用户刚提供的碎片信息
- 还不能判断应长期保存到哪里

### Read

- `templates/inbox-capture.md`
- `memory/index.md`

### Write

- 先写到 `inbox/`
- 若信息已稳定，再归档进 `memory/`

## Memory Update Workflow

### Input

- 已有 memory 条目需要补充
- review 产出了长期有效结论

### Read

- `templates/memory-entry.md`
- 对应主题域下的现有文档
- 相关 `reviews/` 或 `okr/` 文件

### Write

- 更新 `memory/areas/...` 中的 canonical 文档
- 补充 `related` 链接

## Review Workflow

### Input

- 用户要求回顾进度、梳理阻塞、明确下一步

### Read

- `reviews/templates/review-session.md`
- 相关 `memory/` 文档
- 当前季度 `okr/` 文档（如果任务与目标有关）

### Write

- 在 `reviews/logs/<year>/` 创建一条 review 记录
- 如果结论长期有效，更新 `memory/`
- 如果影响目标，更新 `okr/`

## OKR Workflow

### Input

- 用户要求起草季度 OKR
- 用户要求更新季度 check-in 或复盘

### Read

- `okr/templates/okr-draft.md`
- `okr/templates/okr-checkin.md`
- `okr/templates/okr-review.md`
- 当前相关 memory 和 recent reviews

### Write

- 起草到 `okr/<year>/<quarter>/`
- 跟进写到 `checkins/`
- 复盘写到 `review.md`
