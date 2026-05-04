---
name: presentation-i3
description: Generate self-contained HTML slide presentations from briefs, data, and screenshots. Two presets included — iMPACT3 (company brand deck) and Krystee (retreat/offsite deck, fixed 1920×1080 canvas). Produces single-file decks with base64-inlined images and fonts that work anywhere — browser, Netlify, email. Use when asked to create a presentation, slide deck, pitch deck, or data story.
---

# Presentation i3 — Self-Contained Skill for Claude.ai

> Upload this file directly in Claude.ai's **Skills** UI, or as **Project Knowledge** in a Claude.ai Project. Then ask Claude to build you a presentation.

## How this works

This is a presentation builder skill. Give Claude a brief, data, and optionally screenshots, and it will generate a self-contained HTML slide presentation you can open in any browser.

**Environment:** This file is designed for Claude.ai (Skills upload or Project Knowledge). For Claude Code, install the full skill: `git clone https://github.com/universethec/presentation-i3 ~/.claude/skills/presentation-i3`

## Workflow — 5 Phases

Follow these phases in order. Do not skip phases. Do not start building before the storyboard is approved.

---

### Phase 1: Brief & Intake

**Goal:** Understand what to build and gather all inputs.

1. Ask the user: **"What's the audience?"** (stakeholders, team, investors, etc.)
2. Ask: **"What's the single takeaway?"** (one sentence they should remember)
3. This skill includes two presets: **iMPACT3** (default — company brand deck, fluid responsive layout, deep purple base, navigation chrome) and **Krystee** (retreat/offsite deck — fixed 1920×1080 canvas, Inter Tight display type, departmental color system in purple/gold/green, big hero typography). Ask the user: **"Which preset — iMPACT3 or Krystee?"** Save the choice for use in later phases. If the user does not specify and the deck context is generic, default to iMPACT3.
4. Ask for **data sources** — paste or upload markdown files, documents, or descriptions of data to include.
5. Ask for **screenshots** — the user can upload images directly in the conversation, or note that no screenshots are needed.
6. Review all provided data sources. Inventory all uploaded screenshots (list filenames and note what each appears to show).

**Output:** You now have: audience, takeaway, preset, data, and screenshots.

---

### Phase 2: Storyboard

**Goal:** Propose a slide outline and get user approval before building anything.

1. Review the **Narrative Arcs** reference below — select the best arc for the brief.
2. Review the components reference matching the selected preset:
   - iMPACT3 → **Component Patterns** reference below
   - Krystee → **Krystee Component Patterns** reference below

   Understand available component types and their auto-select triggers. The two vocabularies do not overlap — only use components from the reference matching the selected preset.
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
- First slide should always be the preset's title component (iMPACT3: `hero`; Krystee: `cover`).
- Last slide should be actionable (recommendations, priorities, next steps).
- Each slide should make exactly one point.
- If a slide has both data and a screenshot, prefer a `screenshot-with-stat` or `annotated-screenshot` pattern.
- Maximum 15 slides recommended. If the storyboard exceeds 15, suggest cutting.

---

### Phase 3: Build

**Goal:** Generate the complete HTML presentation as an artifact.

1. Use the preset template that matches the user's selection — **Preset: impact3** or **Preset: krystee** section below.
2. Use the components reference matching the selected preset (**Component Patterns** for iMPACT3, **Krystee Component Patterns** for Krystee) for HTML patterns.
3. **iMPACT3 logo (MANDATORY when using iMPACT3 preset):** Slide 0 must include the Impact3 logo SVG as its first element. Paste the full block from the **Impact3 Logo SVG** section (inside Component Patterns) verbatim — do not modify the path data, viewBox, fill color, or the `.hero-logo` wrapper div. Krystee's `cover` component also uses the Impact3 logo (the deck is iMPACT3-branded), but the `cover` CSS auto-inverts it to white via `filter: brightness(0) invert(1)` — do not pre-color.
4. For each slide in the approved storyboard:
   a. Look up the component HTML pattern in the components reference for the selected preset.
   b. Replace all `{{TOKEN}}` placeholders with actual data from the storyboard.
   c. If the slide references an uploaded screenshot:
      - If the user uploads screenshots in the conversation, describe what you see and note where they should appear. For the HTML output, use placeholder images with clear labels (e.g., `[Screenshot: Homepage hero section]`) and instruct the user to replace them with base64-encoded images after downloading.
      - If the user provides base64-encoded image data directly, use `data:image/png;base64,<encoded_data>` (or `data:image/jpeg;base64,...` for JPGs).
   d. If the slide uses a screenshot-pattern (before-after, annotated, etc.), apply the composition from the Screenshot Patterns reference.
   e. Wrap each slide in a comment marker and section tag, **per preset**:

      **iMPACT3:**
      ```html
      <!-- SLIDE N: Title -->
      <section data-section="N" class="CLASS">
        <div class="section-inner">
          ...slide content...
        </div>
      </section>
      ```

      **Krystee:**
      ```html
      <!-- SLIDE N: Title -->
      <section data-label="N Title" class="OPTIONAL_THEME_CLASSES">
        ...slide content (no .section-inner wrapper) ...
      </section>
      ```
      The Krystee scaler reads `data-label` for navigation. Theme classes: `orange`, `cream`, `white`, `dept-graphics`, `dept-web`, `dept-video`, `section-divider`, or any combination.

   f. **iMPACT3 only:** Use `class="hero"` for slide 0. Use `class="dense"` for data-heavy slides. Use `class="top-align"` for tall slides. Default: no extra class.
      **Krystee only:** Apply theme classes per the Krystee components reference — `cover` slide takes no class, chapter dividers take `orange section-divider [dept-X]`, the manifesto component takes `orange`, and most content slides are unclassed (default dark).
   g. **iMPACT3 only:** Add `reveal` and `reveal-delay-N` classes to content elements for staggered animations. Pattern: kicker gets `reveal`, h2 gets `reveal reveal-delay-1`, first content block gets `reveal reveal-delay-2`, etc. Krystee uses scaler-driven slide transitions and does not use `reveal` classes.

5. Replace tokens in the preset:
   - `{{SLIDES}}` -> all generated `<section>` blocks
   - `{{TITLE}}` -> presentation title from the brief
   - `{{SLIDE_COUNT}}` -> total number of slides
   - `{{DATE}}` -> today's date

6. Output as a single HTML artifact.

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
   c. If the request involves swapping a component type, use the components reference matching the selected preset (Component Patterns or Krystee Component Patterns) for the new pattern.
   d. If new screenshots are provided, handle them as described in Phase 3.
   e. Any new or changed text goes through humanizer rules again.
4. After each round of edits, tell the user to review the updated artifact.
5. Repeat until the user is satisfied.

---

## Rules

- Every `<section>` MUST include a `<!-- SLIDE N: Title -->` comment marker.
- All images MUST be base64-encoded inline when possible. No external URLs ever. When base64 is not available, use clearly labeled placeholders.
- The presets below are the ONLY source for HTML structure, CSS, and JS. Never generate these from scratch.
- If the user does not specify a preset, default to **impact3**. The Krystee preset is offered when the deck is for an offsite/retreat or when the user explicitly asks.
- The `.component`/`.section-inner` wrappers are iMPACT3 only. Krystee sections are direct children of `<deck-stage>` and do not use those wrappers.
- Maximum recommended slides: 15. Warn if storyboard exceeds this.
- For iMPACT3: the `.component` wrapper with `.component-inner` is used for all data components. The `hero` component does NOT use the wrapper.
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

**MANDATORY for impact3 preset:** The hero slide MUST include the Impact3 logo SVG before the kicker. Paste the exact `{{LOGO}}` block from the **Impact3 Logo SVG** section below as the first element inside the hero. This is not optional — every impact3 presentation must show the logo on the title slide.

```html
{{LOGO}}
<div class="kicker fade-in" style="animation-delay:0.15s">{{KICKER}}</div>
<h1 class="fade-in" style="animation-delay:0.3s">{{TITLE_LINE1}}<br><strong>{{TITLE_STRONG}}</strong>{{TITLE_LINE2}}</h1>
<p class="subtitle fade-in" style="animation-delay:0.45s">{{SUBTITLE}}</p>
<div class="hero-meta fade-in" style="animation-delay:0.6s">
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

### Impact3 Logo SVG

**This is `{{LOGO}}`.** Paste this exact block verbatim into every impact3 hero slide as the first child, before the `.kicker`. Do not modify the SVG path data, dimensions, fill color, or wrapper classes.

```html
    <div class="hero-logo fade-in">
      <svg width="280" height="57" viewBox="0 0 743 152" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M1.56119 64.1119C2.98045 63.5458 5.3932 62.4135 8.79944 60.7152C12.2057 59.0169 15.8958 57.3186 19.8697 55.6202C23.8436 53.8274 27.6757 52.2235 31.3657 50.8082C35.1504 49.3929 38.0836 48.6853 40.1652 48.6853C42.2468 48.6853 43.4768 49.2514 43.8553 50.3837C44.2337 51.5158 44.423 52.7896 44.423 54.2049V133.177C44.423 137.234 44.7541 140.584 45.4165 143.226C46.1734 145.773 47.498 147.99 49.3904 149.877H1.84504C3.7374 147.99 5.01473 145.773 5.67706 143.226C6.434 140.584 6.81247 137.234 6.81247 133.177V78.8307C6.81247 78.3589 6.81247 77.6513 6.81247 76.7078C6.81247 75.6699 6.67054 74.5849 6.38669 73.4527C6.19745 72.3204 5.77167 71.2354 5.10935 70.1975C4.44703 69.1597 3.45354 68.4049 2.1289 67.9331C1.93966 67.8387 1.51388 67.6972 0.851559 67.5085C0.283853 67.3198 0 66.8009 0 65.9517C0 65.1969 0.189235 64.7251 0.567706 64.5364C1.04079 64.3477 1.37196 64.2062 1.56119 64.1119ZM4.54165 20.6629C4.54165 14.4357 6.434 9.43512 10.2187 5.66106C14.0034 1.88703 19.0181 0 25.2629 0C31.5077 0 36.5224 1.88703 40.3071 5.66106C44.0918 9.43512 45.9842 14.4357 45.9842 20.6629C45.9842 26.8901 44.0918 31.8908 40.3071 35.6648C36.5224 39.4389 31.5077 41.3259 25.2629 41.3259C19.0181 41.3259 14.0034 39.4389 10.2187 35.6648C6.434 31.8908 4.54165 26.8901 4.54165 20.6629Z" fill="#F9F9F9"/>
