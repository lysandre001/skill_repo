# Harvest → canonical (31 cols)

| Canonical | Source |
|-----------|--------|
| 帖子id | videos.video_id |
| 帖子链接 | videos.video_url or watch URL |
| 用户id / 帖子用户名 | channel_id / channel_title |
| 帖子类型 | `video` |
| 帖子标题 / 正文 / 时间 | title / description / published_at |
| 帖子话题 | tags |
| 帖子IP属地 | channel_country |
| 帖子评论数 / 点赞数 | comment_count / like_count |
| 帖子转发数 / 收藏数 | (empty) |
| 一级评论* | comments where is_reply=False |
| 二级评论* | comments where is_reply=True; 一级列重复 |

Script: `youtube爬虫方案/scripts/export_canonical.py`
