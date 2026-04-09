# presentation-i3

Build self-contained HTML slide presentations from a brief, data sources, and screenshots. Every presentation is a single portable `.html` file with base64-inlined images that works anywhere: browser, Netlify, email attachment.

![Impact3 Preset](https://img.shields.io/badge/preset-impact3-7821F7) ![Slides](https://img.shields.io/badge/slides-up%20to%2020-333)

## What it does

Give it a brief, point it at some data, and it builds a complete presentation:

1. **Brief & Intake** — audience, takeaway, preset, data sources, screenshots
2. **Storyboard** — slide-by-slide outline with component selection, approved before building
3. **Build** — generates the full HTML using preset templates and component patterns
4. **Humanize** — strips AI writing patterns from all visible text
5. **Iterate** — surgical edits based on your feedback

Output is a single `.html` file you can open directly in a browser. Press F for fullscreen, arrow keys to navigate.

## Installation

### Claude Code (CLI)

```bash
git clone https://github.com/universethec/presentation-i3.git ~/.claude/skills/presentation-i3
```

That's it. The skill is now available. Start a conversation and say:

```
/presentation-i3
```

Or just ask Claude to "create a presentation about X" and it will pick up the skill automatically.

**Updating:**

```bash
cd ~/.claude/skills/presentation-i3 && git pull
```

### Claude.ai (Projects)

For Claude.ai, you don't install the full skill. Instead:

1. Download the file `claude-ai/presentation-i3.md` from this repo
2. Go to [claude.ai](https://claude.ai) and open or create a **Project**
3. In the Project settings, upload `presentation-i3.md` as **Project Knowledge**
4. Start a conversation in that Project and ask Claude to build a presentation

The claude-ai version bundles all the skill instructions, component patterns, and the preset into a single markdown file that works as Project Knowledge.

> **Note:** The claude-ai version does not include the Impact3 logo SVG or the full preset CSS inline — those are embedded in the Claude Code version's reference files. If you need the logo, copy it from `references/impact3-logo.svg.html`.

## Presets

| Preset | Description |
|--------|-------------|
| `impact3` | Dark purple gradient, Impact3 branding, animated wave canvas, conic-gradient stroke borders (default) |

### impact3 preset rules

- All titles uppercase, zero letter-spacing
- Body text at 70% white opacity for legibility
- 28px border-radius on all components
- Component wrappers: frosted glass (8px backdrop blur)
- Impact3 logo SVG mandatory on hero slide
- Uploaded images (PNG/JPG) never wrapped in component containers

## File structure

```
presentation-i3/
  SKILL.md              # Main skill instructions (Claude Code)
  README.md             # This file
  claude-ai/
    presentation-i3.md  # Single-file version for Claude.ai Projects
  presets/
    impact3.html        # Full HTML shell template (default and only preset)
  references/
    components.md       # HTML patterns for all slide components
    screenshot-patterns.md
    narrative-arcs.md   # Story structure templates
    humanizer-rules.md  # AI writing patterns to strip
    impact3-logo.svg.html  # Impact3 wordmark SVG
```

## Components

Available slide components (see `references/components.md` for full HTML patterns):

- **hero** — title slide with logo, kicker, headline, subtitle, metadata
- **big-number** — single KPI emphasis
- **stat-grid** — 2-6 KPIs side by side
- **funnel** — conversion steps with drop-off rates
- **bar-chart** — horizontal ranked bars
- **data-table** — structured comparison data
- **callout** — key insight highlight (default / warn / good)
- **comparison** — two items side by side with VS separator
- **priority-list** — ranked recommendations with severity
- **dead-click-list** — friction point list with counts
- **browser-mock** — screenshot in a browser chrome frame
- **annotated-screenshot** — screenshot with positioned callout labels

## Output

Presentations are saved to `.claude/presentations/<name>.html` by default, with a `storyboard.md` alongside.

Every presentation is a single HTML file. No build step. No dependencies. Open it in a browser.

## License

MIT