<path d="M51.4452 8.34961H96.5776C99.9838 8.34961 102.68 9.43465 104.667 11.6047C106.654 13.6805 108.499 17.0299 110.202 21.6532C111.906 26.182 113.751 32.079 115.738 39.344C117.725 46.5147 120.421 55.1951 123.827 65.3851C127.234 55.1951 129.93 46.5147 131.917 39.344C133.904 32.079 135.749 26.182 137.452 21.6532C139.155 17.0299 141.001 13.6805 142.988 11.6047C144.974 9.43465 147.671 8.34961 151.077 8.34961H195.926C194.034 10.2366 192.709 12.5011 191.952 15.1429C191.29 17.6904 190.959 20.9927 190.959 25.0498V133.177C190.959 137.234 191.29 140.583 191.952 143.225C192.709 145.773 194.034 147.99 195.926 149.877H148.381C150.273 147.99 151.55 145.773 152.213 143.225C152.97 140.583 153.348 137.234 153.348 133.177V50.8077C153.348 49.2038 153.301 47.5054 153.206 45.7128C153.112 43.8257 152.875 42.1274 152.497 40.6178C152.118 39.1082 151.55 37.8816 150.793 36.9381C150.131 35.9002 149.232 35.3813 148.097 35.3813C146.772 35.3813 145.258 36.9853 143.555 40.1932C141.947 43.3068 140.244 47.4111 138.446 52.5061C136.743 57.6011 135.04 63.3566 133.337 69.7725C131.633 76.1884 129.978 82.6514 128.369 89.1617C126.95 95.1058 125.578 100.861 124.253 106.428C123.023 111.995 121.793 116.948 120.563 121.288C119.428 125.628 118.245 129.12 117.015 131.761C115.785 134.309 114.46 135.583 113.041 135.583C111.811 135.583 110.392 134.167 108.783 131.337C107.269 128.506 105.661 124.874 103.958 120.439C102.349 115.91 100.646 110.863 98.8484 105.296C97.0507 99.7291 95.2529 94.2095 93.4552 88.7371C92.8875 87.0388 92.0832 84.5856 91.0424 81.3777C90.0016 78.0754 88.7716 74.4429 87.3523 70.4801C86.0277 66.5174 84.5611 62.4602 82.9526 58.3088C81.4387 54.1572 79.8776 50.3831 78.2691 46.9865C76.7552 43.5898 75.2413 40.8065 73.7274 38.6364C72.3081 36.4663 71.0308 35.3813 69.8954 35.3813C68.76 35.3813 67.8138 35.9002 67.0569 36.9381C66.3945 37.8816 65.8741 39.1082 65.4957 40.6178C65.1172 42.1274 64.8807 43.8257 64.786 45.7128C64.6914 47.5054 64.6441 49.2038 64.6441 50.8077C64.6441 58.356 65.4484 65.2908 67.0569 71.6123C68.6654 77.8395 70.6523 83.595 73.0178 88.8786C75.3832 94.1623 77.9379 99.0214 80.6818 103.456C83.5203 107.89 86.1223 112.089 88.4878 116.052C90.8532 119.92 92.8402 123.6 94.4487 127.091C96.0572 130.582 96.8614 134.026 96.8614 137.422C96.8614 141.763 95.3002 144.923 92.1778 146.905C89.0555 148.886 85.3654 149.877 81.1076 149.877H51.4452C53.3376 147.99 54.6149 145.773 55.2772 143.225C56.0341 140.583 56.4126 137.234 56.4126 133.177V25.0498C56.4126 20.9927 56.0341 17.6904 55.2772 15.1429C54.6149 12.5011 53.3376 10.2366 51.4452 8.34961Z" fill="#F9F9F9"/>
<path d="M198.775 8.34961H255.971C264.581 8.34961 271.914 9.43465 277.97 11.6047C284.025 13.6805 288.945 16.5582 292.73 20.2379C296.515 23.9176 299.259 28.2578 300.962 33.2584C302.759 38.1646 303.658 43.4955 303.658 49.2509C303.658 54.7233 302.807 60.243 301.104 65.8097C299.495 71.2821 296.846 76.2356 293.156 80.6701C289.56 85.0102 284.877 88.5956 279.105 91.4261C273.333 94.1623 266.332 95.5304 258.1 95.5304C251.855 95.5304 246.32 94.6812 241.495 92.9829C236.764 91.2846 232.648 89.3976 229.147 87.3218C225.741 85.2461 222.855 83.3591 220.489 81.6608C218.124 79.9624 216.232 79.1133 214.812 79.1133C212.636 79.1133 211.548 80.2927 211.548 82.6514C211.548 85.6707 212.447 88.4541 214.245 91.0015C216.137 93.4547 218.502 95.9078 221.341 98.3609C224.18 100.72 227.207 103.173 230.424 105.72C233.641 108.173 236.669 110.863 239.508 113.787C242.346 116.712 244.664 119.967 246.462 123.553C248.354 127.044 249.301 131.054 249.301 135.583C249.301 136.998 249.111 138.555 248.733 140.253C248.354 141.857 247.739 143.414 246.888 144.923C246.036 146.339 244.948 147.518 243.623 148.462C242.393 149.405 240.927 149.877 239.224 149.877H198.775C200.667 147.99 201.944 145.773 202.607 143.225C203.364 140.583 203.742 137.234 203.742 133.177V25.0498C203.742 20.9927 203.364 17.6904 202.607 15.1429C201.944 12.5011 200.667 10.2366 198.775 8.34961ZM253.558 65.9512C258.478 65.9512 263.446 65.8097 268.461 65.5267C273.57 65.2436 278.159 64.6303 282.227 63.6868C286.391 62.7433 289.75 61.3752 292.304 59.5825C294.953 57.6955 296.278 55.1479 296.278 51.9399C296.278 48.732 294.67 46.2317 291.453 44.439C288.236 42.552 284.214 41.1367 279.389 40.1932C274.563 39.2497 269.312 38.6364 263.635 38.3533C257.958 38.0703 252.659 37.9288 247.739 37.9288C244.144 37.9288 240.17 38.0703 235.818 38.3533C231.56 38.6364 227.586 39.2497 223.896 40.1932C220.206 41.1367 217.131 42.552 214.67 44.439C212.21 46.2317 210.98 48.732 210.98 51.9399C210.98 55.1479 212.258 57.6955 214.812 59.5825C217.462 61.3752 220.821 62.7433 224.889 63.6868C229.052 64.6303 233.641 65.2436 238.656 65.5267C243.671 65.8097 248.638 65.9512 253.558 65.9512Z" fill="#F9F9F9"/>
<path d="M283.713 149.877C286.646 147.424 289.011 143.744 290.809 138.838C292.607 133.837 294.405 128.365 296.202 122.421C299.041 112.797 301.501 104.069 303.583 96.238C305.759 88.3125 307.698 80.5285 309.401 72.8861C311.105 65.2436 312.713 57.4124 314.227 49.3925C315.741 41.3726 317.397 32.4564 319.194 22.6438C319.668 19.9077 319.668 17.2186 319.194 14.5768C318.721 11.935 317.681 9.85923 316.072 8.34961H378.945C377.337 9.85923 376.296 11.935 375.823 14.5768C375.35 17.2186 375.35 19.9077 375.823 22.6438C377.621 32.4564 379.277 41.3726 380.79 49.3925C382.304 57.4124 383.913 65.2436 385.616 72.8861C387.319 80.5285 389.212 88.3125 391.293 96.238C393.469 104.069 395.977 112.797 398.815 122.421C400.613 128.365 402.411 133.837 404.208 138.838C406.006 143.744 408.372 147.424 411.305 149.877H352.689C354.298 147.99 355.528 145.678 356.379 142.942C357.325 140.206 357.798 137.375 357.798 134.45C357.798 132.092 357.373 127.751 356.521 121.43C355.67 115.014 354.534 107.749 353.115 99.6347H332.252C334.712 105.579 336.935 111.476 338.922 117.326C341.004 123.081 342.045 128.931 342.045 134.875C342.045 136.573 341.903 138.319 341.619 140.111C341.335 141.81 340.767 143.414 339.916 144.923C339.159 146.339 338.118 147.518 336.793 148.462C335.469 149.405 333.766 149.877 331.684 149.877H283.713ZM351.837 93.266C350.513 86.4727 349.046 79.6794 347.438 72.8861C345.829 66.0928 344.079 59.9599 342.186 54.4874C340.389 49.0151 338.496 44.5805 336.509 41.1839C334.617 37.7872 332.677 36.0889 330.69 36.0889C328.893 36.0889 327.332 36.7022 326.007 37.9288C324.777 39.1553 323.783 40.8065 323.026 42.8822C322.269 44.8636 321.702 47.1752 321.323 49.817C321.039 52.4589 320.898 55.1951 320.898 58.0257C320.898 64.2529 321.749 70.2914 323.452 76.1412C325.25 81.8966 327.284 87.6049 329.555 93.266H351.837Z" fill="#F9F9F9"/>
<path d="M402.858 79.1135C402.858 70.3389 403.898 61.6113 405.98 52.9309C408.062 44.2506 411.231 36.4666 415.489 29.5789C419.747 22.5969 425.093 16.983 431.527 12.7372C438.055 8.39703 445.672 6.22695 454.377 6.22695C457.121 6.22695 459.77 6.51001 462.325 7.07612C464.879 7.64222 467.434 8.30268 469.989 9.0575C472.543 9.71796 475.193 10.3784 477.937 11.0389C480.775 11.605 483.803 11.888 487.02 11.888C489.48 11.888 491.514 11.605 493.123 11.0389C494.826 10.3784 496.292 9.71796 497.522 9.0575C498.753 8.30268 499.841 7.64222 500.787 7.07612C501.828 6.51001 502.916 6.22695 504.051 6.22695C505.47 6.22695 506.417 6.88741 506.89 8.20833C507.457 9.4349 507.647 11.0389 507.457 13.0203C507.363 15.0016 506.937 17.2189 506.18 19.672C505.518 22.1252 504.666 24.484 503.625 26.7484C502.963 28.258 502.111 30.0035 501.071 31.9849C500.124 33.8719 498.894 35.7118 497.381 37.5044C495.772 39.3915 493.832 41.4672 491.562 43.7316C489.385 45.9961 487.304 48.3549 485.317 50.808C483.33 53.1668 481.674 55.6672 480.349 58.3091C479.025 60.8565 478.362 63.404 478.362 65.9515C478.362 68.3103 479.119 70.386 480.633 72.1787C482.147 73.877 484.134 74.7262 486.594 74.7262C488.108 74.7262 489.007 74.4903 489.291 74.0186C489.575 73.5468 490.095 73.3109 490.852 73.3109C491.987 73.3109 492.792 74.2544 493.265 76.1415C493.738 78.0285 493.974 79.6796 493.974 81.0949C493.974 83.7367 493.359 86.2842 492.129 88.7374C490.899 91.1905 489.243 93.3606 487.162 95.2476C485.08 97.1346 482.667 98.6443 479.924 99.7765C477.274 100.909 474.436 101.475 471.408 101.475C468.569 101.475 465.826 100.956 463.176 99.918C460.622 98.7858 458.398 97.3234 456.506 95.5307C454.613 93.6436 453.099 91.4736 451.964 89.0204C450.829 86.5673 450.261 83.9255 450.261 81.0949C450.261 77.5096 450.971 74.396 452.39 71.7541C453.904 69.0179 455.749 66.612 457.925 64.5362C460.101 62.3662 462.419 60.4791 464.879 58.8752C467.434 57.2712 469.8 55.8559 471.976 54.6292C474.152 53.4026 475.95 52.2704 477.369 51.2326C478.883 50.1947 479.64 49.204 479.64 48.2605C479.64 46.4678 478.315 45.3356 475.666 44.8639C470.84 44.0147 466.535 42.6938 462.75 40.9011C458.966 39.1084 455.37 37.3157 451.964 35.5231C448.652 33.636 445.294 31.9849 441.887 30.5696C438.576 29.1543 434.886 28.4467 430.817 28.4467C428.452 28.4467 426.512 29.1072 424.998 30.4281C423.484 31.6547 422.301 33.2115 421.45 35.0985C420.598 36.8912 419.983 38.7782 419.605 40.7596C419.321 42.6466 419.179 44.2506 419.179 45.5715C419.179 50.3834 419.652 55.5729 420.598 61.1396C421.639 66.612 423.153 72.0844 425.14 77.5567C427.222 82.9348 429.729 88.1241 432.662 93.1247C435.595 98.1253 439.001 102.56 442.881 106.428C446.76 110.202 451.065 113.222 455.796 115.486C460.622 117.75 465.873 118.883 471.55 118.883C477.795 118.883 482.668 117.798 486.168 115.628C489.669 113.457 492.46 111.051 494.542 108.41C496.624 105.768 498.279 103.362 499.509 101.192C500.834 99.0217 502.348 97.9366 504.051 97.9366C504.903 97.9366 505.707 98.4084 506.464 99.3519C507.221 100.201 507.599 102.277 507.599 105.579C507.599 110.108 506.558 115.109 504.477 120.581C502.395 125.959 499.32 131.007 495.252 135.724C491.278 140.348 486.358 144.216 480.491 147.33C474.625 150.443 467.907 152 460.338 152C452.39 152 444.915 150.49 437.913 147.471C430.912 144.452 424.809 139.923 419.605 133.885C414.496 127.846 410.427 120.251 407.399 111.099C404.371 101.947 402.858 91.2849 402.858 79.1135Z" fill="#F9F9F9"/>
<path d="M513.278 8.34961H623.553V60.0071C623.553 60.9506 623.269 61.7526 622.701 62.4131C622.228 63.0735 621.471 63.4038 620.43 63.4038C619.295 63.4038 617.97 62.2244 616.456 59.8656C615.037 57.5068 613.476 54.5346 611.773 50.9493C610.164 47.3639 608.461 43.5427 606.663 39.4856C604.96 35.3341 603.257 31.4657 601.554 27.8803C599.851 24.295 598.148 21.3229 596.445 18.9641C594.742 16.6054 593.133 15.426 591.619 15.426C589.539 15.426 588.262 16.4167 587.789 18.398C587.41 20.2851 587.221 22.3136 587.221 24.4837V133.177C587.221 137.234 587.552 140.583 588.215 143.225C588.972 145.773 590.296 147.99 592.187 149.877H544.643C546.536 147.99 547.813 145.773 548.475 143.225C549.232 140.583 549.611 137.234 549.611 133.177V24.4837C549.611 22.3136 549.374 20.2851 548.901 18.398C548.523 16.4167 547.293 15.426 545.211 15.426C543.697 15.426 542.089 16.6054 540.385 18.9641C538.682 21.3229 536.979 24.295 535.276 27.8803C533.573 31.4657 531.823 35.3341 530.025 39.4856C528.322 43.5427 526.619 47.3639 524.916 50.9493C523.307 54.5346 521.793 57.5068 520.374 59.8656C518.955 62.2244 517.63 63.4038 516.4 63.4038C515.359 63.4038 514.555 63.0735 513.987 62.4131C513.514 61.7526 513.278 60.9506 513.278 60.0071V8.34961Z" fill="#F9F9F9"/>
<path d="M630.736 123.129C630.736 120.109 631.724 117.279 633.711 114.637C635.698 111.901 638.164 109.495 641.097 107.419C644.03 105.343 647.099 103.692 650.316 102.466C653.628 101.239 656.561 100.626 659.116 100.626C660.724 100.626 662.143 101.192 663.373 102.324C664.698 103.456 665.366 104.825 665.366 106.429C665.366 108.316 665.177 110.25 664.799 112.231C664.42 114.212 663.941 116.194 663.373 118.175C662.9 120.157 662.48 122.138 662.102 124.119C661.723 126.006 661.534 127.893 661.534 129.78C661.534 133.649 662.386 136.857 664.089 139.404C665.792 141.952 668.394 143.226 671.895 143.226C675.106 143.226 678.701 141.952 682.675 139.404C686.744 136.763 690.576 133.366 694.171 129.214C697.767 124.969 700.8 120.157 703.261 114.779C705.721 109.401 706.951 103.975 706.951 98.503C706.951 94.3515 705.478 91.0964 702.545 88.7376C699.707 86.3788 696.253 85.1994 692.184 85.1994C689.062 85.1994 686.223 85.4825 683.669 86.0486C681.114 86.6147 678.613 87.228 676.153 87.8885C673.787 88.5489 671.416 89.1622 669.05 89.7283C666.685 90.2944 664.13 90.5775 661.386 90.5775C657.129 90.5775 653.87 89.4924 651.599 87.3224C649.423 85.0579 648.329 82.2746 648.329 78.9723C648.329 75.1982 649.512 71.9431 651.877 69.2069C654.337 66.4707 657.507 64.0647 661.386 61.989C665.266 59.9133 669.719 58.0734 674.733 56.4694C679.748 54.8654 684.852 53.3086 690.056 51.7989C695.354 50.2893 700.517 48.7325 705.531 47.1285C710.546 45.4302 714.993 43.496 718.872 41.3259C722.752 39.1558 725.874 36.6555 728.24 33.825C730.7 30.9001 731.93 27.4563 731.93 23.4935C731.93 21.7952 731.409 20.1912 730.369 18.6816C729.328 17.0776 727.625 16.2756 725.259 16.2756C722.326 16.2756 718.825 17.6437 714.757 20.3799C710.688 23.1161 705.957 26.1825 700.558 29.5792C695.171 32.8815 689.015 35.9007 682.108 38.6369C675.295 41.3731 667.631 42.7412 659.116 42.7412C656.372 42.7412 653.681 42.4581 651.032 41.892C648.382 41.3259 646.017 40.5239 643.935 39.4861C641.948 38.4482 640.287 37.1745 638.962 35.6648C637.732 34.1552 637.123 32.5041 637.123 30.7114C637.123 28.1639 637.596 25.4277 638.542 22.5028C639.583 19.4836 640.76 16.7002 642.084 14.1527C643.504 11.6052 644.876 9.48233 646.2 7.784C647.62 5.99133 648.761 5.09499 649.612 5.09499C650.937 5.09499 652.167 5.37804 653.302 5.94415C654.438 6.51026 655.81 7.17072 657.418 7.92553C659.027 8.58599 661.061 9.24645 663.515 9.90691C666.076 10.473 669.387 10.7561 673.456 10.7561C677.992 10.7561 682.108 10.4258 685.798 9.76538C689.582 9.01057 693.225 8.25576 696.726 7.50095C700.227 6.65179 703.828 5.89697 707.518 5.23651C711.303 4.4817 715.466 4.1043 720.008 4.1043C725.685 4.1043 730.315 5.94415 733.911 9.62386C737.506 13.3036 739.31 17.974 739.31 23.635C739.31 30.334 737.979 36.0894 735.33 40.9013C732.681 45.7133 729.369 49.8176 725.395 53.2142C721.421 56.5166 717.116 59.2528 712.48 61.4229C707.938 63.593 703.686 65.48 699.712 67.084C695.739 68.6879 692.427 70.1032 689.778 71.3298C687.128 72.462 685.798 73.7358 685.798 75.151C685.798 76.1889 686.176 76.9437 686.933 77.4155C687.69 77.8872 688.642 78.1231 689.778 78.1231C692.238 78.1231 694.272 77.9344 695.88 77.557C697.584 77.1796 699.239 76.755 700.848 76.2832C702.551 75.8115 704.443 75.3869 706.525 75.0095C708.701 74.6321 711.534 74.4434 715.035 74.4434C718.725 74.4434 722.279 75.1038 725.685 76.4248C729.091 77.6513 732.072 79.444 734.626 81.8028C737.181 84.0672 739.209 86.8506 740.723 90.1529C742.237 93.3608 743 96.9934 743 101.05C743 109.636 741.196 117.043 737.601 123.27C734.011 129.403 729.274 134.451 723.408 138.414C717.542 142.376 710.877 145.301 703.403 147.188C695.928 148.981 688.352 149.877 680.688 149.877C674.917 149.877 669.056 149.17 663.095 147.754C657.134 146.339 651.741 144.452 646.916 142.093C642.185 139.64 638.3 136.81 635.272 133.602C632.244 130.299 630.736 126.808 630.736 123.129Z" fill="#F9F9F9"/>
      </svg>
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

