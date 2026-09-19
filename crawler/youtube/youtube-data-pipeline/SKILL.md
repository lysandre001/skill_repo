---
name: youtube-data-pipeline
description: Explains YouTube crawler artifacts from raw JSON through harvest CSV to robot_failure canonical export (31-column wide table). Use when locating data/raw, videos.csv, comments.csv, export_canonical, or aligning with lysandre001/robot_failure rawdata.
---

# YouTube data pipeline

## Three layers (do not mix)

| Layer | Path | One row means |
|-------|------|----------------|
| **Raw API** | `data/raw/{task_id}/{YYYY-MM-DD}/` | One HTTP response file (`search_*.json`, `videos_*.json`, `threads_*.json`, `replies_*.json`, `channels_*.json`) |
| **Harvest** | `data/output/{task_id}/` | `videos.csv` = one video; `comments.csv` = one comment (flat L1/L2) |
| **Export** | `data/export/youtube/{batch}/` | `canonical.csv` = wide table for [robot_failure](https://github.com/lysandre001/robot_failure) rawdata |

Raw 用于复现与改解析；分析用 harvest 或协作库 clean；**不要把 raw JSON 当表格主表**。

## Harvest CSV (internal schema)

**videos.csv** (subset): `video_id`, `video_url`, `title`, `description`, `published_at`, `view_count`, `comment_count`, `is_short`, `channel_id`, `channel_title`, …

**comments.csv** (subset): `comment_id`, `video_id`, `parent_id`, `text`, `like_count`, `published_at`, `is_reply`, `author_channel_id`, `author_display_name`, …

- **L1**: `is_reply=False`, `parent_id` empty  
- **L2**: `is_reply=True`, `parent_id` = top-level `comment_id`  
- 全量评论：`commentThreads` + 必要时 `comments.list` 补全；写入前按 `like_count` 排序

## Comment tree → wide table

robot_failure 宽表（31 列中文列名，与 TikTok/XHS 一致）：

- 每个一级评论 **一行**（二级列空）
- 每个二级回复 **再一行**（帖子 + 一级列重复）

导出脚本（离线，0 quota）：

```bash
cd youtube爬虫方案
python scripts/export_canonical.py
# presets: 2604-marathon, 2608-olympic
```

产出每批次：`canonical.csv`（utf-8-sig）、`COLUMN_MAP.md`、`EXPORT_QC.md`。

**Filter rule used by export**: 只导出 `comments.csv` 里至少有一条评论的 `video_id`；跳过空正文评论。

## Copy into robot_failure

```text
robot_failure/data/rawdata/youtube/2604-marathon/canonical.csv
robot_failure/data/rawdata/youtube/2608-olympic/canonical.csv
```

规范：[OPERATING.md](https://github.com/lysandre001/robot_failure/blob/main/docs/OPERATING.md) §A，[canonical_comment_schema.md](https://github.com/lysandre001/robot_failure/blob/main/data/canonical_comment_schema.md).

**Note**: `run_preprocess.py --platform youtube` may still need engineering适配（YouTube 字母数字 `帖子id`）。导出完成 = OPERATING 入库；清洗在协作库侧。

## Known batch ↔ harvest mapping (example)

| robot_failure batch | Typical harvest dir |
|---------------------|---------------------|
| `2604-marathon` | `data/output/beijing_robot_marathon_exp__2604/` |
| `2608-olympic` | `data/output/whrg_hashtag__2608/` (comments); official merged task may be video-only |

Adjust `--harvest-dir` if your task_id differs.

Mapping detail: [references/column-map.md](references/column-map.md)
