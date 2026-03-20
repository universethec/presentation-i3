# Screenshot Patterns

Recipes for composing screenshots into slides. All `{{IMAGE_SRC}}` tokens are replaced with `data:image/png;base64,...` (or `data:image/jpeg;base64,...`) during build. Never use external URLs.

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
