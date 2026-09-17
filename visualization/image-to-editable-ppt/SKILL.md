---
name: image-to-editable-ppt
description: Convert a figure PNG or screenshot into native editable PowerPoint objects, uniformly scaled into a 短科研图 (short) or 长科研图 (long) frame. Use when the user wants 图片转PPT, 科研图, PNG to PPTX, 可编辑PPT, short/long figure slots, or astralow-for-editable-ppt.
---

# Image to editable PPT

Rebuild one source figure as **native PowerPoint objects** (text boxes, shapes, lines). Keep the source aspect ratio with **one scale**. Do not wrap the whole page as a single picture.

## Short vs long frames

Template collection `科研图表集合.pptx` is a 13.333″ × 7.5″ (16:9) deck. Slides 1–2 are **模版图** and must not be overwritten.

| Slot | Name | When | Box (EMU: left, top, width, height) |
|------|------|------|--------------------------------------|
| `short` | 短科研图 | Source closer to ~1.82∶1, or the slide keeps a **right sidebar** (logo/chrome) | `554610, 1351499, 8955024, 4918672` |
| `long` | 长科研图 | Wider figures; **full content width**, same height | `554610, 1351499, 11082654, 4918672` |
| `auto` | — | Pick the slot with the larger fill ratio | — |

**Fit:** `scale = min(inner_w / src_w, inner_h / src_h)` with 0.05″ inset, then center. Map every x/y/w/h/font/stroke with that scale. Never stretch X independently of Y.

Measure the live template if the user moved the black frames; the table is the default from the UM-style collection.

## Outputs (each task)

Set library dir with `XY_VIZ_LIBRARY` (or `IMAGE_TO_PPT_LIBRARY`). Default: `~/Documents/科研参考图`. Collection file: `XY_VIZ_COLLECTION` or `科研图表集合.pptx`.

1. `{slug}.png` — original
2. `{slug}.pptx` — one editable slide on the template
3. Append a slide to the collection (do not replace slides 1–2)

## Runtime

Skill directory = folder that contains this `SKILL.md`.

1. Read [tools.md](references/tools.md).
2. Python 3.10+: `python -m pip install -r requirements.txt` (or a venv at `<skill>/.venv`).
3. `sys.path` ← `<skill>/scripts`; `from editable_ppt import Deck`.
4. Draw in **source pixels**, then `Deck.into_library(...)`.
5. Fonts: Inter/Calibri/PingFang; pass `font_path` for width estimates on the current OS.
6. Windows PowerPoint can verify edits; macOS/Linux keep the PPTX and say Office verification was skipped.

```python
deck = Deck.into_library(source_png, "my-figure-slug", title="信息检索", slot="auto")
# native objects in SOURCE PIXEL coordinates
result = deck.finish(render=False)
# result["original"], result["pptx"], result["collection"], result["slot"]
```

Do not write a full-bleed source-pixel deck into the library.

## Workflow

1. Copy the original PNG to `{slug}.png`.
2. Choose `short` / `long` / `auto`. Set the title placeholder.
3. Rebuild with native objects. Crop only icons or complex bits.
4. `finish()`. Check the figure sits inside the black frame, not over the title or sidebar.
5. Deliver original PNG, `{slug}.pptx`, collection path, slot, remaining bitmaps.

A white rect over `(0,0,src_w,src_h)` is OK (it maps into the figure frame only). Do not cover template chrome.
