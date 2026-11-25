---
name: hyperframe
description: Create, edit, preview and render videos with HyperFrames (HTML + CSS + GSAP compositions rendered to deterministic MP4). Use whenever the user asks to make a video, motion graphic, animated intro/outro, explainer, social clip, captions, overlay, animated chart, or to turn a website/slides into a video, or mentions HyperFrames.
---

# HyperFrames video authoring

HyperFrames (open source, by HeyGen) turns plain HTML compositions into video.
The renderer seeks every frame in headless Chrome and encodes with FFmpeg, so
output is deterministic. There is no build step and no React/DSL: a composition
is an `index.html` that also plays as-is in a browser.

## Requirements

- Node.js 22+ (`node -v`)
- FFmpeg on PATH (`ffmpeg -version`)

Check both before rendering. If either is missing, tell the user what to install
instead of guessing a workaround.

## Workflow

1. **Clarify the brief** in one pass if it is vague: length (seconds), aspect
   ratio (1920x1080, 1080x1920, 1080x1080), text/copy, brand colors, media
   assets, music. Otherwise pick sensible defaults and say what you chose.
2. **Scaffold**
   ```bash
   npx hyperframes init my-video
   cd my-video
   ```
3. **Write the composition** in `index.html` following the rules below.
4. **Preview** with `npx hyperframes preview` (live browser preview with
   canvas, timeline and keyframes).
5. **Render** with `npx hyperframes render` (MP4 locally).
6. Report the output path, duration and resolution to the user.

Optional catalog blocks install as editable source:
`npx hyperframes add <block>` (e.g. `flash-through-white`, `instagram-follow`,
`data-chart`).

## Composition rules

- Root stage element carries the composition metadata:
  `id="stage"`, `data-composition-id`, `data-start="0"`, `data-width`,
  `data-height`.
- Every timed element (video, image, text block, audio) gets
  `class="clip"` plus `data-start`, `data-duration` (seconds) and
  `data-track-index` (layer/track number). Audio may add `data-volume` (0–1).
- `<video>` elements should be `muted playsinline`; put sound on `<audio>` clips.
- Animations must be **seekable**: build a GSAP timeline with
  `{ paused: true }`, place every tween at an explicit absolute time, and
  register it on `window.__timelines` under the composition id. Never rely on
  `setTimeout`, `requestAnimationFrame` loops, `Date.now()` or `Math.random()`
  without a seed — they break deterministic rendering.
- Keep assets local (relative paths) so renders are reproducible.
- Other supported animation adapters: CSS, WAAPI, Lottie, Three.js, Anime.js,
  or a custom frame adapter. Default to GSAP.

## Minimal template

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <style>
    html, body { margin: 0; background: #000; }
    #stage { position: relative; width: 1920px; height: 1080px; overflow: hidden;
             font-family: system-ui, sans-serif; color: #fff; }
    #stage .clip { position: absolute; inset: 0; }
    #title { display: flex; align-items: center; justify-content: center;
             font-size: 120px; font-weight: 800; }
  </style>
</head>
<body>
  <div id="stage" data-composition-id="launch" data-start="0"
       data-width="1920" data-height="1080">
    <video class="clip" src="bg.mp4" muted playsinline
           data-start="0" data-duration="6" data-track-index="0"></video>
    <div id="title" class="clip"
         data-start="0" data-duration="6" data-track-index="1">Hello, world</div>
    <audio class="clip" src="music.wav"
           data-start="0" data-duration="6" data-track-index="2" data-volume="0.5"></audio>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/gsap@3/dist/gsap.min.js"></script>
  <script>
    const tl = gsap.timeline({ paused: true });
    tl.from("#title", { opacity: 0, y: 40, duration: 0.8 }, 1);
    tl.to("#title", { opacity: 0, duration: 0.5 }, 5.3);

    window.__timelines = window.__timelines || {};
    window.__timelines.launch = tl; // key must match data-composition-id
  </script>
</body>
</html>
```

See `references/patterns.md` for common motion patterns.

## Quality checklist before rendering

- Total of `data-start + data-duration` of the longest clip equals the intended
  video length.
- Every tween ends within its element's clip window.
- Timeline key matches `data-composition-id`.
- Text is readable: large enough at target resolution, sufficient contrast,
  on screen long enough (~0.3 s per word minimum).
- Safe margins (~5% of width) for text on vertical/social formats.

## Troubleshooting

- Blank or static frames → timeline not registered on `window.__timelines`, or
  not created with `paused: true`.
- Jittery/inconsistent renders → non-seekable animation (timers, rAF, random).
- Render fails immediately → check Node 22+ and FFmpeg.
- For the full CLI and more official agent skills, the upstream project ships
  its own skills: `npx skills add heygen-com/hyperframes`. Docs:
  https://hyperframes.heygen.com