---

## Reference: Krystee Component Patterns

Reference for all components available in the **Krystee preset**. Use this section ONLY when the user has selected the Krystee preset. For iMPACT3, use the **Component Patterns** section above.

The Krystee preset uses a fixed `1920×1080` `<deck-stage>` canvas with auto-scale-to-fit. Slides are direct children of `<deck-stage>` (NOT wrapped in `.component`).

---

### Slide marker convention

Every slide MUST use BOTH markers:

```html
<!-- SLIDE N: Title -->
<section data-label="N Title">
  ...
</section>
```

- The HTML comment is for the skill's edit/locate logic in Phase 5.
- The `data-label` is read by the deck-stage scaler for keyboard navigation and the slide-counter overlay.
- Keep `data-label` short (e.g., `"01 Cover"`, `"04 Graphics Divider"`).

---

### Section themes (apply as class on `<section>`)

| Class | Background | Text | When to use |
|---|---|---|---|
| _(none)_ | dark `#1A1A1A` | white | Default. Every content slide. |
| `orange` | accent `#A06ADC` | white | Section-divider slides, manifesto/key-message slides |
| `cream` | `#F6F2EF` | black | Soft-light slide for breathers, quotes |
| `white` | white | black | Pure light alternative |
| `dept-graphics` | dark, accent rebound to `#A06ADC` (purple) | white | Graphics chapter slides |
| `dept-web` | dark, accent rebound to `#D4B05A` (gold) | white | Web chapter slides |
| `dept-video` | dark, accent rebound to `#6FB58E` (green) | white | Video chapter slides |

Combine with `section-divider` class for big chapter intro slides:
`class="orange section-divider dept-web"` = gold-accent chapter divider.

---

### chrome (used on every slide)

Every slide gets a top chrome bar. Footer is opt-in (only on dividers, close).

```html
<div class="chrome-top">
  <div class="ct-left"><span>{{SECTION_NUM}}</span><span>{{SECTION_NAME}}</span></div>
  <div>{{VERSION_OR_DATE}}</div>
</div>

<!-- optional, on chapter dividers and close: -->
<div class="chrome-bottom">
  <div class="cb-left"><span class="pagenum">{{N}}</span><span>{{DECK_NAME}}</span></div>
  <div>{{FOOTER_RIGHT}}</div>
</div>
```

For the cover slide, the left chrome is just `<div class="ct-left">{{DECK_NAME}}</div>` (no section num/name).

---

### cover

**Use when:** first slide. Title + brand mark + bottom meta strip.

```html
<!-- SLIDE 1: {{TITLE}} -->
<section data-label="01 Cover">
  <div class="chrome-top">
    <div class="ct-left">{{DECK_NAME}}</div>
    <div>{{VERSION}}</div>
  </div>
  <div class="cover">
    <div class="top-line">
      <span class="logo">{{LOGO}}</span>
    </div>
    <div class="center">
      <div class="title">{{TITLE_LINE1}}<br><span class="o">{{TITLE_ACCENT}}</span></div>
      <div class="sub">{{SUBTITLE}}</div>
    </div>
    <div class="bottom-meta">
      <div class="meta-item"><div class="l">{{LABEL_1}}</div><div class="v">{{VALUE_1}}</div></div>
      <div class="meta-item"><div class="l">{{LABEL_2}}</div><div class="v">{{VALUE_2}}</div></div>
      <div class="meta-item"><div class="l">{{LABEL_3}}</div><div class="v">{{VALUE_3}}</div></div>
    </div>
  </div>
</section>
```

`{{LOGO}}` = full content of `references/impact3-logo.svg.html`. The cover CSS auto-inverts it to white via `filter: brightness(0) invert(1)` — do not pre-color.

