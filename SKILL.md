---
name: presentation-i3
description: |
  Generate self-contained HTML slide presentations from briefs, data, and screenshots.
  Produces single-file decks with base64-inlined images that work anywhere — browser, Netlify, email.
  Use when asked to create a presentation, slide deck, pitch deck, or data story.
version: 1.2.0
repository: https://github.com/universethec/presentation-i3
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - Agent
  - AskUserQuestion
---

# Presentation Builder

Build self-contained HTML slide presentations from a brief, data sources, and screenshots. Every presentation is a single portable HTML file with base64-inlined images.

## Workflow — 5 Phases

Follow these phases in order. Do not skip phases. Do not start building before the storyboard is approved.

---

### Phase 1: Brief & Intake

**Goal:** Understand what to build and gather all inputs.

1. Ask the user: **"What's the audience?"** (stakeholders, team, investors, etc.)
2. Ask: **"What's the single takeaway?"** (one sentence they should remember)
3. List all `.html` files in this skill's `presets/` folder. If only one preset exists, auto-select it. If multiple, ask the user to pick.
4. Ask for **data sources** — paths to markdown files, documents, or descriptions of data to include.
5. Ask for **screenshots** — path to a folder containing images (e.g., `/resources`), or note that no screenshots are needed.
6. Read all provided data source files. Inventory all screenshots (list filenames and note what each appears to show).

**Output:** You now have: audience, takeaway, preset, data, and screenshots.

---

### Phase 2: Storyboard

**Goal:** Propose a slide outline and get user approval before building anything.

1. Read `references/narrative-arcs.md` — select the best arc for the brief.
2. Read `references/components.md` — understand available component types and their auto-select triggers.
3. Read `references/screenshot-patterns.md` — understand how to compose images with data.
4. Generate a **slide-by-slide outline**. For each slide, specify:

```
Slide N: "Title"
  Component: [component-name from components.md]
  Data: [what data populates this slide]
  Screenshot: [filename from /resources, or "none"]
  Insight: [the key text/takeaway for this slide]
```

5. Present the storyboard to the user in the terminal.
6. Ask: **"Want to reorder, add, remove, or edit any slides?"**
7. Iterate until the user approves.
8. Save the approved storyboard as `storyboard.md` next to the output HTML.

**Rules:**
- First slide should always be a `hero` component.
- Last slide should be actionable (recommendations, priorities, next steps).
- Each slide should make exactly ONE point. One headline. One visual or one data story. Not both crammed together.
- Every slide with a screenshot: headline + insight on one side, ONE screenshot on the other. Never two screenshots on one slide.
- Before/after comparisons: split into TWO slides. First slide = "before" state. Second slide = "after" reveal.
- Data-only slides: arrange with hero energy. Big number or stat-grid with breathing room. Not a dashboard.
- More slides at higher quality is always better than fewer slides that feel cramped.
- Read the Design Philosophy section before building. Every slide is a hero.

---

### Phase 3: Build

**Goal:** Generate the complete HTML presentation.

1. Read the chosen preset file from `presets/`.
2. Read `references/components.md` for HTML patterns.
3. For each slide in the approved storyboard:
   a. Look up the component HTML pattern in `components.md`.
   b. Replace all `{{TOKEN}}` placeholders with actual data from the storyboard.
   c. If the slide references a screenshot:
      - Read the image file using the Read tool
      - The image will be returned as visual content — use Bash to base64-encode it: `base64 -i /path/to/image.png`
      - Replace `{{IMAGE_SRC}}` with `data:image/png;base64,<encoded_data>`
      - For JPGs use `data:image/jpeg;base64,...`
   d. If the slide uses a screenshot-pattern (before-after, annotated, etc.), read `references/screenshot-patterns.md` and apply the composition.
   e. Wrap each slide in a comment marker and section tag:
      ```html
      <!-- SLIDE N: Title -->
      <section data-section="N" class="CLASS">
        <div class="section-inner">
          ...slide content...
        </div>
      </section>
      ```
   f. Use `class="hero"` for slide 0. Default: no extra class. Avoid `class="dense"` unless absolutely necessary. Every slide should breathe like a website hero section. See the Design Philosophy section.
   g. Add `reveal` and `reveal-delay-N` classes to content elements for staggered animations. Pattern: kicker gets `reveal`, h2 gets `reveal reveal-delay-1`, first content block gets `reveal reveal-delay-2`, etc.

4. Replace tokens in the preset:
   - `{{SLIDES}}` → all generated `<section>` blocks
   - `{{TITLE}}` → presentation title from the brief
   - `{{SLIDE_COUNT}}` → total number of slides
   - `{{DATE}}` → today's date

