---
name: youtube-task-yaml
description: Authors YouTube crawler task YAML (keywords, time window, discovery, harvest, output paths). Use when creating configs/*.yaml, experiment/*.yaml, or changing search queries and crawl scope for the YouTube Data API crawler.
---

# YouTube task YAML

## File locations

| 类型 | 路径 | 加载方式 |
|------|------|----------|
| 正式课题 | `youtube爬虫方案/configs/*.yaml` | `main.py --task` 或默认任务列表 |
| 实验/专题 | `youtube爬虫方案/experiment/*.yaml` | **必须** `python main.py --config experiment/....yaml` |

解析：`yt_crawler/config.py` → `TaskConfig` / `CrawlPlan`；CLI 可覆盖 discovery、phase、test。

## Minimal template

```yaml
task_id: my_study__2604
title: 简短标题
published_after: "2026-04-11T00:00:00Z"
published_before: "2026-04-26T23:59:59Z"

keywords:
  - id: branch_a
    q: '#hashtag | "phrase search"'
  - id: branch_b
    q: (robot | humanoid) marathon

discovery:
  mode: merged              # merged | per_keyword | top500_per_keyword
  order: viewCount
  slice: day                # day | 12h | none
  cap_per_window: 500
  split_on_cap: true

harvest:
  videos: true
  comments: true
  channels_publishers: true
  channels_commenters: true

test:
  max_videos_per_keyword: 2
  max_top_comments: 2

output:
  dir: data/output/my_study__2604
  test_suffix: _test

report:
  harvest: data/output/my_study__2604/report.md
  include_in_summary: true   # 实验常设 false
```

## Field semantics

- **keywords[].q**: YouTube `search.list` 布尔语法（`|` = OR，引号 = 短语）；全量默认 **merged** 合并为一条 query 省配额
- **discovery.mode**: `merged` + 按日切片；`--test` 强制 `per_keyword`；`top500_per_keyword` 每词高热 Top500
- **harvest**: 分 phase 时仍可只开 videos 或 comments
- **output.dir**: 决定 `videos.csv` / `comments.csv` 目录

## CLI overrides (one-off)

```bash
python main.py --config configs/foo.yaml --phase discover_videos
python main.py --config configs/foo.yaml --discovery top500_per_keyword --cap 200
python main.py --config configs/foo.yaml --test
```

业务背景与关键词来源：项目内 `docs/研究需求.md`。

More examples: [references/task-example.yaml](references/task-example.yaml)
