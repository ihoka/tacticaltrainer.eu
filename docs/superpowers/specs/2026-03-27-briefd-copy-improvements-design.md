# Briefd Copy Improvements — Design Spec

## Context

A Briefd website copy analysis (March 25, 2026) audited tacticaltrainer.eu through the lens of sales psychology. The overall verdict: the site has a clear niche and credible visual identity, but the copy describes the product instead of selling the transformation. The analysis rated 5 of 6 categories as "Needs Work" (Tone & Voice was rated "Strong").

This spec addresses all 6 findings with a copy-focused pass. One file modified: `index.html`, plus minimal CSS additions in `marketing.css` for two new risk-reversal text elements.

## Scope

**In scope:** Text changes and minor element additions to `index.html`. Minimal CSS for risk-reversal text in `marketing.css`.
**Out of scope:** Layout changes, new CSS components, new HTML sections, footer/header changes, pricing page, blog posts.

## Changes

### 1. Hero Subheadline (Briefd Finding #1: Headline & Hook)

**File:** `index.html`, line 19-21

**Current:**
```
The complete tactical fitness platform for building strength,
conditioning, and operational readiness.
```

**New:**
```
Tactical Barbell programming without the spreadsheets. Build strength,
conditioning, and operational readiness on a schedule that actually holds.
```

**Rationale:** Names the specific pain (spreadsheets, complexity) and states the outcome in concrete terms. Keeps the identity framing from the headline while creating a hook.

### 2. Hero Primary CTA (Briefd Finding #3: CTA Effectiveness)

**File:** `index.html`, line 28

**Current:** `Start Training Free`
**New:** `Build My Training Plan — Free`

**Rationale:** Tells the visitor exactly what they get after clicking. Makes the outcome tangible before they click.

### 3. Hero Secondary CTA (Briefd Finding #3: CTA Effectiveness)

**File:** `index.html`, line 31

**Current:** `See How It Works`
**New:** `See How Operators Train`

**Rationale:** Creates more identity pull and curiosity than the generic "how it works."

### 4. Add Risk Reversal Below Hero CTAs (Briefd Finding #5: Trust & Proof)

**File:** `index.html`, after line 33 (after the `</div>` that closes the `hero-ctas` div)

**Add:**
```html
<p class="hero-risk-reversal">No credit card required.</p>
```

**Rationale:** Removes the risk perception that prevents sign-ups. The free plan requires no card, so we should say that explicitly.

**CSS addition** in `marketing.css`: Add a `.hero-risk-reversal` rule — small font, muted color (`var(--tactical-text-secondary)`), small top margin. ~3 lines of CSS.

### 5. Pain Section — Consequence Amplification (Briefd Finding #4: Pain/Desire)

**File:** `index.html`, problem cards within lines 60-90

All consequence text stays within the existing `<p class="problem-text">` elements — no new HTML tags. Just appended sentences.

**Current problem texts → New problem texts with consequence:**

1. `Generic programs don't work for tactical demands`
   → `Generic programs don't work for tactical demands. You end up overtrained for the gym and underprepared for the field.`

2. `Tracking workouts in spreadsheets is tedious`
   → `Tracking workouts in spreadsheets is tedious. Most athletes quit their program before the first block ends.`

3. `Not sure how to balance strength and conditioning`
   → `Not sure how to balance strength and conditioning. Too much of one wrecks the other — and you feel it on the job.`

**Rationale:** Each consequence makes the cost real and specific. The visitor feels the stakes rather than just reading a flat statement.

### 6. Feature Section — Tone Fix (Briefd Finding #6: Tone & Voice)

**File:** `index.html`, line 175

**Current:** `Evidence-based programming decisions`
**New:** `Programming built on what actually works for operational athletes`

**Rationale:** "Evidence-based programming decisions" reads clinical compared to the rest of the copy. The replacement preserves the credibility claim without the lab-report phrasing.

### 7. Testimonials Header — Vague Social Proof (Briefd Finding #5: Trust & Proof)

**File:** `index.html`, line 242

**Current:** `Trusted by Tactical Athletes`
**New:** `Trusted by Tactical Athletes Worldwide`

**Rationale:** Adds geographic scope without making unverifiable specific number claims.

### 8. Final CTA Section — Remove Competing CTA (Briefd Finding #3: CTA Effectiveness)

**File:** `index.html`, lines 294-315

**Changes:**
- Remove the "Read the Blog" button entirely
- Update subtitle: `Join tactical athletes who take their training seriously.`
  → `Join tactical athletes worldwide who stopped guessing and started progressing.`
- Update primary CTA text: `Start Training Free` → `Build My Training Plan — Free`
- Add risk reversal: `<p class="cta-risk-reversal">No credit card required.</p>` after the `cta-buttons` div
- The `cta-buttons` container keeps its class even with one button — CSS flexbox handles single children fine

**CSS addition** in `marketing.css`: Add a `.cta-risk-reversal` rule — same styling as `.hero-risk-reversal`. ~3 lines of CSS.

**Rationale:** One primary action at the bottom of the funnel. The blog is secondary content accessible via navigation and the blog preview section above. The new subtitle reinforces the pain/transformation language.

## Summary of All Edits

| # | Section | Type | Briefd Finding |
|---|---------|------|----------------|
| 1 | Hero subheadline | Text change | #1 Headline & Hook |
| 2 | Hero primary CTA | Text change | #3 CTA Effectiveness |
| 3 | Hero secondary CTA | Text change | #3 CTA Effectiveness |
| 4 | Hero risk reversal | New element | #5 Trust & Proof |
| 5a | Pain card 1 | Text change | #4 Pain/Desire |
| 5b | Pain card 2 | Text change | #4 Pain/Desire |
| 5c | Pain card 3 | Text change | #4 Pain/Desire |
| 6 | AI feature list item | Text change | #6 Tone & Voice |
| 7 | Testimonials header | Text change | #5 Trust & Proof |
| 8a | Final CTA subtitle | Text change | #3 CTA Effectiveness |
| 8b | Final CTA button text | Text change | #3 CTA Effectiveness |
| 8c | Final CTA remove blog button | Remove element | #3 CTA Effectiveness |
| 8d | Final CTA risk reversal | New element | #5 Trust & Proof |

## Verification

1. Run `bundle exec jekyll build` — confirm no build errors
2. Run `bundle exec jekyll serve` — open http://localhost:4000/ in browser
3. Verify each section visually:
   - Hero: new subheadline, new CTA text, "No credit card required" visible
   - Pain section: consequence sentences appear after each problem
   - Features: "evidence-based" phrase replaced
   - Testimonials: header says "Worldwide"
   - Final CTA: only one button, new subtitle, risk reversal text
4. Check mobile viewport (640px) — ensure no text overflow from longer copy
5. Verify all links still work (CTA → app.tacticaltrainer.eu, secondary → #features)