5. Write the complete HTML file to the output path.
   - Default: `.claude/presentations/<name>.html`
   - Ask the user for the path if not obvious from context.

---

### Phase 4: Humanize

**Goal:** Strip AI writing patterns from all slide text.

1. Read `references/humanizer-rules.md`.
2. Read the generated HTML file.
3. Identify all human-visible text:
   - Headings (h1, h2, h3)
   - Kicker text (`.kicker`)
   - Subtitle text (`.subtitle`)
   - Callout text (`.callout`)
   - Stat labels and subtitles (`.stat-label`, `.stat-sub`, `.big-label`)
   - Priority descriptions (`.priority-text p`)
   - Any other text content visible to the reader
4. Apply humanizer rules:
   - Strip AI transitional phrases, promotional language, inflated adjectives
   - Replace with direct, human voice
   - Keep headlines short and punchy
   - Keep insights to one sentence
5. **NEVER modify:**
   - Numbers, percentages, data values
   - Technical terms and product names
   - CSS, JavaScript, or HTML structure
   - Tokens that have already been replaced with data
6. Use the Edit tool to replace text strings in-place.
7. Do a final pass: read the file and check if any text still sounds like AI wrote it.

---

### Phase 5: Iterate

**Goal:** Refine the presentation based on user feedback.

1. Tell the user: **"Open [path] in your browser to review. Press F for fullscreen."**
2. Wait for feedback.
3. For each change request:
   a. Locate the slide using `<!-- SLIDE N: Title -->` comment markers.
   b. Make the edit using the Edit tool — surgical changes, not full rebuilds.
   c. If the request involves swapping a component type, read `components.md` for the new pattern.
   d. If new screenshots are added, base64-encode them.
   e. Any new or changed text goes through humanizer rules again.
4. After each round of edits, tell the user to refresh their browser.
5. Repeat until the user is satisfied.

---

## Reference Files

This skill reads these files as needed during each phase:

| File | Used in | Purpose |
|------|---------|---------|
| `references/components.md` | Phase 2, 3, 5 | HTML patterns for all component types |
| `references/screenshot-patterns.md` | Phase 2, 3, 5 | Image composition recipes |
| `references/narrative-arcs.md` | Phase 2 | Story structure templates |
| `references/humanizer-rules.md` | Phase 4, 5 | AI writing patterns to strip |
| `references/impact3-logo.svg.html` | Phase 3 | **MANDATORY** Impact3 logo SVG for hero slide |
| `presets/*.html` | Phase 1, 3 | Full HTML shell templates |

Read reference files at the start of each phase that needs them. Do not read all files upfront.

## Design Philosophy — Every Slide Is a Hero

**This is the most important section of this skill. Read it before building anything.**

Every slide must feel like a standalone website hero section. Not a document page. Not a dashboard. A hero. The kind of section you'd see at the top of a premium landing page.

### The Layout Rule

Every slide follows one of two patterns:

**Pattern A — Asset slide (when a screenshot or visual exists):**
- Left side: big headline (Impact/h2) + 1-2 lines of insight + one data point maximum
- Right side: ONE visual asset, properly scaled, with breathing room
- Never two raw screenshots side by side on the same slide
- The visual should have subtle glow, shadow, or framing — never dumped raw
- Asymmetric layout. Text takes ~40%, visual takes ~60%. Or vice versa.

**Pattern B — Data slide (no screenshot):**
- Arrange components (stat-grids, funnels, callouts) with the same hero energy
- Big headline at top, one clear data story below
- Breathing room everywhere. Whitespace is design.
- Reference `posthog-skymoney-audit-interpreted.html` for how to arrange data beautifully

### Before/After Rule

Before/after comparisons are ALWAYS two separate slides:
- Slide A: the "before" state with its own headline, context, and one clean screenshot
- Slide B: the "after" state with the punchline, the contrast, and one clean screenshot
- This creates narrative tension. The audience sees the first state, absorbs it, then sees what changed.
- NEVER put two screenshots side by side on one slide.

### Screenshot Rules

- One screenshot per slide maximum
- Screenshots need context: a headline that frames what you're looking at, and text that tells you what to notice
- Scale screenshots to look good, not to fill space. Smaller with breathing room is better than full-bleed and cramped.
- Browser-mock frames are optional. Sometimes just the screenshot with a subtle border-radius and shadow is cleaner.
- If a screenshot is a mobile UI, display it at mobile proportions (tall and narrow), not stretched wide.
- ALL screenshot containers must have `max-height: 70vh` so they never overflow the slide viewport. Use `object-fit: contain` on the image.
- **NEVER wrap uploaded images (base64 PNG/JPG) in a `.component` container.** The `.component` wrapper adds a background fill and animated stroke border meant for data elements (stat-grids, funnels, callouts, tables). Screenshots and uploaded images sit directly inside `.section-inner` or inside a plain `<div>` with no component styling. The `max-height` and `object-fit` rules are applied via inline styles or preset CSS on the `<img>` element itself — they do not depend on the component wrapper.

