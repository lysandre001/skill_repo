# Uniform fit

Source size: `(src_w, src_h)` from the PNG.

Target box on the slide: `(box_x, box_y, box_w, box_h)` after inspecting chrome (title, sidebar). Full-bleed default: `(0, 0, slide.width, slide.height)` usually `1920×1080`.

```
scale = min(box_w / src_w, box_h / src_h)
used_w = src_w * scale
used_h = src_h * scale
origin_x = box_x + (box_w - used_w) / 2
origin_y = box_y + (box_h - used_h) / 2
```

The figure frame is exactly `used_w × used_h` at `(origin_x, origin_y)`. Image fill scaleMode `FILL` on a frame that already has the source aspect is equivalent to uniform scale (no stretch).

Pick a named slot only if it exists on the slide. Do not invent PPT EMU boxes unless the user asks to match 科研图表集合 短图/长图:

| Slot | Meaning (PPT, for reference only) |
|------|-------------------------------------|
| short | Narrower content + right sidebar |
| long | Full content width, same height |

In Figma, **measure the actual frames** in the target file instead of copying EMU numbers.

Fill ratio for `auto`: `used_w * used_h / (box_w * box_h)` — pick the slot with the larger ratio.