---

### bigwords

**Use when:** a manifesto-style slide where one sentence is the whole slide. 140px display type.

```html
<!-- SLIDE N: {{HEADLINE}} -->
<section data-label="N {{LABEL}}">
  <div class="chrome-top">
    <div class="ct-left"><span>{{SECTION_NUM}}</span><span>{{SECTION_NAME}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div class="bigwords">
    <div class="line">{{LINE_1}}</div>
    <div class="line"><span class="accent">{{LINE_2_ACCENT}}</span></div>
  </div>
</section>
```

---

### section-divider (chapter intro)

**Use when:** introducing a new section/chapter. Title + intro on left, TOC list on right.

```html
<!-- SLIDE N: {{CHAPTER_NAME}} Divider -->
<section data-label="N {{CHAPTER_NAME}} Divider" class="orange section-divider {{DEPT_CLASS}}">
  <div class="chrome-top">
    <div class="ct-left"><span>{{CHAPTER_NUM}}</span><span>{{CHAPTER_NAME}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div class="grid">
    <div class="left-col">
      <div class="title-lg heading">{{CHAPTER_TITLE}}</div>
      <div class="intro">{{CHAPTER_INTRO}}</div>
      <div class="big-number">{{CHAPTER_NUM_BIG}}</div>
    </div>
    <div class="right-col">
      <div class="toc-list">
        <div class="toc-row"><div>{{ITEM_1}}</div><div class="page">→</div></div>
        <div class="toc-row"><div>{{ITEM_2}}</div><div class="page">→</div></div>
        <!-- repeat -->
      </div>
    </div>
  </div>
  <div class="chrome-bottom">
    <div class="cb-left"><span class="pagenum">{{N}}</span><span>{{DECK_NAME}}</span></div>
    <div></div>
  </div>
</section>
```

`{{DEPT_CLASS}}`: optional, one of `dept-graphics` / `dept-web` / `dept-video` to swap the accent.

---

### contents-page (3-column master TOC)

**Use when:** master table of contents listing all sections at once.

```html
<!-- SLIDE N: Contents -->
<section data-label="N Contents" class="contents-page">
  <div class="chrome-top">
    <div class="ct-left"><span>00</span><span>Contents</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div class="contents-grid">
    <div class="contents-section">
      <div class="contents-head"><div class="num">01</div><div class="name">{{SECTION_1_NAME}}</div></div>
      <div class="contents-row"><div class="pad"></div><div class="item">{{ITEM_1}}</div><div class="pn">{{PAGE}}</div></div>
      <!-- repeat rows -->
    </div>
    <!-- repeat sections (max 3 cols) -->
  </div>
</section>
```

---

### three-cards (sub-departments / role split)

**Use when:** introducing 3 parallel things side by side, each in its own colored card. Used for sub-departments, team roles, product pillars.

```html
<!-- SLIDE N: {{HEADLINE}} -->
<section data-label="N Sub-departments">
  <div class="chrome-top">
    <div class="ct-left"><span>{{SECTION_NUM}}</span><span>{{SECTION_NAME}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div style="display:flex;flex-direction:column;height:100%;padding:120px 80px 80px;gap:48px">
    <div class="title-md" style="font-size:72px;line-height:1;letter-spacing:-0.02em">{{HEADLINE}}</div>
    <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:24px;flex:1">
      <!-- repeat 3 cards. Use --dept-graphics, --dept-web, --dept-video for accent backgrounds -->
      <div style="background:var(--dept-graphics);color:var(--white);border-radius:8px;padding:48px 44px;display:flex;flex-direction:column;justify-content:space-between">
        <div style="display:flex;justify-content:space-between;align-items:flex-start">
          <div style="font-size:24px;font-weight:500;letter-spacing:0.04em;opacity:0.7">{{CARD_NUM}}</div>
          <div style="font-size:18px;font-weight:500;letter-spacing:0.08em;opacity:0.7">{{CARD_TAG}}</div>
        </div>
        <div>
          <div style="font-size:88px;line-height:0.95;font-weight:500;letter-spacing:-0.02em;margin-bottom:24px">{{CARD_NAME}}</div>
          <div style="font-size:22px;line-height:1.45;font-weight:500;opacity:0.92;max-width:380px">{{CARD_DESC}}</div>
        </div>
        <div style="display:flex;flex-wrap:wrap;gap:8px;margin-top:24px">
          <span style="border:1.5px solid rgba(255,255,255,0.5);padding:8px 16px;border-radius:999px;font-size:16px;font-weight:500">{{TAG_1}}</span>
          <!-- repeat tags -->
        </div>
      </div>
      <!-- repeat for 2nd and 3rd card -->
    </div>
  </div>
</section>
```

For light/gold cards (web), swap `color:var(--black)` and `rgba(0,0,0,0.4)` for the tag border.

---

### bento (capability grid)

**Use when:** showing 5–8 capabilities as a featured-cell + smaller cells layout.

```html
<!-- SLIDE N: {{NAME}} Detail -->
<section data-label="N {{NAME}} Detail" class="{{DEPT_CLASS}}">
  <div class="chrome-top">
    <div class="ct-left"><span>{{NUM}}</span><span>{{NAME}} — capabilities</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div class="service-head">
    <div class="left">
      <div class="num">{{NUM}}</div>
      <div class="name">{{NAME}}.</div>
    </div>
    <div class="right">{{INTRO_TEXT}}</div>
  </div>
  <div class="bento cols-4" style="flex:1">
    <div class="bento-cell fill-orange span-2">
      <div>
        <div class="bento-label">{{FEATURED_LABEL}}</div>
        <div class="bento-sub">{{FEATURED_DESC}}</div>
      </div>
      <div><span class="bento-tag">{{TAG_1}}</span><span class="bento-tag">{{TAG_2}}</span></div>
    </div>
    <div class="bento-cell">
      <div>
        <div class="bento-label">{{LABEL}}</div>
        <div class="bento-sub">{{DESC}}</div>
      </div>
      <span class="bento-tag">{{TAG}}</span>
    </div>
    <!-- repeat cells -->
  </div>
</section>
```

**Bento variants:**
- `cols-3` or `cols-4` on the `.bento` parent
- `.bento-cell` defaults: outline only
- `.bento-cell.fill-orange` — filled with accent color
- `.bento-cell.fill-white` — filled white, black text
- `.bento-cell.outline-orange` — accent-colored outline + accent text
- `.bento-cell.dim` — dashed, faded (use for "we want this but don't have it yet")
- `.bento-cell.span-2` — takes 2 columns (use for the feature/hero capability)

---

### pillar-grid (3 cards, one is the "primary home")

**Use when:** showing where something lives across 3 categories. One card is `primary` (highlighted), one is supporting, one is `dim` (deprioritized).

```html
<!-- SLIDE N: {{HEADLINE}} -->
<section data-label="N Pillar Alignment">
  <div class="chrome-top">
    <div class="ct-left"><span>{{NUM}}</span><span>{{SECTION_NAME}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div style="margin-top:64px;margin-bottom:24px">
    <div class="title-md">{{HEADLINE_PRE}} <span style="color:var(--orange)">{{HEADLINE_ACCENT}}</span></div>
    <div class="body-md" style="margin-top:24px;max-width:1000px;color:var(--gray-light)">{{INTRO}}</div>
  </div>
  <div class="pillar-grid">
    <div class="pillar-card">
      <div class="label">{{LABEL_1}}</div>
      <div>
        <div class="name">{{NAME_1}}</div>
        <div class="desc">{{DESC_1}}</div>
      </div>
    </div>
    <div class="pillar-card primary">
      <div class="label" style="color:rgba(0,0,0,0.6);opacity:1">{{LABEL_2}}</div>
      <div>
        <div class="name">{{NAME_2}}</div>
        <div class="desc" style="opacity:1;color:rgba(0,0,0,0.85)">{{DESC_2}}</div>
      </div>
    </div>
    <div class="pillar-card dim">
      <div class="label">{{LABEL_3}}</div>
      <div>
        <div class="name">{{NAME_3}}</div>
        <div class="desc">{{DESC_3}}</div>
      </div>
    </div>
  </div>
</section>
```

---

### num-list (numbered priority list)

**Use when:** 3–6 ordered items each with a title + paragraph. Looks like a numbered changelog.

```html
<!-- SLIDE N: {{HEADLINE}} -->
<section data-label="N {{LABEL}}">
  <div class="chrome-top">
    <div class="ct-left"><span>{{NUM}}</span><span>{{SECTION_NAME}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div style="margin-top:64px">
    <div class="title-md">{{HEADLINE_PRE}} <span style="color:var(--orange)">{{HEADLINE_ACCENT}}</span></div>
    <div class="body-md" style="margin-top:24px;max-width:1000px;color:var(--gray-light)">{{INTRO}}</div>
  </div>
  <div class="num-list">
    <div class="num-row">
      <div class="n">01</div>
      <div><h4>{{ITEM_TITLE_1}}</h4></div>
      <div><p>{{ITEM_BODY_1}}</p></div>
    </div>
    <!-- repeat -->
  </div>
</section>
```

---

### roadmap (3-stage timeline)

**Use when:** showing a phased plan (NOW / 60–90 / 90–180). Optionally pair with a "problems" sidebar on the left.

```html
<!-- SLIDE N: Roadmap -->
<section data-label="N Roadmap">
  <div class="chrome-top">
    <div class="ct-left"><span>{{NUM}}</span><span>{{SECTION_NAME}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div style="display:flex;flex-direction:column;height:100%;padding:80px;gap:48px">
    <div>
      <div style="font-size:18px;font-weight:500;letter-spacing:0.16em;color:var(--orange);margin-bottom:24px">{{KICKER}}</div>
      <div class="title-md" style="font-size:96px">{{HEADLINE_PRE}} <span style="color:var(--orange)">{{HEADLINE_ACCENT}}</span></div>
      <div style="font-size:26px;line-height:1.45;font-weight:500;color:var(--gray-light);margin-top:24px;max-width:1100px">{{INTRO}}</div>
    </div>
    <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:16px">
      <div style="border:1.5px solid var(--rule);border-radius:8px;padding:28px;display:flex;flex-direction:column;gap:14px;min-height:340px">
        <div style="display:inline-flex;align-self:flex-start;background:var(--orange);color:var(--white);padding:6px 14px;border-radius:999px;font-size:14px;font-weight:500;letter-spacing:0.08em">{{STAGE_1_TAG}}</div>
        <div style="font-size:30px;line-height:1.1;font-weight:500;letter-spacing:-0.01em">{{STAGE_1_TITLE}}</div>
        <div style="font-size:18px;line-height:1.5;font-weight:500;color:var(--gray-light)">{{STAGE_1_DESC}}</div>
      </div>
      <!-- repeat for stage 2 -->
      <!-- For final/featured stage, swap border to var(--orange) and add background:rgba(160,106,220,0.08) -->
    </div>
  </div>
</section>
```

---

### matrix (departments × pillars intersection grid)

**Use when:** showing how N rows × M cols intersect. Headers are colored dot + label; rows are categorized as PRIMARY / SUPPORTING / DOWNSTREAM (decreasing emphasis). Cells are bullet lists.

