# Visualization skills

把科研/咨询图转成 **可编辑** 的 PowerPoint 或 Figma 图层。两套 skill 平级，按交付选一个。

| Skill | 目录 | 做什么 |
|-------|------|--------|
| 图转 PPT | [`image-to-editable-ppt/`](image-to-editable-ppt/) | 原生文本框/形状，放进模板的 **短图** 或 **长图** 框 |
| 图转 Figma | [`image-to-figma/`](image-to-figma/) | 原生文字/矩形/线条写入 Figma Slides 或 Design |

共同规则：在 **源图像素** 里画，再用 **一个 scale** 放进目标框，不单独拉宽或压扁。禁止把整页 PNG 当成「可编辑成品」。

## 图转 PPT：短图和长图

模板文件默认叫 `科研图表集合.pptx`（可用环境变量改路径，见下）。前两页是模版，不要覆盖。

- **短科研图 (`short`)**：内容区较窄，右侧留给校徽/侧栏。适合接近 16∶9、需要留边栏的图。
- **长科研图 (`long`)**：同一高度，内容区拉满宽度。适合更宽的流程图、多栏表。
- **`auto`**：算哪种框填得更满就用哪种。

每张图交付三样：`{slug}.png` 原图、`{slug}.pptx` 单页可编辑稿、追加进集合 PPT 的一页。

库目录：

```bash
export XY_VIZ_LIBRARY="$HOME/Documents/科研参考图"   # 或你的模板文件夹
# 可选：export XY_VIZ_COLLECTION="科研图表集合.pptx"
```

对 agent 说例如：

```text
用 image-to-editable-ppt，把这张图转成可编辑 PPT。slot=auto，标题「信息检索」。
```

## 图转 Figma：流程

1. Agent 能调用 **远程** Figma MCP（`https://mcp.figma.com/mcp`），不是只开桌面 Dev Mode。
2. 给出 Figma 链接或 `FIGMA_SLIDES_FILE_KEY`。空文件先 `createSlide()`，不要先建空 row。
3. 在 `(src_w × src_h)` 的 Frame 里用文字/形状还原，再 `rescale` 一次放到 1920×1080（或你指定的框）。
4. 位图只作隐藏对照。交差标准：图层里的字可以点进去改。

```text
用 image-to-figma，把这张图还原成 Figma 可编辑图层。fileKey 用我给你的 Slides 链接。
```

## 安装（主流 coding agent）

仓库里每个 skill 都是标准 `SKILL.md` 目录（Cursor、Claude Code、Codex、Amp、Factory Droid 都能认）。**目录名必须等于** frontmatter 里的 `name`。

把本目录下两个文件夹拷到对应 skills 根（任选其一，或都拷）：

| Agent | Skills 目录 |
|-------|-------------|
| Cursor / Cursor Web（本机） | `~/.cursor/skills/` 或项目 `.cursor/skills/` |
| Cursor Web（云） | 把 skill 放进仓库 `.cursor/skills/` 并打开该仓库 |
| Claude Code | `~/.claude/skills/` 或项目 `.claude/skills/` |
| Codex | `~/.codex/skills/` |
| Amp | `~/.agents/skills/` 或 `amp skill add <path>` |
| Factory Droid | `~/.factory/skills/` 或项目 `.factory/skills/` |

```bash
git clone https://github.com/lysandre001/skill_repo.git
SKILLS_ROOT="$HOME/.cursor/skills"   # 换成上表路径
mkdir -p "$SKILLS_ROOT"
cp -R skill_repo/visualization/image-to-editable-ppt "$SKILLS_ROOT/"
cp -R skill_repo/visualization/image-to-figma "$SKILLS_ROOT/"
python3 -m pip install -r "$SKILLS_ROOT/image-to-editable-ppt/requirements.txt"
```

Codex 还带了 `agents/openai.yaml` 的默认触发文案。Figma 写入依赖各产品里的 Figma MCP 登录，与拷贝 skill 文件是两件事。
