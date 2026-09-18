# ditherme

An interactive generative-art toy. A single colored "spore" divides and multiplies
until it swarms into a random organic shape, which is rendered as a crisp **two-tone
ordered-dither** field with a complementary outline. Generate new forms on demand, or
**draw your own** and watch it fill — then drag past artifacts back onto the canvas to
compose them like paint.

It's plain HTML/JS — no build step, no dependencies. `index.html` is a landing page
(a dithered rainbow that fills the screen) with a button into the editor; the editor
itself is the self-contained `editor.html`.

| | |
|---|---|
| ![square dither, pink/blue blob](screenshots/dither-shot-1.png) | ![round-dot dither, orange/purple](screenshots/dither-shot-2.png) |
| ![plus-pixel dither, green/magenta star](screenshots/dither-shot-3.png) | ![diamond-pixel dither, lime/magenta flower](screenshots/dither-shot-4.png) |

*Four artifacts — each a different random form, pixel style, and two-tone palette.*

---

## What's happening

1. **A shape is born.** A random closed outline is generated from a sum of radial
   harmonics (random frequencies, amplitudes, phases, elongation and rotation), so
   every form is unique.
2. **Spores multiply into it.** Starting from a single spore, each one divides —
   children spawn beside a random parent — until ~1,500 fill the shape's interior
   (found by point-in-polygon sampling).
3. **It's dithered in two tones.** Each spore carries a center→rim *mix* value,
   clamped so **both** tones always appear. The two colors are Bayer-dithered against
   each other per pixel, so every region is a crisp stipple of both — never a flat patch.
4. **A complementary outline** traces the boundary in a third color (opposite the two
   fills on the color wheel).
5. Then it dissolves and a new form grows. Forever.

The dither is computed on a small low-resolution grid and upscaled, then intersected
with a repeating **pixel-shape tile** so each dither cell can be a square, circle,
cross, diamond, and so on.

## Features

- **Endless random forms** — organic blobs, stars, flowers, gears; no two alike.
- **Two-tone Bayer dithering** with a guaranteed accent (no solid-color patches).
- **9 pixel styles** — `square`, `round`, `dot`, `diamond`, `plus`, `x`, `ring`,
  `hline`, `vline`.
- **Complementary outline** in an optional third color.
- **Draw mode** — sketch a closed loop and it fills your shape with the dither.
- **Postcard gallery** — the last 10 finished artifacts are captured as thumbnails
  down the left edge; click any to download it as a full-resolution PNG. The strip
  collapses out of the way.
- **Full control panel** — live knobs for every parameter (below).
- **Liquid-glass UI** — frosted, translucent panel and controls; the postcard stack
  uses an SVG goo filter so adjacent cards fuse where they meet, tunable from crisp
  joints to soft gooey blobs via the **Postcard merge** knob.
- **PNG export** of the current frame.

## Controls

A collapsible panel (top-right) exposes everything live:

| Group | Knobs |
|---|---|
| **Shape** | cycle length · fill fraction · harmonics min/max · complexity |
| **Population** | max spores · multiply speed · motion smoothing · spore size |
| **Render** | resolution (dither pixel size) · dither scale · **pixel style** |
| **Liquid** | thumbnail merge (crisp → gooey glass) |
| **Tones** | mix floor / ceiling · boundary fuzz · outline width |
| **Colors** | random-colors toggle · Tone A / Tone B / background / outline (Tone C) |
| **Playback** | tool (Drag / Fill / Pen) · auto-cycle · New shape · Shuffle colors · Pause |
| **Placed shapes** | Undo · Clear |
| **Export** | Download PNG |

### Tools

Pick a canvas tool in the Playback group:

- **Drag** *(default)* — move placed shapes around and grab/generate shapes.
- **Fill** — drag a closed loop; on release the spores regrow to fill inside it as a new
  dithered shape (no outline left behind).
- **Pen** — draw a freehand line that stays as a movable stroke you can reposition or
  bin like any other shape. It's stamped at the field's resolution (so it stays chunky
  and dithered) in the outline color (Tone C).

### Pan & zoom

The placed shapes live in a large world you can move around:

- **Zoom** with the scroll wheel (zooms toward the cursor).
- **Pan** by dragging empty canvas in the Drag tool, or middle-mouse-drag in any tool.
- **Reset view (1:1)** in the panel returns to the origin at 100%.

The active spore field stays centred on your current view; when you commit it (New shape,
grab, fill) it drops into the world where it appears, so you can build compositions far
bigger than the screen.

### Import image

**Import image…** (in the panel) loads a picture, orders-dithers it with the Bayer
matrix (2–8 **Dither levels**), and drops it on the canvas as a movable object — dithered
at the field's cell size so it matches the rest. It's a normal shape after that: drag it,
undo/clear it, or bin it.

### Shapes on the canvas

Everything on the canvas is a **movable shape**. Generate one and it grows on top of
everything; **drag any shape to move it** — grabbing is pixel-accurate, so you can only
pick a shape up by its actual filled body, not the empty space around it. **New shape**
commits the current shape and grows a new one above it, so shapes accumulate into a
composition. **Undo** / **Clear** manage the placed shapes.

Drop a shape on the **trash can** (bottom-left) to delete it — the shape shrinks as it
nears the can and the can lights up; release elsewhere and it springs back to full size.
Clicking the trash clears everything.

Each generated shape adds a **thumbnail of that shape** to the gallery (up to the last
10, newest on top). Drag any thumbnail onto the canvas to drop a movable copy of *that*
shape — it **pops out** of the thumbnail at thumbnail size and springs up to full size.
A plain click downloads it.

Auto-cycling is **off by default** — nothing regenerates on its own. Use **New shape**,
**Draw mode**, or re-enable **Auto-cycle**.

## Running it

No install. Either:

- **Double-click** `index.html`, or
- serve the folder and open it:

```bash
python -m http.server
# then visit http://localhost:8000/
```

## Project structure

```
ditherme/
├── index.html              # landing page (dithered rainbow + "Open the editor")
├── editor.html             # the editor — markup, styles, and canvas engine
├── README.md
└── screenshots/            # images used in this README
```

Built with vanilla JavaScript and the Canvas 2D API.
