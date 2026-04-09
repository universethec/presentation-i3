# Presentation i3 — Self-Contained Skill for Claude.ai

> Upload this file as Project Knowledge in a Claude.ai Project. Then ask Claude to build you a presentation.

## How this works

This is a presentation builder skill. Give Claude a brief, data, and optionally screenshots, and it will generate a self-contained HTML slide presentation you can open in any browser.

**Environment:** This file is designed for Claude.ai (Project Knowledge). For Claude Code, install the full skill: `git clone <repo> ~/.claude/skills/presentation-i3`

## Workflow — 5 Phases

Follow these phases in order. Do not skip phases. Do not start building before the storyboard is approved.

---

### Phase 1: Brief & Intake

**Goal:** Understand what to build and gather all inputs.

1. Ask the user: **"What's the audience?"** (stakeholders, team, investors, etc.)
2. Ask: **"What's the single takeaway?"** (one sentence they should remember)
3. This skill includes one preset: **impact3**. It will be used automatically.
4. Ask for **data sources** — paste or upload markdown files, documents, or descriptions of data to include.
5. Ask for **screenshots** — the user can upload images directly in the conversation, or note that no screenshots are needed.
6. Review all provided data sources. Inventory all uploaded screenshots (list filenames and note what each appears to show).

**Output:** You now have: audience, takeaway, preset, data, and screenshots.

---

### Phase 2: Storyboard

**Goal:** Propose a slide outline and get user approval before building anything.

1. Review the **Narrative Arcs** reference below — select the best arc for the brief.
2. Review the **Component Patterns** reference below — understand available component types and their auto-select triggers.
3. Review the **Screenshot Patterns** reference below — understand how to compose images with data.
4. Generate a **slide-by-slide outline**. For each slide, specify:

```
Slide N: "Title"
  Component: [component-name from Component Patterns]
  Data: [what data populates this slide]
  Screenshot: [uploaded image reference, or "none"]
  Insight: [the key text/takeaway for this slide]
```

5. Present the storyboard to the user.
6. Ask: **"Want to reorder, add, remove, or edit any slides?"**
7. Iterate until the user approves.

**Rules:**
- First slide should always be a `hero` component.
- Last slide should be actionable (recommendations, priorities, next steps).
- Each slide should make exactly one point.
- If a slide has both data and a screenshot, prefer a `screenshot-with-stat` or `annotated-screenshot` pattern.
- Maximum 15 slides recommended. If the storyboard exceeds 15, suggest cutting.

---

### Phase 3: Build

**Goal:** Generate the complete HTML presentation as an artifact.

1. Use the **impact3** preset template from the Preset section below.
2. Use the **Component Patterns** reference below for HTML patterns.
3. For each slide in the approved storyboard:
   a. Look up the component HTML pattern in the Component Patterns reference.
   b. Replace all `{{TOKEN}}` placeholders with actual data from the storyboard.
   c. If the slide references an uploaded screenshot:
      - If the user uploads screenshots in the conversation, describe what you see and note where they should appear. For the HTML output, use placeholder images with clear labels (e.g., `[Screenshot: Homepage hero section]`) and instruct the user to replace them with base64-encoded images after downloading.
      - If the user provides base64-encoded image data directly, use `data:image/png;base64,<encoded_data>` (or `data:image/jpeg;base64,...` for JPGs).
   d. If the slide uses a screenshot-pattern (before-after, annotated, etc.), apply the composition from the Screenshot Patterns reference.
   e. Wrap each slide in a comment marker and section tag:
      ```html
      <!-- SLIDE N: Title -->
      <section data-section="N" class="CLASS">
        <div class="section-inner">
          ...slide content...
        </div>
      </section>
      ```
   f. Use `class="hero"` for slide 0. Use `class="dense"` for data-heavy slides. Use `class="top-align"` for tall slides. Default: no extra class.
   g. Add `reveal` and `reveal-delay-N` classes to content elements for staggered animations. Pattern: kicker gets `reveal`, h2 gets `reveal reveal-delay-1`, first content block gets `reveal reveal-delay-2`, etc.

4. Replace tokens in the preset:
   - `{{SLIDES}}` -> all generated `<section>` blocks
   - `{{TITLE}}` -> presentation title from the brief
   - `{{SLIDE_COUNT}}` -> total number of slides
   - `{{DATE}}` -> today's date

5. Output as a single HTML artifact.

---

### Phase 4: Humanize

**Goal:** Strip AI writing patterns from all slide text.

1. Review the **Humanizer Rules** reference below.
2. Review all human-visible text in the generated HTML:
   - Headings (h1, h2, h3)
   - Kicker text (`.kicker`)
   - Subtitle text (`.subtitle`)
   - Callout text (`.callout`)
   - Stat labels and subtitles (`.stat-label`, `.stat-sub`, `.big-label`)
   - Priority descriptions (`.priority-text p`)
   - Any other text content visible to the reader
3. Apply humanizer rules:
   - Strip AI transitional phrases, promotional language, inflated adjectives
   - Replace with direct, human voice
   - Keep headlines short and punchy
   - Keep insights to one sentence
4. **NEVER modify:**
   - Numbers, percentages, data values
   - Technical terms and product names
   - CSS, JavaScript, or HTML structure
   - Tokens that have already been replaced with data
5. Apply all humanizer changes directly in the artifact before outputting it. The artifact you produce should already be humanized.
6. Do a final pass: check if any text still sounds like AI wrote it.

---

### Phase 5: Iterate

**Goal:** Refine the presentation based on user feedback.

1. Tell the user: **"View the artifact to review the presentation. Let me know what you'd like to change."**
2. Wait for feedback.
3. For each change request:
   a. Locate the slide using `<!-- SLIDE N: Title -->` comment markers.
   b. Regenerate the artifact with the changes applied — surgical changes, not full rebuilds where possible.
   c. If the request involves swapping a component type, use the Component Patterns reference for the new pattern.
   d. If new screenshots are provided, handle them as described in Phase 3.
   e. Any new or changed text goes through humanizer rules again.
4. After each round of edits, tell the user to review the updated artifact.
5. Repeat until the user is satisfied.

---

## Rules

- Every `<section>` MUST include a `<!-- SLIDE N: Title -->` comment marker.
- All images MUST be base64-encoded inline when possible. No external URLs ever. When base64 is not available, use clearly labeled placeholders.
- The preset below is the ONLY source for HTML structure, CSS, and JS. Never generate these from scratch.
- If the user does not specify a preset, use **impact3** (the only preset included in this file).
- Maximum recommended slides: 15. Warn if storyboard exceeds this.
- The `.component` wrapper with `.component-inner` is used for all data components. The `hero` component does NOT use the wrapper.
- Output the presentation as a single HTML artifact.

---

## Reference: Component Patterns

Reference for all available slide components. Each pattern uses `{{TOKEN}}` placeholders that get replaced with actual data during build.

All components except `hero` are wrapped in the `.component` container for the animated stroke treatment:

