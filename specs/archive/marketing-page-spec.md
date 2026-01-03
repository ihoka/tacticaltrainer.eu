# Marketing Page Specification

## Tactical Training App - Marketing Landing Page

**Document Version:** 1.0
**Date:** 2026-01-02
**App URL:** https://app.tacticaltraining.eu
**Marketing Page URL:** https://tacticaltraining.eu/ (root of marketing site)

---

## Overview

A high-converting marketing landing page for the Tactical Training app - a fitness application designed for tactical athletes, military professionals, first responders, and serious athletes following Tactical Barbell methodology.

### Target Audience

- Military personnel (active duty and veterans)
- Law enforcement officers
- Firefighters and EMTs
- Tactical athletes
- Serious fitness enthusiasts following structured programming
- Masters athletes (35+)

---

## Page Structure

### 1. Hero Section

**Purpose:** Immediately communicate the app's value proposition and drive sign-ups.

**Content:**
- **Headline:** "Train Like an Operator"
- **Subheadline:** "The complete tactical fitness platform for building strength, conditioning, and operational readiness."
- **Primary CTA:** "Start Training Free" → links to `https://app.tacticaltraining.eu/`
- **Secondary CTA:** "See How It Works" → smooth scroll to Features section
- **Visual:** App screenshot mockup showing the Calendar view (the most visually impressive screen)

**Design Notes:**
- Full-viewport hero with dark tactical background
- Subtle gradient overlay or military-inspired pattern
- Shield/tactical logo prominent
- Mobile-first responsive layout

---

### 2. Problem/Solution Section

**Purpose:** Address pain points and position the app as the solution.

**Headline:** "Stop Guessing. Start Progressing."

**Pain Points (3 cards):**

| Icon | Problem                                             | Solution                                                  |
| ---- | --------------------------------------------------- | --------------------------------------------------------- |
| 📋    | "Generic programs don't work for tactical demands"  | "Programming built specifically for operational athletes" |
| 📊    | "Tracking workouts in spreadsheets is tedious"      | "Automated tracking with intelligent progress monitoring" |
| 🤔    | "Not sure how to balance strength and conditioning" | "Science-based periodization with HIC/LISS/SE protocols"  |

---

### 3. Features Section

**Purpose:** Showcase the three core features with app screenshots.

#### Feature 1: Training Calendar
**Headline:** "Your Mission, Visualized"
**Description:**
- See your entire training block at a glance
- Color-coded workouts: Strength, HIC, LISS, SE
- Track completion and maintain consistency
- Never miss a workout with clear daily schedules

**Screenshot:** Calendar view with colored workout indicators

**Key Details to Highlight:**
- Weekly structure visibility
- Rest day indicators
- Workout type legend (Strength = Blue, HIC = Red, LISS = Green, SE = Purple)
- Progress tracking per training block

---

#### Feature 2: Exercise Plan Creator
**Headline:** "Tactical Barbell, Simplified"
**Description:**
- Create customized Operator, Fighter, or Mass programs
- Select your primary lifts (Squat, Bench, Deadlift, Weighted Pull-ups, etc.)
- Configure conditioning splits (HIC:LISS ratios)
- Automatic training max calculations

**Screenshot:** Plan detail view showing program configuration

**Key Details to Highlight:**
- Program types: Operator, Fighter, Mass protocols
- 6-week training blocks
- Conditioning split configuration (2:1 HIC:LISS)
- Training maxes with automatic calculations
- Plan status tracking (Draft → Active → Completed)

---

#### Feature 3: KENT - AI-Powered Programming
**Headline:** "Intelligence Behind Every Rep"
**Description:**
- AI that works behind the scenes to optimize your training
- Automatically tailors programming to your strength levels and goals
- Calculates training maxes and progression intelligently
- Adapts conditioning recommendations to your fitness profile

**Visual:** Abstract AI/algorithm visualization or profile metrics showing personalized data

**Key Details to Highlight:**
- Seamless, automatic personalization
- No manual calculations needed
- Evidence-based programming decisions
- Tailored to your athlete profile (age, weight, strength levels)
- Smart conditioning recommendations (ruck weights, HIC:LISS ratios)

---

### 4. Athlete Profile Section

**Purpose:** Show the depth of personalization available.

**Headline:** "Built Around You"
**Description:** "Your profile powers intelligent recommendations based on age, weight, strength levels, and training history."

**Screenshot:** Athlete Profile view

**Metrics Showcased:**
- Age category classification (Masters, Open, etc.)
- Body weight with unit conversion (kg/lbs)
- Strength profile with bodyweight ratios
- Conditioning recommendations (ruck weight calculations)
- Primary lifts for Tactical Barbell programming

---

### 5. Social Proof Section

**Purpose:** Build trust and credibility.

**Headline:** "Trusted by Tactical Athletes"

**Content Options:**
- Testimonial quotes (if available)
- User statistics ("X athletes training")
- Military/LE organization logos (if partnerships exist)
- "As featured in" badges

**Placeholder Content:**
> "Finally, an app that understands tactical fitness isn't just about looking good—it's about being ready."
> — *Active Duty Military*

> "The calendar view alone has transformed how I track my Tactical Barbell programming."
> — *Law Enforcement Officer*

---

### 6. Pricing Section (Optional)

**Purpose:** Clear pricing information if applicable.

**Options:**
- Free tier vs Premium comparison
- Simple "Free to start" messaging
- "Coming soon" for premium features

**Recommendation:** Keep simple initially with "Start Free" messaging.

---

### 7. Final CTA Section

**Purpose:** Final conversion push before footer.

