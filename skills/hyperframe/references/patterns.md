# HyperFrames motion patterns

All snippets assume `const tl = gsap.timeline({ paused: true });` and absolute
position arguments (the last number = seconds from composition start).

## Staggered word reveal
```js
// <h1 id="headline"><span>Ship</span> <span>faster</span> <span>today</span></h1>
tl.from("#headline span", { opacity: 0, y: 30, duration: 0.5, stagger: 0.12 }, 0.5);
```

## Lower third
```js
tl.from("#lower-third", { xPercent: -110, duration: 0.6, ease: "power3.out" }, 2);
tl.to("#lower-third",   { xPercent: -110, duration: 0.4, ease: "power2.in" }, 7);
```

## Ken Burns on a still image
```js
tl.fromTo("#photo", { scale: 1 }, { scale: 1.12, duration: 6, ease: "none" }, 0);
```

## Crossfade between scenes
```js
tl.to("#scene1", { opacity: 0, duration: 0.6 }, 4.7);
tl.from("#scene2", { opacity: 0, duration: 0.6 }, 4.7);
```
Give each scene a clip window that overlaps by the fade length.

## Counter / number tick-up
```js
const counter = { v: 0 };
tl.to(counter, {
  v: 1250, duration: 2, ease: "power1.out",
  onUpdate: () => (document.querySelector("#count").textContent = Math.round(counter.v).toLocaleString())
}, 1);
```
`onUpdate` runs on seek too, so this stays deterministic.

## Timed captions
Make each caption line its own `.clip` with `data-start`/`data-duration` from
the transcript timestamps, all on the same `data-track-index`, and fade each in
with a short tween at its start time.

## Aspect-ratio presets
| Format            | data-width × data-height |
|-------------------|--------------------------|
| Landscape (YouTube) | 1920 × 1080 |
| Vertical (Reels/TikTok/Shorts) | 1080 × 1920 |
| Square            | 1080 × 1080 |