```html
<div class="component">
  <div class="component-inner">
    ...component content...
  </div>
</div>
```

---

### hero

**Use when:** first slide of any presentation
**Auto-select trigger:** title + subtitle + optional metadata items

Does NOT use the `.component` wrapper. Uses the `.hero` section class directly.

```html
<div class="kicker fade-in">{{KICKER}}</div>
<h1 class="fade-in" style="animation-delay:0.15s">{{TITLE_LINE1}}<br><strong>{{TITLE_STRONG}}</strong>{{TITLE_LINE2}}</h1>
<p class="subtitle fade-in" style="animation-delay:0.3s">{{SUBTITLE}}</p>
<div class="hero-meta fade-in" style="animation-delay:0.45s">
  {{META_ITEMS}}
</div>
```

Where `{{META_ITEMS}}` repeats:

```html
<div class="hero-meta-item">
  <div class="label">{{META_LABEL}}</div>
  <div class="value">{{META_VALUE}}</div>
</div>
```

---

### big-number

**Use when:** single KPI number to emphasize
**Auto-select trigger:** one number + one label + optional color

```html
<div class="component">
  <div class="component-inner">
    <div style="display:flex;align-items:baseline;gap:16px">
      <div class="big-number {{COLOR_CLASS}}">{{VALUE}}</div>
      <div class="big-label">{{LABEL}}</div>
    </div>
  </div>
</div>
```

`{{COLOR_CLASS}}` options: _(empty for default purple gradient)_, `red`, `green`, `yellow`

---

### stat-grid

**Use when:** 2-6 KPIs to compare side by side
**Auto-select trigger:** multiple items each with value + label

```html
<div class="component">
  <div class="component-inner">
    <div class="stat-grid">
      {{CARDS}}
    </div>
  </div>
</div>
```

Where `{{CARDS}}` repeats the stat-card inner pattern (without its own `.component` wrapper):

```html
<div class="stat-card {{VARIANT}}">
  <div class="stat-value" style="color: {{COLOR}}">{{VALUE}}</div>
  <div class="stat-label">{{LABEL}}</div>
  <div class="stat-sub">{{SUB}}</div>
</div>
```

---

### stat-card

**Use when:** single KPI card (used inside stat-grid or standalone)
**Auto-select trigger:** value + label + optional subtitle + optional variant

```html
<div class="component">
  <div class="component-inner">
    <div class="stat-card {{VARIANT}}">
      <div class="stat-value" style="color: {{COLOR}}">{{VALUE}}</div>
      <div class="stat-label">{{LABEL}}</div>
      <div class="stat-sub">{{SUB}}</div>
    </div>
  </div>
</div>
```

`{{VARIANT}}` options: _(empty for default)_, `highlight` (red accent border + bg), `success` (green accent border + bg)

---

### bar-chart

**Use when:** ranked list of items with values (horizontal bars)
**Auto-select trigger:** list of items with labels and numeric values

```html
<div class="component">
  <div class="component-inner">
    <div class="bar-chart">
      {{BARS}}
    </div>
  </div>
</div>
```

Where `{{BARS}}` repeats:

```html
<div class="bar-row">
  <div class="bar-label">{{LABEL}}</div>
  <div class="bar-track">
    <div class="bar-fill" style="width:{{WIDTH_PCT}}%;background:{{GRADIENT}}">{{VALUE}}</div>
  </div>
  <div class="bar-value">{{EXTRA}}</div>
</div>
```

---

### funnel

**Use when:** sequential conversion steps with drop-off rates
**Auto-select trigger:** ordered stages with values and percentages

```html
<div class="component">
  <div class="component-inner">
    <div class="funnel">
      {{STEPS}}
    </div>
  </div>
</div>
```

Where each step is:

```html
<div class="funnel-step">
  <div class="funnel-label">{{LABEL}}</div>
  <div class="funnel-bar-wrap">
    <div class="funnel-bar" style="width:{{WIDTH_PCT}}%;background:{{GRADIENT}}">{{VALUE}}</div>
  </div>
  <div class="funnel-pct">{{PCT}}</div>
  <div class="funnel-time">{{TIME}}</div>
</div>
```

And between steps, optional drop annotation:

```html
<div class="funnel-drop">
  <div class="funnel-drop-line"></div>
  <div class="funnel-drop-text">{{DROP_TEXT}}</div>
</div>
```

---

### data-table

**Use when:** structured comparison data in rows and columns
**Auto-select trigger:** tabular data with headers

```html
<div class="component">
  <div class="component-inner">
    <div class="table-wrap">
      <div class="tw-title">{{TABLE_TITLE}}</div>
      <table class="data-table">
        <thead><tr>{{HEADERS}}</tr></thead>
        <tbody>{{ROWS}}</tbody>
      </table>
    </div>
  </div>
</div>
```

Where `{{HEADERS}}` repeats:

```html
<th class="{{ALIGN}}">{{HEADER}}</th>
```

`{{ALIGN}}`: _(empty for left-align)_, `r` (right-align)

Where `{{ROWS}}` repeats:

```html
<tr>{{CELLS}}</tr>
```

Where `{{CELLS}}` repeats:

```html
<td class="{{CELL_CLASS}}">{{CELL_VALUE}}</td>
```

`{{CELL_CLASS}}` options: `r` (right-align), `em` (emphasis white), `red`, `green`, `purple`, `dim`

---

### callout

**Use when:** key insight or takeaway to highlight
**Auto-select trigger:** important text that needs visual emphasis

```html
<div class="component">
  <div class="component-inner">
    <div class="callout {{VARIANT}}">
      {{TEXT}}
    </div>
  </div>
</div>
```

`{{VARIANT}}` options: _(empty for default purple)_, `warn` (red), `good` (green)

Text can include `<strong>` tags for emphasis.

---

### comparison

**Use when:** comparing two things side by side with VS separator
**Auto-select trigger:** two items to compare directly

```html
<div class="component">
  <div class="component-inner">
    <div class="comparison">
      <div class="comparison-card">
        {{LEFT_CONTENT}}
      </div>
      <div class="comparison-vs">VS</div>
      <div class="comparison-card">
        {{RIGHT_CONTENT}}
      </div>
    </div>
  </div>
</div>
```

---

### two-col

**Use when:** two equal content blocks side by side
**Auto-select trigger:** two distinct content areas

```html
<div class="component">
  <div class="component-inner">
    <div class="two-col">
      <div>{{LEFT}}</div>
      <div>{{RIGHT}}</div>
    </div>
  </div>
</div>
```

---

### priority-list

**Use when:** ranked recommendations or action items with severity
**Auto-select trigger:** numbered items with severity levels

```html
<div class="component">
  <div class="component-inner">
    <div class="priority-list">
      {{ITEMS}}
    </div>
  </div>
</div>
```

Where `{{ITEMS}}` repeats:

