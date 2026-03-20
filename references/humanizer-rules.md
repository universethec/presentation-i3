# Humanizer Rules

Rules for stripping AI writing patterns from presentation text. Apply during Phase 4 (Humanize) and when editing text in Phase 5 (Iterate).

---

## Strip these patterns

### Transitional filler
Remove: "Furthermore", "Moreover", "It's worth noting", "In conclusion", "Interestingly", "Additionally", "Notably", "Importantly", "It is essential to note"

### Em dash overuse
Replace em dashes (—) with commas or periods. One em dash per slide maximum. Zero is better.

### Rule-of-three padding
If a list of three items exists only for rhythm and the third adds no information, cut it to two. "Innovation, inspiration, and industry insights" → "Innovation and practical insights"

### Inflated language
Remove: "fundamental shift", "paradigm", "transformative", "groundbreaking", "revolutionary", "game-changing", "cutting-edge", "next-generation", "state-of-the-art"

### Promotional superlatives
Remove: "remarkable", "extraordinary", "unprecedented", "stunning", "incredible", "unparalleled", "best-in-class"

### Vague attributions
Remove: "experts say", "studies show", "research indicates", "industry leaders agree", "observers have noted". Replace with specific sources or remove entirely.

### Hedging
Remove: "It could be argued that", "One might consider", "It is possible that", "There is evidence to suggest". State the claim directly or don't make it.

### AI sentence starters
Remove: "Delve into", "In today's landscape", "It is essential to", "When it comes to", "In the realm of", "At its core", "Let's explore"

### Symbolic inflation
Remove sentences that assign deep meaning to mundane observations. "This represents a pivotal moment in the evolution of..." → state the fact.

### Copula avoidance
Replace: "serves as", "stands as", "functions as", "acts as" → use "is" or "are"

### Negative parallelisms
Remove: "It's not just X, it's Y", "Not only... but also...", "It's not merely... it's..."

### Sycophantic artifacts
Remove: "Great question!", "Absolutely!", "That's an excellent point", "I hope this helps"

---

## Preserve always

- **Exact numbers** — never round, never editorialize data. "1,448" stays "1,448", not "nearly 1,500"
- **Percentages** — keep exact. "70.9%" stays "70.9%"
- **Technical terms** — product names, feature names, protocol names
- **Direct quotes** — if attributed to a specific person or source
- **Specific dates** — "Feb 16 – Mar 18, 2026" stays exact

---

## Voice guidelines

### Headlines
Short, punchy, no filler. Lead with the insight or the number.
- Bad: "A significant decline in user retention was observed across the platform"
- Good: "Users stopped coming back"
- Bad: "Comprehensive analysis reveals concerning conversion metrics"
- Good: "4.8% of visitors convert"

### Callouts
Analyst notes, not marketing copy. State the fact, state why it matters. One to two sentences.
- Bad: "It's worth noting that this represents a significant opportunity for improvement in the user journey"
- Good: "84% drop off between CTA click and wallet connect. This is the biggest leak."

### Stat labels
Bare minimum words. Just the metric name.
- Bad: "The observed bounce rate metric"
- Good: "Bounce rate"
- Bad: "Total number of unique users"
- Good: "Unique users"

### Insight text
One sentence, direct. Lead with the number or the action.
- Bad: "Furthermore, it should be noted that mobile users experience a substantially higher rate of transaction failures compared to their desktop counterparts"
- Good: "Mobile transactions fail 16% more than desktop"

### Priority descriptions
Plain language. Describe the problem like you're telling a colleague.
- Bad: "A transformative optimization of the onboarding funnel represents a pivotal opportunity"
- Good: "84% of people who click Launch App never connect their wallet. Something about the handoff is broken."
