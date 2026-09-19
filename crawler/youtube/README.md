# YouTube crawler skills

面向 **YouTube Data API v3** 研究爬虫（典型仓库：`社交媒体爬虫/youtube爬虫方案`）。五套 skill 分工如下；按任务只加载需要的 skill。

| Skill | 目录 | 何时用 |
|-------|------|--------|
| 全流程 | [`youtube-crawler-workflow/`](youtube-crawler-workflow/) | 从零跑爬取、续跑、分 phase、测试 vs 全量 |
| API 密钥 | [`youtube-api-keys/`](youtube-api-keys/) | 配置 / 校验 `youtube_api_key*`，多 key 轮换 |
| 任务 YAML | [`youtube-task-yaml/`](youtube-task-yaml/) | 写关键词、时间窗、discovery、harvest、output |
| 数据链路 | [`youtube-data-pipeline/`](youtube-data-pipeline/) | `data/raw` → `output` → `export`（含 robot_failure 宽表） |
| Notebook | [`youtube-data-explore/`](youtube-data-explore/) | 用 notebooks 查看 videos / comments |

**安全**：所有 skill 禁止写入或提交真实 API key；密钥只放在本机 `.env`，且勿提交 git。

```bash
git clone https://github.com/lysandre001/skill_repo.git
SKILLS_ROOT="$HOME/.cursor/skills"
cp -R skill_repo/crawler/youtube/youtube-crawler-workflow "$SKILLS_ROOT/"
cp -R skill_repo/crawler/youtube/youtube-api-keys "$SKILLS_ROOT/"
cp -R skill_repo/crawler/youtube/youtube-task-yaml "$SKILLS_ROOT/"
cp -R skill_repo/crawler/youtube/youtube-data-pipeline "$SKILLS_ROOT/"
cp -R skill_repo/crawler/youtube/youtube-data-explore "$SKILLS_ROOT/"
```