```html
<div class="priority-item">
  <div class="priority-num {{SEVERITY}}">{{NUM}}</div>
  <div class="priority-text">
    <h4>{{TITLE}}</h4>
    <p>{{DESCRIPTION}}</p>
  </div>
</div>
```

`{{SEVERITY}}` options: `critical` (red), `high` (yellow), `strategic` (purple)

---

### dead-click-list

**Use when:** listing problematic UI elements or friction points
**Auto-select trigger:** list of elements with counts and optional bar widths

```html
<div class="component">
  <div class="component-inner">
    <div class="dead-click-list">
      {{ITEMS}}
    </div>
  </div>
</div>
```

Where `{{ITEMS}}` repeats:

```html
<div class="dead-click-item">
  <div class="dc-element">{{ELEMENT}}</div>
  <div class="dc-bar"><div class="dc-bar-fill" style="width:{{BAR_WIDTH}}%"></div></div>
  <div class="dc-count">{{COUNT}}</div>
  <div class="dc-users">{{USERS}}</div>
</div>
```

---

### sparkline-wrap

**Use when:** showing a trend line or mini chart
**Auto-select trigger:** time series data or trend visualization

```html
<div class="component">
  <div class="component-inner">
    <div class="sparkline-wrap">
      <div class="sparkline-head">
        <span class="sparkline-title">{{TITLE}}</span>
        <span class="sparkline-range">{{RANGE}}</span>
      </div>
      {{CHART_CONTENT}}
    </div>
  </div>
</div>
```

`{{CHART_CONTENT}}` is typically an inline SVG sparkline.

---

### browser-mock

**Use when:** showing a screenshot of a website or app in a browser frame
**Auto-select trigger:** a screenshot that needs a browser chrome frame

```html
<div class="component">
  <div class="component-inner">
    <div class="browser-mock">
      <div class="browser-chrome">
        <div class="browser-dots"><span></span><span></span><span></span></div>
        <div class="browser-url">{{URL}}</div>
      </div>
      <div class="browser-body">
        <img src="{{IMAGE_SRC}}" alt="{{ALT}}">
      </div>
    </div>
  </div>
</div>
```

---

### before-after

**Use when:** comparing two states (before/after screenshots)
**Auto-select trigger:** two screenshots showing change

```html
<div class="component">
  <div class="component-inner">
    <div class="two-col">
      <div>
        <h3 style="margin-bottom:12px;color:var(--gray-40)">{{BEFORE_LABEL}}</h3>
        <div class="browser-mock">
          <div class="browser-chrome">
            <div class="browser-dots"><span></span><span></span><span></span></div>
            <div class="browser-url">{{BEFORE_URL}}</div>
          </div>
          <div class="browser-body">
            <img src="{{BEFORE_IMAGE}}" alt="Before">
          </div>
        </div>
      </div>
      <div>
        <h3 style="margin-bottom:12px;color:var(--emerald-3)">{{AFTER_LABEL}}</h3>
        <div class="browser-mock">
          <div class="browser-chrome">
            <div class="browser-dots"><span></span><span></span><span></span></div>
            <div class="browser-url">{{AFTER_URL}}</div>
          </div>
          <div class="browser-body">
            <img src="{{AFTER_IMAGE}}" alt="After">
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

---

### annotated-screenshot

**Use when:** screenshot with callout labels pointing to specific areas
**Auto-select trigger:** screenshot + list of annotations with positions

```html
<div class="component">
  <div class="component-inner">
    <div class="annotated-container">
      <img src="{{IMAGE_SRC}}" alt="{{ALT}}">
      {{ANNOTATIONS}}
    </div>
  </div>
</div>
```

Where `{{ANNOTATIONS}}` repeats:

```html
<div class="annotation-badge" style="top:{{Y}}%;left:{{X}}%;background:{{COLOR}}">{{TEXT}}</div>
```

---

## Reference: Screenshot Patterns

Recipes for composing screenshots into slides. All `{{IMAGE_SRC}}` tokens are replaced with `data:image/png;base64,...` (or `data:image/jpeg;base64,...`) during build when available. When base64 data is not available, use clearly labeled placeholder text. Never use external URLs.

---

### single-screenshot

**Use when:** Showing one UI state or screen capture.

```html
<div class="component">
  <div class="component-inner">
    <div class="browser-mock">
      <div class="browser-chrome">
        <div class="browser-dots"><span></span><span></span><span></span></div>
        <div class="browser-url">{{URL}}</div>
      </div>
      <div class="browser-body">
        <img src="{{IMAGE_SRC}}" alt="{{ALT}}">
      </div>
    </div>
  </div>
</div>
```

---

### before-after

**Use when:** Comparing two states — current vs proposed, old vs new.

```html
<div class="component">
  <div class="component-inner">
    <div class="two-col">
      <div>
        <h3 style="margin-bottom:12px;color:var(--gray-40)">{{BEFORE_LABEL}}</h3>
        <div class="browser-mock">
          <div class="browser-chrome">
            <div class="browser-dots"><span></span><span></span><span></span></div>
            <div class="browser-url">{{BEFORE_URL}}</div>
          </div>
          <div class="browser-body">
            <img src="{{BEFORE_IMAGE}}" alt="Before">
          </div>
        </div>
      </div>
      <div>
        <h3 style="margin-bottom:12px;color:var(--emerald-3)">{{AFTER_LABEL}}</h3>
        <div class="browser-mock">
          <div class="browser-chrome">
            <div class="browser-dots"><span></span><span></span><span></span></div>
            <div class="browser-url">{{AFTER_URL}}</div>
          </div>
          <div class="browser-body">
            <img src="{{AFTER_IMAGE}}" alt="After">
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

---

### annotated-screenshot

**Use when:** Pointing out specific problems or features in a screenshot with positioned callout labels.

```html
<div class="component">
  <div class="component-inner">
    <div class="annotated-container">
      <img src="{{IMAGE_SRC}}" alt="{{ALT}}">
      {{ANNOTATIONS}}
    </div>
  </div>
</div>
```

Each annotation:
```html
<div class="annotation-badge" style="top:{{Y}}%;left:{{X}}%;background:{{COLOR}}">{{TEXT}}</div>
```

Common colors:
- Red issue: `rgba(255,109,109,0.9)`
- Yellow warning: `rgba(255,192,68,0.9)`
- Green positive: `rgba(170,242,225,0.9)` with `color:var(--sky-black)`
- Purple neutral: `rgba(106,108,251,0.9)`

---

### screenshot-with-stat

**Use when:** Pairing visual evidence with a data point — screenshot on one side, metric on the other.

```html
<div class="component">
  <div class="component-inner">
    <div class="two-col">
      <div>
        <div class="browser-mock">
          <div class="browser-chrome">
            <div class="browser-dots"><span></span><span></span><span></span></div>
            <div class="browser-url">{{URL}}</div>
          </div>
          <div class="browser-body">
            <img src="{{IMAGE_SRC}}" alt="{{ALT}}">
          </div>
        </div>
      </div>
      <div>
        <div style="display:flex;align-items:baseline;gap:16px">
          <div class="big-number {{COLOR_CLASS}}">{{VALUE}}</div>
          <div class="big-label">{{LABEL}}</div>
        </div>
        <p style="color:var(--gray-40);font-size:15px;line-height:1.8;margin-top:16px">{{INSIGHT}}</p>
      </div>
    </div>
  </div>
</div>
```

