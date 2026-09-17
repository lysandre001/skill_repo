---
name: image-to-figma
description: Reconstruct a figure PNG as native editable Figma layers (text, rectangles, lines) with one uniform scale. Use when the user wants 图片转Figma, image to Figma, 等比例还原, Figma Slides, use_figma, or an editable research figure in Figma.
---

# Image to Figma

Turn one source PNG into **editable Figma layers**. Keep the original aspect ratio with **one scale**. A bitmap-only paste is a failure unless the user explicitly wants a photo.

Prefer Figma when proportion matters more than staying in PowerPoint.

Target file: use the URL/`fileKey` the user gives. Optional env: `FIGMA_SLIDES_FILE_KEY`. If missing, `create_new_file` with `editorType: "slides"`. Do not invent a local `.fig` file.

## MCP

Needs the **remote** Figma MCP (`https://mcp.figma.com/mcp`). Desktop-only MCP cannot write.

| Client | How to connect |
|--------|----------------|
| Cursor / Cursor Web | Figma plugin → Settings → Tools & MCP → Connect |
| Claude Code | Add remote MCP `https://mcp.figma.com/mcp` and authenticate |
| Codex | Same remote MCP in Codex MCP config |
| Other agents | Remote Figma MCP + OAuth; tools: `use_figma`, `upload_assets`, `get_screenshot`, `create_new_file` |

For Slides, pass `skillNames` including `figma-use-slides` (or `resource:figma-use-slides`).

If those tools are missing, stop and ask the user to authenticate. Do not dump SVG and call it done.

Details: [mcp.md](references/mcp.md). Fit math: [fit.md](references/fit.md).

## Proportion lock

Draw in **source pixels** inside a Frame of size `(src_w, src_h)`, then `frame.rescale(scale)` once:

```
scale = min(box_w / src_w, box_h / src_h)
```

Center the frame in the slide (typically 1920×1080) or in a named slot the user already has. Never stretch X independently of Y.

Empty Slides files: call `figma.createSlide()` **before** `createSlideRow()`. An empty row can block slide creation.

## Workflow

1. Inspect the deck with read-only `use_figma`. `get_metadata` does not work on Slides.
2. Keep `{slug}.png` if a library folder is in use.
3. Rebuild **native** text/rects/lines in source-pixel space; `rescale` once. Crop only icons.
4. Optional: `upload_assets` PNG onto a locked back rectangle, then `visible = false`. Never leave the PNG as the only content.
5. `appendChild` before setting `x`/`y` at every nesting level (Slides −240 bug).
6. Screenshot vs PNG. Fix copy/overlap; do not stretch.

Do not delete existing slides unless the user says start over.

## Done when

- One Frame’s aspect equals the PNG.
- Text and shapes are native and editable.
- User gets the Figma URL and frame/slide name.
