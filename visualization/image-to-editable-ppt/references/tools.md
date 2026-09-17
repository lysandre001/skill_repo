# 工具接口

用 Skill 目录下 `.venv/bin/python`。将 `scripts` 加入 `sys.path`，`from editable_ppt import Deck`。

科研图默认走库：

```python
from editable_ppt import Deck
deck = Deck.into_library("input.png", "my-figure-slug", title="信息检索", slot="auto")
deck.rect(20, 20, 400, 100, fill="#F4F6F8", line="#777777")
result = deck.finish(render=False)
# result["original"], result["pptx"], result["collection"], result["slot"]
```

坐标仍是**源图像素**；`into_library` 用单一 scale 映射进短图/长图框。不要用空白全页 `Deck(...)` 写入科研库。

仅调试空白页时：

```python
deck = Deck("input.png", "output")
```

| 接口 | 用法 |
|---|---|
| `Deck.into_library(png, slug, title=, slot=)` | 归档原图、复制短/长模版页、等比放入图框 |
| `Deck(source_png, out_dir)` | 空白页（源图 96px/in 全页），不用于科研库 |
| `text/rect/line/crop` | 源图像素；字号与线宽随 scale 变 |
| `finish(render=True)` | 写集合 PPT、`{slug}.pptx`、报告 |

`slot`：`short` / `long` / `auto`。库路径：`XY_VIZ_LIBRARY`（默认 `~/Documents/科研参考图`），见 SKILL.md。
