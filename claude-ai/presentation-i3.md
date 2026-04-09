---
name: presentation-i3
description: Generate self-contained HTML slide presentations from briefs, data, and screenshots. Produces single-file decks with base64-inlined images that work anywhere — browser, Netlify, email. Use when asked to create a presentation, slide deck, pitch deck, or data story.
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
3. **Hero slide logo (MANDATORY):** Slide 0 must include the Impact3 logo SVG as its first element. Paste the full block from the **Impact3 Logo SVG** section (inside Component Patterns) verbatim — do not modify the path data, viewBox, fill color, or the `.hero-logo` wrapper div.
4. For each slide in the approved storyboard:
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
    <div class="hero-logo fade-in" style="margin-bottom:32px;max-width:280px">
      <svg viewBox="0 0 743 152" fill="none" xmlns="http://www.w3.org/2000/svg">
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