---

### screenshot-grid

**Use when:** Showing multiple screens at once — overview of different pages or states.

```html
<div class="component">
  <div class="component-inner">
    <div class="stat-grid" style="grid-template-columns: 1fr 1fr">
      {{SCREENSHOTS}}
    </div>
  </div>
</div>
```

Each screenshot cell:
```html
<div>
  <div class="browser-mock">
    <div class="browser-chrome">
      <div class="browser-dots"><span></span><span></span><span></span></div>
      <div class="browser-url">{{URL}}</div>
    </div>
    <div class="browser-body">
      <img src="{{IMAGE_SRC}}" alt="{{ALT}}">
    </div>
  </div>
  <div style="font-size:12px;color:var(--gray-40);margin-top:8px;text-align:center">{{CAPTION}}</div>
</div>
```

---

## Reference: Narrative Arcs

Story structures for organizing slides. Pick the arc that best fits the brief, then use the slide skeleton as a starting point for the storyboard.

---

### problem-evidence-insight-action

**Use when:** Audits, reviews, post-mortems, diagnostic presentations.
**Recommended slides:** 10-14

| Slot | Purpose | Suggested component |
|------|---------|-------------------|
| 1 | Hero — set context, scope, key numbers | hero |
| 2 | The big picture — overview metrics | stat-grid + callout |
| 3-4 | Problem evidence — data proving what's broken | bar-chart, funnel, data-table |
| 5-6 | Deep dives — worst areas with details | dead-click-list, comparison, annotated-screenshot |
| 7-8 | Supporting evidence — device splits, cohorts, segments | data-table, comparison |
| 9 | Synthesis — the full story in one visual | funnel or custom flow |
| 10-12 | Recommendations — ranked by priority | priority-list |

**Narrative flow:** "Here's what we looked at" -> "Here's what's broken" -> "Here's the proof" -> "Here's what to do about it"

---

### before-after

**Use when:** Redesign pitches, improvement proposals, migration plans.
**Recommended slides:** 8-12

| Slot | Purpose | Suggested component |
|------|---------|-------------------|
| 1 | Hero — the change being proposed | hero |
| 2-3 | Current state — screenshots + pain points | before-after, annotated-screenshot |
| 4-5 | The gap — data showing impact of current problems | bar-chart, big-number, callout |
| 6-7 | Proposed solution — what changes and why | before-after, screenshot-with-stat |
| 8-9 | Expected outcome — projected improvements | stat-grid, comparison |
| 10 | Next steps — timeline or action items | priority-list |

**Narrative flow:** "This is where we are" -> "This is what it's costing us" -> "This is where we could be" -> "Here's how we get there"

---

### funnel-walkthrough

**Use when:** Conversion analysis, growth reviews, user journey audits.
**Recommended slides:** 10-14

| Slot | Purpose | Suggested component |
|------|---------|-------------------|
| 1 | Hero — funnel overview with end-to-end conversion rate | hero |
| 2 | Full funnel — all stages at a glance | funnel |
| 3-6 | Deep dive per stage — biggest drop first, then next | big-number + bar-chart, data-table, dead-click-list |
| 7-8 | Cross-cutting issues — device, geography, cohort splits | comparison, data-table |
| 9 | Synthesis — where the biggest opportunities are | callout or stat-grid |
| 10-12 | Priorities — what to fix, in order | priority-list |

**Narrative flow:** "Here's the full funnel" -> "Here's where people drop off most" -> "Here's why" -> "Here's what to fix first"

---

### executive-summary

**Use when:** Board decks, investor updates, stakeholder briefings.
**Recommended slides:** 6-10

| Slot | Purpose | Suggested component |
|------|---------|-------------------|
| 1 | Hero — the single most important number | hero |
| 2-4 | Headline slides — one stat + one insight each | big-number + callout |
| 5-6 | Health check — cohort table or key segments | data-table, stat-grid |
| 7-8 | Risks or blockers | callout (warn), comparison |
| 9-10 | Recommendations or next steps | priority-list |

**Narrative flow:** "Here's the headline" -> "Here are the 3 things that matter" -> "Here's the health of the business" -> "Here's what we need"

**Key principle:** Every slide should be understandable in under 5 seconds. No deep dives — link to detailed decks for those.

---

## Reference: Humanizer Rules

Rules for stripping AI writing patterns from presentation text. Apply during Phase 4 (Humanize) and when editing text in Phase 5 (Iterate).

---

### Strip these patterns

#### Transitional filler
Remove: "Furthermore", "Moreover", "It's worth noting", "In conclusion", "Interestingly", "Additionally", "Notably", "Importantly", "It is essential to note"

#### Em dash overuse
Replace em dashes (—) with commas or periods. One em dash per slide maximum. Zero is better.

#### Rule-of-three padding
If a list of three items exists only for rhythm and the third adds no information, cut it to two. "Innovation, inspiration, and industry insights" -> "Innovation and practical insights"

#### Inflated language
Remove: "fundamental shift", "paradigm", "transformative", "groundbreaking", "revolutionary", "game-changing", "cutting-edge", "next-generation", "state-of-the-art"

#### Promotional superlatives
Remove: "remarkable", "extraordinary", "unprecedented", "stunning", "incredible", "unparalleled", "best-in-class"

#### Vague attributions
Remove: "experts say", "studies show", "research indicates", "industry leaders agree", "observers have noted". Replace with specific sources or remove entirely.

#### Hedging
Remove: "It could be argued that", "One might consider", "It is possible that", "There is evidence to suggest". State the claim directly or don't make it.

#### AI sentence starters
Remove: "Delve into", "In today's landscape", "It is essential to", "When it comes to", "In the realm of", "At its core", "Let's explore"

#### Symbolic inflation
Remove sentences that assign deep meaning to mundane observations. "This represents a pivotal moment in the evolution of..." -> state the fact.

#### Copula avoidance
Replace: "serves as", "stands as", "functions as", "acts as" -> use "is" or "are"

#### Negative parallelisms
Remove: "It's not just X, it's Y", "Not only... but also...", "It's not merely... it's..."

#### Sycophantic artifacts
Remove: "Great question!", "Absolutely!", "That's an excellent point", "I hope this helps"

---

### Preserve always

- **Exact numbers** — never round, never editorialize data. "1,448" stays "1,448", not "nearly 1,500"
- **Percentages** — keep exact. "70.9%" stays "70.9%"
- **Technical terms** — product names, feature names, protocol names
- **Direct quotes** — if attributed to a specific person or source
- **Specific dates** — "Feb 16 - Mar 18, 2026" stays exact

---