**Headline:** "Ready to Train?"
**Subheadline:** "Join tactical athletes who take their training seriously."
**CTA Button:** "Start Training Free" → `https://app.tacticaltraining.eu/`

**Design:**
- High contrast section with olive/gold accent colors
- Possibly include app store badges if mobile apps exist

---

### 8. Footer

**Purpose:** Navigation, legal, and contact information.

**Content:**
- Logo and tagline
- Navigation links (Blog, About, Contact)
- Social media links
- Legal links (Privacy Policy, Terms of Service)
- Copyright notice

---

## Design Specifications

### Color Palette (Matching App)

Use existing CSS variables from the blog:

```css
/* Primary Backgrounds */
--tactical-bg: #0d0f0e;
--tactical-surface: #232826;
--tactical-elevated: #2d3230;

/* Accent Colors */
--tactical-olive: #4a5d23;        /* Primary action buttons */
--tactical-olive-light: #5c7a2e;  /* Hover states */
--tactical-accent: #d4a84b;       /* Gold highlights, links */

/* Text */
--tactical-text: #e8ebe9;
--tactical-text-secondary: #a3a8a5;

/* Status (from app) */
--status-active: #4ade80;         /* Green - Active badges */
--status-completed: #4a5d23;      /* Olive - Completed */
--status-hic: #ef4444;            /* Red - HIC workouts */
--status-strength: #60a5fa;       /* Blue - Strength workouts */
```

### Typography

- **Headings:** Bold, uppercase for section headers (matches app style)
- **Body:** System UI font stack
- **Hierarchy:** Clear distinction between H1 (hero), H2 (sections), H3 (features)

### Layout

- **Max Width:** 1200px container
- **Sections:** Full-width backgrounds with contained content
- **Spacing:** Generous whitespace between sections (4rem+)
- **Mobile:** Stack all content vertically, full-width CTAs

### Interactive Elements

- **Buttons:**
  - Primary: Olive green background, light text
  - Secondary: Transparent with border
  - Hover: Lighter olive or gold accent

- **Cards:**
  - Dark surface background
  - Subtle border
  - Hover elevation effect

- **Screenshots:**
  - Phone mockup frames (optional)
  - Subtle shadow/glow effect
  - Responsive sizing

---

## Technical Implementation

### File Structure

```
/
├── index.html              # Marketing landing page (replace current blog home)
├── _layouts/
│   └── marketing.html      # New layout for marketing page
├── _includes/
│   ├── marketing-header.html   # Simplified header for marketing
│   └── marketing-footer.html   # Marketing-specific footer
├── assets/
│   ├── css/
│   │   └── marketing.css   # Marketing page specific styles
│   └── images/
│       └── app/            # App screenshots
│           ├── calendar.png
│           ├── plans.png
│           ├── profile.png
│           └── plan-detail.png
```

### Navigation Changes

**Current:** Blog is at root (`/blog`)
**Proposed:**
- Marketing page at root (`/`)
- Blog moves to `/blog` path (already configured in baseurl)

### SEO Considerations

- **Title:** "Tactical Training - Fitness App for Tactical Athletes"
- **Description:** "The complete tactical fitness platform for military, law enforcement, and first responders. Build strength, conditioning, and operational readiness with Tactical Barbell programming."
- **Keywords:** tactical fitness, tactical barbell, military fitness, first responder training, operator fitness, strength and conditioning

### Performance

- Optimize screenshot images (WebP format, compressed)
- Lazy load images below the fold
- Minimize CSS/JS
- Target < 3s load time

---

## Content Requirements

### Copy Needed

1. Hero headline and subheadline (final version)
2. Feature descriptions (3x)
3. Problem/solution statements
4. Testimonials (real or placeholder)
5. Final CTA copy
6. Meta description for SEO

### Assets Needed

1. App screenshots (provided - need optimization)
2. Logo variations (if not using existing shield)
3. Background patterns/textures (optional)
4. Device mockup frames (optional)

---

## Success Metrics

- **Primary:** Click-through to app signup (`https://app.tacticaltraining.eu/`)
- **Secondary:**
  - Time on page
  - Scroll depth
  - Blog navigation (users exploring content)

---

## Implementation Phases

### Phase 1: Core Page
- Marketing layout
- Hero section
- Features section with screenshots
- Final CTA
- Basic responsive design

### Phase 2: Enhancement
- Athlete profile section
- Problem/solution section
- Animations/transitions
- Image optimization

### Phase 3: Polish
- Testimonials (when available)
- A/B testing headlines
- Performance optimization
- Analytics integration

---

## Open Questions

1. **Pricing:** Is there a free tier? Premium features?
2. **Testimonials:** Are there real user testimonials available?
3. **App Store:** Are there iOS/Android apps or web-only?
4. **Blog Integration:** Should blog posts appear on marketing page (recent articles section)?

---

## Appendix: Screenshot Analysis

### Screenshot 1: Athlete Profile
- Shows personalization depth
- Strength ratios are unique differentiator
- Ruck weight recommendations show tactical focus
- Masters category shows age-appropriate programming

### Screenshot 2: Training Plans
- Plan lifecycle visible (Draft → Active → Completed)
- Tactical Barbell "Operator" program type
- Progress tracking per plan
- Exercise tags show customization

### Screenshot 3: Training Calendar
- Most visually impressive for marketing
- Color coding is intuitive
- Shows structure and planning
- Daily workout details below calendar

### Screenshot 4: Plan Detail
- Shows depth of plan configuration
- Training maxes are professional touch
- Progress visualization clear
- Conditioning split (HIC:LISS) shows methodology

---

*Specification created for Tactical Training marketing page. Ready for implementation upon approval.*
