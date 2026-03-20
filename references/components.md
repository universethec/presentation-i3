# Component Patterns

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

**MANDATORY for impact3 preset:** The hero slide MUST include the Impact3 logo SVG before the kicker. Read the logo from `references/impact3-logo.svg.html` and include it as the first element. This is not optional — every impact3 presentation must show the logo on the title slide.

```html
{{LOGO}}
<div class="kicker fade-in" style="animation-delay:0.15s">{{KICKER}}</div>
<h1 class="fade-in" style="animation-delay:0.3s">{{TITLE_LINE1}} <strong>{{TITLE_STRONG}}</strong>{{TITLE_LINE2}}</h1>
<p class="subtitle fade-in" style="animation-delay:0.45s">{{SUBTITLE}}</p>
<div class="hero-meta fade-in" style="animation-delay:0.6s">
  {{META_ITEMS}}
</div>
```

Where `{{LOGO}}` is the content of `references/impact3-logo.svg.html` (the Impact3 wordmark SVG wrapped in a `.hero-logo` div).

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