### Voice guidelines

#### Headlines
Short, punchy, no filler. Lead with the insight or the number.
- Bad: "A significant decline in user retention was observed across the platform"
- Good: "Users stopped coming back"
- Bad: "Comprehensive analysis reveals concerning conversion metrics"
- Good: "4.8% of visitors convert"

#### Callouts
Analyst notes, not marketing copy. State the fact, state why it matters. One to two sentences.
- Bad: "It's worth noting that this represents a significant opportunity for improvement in the user journey"
- Good: "84% drop off between CTA click and wallet connect. This is the biggest leak."

#### Stat labels
Bare minimum words. Just the metric name.
- Bad: "The observed bounce rate metric"
- Good: "Bounce rate"
- Bad: "Total number of unique users"
- Good: "Unique users"

#### Insight text
One sentence, direct. Lead with the number or the action.
- Bad: "Furthermore, it should be noted that mobile users experience a substantially higher rate of transaction failures compared to their desktop counterparts"
- Good: "Mobile transactions fail 16% more than desktop"

#### Priority descriptions
Plain language. Describe the problem like you're telling a colleague.
- Bad: "A transformative optimization of the onboarding funnel represents a pivotal opportunity"
- Good: "84% of people who click Launch App never connect their wallet. Something about the handoff is broken."

---

## Preset: impact3

The following is the complete HTML preset template. When building a presentation, copy this template and replace `{{SLIDES}}`, `{{TITLE}}`, `{{SLIDE_COUNT}}`, and `{{DATE}}` with the generated content.