```html
<!-- SLIDE N: Integration -->
<section data-label="N Integration">
  <div class="chrome-top">
    <div class="ct-left"><span>{{NUM}}</span><span>{{SECTION_NAME}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div style="display:flex;flex-direction:column;height:100%;padding:120px 80px 80px;gap:40px">
    <div>
      <div class="title-md" style="font-size:64px">{{HEADLINE_PRE}} <span style="color:var(--gray-light)">{{HEADLINE_REST}}</span></div>
      <div class="body-md" style="margin-top:20px;max-width:1100px;color:var(--gray-light);font-size:22px">{{INTRO}}</div>
    </div>
    <div style="display:grid;grid-template-columns:200px repeat(3,1fr);gap:1px;background:rgba(255,255,255,0.15);border:1px solid rgba(255,255,255,0.15);flex:1">
      <!-- HEADER ROW -->
      <div style="background:#1A1A1A;padding:20px"></div>
      <div style="background:#1A1A1A;padding:20px 24px;display:flex;align-items:center;gap:12px">
        <div style="width:14px;height:14px;border-radius:50%;background:var(--dept-graphics)"></div>
        <div style="font-size:22px;font-weight:500">{{COL_1}}</div>
      </div>
      <!-- repeat for col 2, col 3 -->

      <!-- ROW: PRIMARY (full opacity, accent border-left) -->
      <div style="background:#1A1A1A;padding:24px;display:flex;flex-direction:column;justify-content:center;border-left:3px solid var(--orange)">
        <div style="font-size:14px;font-weight:500;letter-spacing:0.1em;opacity:0.6;margin-bottom:8px">PRIMARY HOME</div>
        <div style="font-size:28px;font-weight:500;line-height:1.05;letter-spacing:-0.01em">{{ROW_1_NAME}}</div>
      </div>
      <div style="background:#1A1A1A;padding:24px;display:flex;flex-direction:column;gap:10px;font-size:16px;font-weight:500;line-height:1.4">
        <div>{{CELL_BULLET_1}}</div>
        <!-- repeat -->
      </div>
      <!-- repeat 2 more cells for this row -->

      <!-- ROW: SUPPORTING (color: gray-light) -->
      <!-- ROW: DOWNSTREAM (color: gray-light, opacity: 0.85) -->
    </div>
  </div>
</section>
```

---

### manifesto ("the one thing" — orange bg, struck-through type)

**Use when:** a high-impact "redefine the thing" slide. Always orange background. Strikes through the OLD framing, replaces with a highlighted NEW framing.

```html
<!-- SLIDE N: The one thing -->
<section data-label="N The one thing" class="orange" style="background:#A06ADC;color:var(--white)">
  <div class="chrome-top">
    <div class="ct-left"><span>{{NUM}}</span><span>The one thing</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div style="display:flex;flex-direction:column;height:100%;padding:100px;justify-content:space-between">
    <div>
      <div style="font-size:108px;line-height:0.96;letter-spacing:-0.025em;font-weight:500;max-width:1500px">
        {{OLD_FRAME_PRE}} <span style="opacity:0.45;text-decoration:line-through;text-decoration-thickness:6px">{{OLD_FRAME_STRIKE}}</span>
      </div>
      <div style="font-size:108px;line-height:1.2;letter-spacing:-0.025em;font-weight:500;max-width:1500px;margin-top:24px">
        {{NEW_FRAME_PRE}} <em style="font-style:normal;background:var(--white);color:#A06ADC;padding:0 18px;border-radius:6px">{{NEW_FRAME_HIGHLIGHT}}</em>
      </div>
    </div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:80px;padding-top:48px;border-top:1px solid rgba(255,255,255,0.3)">
      <div>
        <div style="font-size:18px;font-weight:500;letter-spacing:0.16em;opacity:0.6;margin-bottom:16px">{{STAKES_KICKER}}</div>
        <div style="font-size:30px;line-height:1.35;font-weight:500">{{STAKES_BODY}}</div>
      </div>
      <div style="display:flex;gap:12px;align-items:flex-end;justify-content:flex-end;flex-wrap:wrap">
        <span style="border:1.5px solid rgba(255,255,255,0.6);padding:14px 28px;border-radius:999px;font-size:20px;font-weight:500">{{PILL_1}}</span>
        <span style="border:1.5px solid rgba(255,255,255,0.6);padding:14px 28px;border-radius:999px;font-size:20px;font-weight:500">{{PILL_2}}</span>
        <span style="background:var(--white);color:#A06ADC;padding:14px 28px;border-radius:999px;font-size:20px;font-weight:500">{{PILL_3}}</span>
      </div>
    </div>
  </div>
</section>
```

---

### close

**Use when:** final slide. Big closing line + meta pills. Always last.

```html
<!-- SLIDE N: Close -->
<section data-label="N Close" style="background:#1A1A1A;color:#fff">
  <div class="chrome-top">
    <div class="ct-left">{{DECK_NAME}}</div>
    <div>{{VERSION_END}}</div>
  </div>
  <div class="close">
    <div class="big">
      {{LINE_1}}<br>
      <span class="o">{{LINE_2_ACCENT}}</span><br>
      {{LINE_3}}
    </div>
    <div class="meta-row">
      <span class="meta-pill solid">{{PILL_1}}</span>
      <span class="meta-pill">{{PILL_2}}</span>
      <span class="meta-pill">{{PILL_3}}</span>
    </div>
  </div>
  <div class="chrome-bottom">
    <div class="cb-left"><span class="pagenum">{{N}}</span><span>{{DECK_NAME}}</span></div>
    <div>{{SIGNOFF}}</div>
  </div>
</section>
```

---

### intro-spread (2-col chapter intro on accent bg)

**Use when:** opening a chapter with a big title left + body copy right. Used as alternative to `section-divider`.

```html
<section data-label="N {{CHAPTER}} Intro" class="orange">
  <div class="chrome-top">
    <div class="ct-left"><span>{{NUM}}</span><span>{{CHAPTER}}</span></div>
    <div>{{VERSION}}</div>
  </div>
  <div class="intro-spread">
    <div class="heading">{{HEADING}}</div>
    <div class="body">{{BODY}}</div>
  </div>
</section>
```

---

### Typography utility classes

| Class | Size | Use |
|---|---|---|
| `title-xl` | 200px | Cover, manifesto |
| `title-lg` | 120px | Chapter dividers |
| `title-md` | 96px | Section headers |
| `title-sm` | 72px | Sub-section headers |
| `title-xs` | 56px | Slide headlines on dense slides |
| `body-lg` | 32px | Lede text |
| `body-md` | 24px | Body |
| `body-sm` | 20px | Captions, footnotes |
| `eyebrow` | 16px | Kickers (use with `letter-spacing:0.16em` for tracked-out caps) |

---

### Pill components (concept slides)

Used inside `.pill-canvas` containers when the slide is a concept-as-language exercise.

```html
<div class="pill-canvas">
  <div class="pill-row">
    <span class="pill solid-white">{{TEXT}}</span>
    <span class="pill arrow">→</span>
    <span class="pill solid-orange">{{TEXT}}</span>
  </div>
  <div class="pill-row">
    <span class="pill outline-orange">{{TEXT}}</span>
  </div>
</div>
```

Pill variants: `solid-white`, `solid-orange`, `outline-orange`, `solid-orange-text-orange`, `arrow` (130px circular), `arrow.outline`.

---

### Design rules for the Krystee preset

1. **One idea per slide.** Krystee slides are huge. Don't pack multiple ideas. Split into two slides.
2. **Big type is the design.** Default headline sizes are 72–200px. Don't shrink them to fit more copy — cut copy instead.
3. **Accent is purple by default.** Use `dept-graphics` (purple), `dept-web` (gold), `dept-video` (green) chapter classes when grouping content into themes.
4. **Chrome is non-negotiable.** Every slide gets `chrome-top`. Section number + name + version is the consistent header language.
5. **Strikethrough + highlight is a signature move.** Reserve the manifesto component for slides that earn it (1-2 per deck).
6. **The deck-stage scales itself.** Don't add `viewport`, `width`, or `height` overrides. Don't try to make slides responsive — they're 1920×1080 fixed.
7. **No external image URLs.** Base64 every image. The scaler script loads file:// just fine when fonts and images are inlined.


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
  .hero-logo { position: relative; z-index: 1; max-width: 280px; margin-bottom: 32px; }
  .hero-logo svg { display: block; width: 100%; height: auto; }
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

---

## Preset: krystee

The following is the complete HTML preset template for the Krystee retreat deck. When building a presentation, copy this template and replace `{{SLIDES}}`, `{{TITLE}}`, and `{{DATE}}` with the generated content. The preset includes Inter Tight as a base64-inlined variable font and a `<deck-stage>` web component that auto-scales the 1920×1080 canvas to fit the viewport.

> **Note on size:** This preset embeds the Inter Tight font (~135KB) and a self-contained scaler script (~22KB). The full template is large but produces a single self-contained HTML file with no external dependencies.

