---
name: youtube-crawler-workflow
description: Runs the YouTube Data API v3 research crawler end-to-end (discover, videos, comments, resume, reports). Use when crawling YouTube for a study, running main.py, phases, test mode, or continuing after quota stop.
---

# YouTube crawler workflow

## Prerequisites

1. Keys in parent `.env` — follow skill **youtube-api-keys**
2. Task YAML — follow skill **youtube-task-yaml**
3. `cd youtube爬虫方案 && pip install -r requirements.txt`

## End-to-end flow

```
YAML → CrawlPlan → YouTubeCrawler.run_plan()
  discover     search.list (merged + time slices)
  videos       videos.list (batch 50) + channels.list (publishers)
  comments     commentThreads.list + comments.list (reply补全) + channels (commenters)
→ data/raw/{task_id}/{date}/*.json   (API 原样，先写后解析)
→ data/output/{task_id}/videos.csv | comments.csv
→ optional: scripts/export_canonical.py → data/export/youtube/{batch}/
```

## Commands (typical)

```bash
python main.py --check-keys

# 链路测试：每关键词 2 视频 + 每视频 2 条顶级评论
python main.py --config experiment/whrg_hashtag.yaml --test

# 全量（读 YAML discovery 默认）
python main.py --config configs/beijing_robot_marathon.yaml

# 分阶段 / 续跑（state 自动跳过已完成 slice/video）
python main.py --config PATH.yaml --phase discover_videos
python main.py --config PATH.yaml --phase comments
python main.py --config PATH.yaml --phase all
```

## Phase gate

| `--phase` | discover | videos | comments |
|-----------|:--------:|:------:|:--------:|
| `all` | ✓ | ✓ | ✓ |
| `discover_videos` | ✓ | ✓ | |
| `discover` / `videos` / `comments` | 单项 | | |

## Resume state

```
data/state/{task_id}/
  seen_videos.txt
  comments_done.txt
  channels_done.txt
data/state/{task_id}.json    # 切片进度、comments_disabled 等
```

配额耗尽会停并保存；次日同命令续跑。

## After crawl

```bash
python main.py --report-only --config PATH.yaml
python main.py --corpus-report          # API 规模估算（读 raw 缓存）
```

## Agent checklist

- [ ] Keys validated (`--check-keys`) without exposing values
- [ ] Correct YAML path (`configs/` vs `experiment/`)
- [ ] `--test` only for pipeline smoke, not corpus sizing
- [ ] Do not confuse `data/output/comment_samples/` (demo) with full harvest
- [ ] Export for robot_failure: skill **youtube-data-pipeline**

Deep reference: project `youtube爬虫方案/README.md`, `docs/infra.md`.
