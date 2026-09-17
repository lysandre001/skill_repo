# Figma MCP for this skill

Remote server: `https://mcp.figma.com/mcp`. Cursor plugin id is typically `figma`.

## Tools

| Tool | Use |
|------|-----|
| `use_figma` | Inspect and create layers via Plugin API. Pass `fileKey`, script, `skillNames`. |
| `upload_assets` | **Only** supported way to put PNG/JPG on Slides. Max 10MB. |
| `get_screenshot` | Visual QA of a slide/node. Works on Slides. |
| `create_new_file` | Blank Slides/Design/FigJam if no target file. |

`generate_figma_design` is for capturing live UI into Design files, not for placing a research PNG into Slides.

## use_figma (Slides)

- Always include `skillNames`: `figma-use-slides`.
- `get_metadata` does not work on Slides files.
- Do not `figma.createPage()`.
- `console.log` is not returned; `return` data from the script.
- Inspect first:

```js
const grid = figma.getSlideGrid();
return {
  slideSize: grid[0] && grid[0][0] ? { w: grid[0][0].width, h: grid[0][0].height } : null,
  slides: grid.map((row, r) => row.map((slide, c) => ({
    id: slide.id, name: slide.name, row: r, col: c,
  }))),
};
```

## Images

1. `use_figma`: create a rectangle or frame, `appendChild` then `resize(fittedW, fittedH)`, set `x`/`y`. Return `nodeId`.
2. `upload_assets` with that `fileKey` and `nodeIds: [nodeId]` so the PNG becomes the fill.
3. Do not use REST `POST /v1/images` as a substitute when MCP is connected.

## Helpers (append first)

```js
function addFrame(parent, x, y, w, h, fill, radius) {
  const f = figma.createFrame();
  parent.appendChild(f);
  f.resize(w, h);
  if (fill) f.fills = [{ type: "SOLID", color: fill }];
  if (radius !== undefined) f.cornerRadius = radius;
  f.x = x; f.y = y;
  return f;
}
function addRect(parent, x, y, w, h, fill, radius) {
  const r = figma.createRectangle();
  parent.appendChild(r);
  r.resize(w, h);
  if (fill) r.fills = [{ type: "SOLID", color: fill }];
  if (radius) r.cornerRadius = radius;
  r.x = x; r.y = y;
  return r;
}
function addText(parent, family, style, size, color, chars, x, y, w, h) {
  const t = figma.createText();
  parent.appendChild(t);
  t.fontName = { family, style };
  t.fontSize = size;
  t.characters = chars;
  t.fills = [{ type: "SOLID", color }];
  if (w !== undefined) t.resize(w, h);
  t.x = x; t.y = y;
  return t;
}
```

Load fonts with `loadFontAsync` before mutating text. Inter is often preloaded; Calibri/PingFang still need an explicit load.

## Auth missing

If only `mcp_auth` is listed, tell the user to authenticate the **remote** Figma MCP in that product (Cursor: Settings → Tools & MCP → Figma → Connect; Claude Code / Codex: add `https://mcp.figma.com/mcp` and log in). Then retry. Do not continue with a local SVG dump as if it were in their Figma file.
