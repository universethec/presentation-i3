# Krystee Component Patterns

Reference for all components available in the **Krystee preset**. Use this file ONLY when the user has selected `krystee.html` as the preset. For the iMPACT3 preset, use `components.md`.

The Krystee preset uses a fixed `1920×1080` `<deck-stage>` canvas with auto-scale-to-fit. Slides are direct children of `<deck-stage>` (NOT wrapped in `.component` — that's a different preset).

---

## Slide marker convention

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

## Section themes (apply as class on `<section>`)

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

## chrome (used on every slide)

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

## cover

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

## bigwords

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

## section-divider (chapter intro)

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

## contents-page (3-column master TOC)

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

## three-cards (sub-departments / role split)

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

## bento (capability grid)

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

## pillar-grid (3 cards, one is the "primary home")

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

## num-list (numbered priority list)

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

## roadmap (3-stage timeline)

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

## matrix (departments × pillars intersection grid)

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

## manifesto ("the one thing" — orange bg, struck-through type)

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

## close

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

## intro-spread (2-col chapter intro on accent bg)

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

## Typography utility classes

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

## Pill components (concept slides)

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

## Design rules for the Krystee preset

1. **One idea per slide.** Krystee slides are huge. Don't pack multiple ideas. Split into two slides.
2. **Big type is the design.** Default headline sizes are 72–200px. Don't shrink them to fit more copy — cut copy instead.
3. **Accent is purple by default.** Use `dept-graphics` (purple), `dept-web` (gold), `dept-video` (green) chapter classes when grouping content into themes.
4. **Chrome is non-negotiable.** Every slide gets `chrome-top`. Section number + name + version is the consistent header language.
5. **Strikethrough + highlight is a signature move.** Reserve the manifesto component for slides that earn it (1-2 per deck).
6. **The deck-stage scales itself.** Don't add `viewport`, `width`, or `height` overrides. Don't try to make slides responsive — they're 1920×1080 fixed.
7. **No external image URLs.** Base64 every image. The scaler script loads file:// just fine when fonts and images are inlined.