### Wide vs Tall Asset Rule

Detect the aspect ratio of each screenshot:
- **Tall/portrait assets** (mobile UI screenshots): use the two-col hero layout. Text left, screenshot right.
- **Wide/landscape assets** (desktop screenshots, cards, banners): use centered layout. Text centered at top, asset centered below at full width (`max-width: 100%`). NEVER use a fixed pixel `max-width` that could clip the image. NEVER use `overflow: hidden` on wide asset containers — it clips the edges. NEVER force a wide asset into a narrow right column.

This is critical. A wide asset in a two-col layout gets crushed into 50% width and looks terrible. Center it and let it breathe.

### Viewport Overflow Rule — NEVER SCROLL

Slides must NEVER require scrolling. Everything on a slide must fit within the viewport. This is enforced at the preset level with:
These CSS rules are baked into every preset. Do NOT override them inline:
```css
section img { object-fit: contain; border-radius: 16px; max-height: 80vh; }
[style*="text-align:center"] img { max-height: 60vh; }
```
- ALL images: `max-height: 80vh` — prevents any image from overflowing the slide
- Centered layouts (wide assets): `max-height: 60vh` — tighter cap because text sits above
- These are preset-level rules. Never remove them. Never override them with inline styles.
- `section .section-inner > * { max-height: calc(100vh - 120px); overflow: hidden; }` — all direct children capped

If content doesn't fit, the solution is ALWAYS to split into more slides. Never cram. Never allow overflow.

### Data Presentation Rules

- One data point per slide lands harder than three
- Big numbers should be BIG. 80px+ font. Let them breathe.
- Stat grids: maximum 3-4 cards. More than that is a table, not a hero.
- Callouts are analyst notes, not paragraphs. Two sentences max.
- Funnels get their own slide. They're complex enough to deserve full attention.

### Slide Count

- More slides is fine. Quality over compression.
- Splitting content across more slides (15-20) with each slide making ONE visual impact is better than cramming 10 slides with multiple elements each.
- Maximum 20 slides. If exceeding, suggest cutting content, not cramming.

## Technical Rules

- Every `<section>` MUST include a `<!-- SLIDE N: Title -->` comment marker.
- All images MUST be base64-encoded inline. No external URLs ever.
- Presets are the ONLY source for HTML structure, CSS, and JS. Never generate these from scratch.
- Default output path: `.claude/presentations/`
- Storyboard saved as `storyboard.md` next to the output HTML.
- If only one preset exists in `presets/`, auto-select it without asking.
- The `.component` wrapper with `.component-inner` is used for all data components. The `hero` component does NOT use the wrapper.

## Version & Updates

**Current version:** 1.2.0
**Repository:** https://github.com/universethec/presentation-i3

### Checking for updates

When the user asks "what version is presentation-i3?", "is presentation-i3 up to date?", or "update presentation-i3":

1. Report the current local version: **1.2.0**
2. Check the latest version on GitHub:
   ```bash
   git -C ~/.claude/skills/presentation-i3 fetch origin --tags 2>/dev/null && git -C ~/.claude/skills/presentation-i3 describe --tags --abbrev=0 origin/main 2>/dev/null
   ```
3. Compare local vs remote:
   - If same: "Presentation Builder is up to date (v1.2.0)."
   - If remote is newer: "Update available: v{local} → v{remote}. Run update?"
   - If no .git directory: "No git repo linked yet. To enable updates, clone from the repository URL."

### Running the update

When the user confirms they want to update:

1. **Stash local preset customizations:**
   ```bash
   cd ~/.claude/skills/presentation-i3 && git stash --include-untracked
   ```
2. **Pull the latest:**
   ```bash
   cd ~/.claude/skills/presentation-i3 && git pull origin main --tags
   ```
3. **Restore local data:**
   ```bash
   cd ~/.claude/skills/presentation-i3 && git stash pop 2>/dev/null
   ```
4. **Show changelog:**
   ```bash
   cd ~/.claude/skills/presentation-i3 && git log v{old}..v{new} --oneline
   ```
5. Report what changed and confirm the new version.

### What updates can change
- SKILL.md (workflow improvements)
- references/*.md (new components, patterns, rules)
- presets/*.html (new or improved presets)

### What updates never touch
- User-created presentations in `.claude/presentations/`
- User-added custom presets (gitignored if placed in `presets/custom/`)
