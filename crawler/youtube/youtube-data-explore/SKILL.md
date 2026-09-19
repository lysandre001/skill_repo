---
name: youtube-data-explore
description: Explores downloaded YouTube harvest data with project notebooks (videos.csv, comments.csv, L1/L2 stats). Use when inspecting crawl results, descriptive stats, or Jupyter notebooks under youtube爬虫方案/notebooks.
---

# YouTube data explore (notebooks)

## Location

```
youtube爬虫方案/notebooks/
  comment_explore.ipynb      # 评论：L1/L2、点赞、长度、视频嵌套
  video_metadata_explore.ipynb  # 视频元数据分布
```

Requires: `pandas`, `matplotlib`, `jupyter` (install in your venv as needed).

## Run

```bash
cd youtube爬虫方案/notebooks
jupyter lab   # or: jupyter notebook
```

Use **Kernel → Restart & Run All** after changing data paths.

## Point notebooks at your task

In the first data-loading cells, set:

```python
ROOT = Path("..").resolve()
TASK_DIR = ROOT / "data/output/{task_id}"
COMMENTS_CSV = TASK_DIR / "comments.csv"
VIDEOS_CSV = TASK_DIR / "videos.csv"
```

Examples:

- `whrg_hashtag__2608` — default in `comment_explore.ipynb`
- `beijing_robot_marathon_exp__2604` — marathon experiment harvest

## What to inspect

| Question | Where |
|----------|--------|
| 爬了多少视频/评论 | `report.md` in output dir, or notebook counts |
| L1 vs L2 | `comments.csv`: `is_reply`, `parent_id` |
| 单视频线程 | filter `video_id`, sort by `like_count` |
| Short vs long | `videos.csv`: `is_short`, `duration_seconds` |
| 协作库宽表 | not in notebooks — use `data/export/youtube/*/canonical.csv` + **youtube-data-pipeline** |

## Agent rules

- Read-only on harvest CSV unless user asks to transform
- Do not load or print `.env` / API keys
- For robot_failure-wide format, use export script + pipeline skill, not notebook reimplementation
