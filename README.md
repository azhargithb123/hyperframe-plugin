# hyperframe-plugin

A [Claude Code](https://claude.com/claude-code) plugin that adds a **`hyperframe`** skill:
describe a video in plain English and Claude builds it with
[HyperFrames](https://github.com/heygen-com/hyperframes) (HTML + CSS + GSAP) and
renders it to MP4.

## Features

- 🎬 **Prompt to video**: intros, outros, explainers, social clips, lower thirds, animated charts, captions
- 🧱 **Plain HTML compositions**: no build step, no React; every video is an `index.html` you can open and edit
- ⏱️ **Deterministic renders**: the skill enforces seekable GSAP timelines so every render comes out the same
- 📐 **Any format**: landscape 1920×1080, vertical 1080×1920, square 1080×1080
- ✅ **Built-in checklist**: timing, readability and safe-margin checks before rendering
- 📚 **Motion recipes**: staggered text, Ken Burns, crossfades, counters, timed captions (`skills/hyperframe/references/patterns.md`)

## Install

```bash
claude plugin marketplace add azhargithb123/hyperframe-plugin
```

```bash
claude plugin install hyperframe-plugin@hyperframe-local
```

Restart Claude Code, then run `/plugin` to check that `hyperframe-plugin` is enabled.

**Try it without installing:**

```bash
git clone https://github.com/azhargithb123/hyperframe-plugin
claude --plugin-dir ./hyperframe-plugin
```

**Install only the skill:** copy `skills/hyperframe/` into `~/.claude/skills/`.

## Usage

Ask Claude for a video and the skill loads automatically. Example prompts:

- "Make a 6-second vertical intro for my app called Nimbus, dark background, blue accent."
- "Create a 15-second explainer with three scenes and a lower third with my name."
- "Turn these sales numbers into an animated bar chart video, 1920×1080."

Claude will scaffold a project (`npx hyperframes init`), write the composition,
preview it (`npx hyperframes preview`) and render it (`npx hyperframes render`).

## Requirements

- [Claude Code](https://claude.com/claude-code)
- Node.js 22+
- FFmpeg on your `PATH`

## Repository layout

```
hyperframe-plugin/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   └── marketplace.json     # makes this repo installable as a marketplace
├── skills/
│   └── hyperframe/
│       ├── SKILL.md         # the skill
│       └── references/
│           └── patterns.md  # motion recipes
├── LICENSE
└── README.md
```

## Related

- [HyperFrames](https://github.com/heygen-com/hyperframes): the open-source rendering framework by HeyGen
- HeyGen also publishes official HyperFrames skills: `npx skills add heygen-com/hyperframes`

This is an independent community plugin. It is not affiliated with HeyGen or Anthropic.

## License

[MIT](LICENSE) © azhargithb123