```html
<!-- Generated: {{DATE}} -->
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{TITLE}}</title>
<style>
  :root {
    --i3-dark: #12002E;
    --gray-100: #1C1C22;
    --gray-80: #2B2B36;
    --gray-50: #6F6E80;
    --gray-40: #9593A6;
    --gray-20: #D0CFD9;
    --white: #FFFFFF;
    --dark-purple: #2A197D;
    --sky-purple: #4331E9;
    --purple-5: #6A6CFB;
    --purple-3: #B68EFF;
    --cyan-4: #00DDFB;
    --red-6: #FF6D6D;
    --red-2: #FFD2B9;
    --emerald-3: #AAF2E1;
    --yellow-5: #FFC044;
    --magenta-6: #EB5EDF;
    --blue-6: #0075FF;
    --i3-accent: #7821F7;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }
  html { overflow: hidden; height: 100%; }
  body {
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro', sans-serif;
    background: linear-gradient(to top left, #7821F7, #12002E);
    color: var(--white);
    line-height: 1.6;
    overflow: hidden;
    height: 100%;
  }

  h1, h2 { font-family: Impact, 'Arial Black', sans-serif; text-transform: uppercase; }
  h1 strong { font-weight: 600; color: #FFFFFF; -webkit-text-fill-color: #FFFFFF; background: none; -webkit-background-clip: unset; background-clip: unset; }

  /* --- NAV --- */
  .nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 100;
    padding: 16px 40px;
    display: flex; justify-content: space-between; align-items: center;
    background: rgba(18,0,46,0.85);
    backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
    border-bottom: 1px solid rgba(120,33,247,0.15);
  }
  .nav-logo { font-weight: 600; font-size: 14px; letter-spacing: -0.02em; color: var(--gray-40); }
  .nav-logo span { color: #7821F7; }
  .nav-progress { height: 2px; background: linear-gradient(90deg, #7821F7, #B68EFF); position: fixed; top: 0; left: 0; z-index: 101; transition: width 0.3s ease; }
  .nav-dots { display: flex; gap: 8px; align-items: center; }
  .nav-dot { width: 6px; height: 6px; border-radius: 50%; background: var(--gray-50); cursor: pointer; transition: all 0.3s ease; }
  .nav-dot.active { background: #7821F7; width: 24px; border-radius: 3px; }
  .nav-dot:hover { background: var(--gray-40); }
  .slide-counter { font-size: 12px; color: var(--gray-50); font-variant-numeric: tabular-nums; font-weight: 500; }
  .slide-counter .current { color: #7821F7; font-weight: 700; }
  .slide-arrows { display: flex; gap: 4px; align-items: center; }
  .slide-arrows button {
    width: 32px; height: 32px; border-radius: 8px; border: 1px solid rgba(120,33,247,0.15);
    background: rgba(28,28,34,0.6); color: var(--gray-40); cursor: pointer; display: flex;
    align-items: center; justify-content: center; transition: all 0.2s; font-size: 14px;
  }
  .slide-arrows button:hover { border-color: rgba(120,33,247,0.4); color: var(--white); background: rgba(28,28,34,0.9); }
  .slide-arrows button:disabled { opacity: 0.3; cursor: default; }
  .slide-hint {
    position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%); z-index: 100;
    font-size: 11px; color: var(--gray-50); letter-spacing: 0.08em;
    background: rgba(28,28,34,0.8); padding: 8px 20px; border-radius: 100px;
    border: 1px solid rgba(120,33,247,0.1); backdrop-filter: blur(12px);
    transition: opacity 0.5s; pointer-events: none;
  }

  /* --- SLIDES --- */
  section {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    padding: 64px 80px 40px;
    display: flex; flex-direction: column; justify-content: center;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.5s ease, transform 0.5s ease;
    transform: translateY(20px);
    overflow-y: auto;
    overflow-x: hidden;
    -ms-overflow-style: none;
    scrollbar-width: none;
    z-index: 1;
  }
  section::-webkit-scrollbar { display: none; }
  section.active {
    opacity: 1;
    pointer-events: auto;
    transform: translateY(0);
    z-index: 2;
  }
  section.exiting {
    opacity: 0;
    transform: translateY(-20px);
    z-index: 1;
  }
  section.dense {
    justify-content: center;
    padding-top: 60px;
    padding-bottom: 24px;
  }
  section.top-align {
    justify-content: flex-start;
    padding-top: 148px;
    padding-bottom: 24px;
  }
  .section-inner { max-width: 1200px; margin: 0 auto; width: 100%; }
  section img { object-fit: contain; border-radius: 16px; max-height: 80vh; }
  [style*="text-align:center"] img { max-height: 60vh; }
  section .section-inner > * { max-height: calc(100vh - 120px); overflow: hidden; }

  /* --- TYPOGRAPHY --- */
  .kicker { font-size: 11px; font-weight: 600; letter-spacing: 0.15em; text-transform: uppercase; color: #7821F7; margin-bottom: 6px; font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro', sans-serif; }
  h1 { font-size: clamp(48px, 7vw, 96px); font-weight: 300; letter-spacing: 0em; line-height: 0.95; margin-bottom: 32px; color: #FFFFFF; }
  h2 { font-size: clamp(32px, 4vw, 52px); font-weight: 300; letter-spacing: 0em; line-height: 1.05; margin-bottom: 12px; color: #FFFFFF; }
  h2 strong { font-weight: 600; }
  h3 { font-size: 18px; font-weight: 500; letter-spacing: 0em; margin-bottom: 8px; }
  .subtitle { font-size: 17px; color: rgba(255,255,255,0.7); max-width: 600px; line-height: 1.7; font-weight: 400; margin-bottom: 20px; }
  .big-number { font-size: clamp(48px, 8vw, 80px); font-weight: 700; letter-spacing: -0.05em; line-height: 1; background: linear-gradient(135deg, #7821F7, #B68EFF); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; }
  .big-number.red { background: linear-gradient(135deg, var(--red-6), var(--red-2)); -webkit-background-clip: text; background-clip: text; }
  .big-number.green { background: linear-gradient(135deg, var(--emerald-3), var(--cyan-4)); -webkit-background-clip: text; background-clip: text; }
  .big-number.yellow { background: linear-gradient(135deg, var(--yellow-5), var(--red-2)); -webkit-background-clip: text; background-clip: text; }
  .big-label { font-size: 15px; color: rgba(255,255,255,0.7); font-weight: 500; margin-top: 8px; letter-spacing: 0.02em; }

  /* --- HERO --- */
  .hero { display: flex; align-items: center; position: absolute; top:0;left:0;right:0;bottom:0; overflow: hidden; z-index: 1; }
  .hero::before { content: ''; position: absolute; top: -30%; right: -20%; width: 800px; height: 800px; background: radial-gradient(circle, rgba(120,33,247,0.2) 0%, transparent 70%); pointer-events: none; }
  .hero::after { content: ''; position: absolute; bottom: -20%; left: -10%; width: 600px; height: 600px; background: radial-gradient(circle, rgba(182,142,255,0.1) 0%, transparent 70%); pointer-events: none; }
  .hero-logo { position: relative; z-index: 1; }
  .hero-meta { display: flex; gap: 32px; margin-top: 48px; flex-wrap: wrap; }
  .hero-meta-item { padding: 16px 24px; background: rgba(18,0,46,0.6); border: 1px solid rgba(120,33,247,0.15); border-radius: 28px; }
  .hero-meta-item .label { font-size: 11px; font-weight: 600; letter-spacing: 0.1em; text-transform: uppercase; color: var(--gray-50); margin-bottom: 4px; }
  .hero-meta-item .value { font-size: 15px; font-weight: 500; color: var(--gray-20); }

  /* --- GRIDS --- */
  .stat-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 24px; margin-top: 48px; }
  .stat-card { background: rgba(18,0,46,0.5); border: 1px solid rgba(120,33,247,0.1); border-radius: 16px; padding: 20px 24px; transition: transform 0.3s ease, border-color 0.3s ease; }
  .stat-card:hover { transform: translateY(-2px); border-color: rgba(120,33,247,0.3); }
  .stat-card .stat-value { font-size: 32px; font-weight: 700; letter-spacing: -0.03em; line-height: 1.1; margin-bottom: 8px; }
  .stat-card .stat-label { font-size: 14px; color: rgba(255,255,255,0.7); font-weight: 500; }
  .stat-card .stat-sub { font-size: 13px; color: rgba(255,255,255,0.6); margin-top: 12px; line-height: 1.6; }
  .stat-card.highlight { border-color: rgba(255,109,109,0.3); background: linear-gradient(135deg, rgba(18,0,46,0.5), rgba(255,109,109,0.05)); }
  .stat-card.success { border-color: rgba(170,242,225,0.3); background: linear-gradient(135deg, rgba(18,0,46,0.5), rgba(170,242,225,0.05)); }
  .two-col { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; align-items: start; }
  .three-col { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 32px; }

  /* --- FUNNEL --- */
  .funnel { display: flex; flex-direction: column; gap: 0; margin-top: 24px; max-width: 800px; }
  .funnel-step { display: flex; align-items: center; gap: 24px; }
  .funnel-bar-wrap { flex: 1; }
  .funnel-bar { height: 40px; border-radius: 8px; display: flex; align-items: center; padding: 0 14px; font-weight: 600; font-size: 13px; position: relative; overflow: visible; white-space: nowrap; }
  .funnel-bar::after { content: ''; position: absolute; top: 0; left: 0; right: 0; bottom: 0; background: linear-gradient(90deg, transparent 60%, rgba(255,255,255,0.05)); }
  .funnel-label { width: 160px; font-size: 14px; color: rgba(255,255,255,0.7); text-align: right; flex-shrink: 0; }
  .funnel-pct { width: 80px; font-size: 14px; color: rgba(255,255,255,0.6); text-align: left; flex-shrink: 0; }
  .funnel-time { width: 80px; font-size: 12px; color: rgba(255,255,255,0.5); text-align: left; flex-shrink: 0; font-style: italic; }
  .funnel-drop { display: flex; align-items: center; gap: 24px; padding: 4px 0 4px 184px; }
  .funnel-drop-line { width: 2px; height: 24px; background: rgba(255,109,109,0.3); margin-left: 20px; }
  .funnel-drop-text { font-size: 13px; color: var(--red-6); font-weight: 500; }

  /* --- BAR CHART --- */
  .bar-chart { display: flex; flex-direction: column; gap: 8px; margin-top: 32px; }
  .bar-row { display: flex; align-items: center; gap: 16px; }
  .bar-label { width: 120px; font-size: 13px; color: var(--gray-40); text-align: right; flex-shrink: 0; }
  .bar-track { flex: 1; height: 26px; background: rgba(18,0,46,0.6); border-radius: 6px; overflow: hidden; }
  .bar-fill { height: 100%; border-radius: 6px; display: flex; align-items: center; padding: 0 12px; font-size: 12px; font-weight: 600; white-space: nowrap; }
  .bar-value { width: 80px; font-size: 12px; color: var(--gray-50); flex-shrink: 0; }

  /* --- TABLE --- */
  .data-table { width: 100%; border-collapse: collapse; margin-top: 24px; font-size: 13px; }
  .data-table th { text-align: left; padding: 10px 14px; font-size: 10px; font-weight: 600; letter-spacing: 0.1em; text-transform: uppercase; color: var(--gray-50); border-bottom: 1px solid rgba(120,33,247,0.15); }
  .data-table th.r { text-align: right; }
  .data-table td { padding: 10px 14px; border-bottom: 1px solid rgba(18,0,46,0.8); color: var(--gray-20); }
  .data-table td.r { text-align: right; font-variant-numeric: tabular-nums; font-weight: 500; }
  .data-table td.em { color: var(--white); font-weight: 600; }
  .data-table td.red { color: var(--red-6); font-weight: 600; }
  .data-table td.green { color: var(--emerald-3); }
  .data-table td.purple { color: #7821F7; font-weight: 600; }
  .data-table td.dim { color: var(--gray-50); }
  .data-table tr:hover td { background: rgba(18,0,46,0.4); }
  .table-wrap { background: rgba(18,0,46,0.5); border: 1px solid rgba(120,33,247,0.1); border-radius: 16px; padding: 20px; overflow: hidden; }
  .table-wrap .tw-title { font-size: 12px; font-weight: 600; color: var(--gray-40); letter-spacing: 0.05em; text-transform: uppercase; margin-bottom: 4px; }

  /* --- COMPARISON --- */
  .comparison { display: grid; grid-template-columns: 1fr auto 1fr; gap: 32px; align-items: center; margin-top: 24px; }
  .comparison-card { background: rgba(18,0,46,0.5); border-radius: 20px; padding: 28px; border: 1px solid rgba(120,33,247,0.1); }
  .comparison-vs { font-size: 14px; font-weight: 700; color: var(--gray-50); letter-spacing: 0.1em; }

  /* --- CALLOUT --- */
  .callout { background: linear-gradient(135deg, rgba(120,33,247,0.1), rgba(182,142,255,0.05)); border: 1px solid rgba(120,33,247,0.2); border-radius: 16px; padding: 20px 28px; margin-top: 32px; font-size: 15px; line-height: 1.7; color: rgba(255,255,255,0.75); }
  .callout strong { color: var(--white); }
  .callout.warn { background: linear-gradient(135deg, rgba(255,109,109,0.08), rgba(255,192,68,0.04)); border-color: rgba(255,109,109,0.2); }
  .callout.good { background: linear-gradient(135deg, rgba(170,242,225,0.08), rgba(0,221,251,0.04)); border-color: rgba(170,242,225,0.2); }

  /* --- DEAD CLICK LIST --- */
  .dead-click-list { display: flex; flex-direction: column; gap: 8px; margin-top: 24px; }
  .dead-click-item { display: flex; align-items: center; gap: 16px; padding: 8px 12px; border-radius: 10px; background: rgba(18,0,46,0.5); border: 1px solid rgba(255,109,109,0.05); }
  .dc-element { flex: 1; font-size: 13px; color: var(--gray-20); font-weight: 500; }
  .dc-element code { background: rgba(120,33,247,0.1); padding: 2px 8px; border-radius: 4px; font-size: 12px; font-family: 'SF Mono', monospace; color: var(--purple-3); }
  .dc-count { font-variant-numeric: tabular-nums; font-size: 13px; font-weight: 600; color: var(--red-6); width: 60px; text-align: right; }
  .dc-users { font-variant-numeric: tabular-nums; font-size: 11px; color: var(--gray-50); width: 50px; text-align: right; }
  .dc-bar { width: 120px; height: 6px; background: rgba(255,109,109,0.1); border-radius: 3px; overflow: hidden; }
  .dc-bar-fill { height: 100%; background: linear-gradient(90deg, var(--red-6), rgba(255,109,109,0.4)); border-radius: 3px; }

  /* --- PRIORITY LIST --- */
  .priority-list { display: flex; flex-direction: column; gap: 10px; margin-top: 32px; }
  .priority-item { display: flex; gap: 20px; align-items: flex-start; padding: 16px 20px; background: rgba(18,0,46,0.5); border-radius: 14px; border: 1px solid rgba(120,33,247,0.08); }
  .priority-num { width: 36px; height: 36px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 14px; font-weight: 700; flex-shrink: 0; }
  .priority-num.critical { background: rgba(255,109,109,0.15); color: var(--red-6); }
  .priority-num.high { background: rgba(255,192,68,0.15); color: var(--yellow-5); }
  .priority-num.strategic { background: rgba(120,33,247,0.15); color: #7821F7; }
  .priority-text h4 { font-size: 15px; font-weight: 600; margin-bottom: 4px; }
  .priority-text p { font-size: 13px; color: var(--gray-40); line-height: 1.6; }

  .divider { width: 48px; height: 2px; background: linear-gradient(90deg, #7821F7, #B68EFF); margin: 12px 0; }
  .pill { display: inline-block; padding: 4px 12px; border-radius: 100px; font-size: 11px; font-weight: 600; letter-spacing: 0.05em; }
  .pill.critical { background: rgba(255,109,109,0.15); color: var(--red-6); }
  .pill.high { background: rgba(255,192,68,0.15); color: var(--yellow-5); }
  .pill.strategic { background: rgba(120,33,247,0.15); color: #7821F7; }

  /* --- SPARKLINE --- */
  .sparkline-wrap { background: rgba(18,0,46,0.5); border: 1px solid rgba(120,33,247,0.1); border-radius: 16px; padding: 20px 24px 16px; margin-top: 24px; }
  .sparkline-head { display: flex; justify-content: space-between; margin-bottom: 8px; }
  .sparkline-title { font-size: 11px; font-weight: 600; color: var(--gray-40); letter-spacing: 0.08em; text-transform: uppercase; }
  .sparkline-range { font-size: 11px; color: var(--gray-50); }

  /* --- BROWSER MOCK --- */
  .browser-mock { border-radius: 12px; overflow: hidden; border: 1px solid rgba(255,255,255,0.08); }
  .browser-chrome { background: rgba(18,0,46,0.8); padding: 10px 16px; display: flex; align-items: center; gap: 8px; }
  .browser-dots { display: flex; gap: 6px; }
  .browser-dots span { width: 10px; height: 10px; border-radius: 50%; background: rgba(255,255,255,0.1); }
  .browser-url { flex: 1; background: rgba(255,255,255,0.05); border-radius: 6px; padding: 4px 12px; font-size: 11px; color: var(--gray-50); font-family: 'SF Mono', monospace; }
  .browser-body { background: rgba(18,0,46,0.5); }
  .browser-body img { width: 100%; display: block; }

  /* --- ANNOTATION --- */
  .annotated-container { position: relative; display: inline-block; }
  .annotated-container img { width: 100%; display: block; border-radius: 8px; }
  .annotation-badge { position: absolute; padding: 4px 10px; border-radius: 6px; font-size: 11px; font-weight: 600; white-space: nowrap; transform: translate(-50%, -50%); pointer-events: none; }

  /* --- COMPONENT CONTAINER --- */
  @property --angle {
    syntax: '<angle>';
    initial-value: 0deg;
    inherits: false;
  }
  .component {
    background: rgba(255, 255, 255, 0.04);
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    border-radius: 28px;
    position: relative;
    padding: 24px;
  }
  .component-inner { position: relative; z-index: 1; }
  .component::before {
    content: '';
    position: absolute;
    inset: 0;
    border-radius: 12px;
    padding: 1px;
    background: conic-gradient(
      from var(--angle),
      transparent 40%,
      rgba(255,255,255,0.3) 50%,
      transparent 60%
    );
    mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
    -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
    mask-composite: exclude;
    -webkit-mask-composite: xor;
    animation: stroke-rotate 3s linear infinite;
  }
  .component::after {
    content: '';
    position: absolute;
    inset: 0;
    border-radius: 12px;
    border: 1px solid rgba(255,255,255,0.05);
    pointer-events: none;
  }
  @keyframes stroke-rotate {
    to { --angle: 360deg; }
  }
  .component { margin-top: 48px; }
  .component + .component { margin-top: 12px; }

  .bg-glow { position: absolute; border-radius: 50%; pointer-events: none; filter: blur(120px); opacity: 0.4; }
  .reveal { opacity: 0; transform: translateY(20px); transition: opacity 0.6s cubic-bezier(0.22, 1, 0.36, 1), transform 0.6s cubic-bezier(0.22, 1, 0.36, 1); }
  .reveal.visible { opacity: 1; transform: translateY(0); }
  section.active .reveal { opacity: 1; transform: translateY(0); }
  .reveal-delay-1 { transition-delay: 0.1s; }
  .reveal-delay-2 { transition-delay: 0.2s; }
  .reveal-delay-3 { transition-delay: 0.3s; }
  .reveal-delay-4 { transition-delay: 0.4s; }
  .reveal-delay-5 { transition-delay: 0.5s; }
  .fade-in { animation: fadeIn 1s ease forwards; }
  @keyframes fadeIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

  @media (max-width: 900px) {
    section { padding: 72px 24px 40px; }
    .two-col, .comparison { grid-template-columns: 1fr; gap: 24px; }
    .three-col { grid-template-columns: 1fr; }
    .comparison-vs { text-align: center; }
    .funnel-label { width: 100px; font-size: 11px; }
    .stat-grid { grid-template-columns: 1fr 1fr; }
    .nav { padding: 12px 20px; }
    .bar-label { width: 80px; font-size: 11px; }
  }
  @media (max-width: 600px) { .stat-grid { grid-template-columns: 1fr; } .nav-dots { display: none; } }
</style>
</head>
<body>

<canvas id="waveCanvas" style="position:fixed;top:0;left:0;width:100%;height:100%;z-index:0;pointer-events:none"></canvas>

<div class="nav-progress" id="progress" style="width:0%"></div>
<nav class="nav">
  <div class="nav-logo">{{TITLE}}</div>
  <div style="display:flex;align-items:center;gap:20px">
    <div class="nav-dots" id="navDots"></div>
    <div class="slide-counter"><span class="current" id="slideNum">1</span> / <span id="slideTotal">{{SLIDE_COUNT}}</span></div>
    <div class="slide-arrows">
      <button id="prevBtn" onclick="goSlide(currentSlide-1)" disabled>&larr;</button>
      <button id="nextBtn" onclick="goSlide(currentSlide+1)">&rarr;</button>
    </div>
    <button id="fsBtn" onclick="if(!document.fullscreenElement){document.documentElement.requestFullscreen().catch(()=>{})}else{document.exitFullscreen()}" style="width:32px;height:32px;border-radius:8px;border:1px solid rgba(120,33,247,0.15);background:rgba(18,0,46,0.6);color:var(--gray-40);cursor:pointer;display:flex;align-items:center;justify-content:center;transition:all 0.2s;font-size:12px" title="Fullscreen (F)">&#x26F6;</button>
  </div>
</nav>
<div class="slide-hint" id="slideHint">Arrow keys to navigate · F for fullscreen</div>

{{SLIDES}}

<script>
/* --- WAVE CANVAS ANIMATION --- */
const waveCanvas = document.getElementById('waveCanvas');
const wCtx = waveCanvas.getContext('2d');
let waveTime = 0;

function resizeWaveCanvas() {
  waveCanvas.width = window.innerWidth;
  waveCanvas.height = window.innerHeight;
}
window.addEventListener('resize', resizeWaveCanvas);
resizeWaveCanvas();

function drawWaves() {
  wCtx.clearRect(0, 0, waveCanvas.width, waveCanvas.height);
  const w = waveCanvas.width;
  const h = waveCanvas.height;

  for (let i = 0; i < 10; i++) {
    wCtx.beginPath();
    wCtx.strokeStyle = `rgba(120, 33, 247, ${0.06 + i * 0.012})`;
    wCtx.lineWidth = 1.5;

    const baseY = h * 0.3 + i * (h * 0.05);
    const amplitude = h * 0.15 + i * 8;
    const frequency = 0.003 + i * 0.0002;
    const phase = waveTime * 0.4 + i * 0.8;

    for (let x = 0; x <= w; x += 3) {
      const y = baseY + Math.sin(x * frequency + phase) * amplitude
                + Math.sin(x * frequency * 0.5 + phase * 1.3) * (amplitude * 0.3);
      if (x === 0) wCtx.moveTo(x, y);
      else wCtx.lineTo(x, y);
    }
    wCtx.stroke();
  }

  waveTime += 0.015;
  requestAnimationFrame(drawWaves);
}
drawWaves();

/* --- SLIDE ENGINE --- */
const slides = document.querySelectorAll('section[data-section]');
const totalSlides = slides.length;
let currentSlide = 0;

document.getElementById('slideTotal').textContent = totalSlides;

// Build nav dots
const navDots = document.getElementById('navDots');
slides.forEach((s, i) => {
  const dot = document.createElement('div');
  dot.className = 'nav-dot' + (i === 0 ? ' active' : '');
  dot.addEventListener('click', () => goSlide(i));
  navDots.appendChild(dot);
});

// Initialize first slide
slides[0].classList.add('active');

function goSlide(idx) {
  if (idx === currentSlide || idx < 0 || idx >= totalSlides) return;

  // Cancel any in-progress transition
  slides.forEach(s => { s.classList.remove('active', 'exiting'); });

  const next = slides[idx];
  next.scrollTop = 0;
  next.classList.add('active');

  currentSlide = idx;
  updateUI();
}

function updateUI() {
  document.getElementById('slideNum').textContent = currentSlide + 1;
  document.getElementById('progress').style.width = ((currentSlide / (totalSlides - 1)) * 100) + '%';
  document.querySelectorAll('.nav-dot').forEach((d, i) => d.classList.toggle('active', i === currentSlide));
  document.getElementById('prevBtn').disabled = currentSlide === 0;
  document.getElementById('nextBtn').disabled = currentSlide === totalSlides - 1;
  if (currentSlide > 0) document.getElementById('slideHint').style.opacity = '0';
}

// Keyboard navigation
document.addEventListener('keydown', (e) => {
  if (e.key === 'ArrowRight' || e.key === 'ArrowDown' || e.key === ' ') {
    e.preventDefault();
    goSlide(currentSlide + 1);
  } else if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') {
    e.preventDefault();
    goSlide(currentSlide - 1);
  } else if (e.key === 'Home') {
    e.preventDefault();
    goSlide(0);
  } else if (e.key === 'End') {
    e.preventDefault();
    goSlide(totalSlides - 1);
  } else if (e.key === 'f' || e.key === 'F') {
    if (!document.fullscreenElement) {
      document.documentElement.requestFullscreen().catch(() => {});
    } else {
      document.exitFullscreen();
    }
  }
});

// Hide hint after 4 seconds
setTimeout(() => { document.getElementById('slideHint').style.opacity = '0'; }, 4000);
</script>
</body>
</html>
```
