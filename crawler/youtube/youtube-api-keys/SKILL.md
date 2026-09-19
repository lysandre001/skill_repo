---
name: youtube-api-keys
description: Configures and validates YouTube Data API v3 keys for the research crawler (multi-key pool, main-first rotation). Use when setting up .env, youtube_api_key, quota, check-keys, or API authentication—never embed secrets in code or skills.
---

# YouTube API keys

## Where keys live

- File: **project root** `.env` (与 `youtube爬虫方案/` 同级)，例如 `社交媒体爬虫/.env`
- **Never** commit `.env`, paste keys into chat, skills, YAML, CSV, or `data/raw/` JSON
- Code loads via `yt_crawler/paths.py` → `ENV_PATH` = `{PROJECT_ROOT}/.env`

## Variable names

```env
youtube_api_key_main=YOUR_KEY_HERE
youtube_api_key_1=
youtube_api_key_2=
```

- 非空条目才会加载；`main` **优先**，耗尽后按 `_1`、`_2`… 轮换
- 本地安全线约 **9500 units/key/天**（太平洋日重置）

## Validate

```bash
cd youtube爬虫方案
pip install -r requirements.txt
python main.py --check-keys
```

每 key 调用 `videos.list`（1 unit）验证可用性。

## Agent rules

1. 若用户要配 key：说明 `.env` 路径与变量名，让用户**自行粘贴**到本机 `.env`；不要生成示例 key，不要 `echo key >> .env` 带真实值
2. 若 `check-keys` 失败：检查文件是否保存、变量名是否小写 `youtube_api_key*`、网络/代理
3. 配额问题：Google Cloud Console 对照 in-process 计数；search.list 占配额大头（100/次）

## Quota reference (units)

| Endpoint | Cost |
|----------|-----:|
| search.list | 100 |
| videos.list | 1 |
| commentThreads.list | 1 |
| comments.list | 1 |
| channels.list | 1 |

Quota and endpoints: `youtube爬虫方案/docs/infra.md`.