```html
<!-- preset-name: Krystee -->
<!-- preset-description: Krystee's retreat presentation deck — fixed 1920×1080 canvas with auto-scale, Inter Tight display type, dark base with purple/gold/green departmental accents. Big hero typography, asymmetric grids, pill-heavy concept slides, bento and pillar layouts. Use for offsites, internal retreats, capability maps, and brand experience decks. -->
<!-- Generated: {{DATE}} -->
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{TITLE}}</title>
<style>
  /* Inter Tight — variable font, base64-inlined. Self-contained, no network needed. */
  @font-face {
    font-family: 'Inter Tight';
    font-style: normal;
    font-weight: 100 900;
    font-display: swap;
    src: url('data:font/woff2;base64,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') format('woff2');
    unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA, U+02DC, U+0304, U+0308, U+0329, U+2000-206F, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215, U+FEFF, U+FFFD;
  }
  @font-face {
    font-family: 'Inter Tight';
    font-style: normal;
    font-weight: 100 900;
    font-display: swap;
    src: url('data:font/woff2;base64,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') format('woff2');
    unicode-range: U+0100-02BA, U+02BD-02C5, U+02C7-02CC, U+02CE-02D7, U+02DD-02FF, U+0304, U+0308, U+0329, U+1D00-1DBF, U+1E00-1E9F, U+1EF2-1EFF, U+2020, U+20A0-20AB, U+20AD-20C0, U+2113, U+2C60-2C7F, U+A720-A7FF;
  }

  /* ============ DECK TOKENS + BASE ============ */
  :root {
    --orange: #A06ADC;
    --dept-graphics: #A06ADC;
    --dept-web: #D4B05A;
    --dept-video: #6FB58E;
    --black: #1A1A1A;
    --white: #FFFFFF;
    --cream: #F6F2EF;
    --gray-light: #C1BFC3;
    --gray-mid: #969496;
    --gray-dark: #565657;
    --gray-deep: #44403D;
    --rule: rgba(255,255,255,0.25);
    --rule-dark: rgba(0,0,0,0.18);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }
  html, body { height: 100%; background: #000; }
  body {
    font-family: 'Inter Tight', -apple-system, BlinkMacSystemFont, sans-serif;
    color: var(--white);
    -webkit-font-smoothing: antialiased;
  }

  /* ============ SLIDE BASE ============ */
  deck-stage > section {
    background: var(--black);
    color: var(--white);
    padding: 80px 80px 100px;
    display: flex;
    flex-direction: column;
    position: relative;
    overflow: hidden;
    font-family: 'Inter Tight', sans-serif;
  }
  deck-stage > section.orange { background: var(--orange); color: var(--white); }
  /* department-specific accent overrides (cascade through any var(--orange) usage) */
  deck-stage > section.dept-graphics { --orange: var(--dept-graphics); }
  deck-stage > section.dept-web { --orange: var(--dept-web); }
  deck-stage > section.dept-video { --orange: var(--dept-video); }
  deck-stage > section.cream { background: var(--cream); color: var(--black); }
  deck-stage > section.white { background: var(--white); color: var(--black); }

  /* ============ HEADER / FOOTER chrome ============ */
  .chrome-top, .chrome-bottom {
    position: absolute;
    left: 80px; right: 80px;
    display: flex; justify-content: space-between; align-items: center;
    font-size: 24px; font-weight: 500; letter-spacing: 0.01em;
  }
  .chrome-top { top: 40px; }
  .chrome-bottom { bottom: 44px; display: none; }
  .chrome-top .ct-left, .chrome-bottom .cb-left { display: flex; gap: 24px; }
  .pagenum { display: inline-block; min-width: 24px; }

  /* ============ TYPOGRAPHY ============ */
  h1, h2, h3, .display { font-family: 'Inter Tight', sans-serif; font-weight: 500; letter-spacing: -0.015em; }
  .zen { font-family: 'Zen Dots', sans-serif; letter-spacing: -0.02em; }

  .title-xl { font-size: 200px; line-height: 0.92; font-weight: 500; letter-spacing: -0.02em; }
  .title-lg { font-size: 120px; line-height: 0.95; font-weight: 500; letter-spacing: -0.02em; }
  .title-md { font-size: 96px; line-height: 0.95; font-weight: 500; letter-spacing: -0.02em; }
  .title-sm { font-size: 72px; line-height: 0.98; font-weight: 500; letter-spacing: -0.015em; }
  .title-xs { font-size: 56px; line-height: 1.0; font-weight: 500; letter-spacing: -0.01em; }

  .body-lg { font-size: 32px; line-height: 1.4; font-weight: 500; }
  .body-md { font-size: 24px; line-height: 1.5; font-weight: 500; }
  .body-sm { font-size: 20px; line-height: 1.55; font-weight: 500; }

  .eyebrow { font-size: 16px; font-weight: 500; letter-spacing: 0.02em; text-transform: none; }

  /* ============ COVER / SECTION DIVIDER LAYOUTS ============ */
  /* Section divider: title left, contents-list right, big number bottom-left */
  .section-divider { padding: 100px 80px 90px; }
  .section-divider .grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 80px;
    flex: 1;
    margin-top: 60px;
  }
  .section-divider .left-col { display: flex; flex-direction: column; }
  .section-divider .left-col .heading { margin-bottom: 56px; }
  .section-divider .left-col .intro { font-size: 26px; line-height: 1.45; font-weight: 500; max-width: 520px; }
  .section-divider .right-col { padding-top: 24px; }
  .section-divider .big-number {
    font-family: 'Inter Tight', sans-serif;
    font-size: 360px;
    line-height: 0.85;
    font-weight: 500;
    letter-spacing: -0.04em;
    margin-top: auto;
    margin-left: -8px;
  }

  /* TOC list rows (used on dividers + main TOC) */
  .toc-list { display: flex; flex-direction: column; }
  .toc-row {
    display: grid;
    grid-template-columns: 1fr 60px;
    gap: 32px;
    padding: 22px 0;
    border-top: 1px solid currentColor;
    font-size: 24px;
    font-weight: 500;
    align-items: center;
  }
  .toc-row:last-child { border-bottom: 1px solid currentColor; }
  .toc-row .num { opacity: 0.95; }
  .toc-row .page { text-align: right; opacity: 0.95; }

  /* ============ CONTENTS PAGE (multi-col) ============ */
  .contents-page { padding: 100px 100px 100px; }
  .contents-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    column-gap: 100px;
    row-gap: 60px;
    margin-top: 40px;
    flex: 1;
  }
  .contents-section { display: flex; flex-direction: column; }
  .contents-head {
    display: grid;
    grid-template-columns: 60px 1fr;
    gap: 24px;
    margin-bottom: 16px;
    align-items: baseline;
  }
  .contents-head .num { font-size: 28px; color: var(--orange); font-weight: 500; }
  .contents-head .name { font-size: 32px; color: var(--orange); font-weight: 500; }
  .contents-row {
    display: grid;
    grid-template-columns: 60px 1fr 50px;
    gap: 24px;
    padding: 10px 0;
    font-size: 24px;
    font-weight: 500;
    color: var(--white);
  }
  .contents-row .pad { }
  .contents-row .item { }
  .contents-row .pn { text-align: right; }

  /* ============ PILL CONCEPT SLIDES ============ */
  .pill-canvas {
    display: flex; flex-direction: column; gap: 28px; flex: 1;
    align-items: flex-start; justify-content: center;
    margin: 60px 0;
  }
  .pill-row { display: flex; gap: 28px; flex-wrap: nowrap; align-items: center; }
  .pill {
    display: inline-flex; align-items: center; justify-content: center;
    padding: 22px 56px;
    border-radius: 999px;
    font-size: 88px;
    font-weight: 500;
    line-height: 1;
    letter-spacing: -0.01em;
    white-space: nowrap;
  }
  .pill.solid-white { background: var(--white); color: var(--black); }
  .pill.solid-orange { background: var(--orange); color: var(--black); }
  .pill.outline-orange { background: transparent; color: var(--orange); border: 2.5px solid var(--orange); }
  .pill.solid-orange-text-orange { background: var(--orange); color: var(--white); }
  .pill.arrow {
    width: 130px; height: 130px; border-radius: 50%; padding: 0;
    background: var(--orange); color: var(--black);
    font-size: 64px;
  }
  .pill.arrow.outline { background: transparent; color: var(--orange); border: 2.5px solid var(--orange); }

  /* ============ BIG-WORDS LAYOUT ============ */
  .bigwords {
    flex: 1;
    display: flex; flex-direction: column; justify-content: center;
    gap: 24px;
  }
  .bigwords .line {
    font-size: 140px; line-height: 0.95; font-weight: 500; letter-spacing: -0.02em;
  }
  .bigwords .line .accent { color: var(--orange); }

  /* ============ TWO-COLUMN INTRO (orange page) ============ */
  .intro-spread {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 100px;
    flex: 1;
    align-items: start;
    padding-top: 30px;
  }
  .intro-spread .heading { font-size: 120px; line-height: 0.92; font-weight: 500; letter-spacing: -0.02em; }
  .intro-spread .body { font-size: 24px; line-height: 1.45; font-weight: 500; max-width: 460px; margin-top: 40px; }

  /* ============ SERVICE AREA SLIDES (bento) ============ */
  .service-head {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 80px;
    align-items: end;
    margin-bottom: 48px;
  }
  .service-head .left .num { display: none; }
  .service-head .left .name { font-size: 96px; font-weight: 500; line-height: 1; }
  .service-head .right { font-size: 26px; line-height: 1.5; font-weight: 500; max-width: 540px; opacity: 0.85; }

  .bento {
    display: grid; gap: 16px;
  }
  .bento.cols-3 { grid-template-columns: repeat(3, 1fr); grid-auto-rows: minmax(220px, auto); }
  .bento.cols-4 { grid-template-columns: repeat(4, 1fr); grid-auto-rows: minmax(210px, auto); }

  .bento-cell {
    border: 1px solid rgba(255,255,255,0.22);
    border-radius: 28px;
    padding: 28px 32px;
    display: flex; flex-direction: column; justify-content: space-between;
    background: transparent;
  }
  .bento-cell.span-2 { grid-column: span 2; }
  .bento-cell.fill-orange { background: var(--orange); border-color: var(--orange); color: var(--white); }
  .bento-cell.fill-white { background: var(--white); border-color: var(--white); color: var(--black); }
  .bento-cell.outline-orange { border-color: var(--orange); color: var(--orange); }
  .bento-cell.dim { opacity: 0.55; border-style: dashed; }

  .bento-label { font-size: 28px; font-weight: 500; line-height: 1.2; letter-spacing: -0.01em; }
  .bento-sub { font-size: 24px; font-weight: 500; line-height: 1.5; opacity: 0.78; margin-top: 12px; }
  .bento-tag {
    display: inline-block;
    margin-top: 18px;
    padding: 6px 14px;
    border-radius: 999px;
    font-size: 24px;
    font-weight: 500;
    border: 1px solid currentColor;
    margin-right: 6px;
  }

  /* ============ PILLAR PAGE ============ */
  .pillar-grid {
    display: grid; grid-template-columns: repeat(3, 1fr); gap: 24px; margin-top: 60px; flex: 1;
  }
  .pillar-card {
    border: 1px solid rgba(255,255,255,0.25);
    border-radius: 32px;
    padding: 56px 40px;
    display: flex; flex-direction: column; justify-content: space-between;
    min-height: 540px;
  }
  .pillar-card.primary { background: var(--orange); border-color: var(--orange); }
  .pillar-card.dim { opacity: 0.5; }
  .pillar-card .label { font-size: 24px; font-weight: 500; opacity: 0.7; margin-bottom: 32px; }
  .pillar-card .name { font-size: 88px; font-weight: 500; line-height: 0.95; letter-spacing: -0.02em; }
  .pillar-card .desc { font-size: 24px; font-weight: 500; line-height: 1.5; margin-top: 32px; opacity: 0.9; }

  /* ============ AI ROADMAP ============ */
  .roadmap-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 80px; margin-top: 56px; flex: 1; }
  .roadmap-col h4 {
    font-size: 24px; font-weight: 500; letter-spacing: 0.04em;
    color: var(--orange); margin-bottom: 28px;
    padding-bottom: 16px; border-bottom: 1px solid var(--orange);
    display: grid; grid-template-columns: 60px 1fr; gap: 24px;
  }
  .roadmap-col ul { list-style: none; }
  .roadmap-col li {
    font-size: 26px; font-weight: 500; line-height: 1.4;
    padding: 18px 0;
    border-bottom: 1px solid rgba(255,255,255,0.14);
    display: grid; grid-template-columns: 60px 1fr; gap: 24px; align-items: baseline;
  }
  .roadmap-col li .n { color: var(--orange); font-size: 24px; font-weight: 500; }

  /* ============ NUMBERED PRIORITY LIST ============ */
  .num-list { display: flex; flex-direction: column; margin-top: 56px; flex: 1; }
  .num-row {
    display: grid; grid-template-columns: 80px 1fr 1fr; gap: 56px;
    padding: 36px 0;
    border-top: 1px solid var(--rule);
    align-items: start;
  }
  .num-row:last-child { border-bottom: 1px solid var(--rule); }
  .num-row .n { font-size: 32px; font-weight: 500; color: var(--orange); }
  .num-row h4 { font-size: 40px; font-weight: 500; line-height: 1.05; letter-spacing: -0.01em; }
  .num-row p { font-size: 24px; font-weight: 500; line-height: 1.5; opacity: 0.85; }

  /* ============ CLOSE PAGE ============ */
  .close {
    flex: 1; display: flex; flex-direction: column; justify-content: center; align-items: flex-start;
    padding: 0;
  }
  .close .big {
    font-size: 200px; line-height: 0.92; font-weight: 500; letter-spacing: -0.025em;
  }
  .close .big .o { color: var(--orange); }
  .close .meta-row {
    display: flex; gap: 16px; flex-wrap: wrap; margin-top: 80px;
  }
  .meta-pill {
    display: inline-flex; align-items: center; padding: 18px 40px;
    border: 1.5px solid var(--orange); color: var(--orange);
    border-radius: 999px; font-size: 24px; font-weight: 500;
  }
  .meta-pill.solid { background: var(--orange); color: var(--white); }
  .meta-pill.solid-white { background: var(--white); color: var(--black); border-color: var(--white); }

  /* ============ HERO COVER ============ */
  .cover {
    flex: 1; display: flex; flex-direction: column; justify-content: space-between;
  }
  .cover .top-line { font-size: 22px; font-weight: 500; display: flex; align-items: center; gap: 16px; }
  .cover .top-line .logo { height: 48px; width: auto; }
  .cover .top-line .logo img { height: 100%; width: auto; display: block; filter: brightness(0) invert(1); }
  .cover .center {
    flex: 1; display: flex; flex-direction: column; justify-content: center;
  }
  .cover .title {
    font-size: 240px; line-height: 0.88; font-weight: 500; letter-spacing: -0.03em;
  }
  .cover .title .o { color: var(--orange); }
  .cover .sub {
    font-size: 28px; font-weight: 500; line-height: 1.4; max-width: 760px; margin-top: 40px;
    color: var(--gray-light);
  }
  .cover .bottom-meta { display: flex; gap: 80px; padding-top: 28px; border-top: 1px solid var(--rule); }
  .cover .meta-item .l { font-size: 24px; opacity: 0.6; font-weight: 500; margin-bottom: 6px; letter-spacing: 0.02em; }
  .cover .meta-item .v { font-size: 26px; font-weight: 500; }
</style>

</style>
<script>
/**
 * <deck-stage> — reusable web component for HTML decks.
 *
 * Handles:
 *  (a) speaker notes — reads <script type="application/json" id="speaker-notes">
 *      and posts {slideIndexChanged: N} to the parent window on nav.
 *  (b) keyboard navigation — ←/→, PgUp/PgDn, Space, Home/End, number keys.
 *  (c) press R to reset to slide 0 (with a tasteful keyboard hint).
 *  (d) bottom-center overlay showing slide count + hints, fades out on idle.
 *  (e) auto-scaling — inner canvas is a fixed design size (default 1920×1080)
 *      scaled with `transform: scale()` to fit the viewport, letterboxed.
 *      Set the `noscale` attribute to render at authored size (1:1) — the
 *      PPTX exporter sets this so its DOM capture sees unscaled geometry.
 *  (f) print — `@media print` lays every slide out as its own page at the
 *      design size, so the browser's Print → Save as PDF produces a clean
 *      one-page-per-slide PDF with no extra setup.
 *
 * Slides are HIDDEN, not unmounted. Non-active slides stay in the DOM with
 * `visibility: hidden` + `opacity: 0`, so their state (videos, iframes,
 * form inputs, React trees) is preserved across navigation.
 *
 * Lifecycle event — the component dispatches a `slidechange` CustomEvent on
 * itself whenever the active slide changes (including the initial mount).
 * The event bubbles and composes out of shadow DOM, so you can listen on
 * the <deck-stage> element or on document:
 *
 *   document.querySelector('deck-stage').addEventListener('slidechange', (e) => {
 *     e.detail.index         // new 0-based index
 *     e.detail.previousIndex // previous index, or -1 on init
 *     e.detail.total         // total slide count
 *     e.detail.slide         // the new active slide element
 *     e.detail.previousSlide // the prior slide element, or null on init
 *     e.detail.reason        // 'init' | 'keyboard' | 'click' | 'tap' | 'api'
 *   });
 *
 * Persistence: none at the deck level. The host app keeps the current slide
 * in its own URL (?slide=) and re-delivers it via location.hash on load, so a
 * bare load with no hash always starts at slide 1.
 *
 * Usage:
 *   <deck-stage width="1920" height="1080">
 *     <section data-label="Title">...</section>
 *     <section data-label="Agenda">...</section>
 *   </deck-stage>
 *
 * Slides are the direct element children of <deck-stage>. Each slide is
 * automatically tagged with:
 *   - data-screen-label="NN Label"   (1-indexed, for comment flow)
 *   - data-om-validate="no_overflowing_text,no_overlapping_text,slide_sized_text"
 */

(() => {
  const DESIGN_W_DEFAULT = 1920;
  const DESIGN_H_DEFAULT = 1080;
  const OVERLAY_HIDE_MS = 1800;
  const VALIDATE_ATTR = 'no_overflowing_text,no_overlapping_text,slide_sized_text';

  const pad2 = (n) => String(n).padStart(2, '0');

  const stylesheet = `
    :host {
      position: fixed;
      inset: 0;
      display: block;
      background: #000;
      color: #fff;
      font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue", Helvetica, Arial, sans-serif;
      overflow: hidden;
    }

    .stage {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .canvas {
      position: relative;
      transform-origin: center center;
      flex-shrink: 0;
      background: #fff;
      will-change: transform;
    }

    /* Slides live in light DOM (via <slot>) so authored CSS still applies.
       We absolutely position each slotted child to stack them. */
    ::slotted(*) {
      position: absolute !important;
      inset: 0 !important;
      width: 100% !important;
      height: 100% !important;
      box-sizing: border-box !important;
      overflow: hidden;
      opacity: 0;
      pointer-events: none;
      visibility: hidden;
    }
    ::slotted([data-deck-active]) {
      opacity: 1;
      pointer-events: auto;
      visibility: visible;
    }

    /* Tap zones for mobile — back/forward thirds like Stories.
       Transparent, no visible UI, don't block the overlay. */
    .tapzones {
      position: fixed;
      inset: 0;
      display: flex;
      z-index: 2147482000;
      pointer-events: none;
    }
    .tapzone {
      flex: 1;
      pointer-events: auto;
      -webkit-tap-highlight-color: transparent;
    }
    /* Only activate tap zones on coarse pointers (touch devices). */
    @media (hover: hover) and (pointer: fine) {
      .tapzones { display: none; }
    }

    .overlay {
      position: fixed;
      left: 50%;
      bottom: 22px;
      transform: translate(-50%, 6px) scale(0.92);
      filter: blur(6px);
      display: flex;
      align-items: center;
      gap: 4px;
      padding: 4px;
      background: #000;
      color: #fff;
      border-radius: 999px;
      font-size: 12px;
      font-feature-settings: "tnum" 1;
      letter-spacing: 0.01em;
      opacity: 0;
      pointer-events: none;
      transition: opacity 260ms ease, transform 260ms cubic-bezier(.2,.8,.2,1), filter 260ms ease;
      transform-origin: center bottom;
      z-index: 2147483000;
      user-select: none;
    }
    .overlay[data-visible] {
      opacity: 1;
      pointer-events: auto;
      transform: translate(-50%, 0) scale(1);
      filter: blur(0);
    }

    .btn {
      appearance: none;
      -webkit-appearance: none;
      background: transparent;
      border: 0;
      margin: 0;
      padding: 0;
      color: inherit;
      font: inherit;
      cursor: default;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      height: 28px;
      min-width: 28px;
      border-radius: 999px;
      color: rgba(255,255,255,0.72);
      transition: background 140ms ease, color 140ms ease;
      -webkit-tap-highlight-color: transparent;
    }
    .btn:hover { background: rgba(255,255,255,0.12); color: #fff; }
    .btn:active { background: rgba(255,255,255,0.18); }
    .btn:focus { outline: none; }
    .btn:focus-visible { outline: none; }
    .btn::-moz-focus-inner { border: 0; }
    .btn svg { width: 14px; height: 14px; display: block; }
    .btn.reset {
      font-size: 11px;
      font-weight: 500;
      letter-spacing: 0.02em;
      padding: 0 10px 0 12px;
      gap: 6px;
      color: rgba(255,255,255,0.72);
    }
    .btn.reset .kbd {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      min-width: 16px;
      height: 16px;
      padding: 0 4px;
      font-family: ui-monospace, "SF Mono", Menlo, Consolas, monospace;
      font-size: 10px;
      line-height: 1;
      color: rgba(255,255,255,0.88);
      background: rgba(255,255,255,0.12);
      border-radius: 4px;
    }

    .count {
      font-variant-numeric: tabular-nums;
      color: #fff;
      font-weight: 500;
      padding: 0 8px;
      min-width: 42px;
      text-align: center;
      font-size: 12px;
    }
    .count .sep { color: rgba(255,255,255,0.45); margin: 0 3px; font-weight: 400; }
    .count .total { color: rgba(255,255,255,0.55); }

    .divider {
      width: 1px;
      height: 14px;
      background: rgba(255,255,255,0.18);
      margin: 0 2px;
    }

    /* ── Print: one page per slide, no chrome ────────────────────────────
       The screen layout stacks every slide at inset:0 inside a scaled
       canvas; for print we want them in document flow at the authored
       design size so the browser paginates one slide per sheet. The
       @page size is set from the width/height attributes via the inline
       <style id="deck-stage-print-page"> that connectedCallback injects
       into <head> (the @page at-rule has no effect inside shadow DOM). */
    @media print {
      :host {
        position: static;
        inset: auto;
        background: none;
        overflow: visible;
        color: inherit;
      }
      .stage { position: static; display: block; }
      .canvas {
        transform: none !important;
        width: auto !important;
        height: auto !important;
        background: none;
        will-change: auto;
      }
      ::slotted(*) {
        position: relative !important;
        inset: auto !important;
        width: var(--deck-design-w) !important;
        height: var(--deck-design-h) !important;
        box-sizing: border-box !important;
        opacity: 1 !important;
        visibility: visible !important;
        pointer-events: auto;
        break-after: page;
        page-break-after: always;
        break-inside: avoid;
        overflow: hidden;
      }
      ::slotted(*:last-child) {
        break-after: auto;
        page-break-after: auto;
      }
      .overlay, .tapzones { display: none !important; }
    }
  `;

  class DeckStage extends HTMLElement {
    static get observedAttributes() { return ['width', 'height', 'noscale']; }

    constructor() {
      super();
      this._root = this.attachShadow({ mode: 'open' });
      this._index = 0;
      this._slides = [];
      this._notes = [];
      this._hideTimer = null;
      this._mouseIdleTimer = null;

      this._onKey = this._onKey.bind(this);
      this._onResize = this._onResize.bind(this);
      this._onSlotChange = this._onSlotChange.bind(this);
      this._onMouseMove = this._onMouseMove.bind(this);
      this._onTapBack = this._onTapBack.bind(this);
      this._onTapForward = this._onTapForward.bind(this);
    }

    get designWidth() {
      return parseInt(this.getAttribute('width'), 10) || DESIGN_W_DEFAULT;
    }
    get designHeight() {
      return parseInt(this.getAttribute('height'), 10) || DESIGN_H_DEFAULT;
    }

    connectedCallback() {
      this._render();
      this._loadNotes();
      this._syncPrintPageRule();
      window.addEventListener('keydown', this._onKey);
      window.addEventListener('resize', this._onResize);
      window.addEventListener('mousemove', this._onMouseMove, { passive: true });
      // Initial collection + layout happens via slotchange, which fires on mount.
    }

    disconnectedCallback() {
      window.removeEventListener('keydown', this._onKey);
      window.removeEventListener('resize', this._onResize);
      window.removeEventListener('mousemove', this._onMouseMove);
      if (this._hideTimer) clearTimeout(this._hideTimer);
      if (this._mouseIdleTimer) clearTimeout(this._mouseIdleTimer);
    }

    attributeChangedCallback() {
      if (this._canvas) {
        this._canvas.style.width = this.designWidth + 'px';
        this._canvas.style.height = this.designHeight + 'px';
        this._canvas.style.setProperty('--deck-design-w', this.designWidth + 'px');
        this._canvas.style.setProperty('--deck-design-h', this.designHeight + 'px');
        this._fit();
        this._syncPrintPageRule();
      }
    }

    _render() {
      const style = document.createElement('style');
      style.textContent = stylesheet;

      const stage = document.createElement('div');
      stage.className = 'stage';

      const canvas = document.createElement('div');
      canvas.className = 'canvas';
      canvas.style.width = this.designWidth + 'px';
      canvas.style.height = this.designHeight + 'px';
      canvas.style.setProperty('--deck-design-w', this.designWidth + 'px');
      canvas.style.setProperty('--deck-design-h', this.designHeight + 'px');

      const slot = document.createElement('slot');
      slot.addEventListener('slotchange', this._onSlotChange);
      canvas.appendChild(slot);
      stage.appendChild(canvas);

      // Tap zones (mobile): left third = back, right third = forward.
      const tapzones = document.createElement('div');
      tapzones.className = 'tapzones export-hidden';
      tapzones.setAttribute('aria-hidden', 'true');
      tapzones.setAttribute('data-noncommentable', '');
      const tzBack = document.createElement('div');
      tzBack.className = 'tapzone tapzone--back';
      const tzMid = document.createElement('div');
      tzMid.className = 'tapzone tapzone--mid';
      tzMid.style.pointerEvents = 'none';
      const tzFwd = document.createElement('div');
      tzFwd.className = 'tapzone tapzone--fwd';
      tzBack.addEventListener('click', this._onTapBack);
      tzFwd.addEventListener('click', this._onTapForward);
      tapzones.append(tzBack, tzMid, tzFwd);

      // Overlay: compact, solid black, with clickable controls.
      const overlay = document.createElement('div');
      overlay.className = 'overlay export-hidden';
      overlay.setAttribute('role', 'toolbar');
      overlay.setAttribute('aria-label', 'Deck controls');
      overlay.setAttribute('data-noncommentable', '');
      overlay.innerHTML = `
        <button class="btn prev" type="button" aria-label="Previous slide" title="Previous (←)">
          <svg viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M10 3L5 8l5 5"/></svg>
        </button>
        <span class="count" aria-live="polite"><span class="current">1</span><span class="sep">/</span><span class="total">1</span></span>
        <button class="btn next" type="button" aria-label="Next slide" title="Next (→)">
          <svg viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M6 3l5 5-5 5"/></svg>
        </button>
        <span class="divider"></span>
        <button class="btn reset" type="button" aria-label="Reset to first slide" title="Reset (R)">Reset<span class="kbd">R</span></button>
      `;

      overlay.querySelector('.prev').addEventListener('click', () => this._go(this._index - 1, 'click'));
      overlay.querySelector('.next').addEventListener('click', () => this._go(this._index + 1, 'click'));
      overlay.querySelector('.reset').addEventListener('click', () => this._go(0, 'click'));

      this._root.append(style, stage, tapzones, overlay);
      this._canvas = canvas;
      this._slot = slot;
      this._overlay = overlay;
      this._countEl = overlay.querySelector('.current');
      this._totalEl = overlay.querySelector('.total');
    }

    /** @page must live in the document stylesheet — it's a no-op inside
     *  shadow DOM. Inject/update a single <head> style tag so the print
     *  sheet matches the design size and Save-as-PDF yields one slide per
     *  page with no margins. */
    _syncPrintPageRule() {
      const id = 'deck-stage-print-page';
      let tag = document.getElementById(id);
      if (!tag) {
        tag = document.createElement('style');
        tag.id = id;
        document.head.appendChild(tag);
      }
      tag.textContent =
        '@page { size: ' + this.designWidth + 'px ' + this.designHeight + 'px; margin: 0; } ' +
        '@media print { html, body { margin: 0 !important; padding: 0 !important; background: none !important; overflow: visible !important; height: auto !important; } ' +
        '* { -webkit-print-color-adjust: exact; print-color-adjust: exact; } }';
    }

    _onSlotChange() {
      this._collectSlides();
      this._restoreIndex();
      this._applyIndex({ showOverlay: false, broadcast: true, reason: 'init' });
      this._fit();
    }

    _collectSlides() {
      const assigned = this._slot.assignedElements({ flatten: true });
      this._slides = assigned.filter((el) => {
        // Skip template/style/script nodes even if someone slots them.
        const tag = el.tagName;
        return tag !== 'TEMPLATE' && tag !== 'SCRIPT' && tag !== 'STYLE';
      });

      this._slides.forEach((slide, i) => {
        const n = i + 1;
        // Determine a label for comment flow: prefer explicit data-label,
        // then an existing data-screen-label, then first heading, else "Slide".
        let label = slide.getAttribute('data-label');
        if (!label) {
          const existing = slide.getAttribute('data-screen-label');
          if (existing) {
            // Strip any leading number the author may have included.
            label = existing.replace(/^\s*\d+\s*/, '').trim() || existing;
          }
        }
        if (!label) {
          const h = slide.querySelector('h1, h2, h3, [data-title]');
          if (h) label = (h.textContent || '').trim().slice(0, 40);
        }
        if (!label) label = 'Slide';
        slide.setAttribute('data-screen-label', `${pad2(n)} ${label}`);

        // Validation attribute for comment flow / auto-checks.
        if (!slide.hasAttribute('data-om-validate')) {
          slide.setAttribute('data-om-validate', VALIDATE_ATTR);
        }

        slide.setAttribute('data-deck-slide', String(i));
      });

      if (this._totalEl) this._totalEl.textContent = String(this._slides.length || 1);
      if (this._index >= this._slides.length) this._index = Math.max(0, this._slides.length - 1);
    }

    _loadNotes() {
      const tag = document.getElementById('speaker-notes');
      if (!tag) { this._notes = []; return; }
      try {
        const parsed = JSON.parse(tag.textContent || '[]');
        if (Array.isArray(parsed)) this._notes = parsed;
      } catch (e) {
        console.warn('[deck-stage] Failed to parse #speaker-notes JSON:', e);
        this._notes = [];
      }
    }

    _restoreIndex() {
      // The host's ?slide= param is delivered as a #<int> hash (1-indexed) on
      // the iframe src. No hash → slide 1; the deck itself keeps no position
      // state across loads.
      const h = (location.hash || '').match(/^#(\d+)$/);
      if (h) {
        const n = parseInt(h[1], 10) - 1;
        if (n >= 0 && n < this._slides.length) this._index = n;
      }
    }

    _applyIndex({ showOverlay = true, broadcast = true, reason = 'init' } = {}) {
      if (!this._slides.length) return;
      const prev = this._prevIndex == null ? -1 : this._prevIndex;
      const curr = this._index;
      // Keep the iframe's own hash in sync so an in-iframe location.reload()
      // (reload banner path in viewer-handle.ts) lands on the current slide,
      // not the stale deep-link hash from initial load.
      try { history.replaceState(null, '', '#' + (curr + 1)); } catch (e) {}
      this._slides.forEach((s, i) => {
        if (i === curr) s.setAttribute('data-deck-active', '');
        else s.removeAttribute('data-deck-active');
      });
      if (this._countEl) this._countEl.textContent = String(curr + 1);

      if (broadcast) {
        // (1) Legacy: host-window postMessage for speaker-notes renderers.
        try { window.postMessage({ slideIndexChanged: curr }, '*'); } catch (e) {}

        // (2) In-page CustomEvent on the <deck-stage> element itself.
        //     Bubbles and composes out of shadow DOM so slide code can listen:
        //       document.querySelector('deck-stage').addEventListener('slidechange', e => {
        //         e.detail.index, e.detail.previousIndex, e.detail.total, e.detail.slide, e.detail.reason
        //       });
        const detail = {
          index: curr,
          previousIndex: prev,
          total: this._slides.length,
          slide: this._slides[curr] || null,
          previousSlide: prev >= 0 ? (this._slides[prev] || null) : null,
          reason: reason, // 'init' | 'keyboard' | 'click' | 'tap' | 'api'
        };
        this.dispatchEvent(new CustomEvent('slidechange', {
          detail,
          bubbles: true,
          composed: true,
        }));
      }

      this._prevIndex = curr;
      if (showOverlay) this._flashOverlay();
    }

    _flashOverlay() {
      if (!this._overlay) return;
      this._overlay.setAttribute('data-visible', '');
      if (this._hideTimer) clearTimeout(this._hideTimer);
      this._hideTimer = setTimeout(() => {
        this._overlay.removeAttribute('data-visible');
      }, OVERLAY_HIDE_MS);
    }

    _fit() {
      if (!this._canvas) return;
      // PPTX export sets noscale so the DOM capture sees authored-size
      // geometry — the scaled canvas is in shadow DOM, so the exporter's
      // resetTransformSelector can't reach .canvas.style.transform directly.
      if (this.hasAttribute('noscale')) {
        this._canvas.style.transform = 'none';
        return;
      }
      const vw = window.innerWidth;
      const vh = window.innerHeight;
      const s = Math.min(vw / this.designWidth, vh / this.designHeight);
      this._canvas.style.transform = `scale(${s})`;
    }

    _onResize() { this._fit(); }

    _onMouseMove() {
      // Keep overlay visible while mouse moves; hide after idle.
      this._flashOverlay();
    }

    _onTapBack(e) {
      e.preventDefault();
      this._go(this._index - 1, 'tap');
    }

    _onTapForward(e) {
      e.preventDefault();
      this._go(this._index + 1, 'tap');
    }

    _onKey(e) {
      // Ignore when the user is typing.
      const t = e.target;
      if (t && (t.isContentEditable || /^(INPUT|TEXTAREA|SELECT)$/.test(t.tagName))) return;
      if (e.metaKey || e.ctrlKey || e.altKey) return;

      const key = e.key;
      let handled = true;

      if (key === 'ArrowRight' || key === 'PageDown' || key === ' ' || key === 'Spacebar') {
        this._go(this._index + 1, 'keyboard');
      } else if (key === 'ArrowLeft' || key === 'PageUp') {
        this._go(this._index - 1, 'keyboard');
      } else if (key === 'Home') {
        this._go(0, 'keyboard');
      } else if (key === 'End') {
        this._go(this._slides.length - 1, 'keyboard');
      } else if (key === 'r' || key === 'R') {
        this._go(0, 'keyboard');
      } else if (/^[0-9]$/.test(key)) {
        // 1..9 jump to that slide; 0 jumps to 10.
        const n = key === '0' ? 9 : parseInt(key, 10) - 1;
        if (n < this._slides.length) this._go(n, 'keyboard');
      } else {
        handled = false;
      }

      if (handled) {
        e.preventDefault();
        this._flashOverlay();
      }
    }

    _go(i, reason = 'api') {
      if (!this._slides.length) return;
      const clamped = Math.max(0, Math.min(this._slides.length - 1, i));
      if (clamped === this._index) {
        this._flashOverlay();
        return;
      }
      this._index = clamped;
      this._applyIndex({ showOverlay: true, broadcast: true, reason });
    }

    // Public API ------------------------------------------------------------

    /** Current slide index (0-based). */
    get index() { return this._index; }
    /** Total slide count. */
    get length() { return this._slides.length; }
    /** Programmatically navigate. */
    goTo(i) { this._go(i, 'api'); }
    next() { this._go(this._index + 1, 'api'); }
    prev() { this._go(this._index - 1, 'api'); }
    reset() { this._go(0, 'api'); }
  }

  if (!customElements.get('deck-stage')) {
    customElements.define('deck-stage', DeckStage);
  }
})();

</script>
</head>
<body>

<deck-stage width="1920" height="1080">

{{SLIDES}}

</deck-stage>

</body>
</html>

```
