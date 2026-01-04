# Emily AI Home Screen Specification

## SwiftTail - Emily AI Private Flight Advisor Interface

**Document Version:** 1.0
**Date:** 2026-01-04
**Prototype URL:** https://adamqureshi.com/swifttail-emily-ui-poc/index.html
**Status:** Draft - Pending Approval

---

## Overview

A complete redesign of the home screen to feature Emily AI as the default search/booking experience. The interface follows a three-column layout optimized for guided flight search workflows, lead qualification, and broker communication.

### Core Concept

Emily AI serves as a conversational flight advisor that:
1. Collects trip details through natural conversation
2. Qualifies leads with contact and budget information
3. Generates clean broker packets for quote requests
4. Provides price estimates and booking guidance

### Target Users

- Private jet charter seekers (business travelers)
- Leisure travelers exploring charter options
- Travel managers and executive assistants
- First-time charter inquirers

---

## Page Structure

### Layout Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              HEADER                                      │
│  [Logo] SwiftTail    Emily Chat | Empty Legs | How it Works | Support   │
│                                                    [Sign in] [Sign up]   │
├──────────────┬─────────────────────────────┬────────────────────────────┤
│              │                             │                            │
│   LEFT       │         CENTER              │         RIGHT              │
│   SIDEBAR    │         CHAT                │         SIDEBAR            │
│              │                             │                            │
│  Navigation  │    Emily AI Chat            │    Lead Qualification      │
│  & Workflow  │    Interface                │    Trip Details            │
│  Steps       │                             │    Broker Packet           │
│              │                             │                            │
│  ~200px      │        ~550px               │        ~350px              │
│              │                             │                            │
├──────────────┴─────────────────────────────┴────────────────────────────┤
│                              FOOTER                                      │
│  POC UI only • Grayscale / contrast-first • Mobile responsive           │
└─────────────────────────────────────────────────────────────────────────┘
```

**Responsive Behavior:**
- Desktop (1200px+): Full three-column layout
- Tablet (768px-1199px): Two columns (chat + collapsible sidebars)
- Mobile (<768px): Single column with tabbed navigation

---

## 1. Header

### Content

| Element | Description |
|---------|-------------|
| Logo | "ST" icon + "SwiftTail" text |
| Subtitle | "EMILY UI proof-of-concept" (POC only) |
| Primary Nav | Emily Chat, Empty Legs, How it Works, Support |
| Auth Buttons | "Sign in" (outlined), "Sign up" (filled) |

### Design Notes

- Clean, minimal header
- Grayscale/high-contrast design
- Fixed position on scroll
- Logo links to home

---

## 2. Left Sidebar - Navigation

### Header

- **Title:** "Navigation"
- **Subtitle:** "Keep it simple for the MVP"
- **Badge:** "USA focus" (top right)

### Workflow Steps

| Step | Indicator | Title | Description |
|------|-----------|-------|-------------|
| 1 | Blue dot | New Trip | Start a private flight request with Emily |
| 2 | Yellow dot | Lead Qualification | Collect contact + budget and confirm intent |
| 3 | Yellow dot | Trip Details | Route, dates, pax, aircraft class, notes |
| 4 | Yellow dot | Broker Packet | One clean summary you can send to brokers |

### Step Indicators

- **Blue dot:** Current/active step
- **Yellow dot:** Pending/upcoming step
- **Green checkmark:** Completed step
- **Gray dot:** Inactive/disabled step

### POC Note (Bottom)

```
POC note: buttons and inputs are static in this demo. Your dev will
wire up chat, validation, and broker handoff.
```

### Design Specifications

- Width: ~200px fixed
- Background: White/light gray
- Border-right: 1px solid light gray
- Padding: 16px
- Step items: 8px vertical spacing

---

## 3. Center - Emily AI Chat Interface

### Header Section

**Title:** "EMILY — your AI Private Flight Advisor"

**Subtitle:** "Tell Emily your trip details and she'll help estimate options and prepare a request you can send to a licensed charter broker for booking."

### Quick Action Buttons

Row of pill-shaped buttons for common intents:

| Button | Purpose |
|--------|---------|
| Get a price estimate | Quick pricing inquiry |
| Request quotes to book | Ready to book flow |
| Business trip | Business travel preset |
| Leisure getaway | Vacation travel preset |
| Multi-city | Complex itinerary |
| Flying with pets | Pet-friendly search |

**Design:**
- Outlined buttons with rounded corners
- Horizontal scrollable on mobile
- Click triggers Emily's response with relevant questions

### Chat Messages

#### Emily Message Format
```
┌─────────────────────────────────────────┐
│ [E]  Message content here               │
│      Emily • 10:02 AM                   │
└─────────────────────────────────────────┘
```
- Avatar: Circle with "E" on green/blue background
- Timestamp: Below message, gray text
- Background: Light gray bubble
- Alignment: Left

#### User Message Format
```
┌─────────────────────────────────────────┐
│                   Message content here  │
│                       You • 10:03 AM    │
└─────────────────────────────────────────┘
```
- Background: Dark (near black) bubble
- Text: White
- Alignment: Right
- Timestamp: Below message

### Sample Conversation Flow

1. **Emily (intro):**
   > Hi — I'm **Emily**. I'll collect your trip details, share a rough price range, and generate a clean request you can send to brokers for live quotes.

2. **Emily (qualifying):**
   > Is this trip for **business** or **leisure**?

3. **User:**
   > Business. NYC to Miami next month. 4 passengers.

4. **Emily (follow-up):**
   > Great. A few quick details so I can recommend the best aircraft class:
   > - One-way or round trip?
   > - Departure date + time window?
   > - Any flexibility on times?

5. **User:**
   > Round trip. Depart Jan 18 (morning), return Jan 21 (evening). ±2 hours flexible.

### Message Input Area

```
┌─────────────────────────────────────────────────┐
│ Message Emily... (demo UI only)         [Send]  │
└─────────────────────────────────────────────────┘
```

**Tip text below input:**
> Tip: in the real build, show a summary + ask for contact only after the user sees value.

### Design Specifications

- Max width: ~550px
- Background: White
- Chat area: Scrollable, flex-grow
- Input: Fixed at bottom
- Send button: Filled, primary color
- Messages: 12px padding, 8px border-radius

---

## 4. Right Sidebar - Forms

### 4.1 Lead Qualification (Step 1)

**Header:**
- Title: "Lead Qualification"
- Subtitle: "Collect contact + budget (USA phone required)."
- Step indicator: "Step 1" badge

**Form Fields:**

| Field | Type | Placeholder/Default | Required |
|-------|------|---------------------|----------|
| Full name | Text input | "Alex Johnson" | Yes |
| Traveler type | Dropdown | Business, Leisure | Yes |
| U.S. mobile number | Tel input | "(212) 555-0123" | Yes |
| Email | Email input | "name@company.com" | No |
| Budget comfort (rough) | Dropdown | $10k-$25k, $25k-$50k, $50k-$100k, $100k+ | Yes |
| How soon? | Dropdown | Next 7 days, Next 30 days, Next 90 days, Flexible | Yes |

**Validation Note:**
> In the real build: validate + SMS verify.

**Consent Checkbox:**
> [ ] I agree to be contacted about this flight request
> SwiftTail and partner brokers may reach out by call/text/email. Msg & data rates may apply.

**CTA Button:** "Save lead (demo)"

**Helper Text:**
> Your dev will connect this to your CRM / broker workflow.

---

### 4.2 Trip Details (Step 2)

**Header:**
- Title: "Trip Details"
- Subtitle: "The minimum info brokers need for quotes."
- Step indicator: "Step 2" badge

**Form Fields:**

| Field | Type | Options/Placeholder | Required |
|-------|------|---------------------|----------|
| From (airport or city) | Text/Autocomplete | "NYC / Teterboro (TEB)" | Yes |
| To (airport or city) | Text/Autocomplete | "Miami / Opa-locka (OPF)" | Yes |
| Trip type | Dropdown | One-way, Round trip, Multi-city | Yes |
| Passengers | Dropdown | 1-16 | Yes |
| Depart date | Date picker | dd/mm/yyyy | Yes |
| Depart time window | Dropdown | Morning, Afternoon, Evening, Flexible | Yes |
| Return date | Date picker | dd/mm/yyyy | If round trip |
| Return time window | Dropdown | Morning, Afternoon, Evening, Flexible | If round trip |
| Flexibility | Dropdown | Exact times, ±1 hour, ±2 hours, Flexible | Yes |
| Aircraft preference | Dropdown | Recommend best fit, Light jet, Midsize, Super-mid, Heavy, Ultra-long range | No |
| Baggage | Dropdown | Standard, Heavy, Oversized | No |
| Pets | Dropdown | No, Yes - small, Yes - large | No |
| Notes for the broker | Textarea | "Catering preferences, onboard Wi-Fi needed, meeting schedule, preferred airports, etc." | No |

**CTA Button:** "Save trip (demo)"

---

### 4.3 Broker Trip Packet (Preview) (Step 3)

**Header:**
- Title: "Broker Trip Packet (Preview)"
- Subtitle: "What you send to brokers — clean, consistent, copy/paste ready."
- Step indicator: "Step 3" badge

**Packet Content (Monospace/Code Style):**

```
SwiftTail — Qualified Lead (POC)

CONTACT
- Name: Alex Johnson
- Phone (US): (212) 555-0123
- Email: alex@company.com
- Trip purpose: Business
- Budget comfort: $25k-$50k
- Timing: Next 30 days

ITINERARY
- Trip type: Round trip
- Route: NYC (TEB) → Miami (OPF)
- Depart: Jan 18 (Morning) | Flex: ±2 hours
- Return: Jan 21 (Evening) | Flex: ±2 hours
- Pax: 4

AIRCRAFT
- Preference: Recommend best fit
- Priorities: Best value + Wi-Fi

NOTES
- Catering: Light breakfast + coffee
- Ground transport: Not requested

NEXT STEP
Please respond with available aircraft options and firm quotes.
```

**Helper Text:**
> In the real build, this packet should be auto-generated from form + chat data and sent to your broker channel (email/CRM/API).

**Action Buttons:**
- "Copy packet (demo)" - Outlined button
- "Send to brokers (demo)" - Filled primary button

---

## 5. Footer

### Disclaimer Text

> SwiftTail is a technology platform. It does not sell air transportation or operate aircraft. Final quotes and booking are handled by licensed charter brokers and operators.

### POC Badge

> POC UI only • Grayscale / contrast-first • Mobile responsive

---

## Design Specifications

### Color Palette

```css
/* Grayscale / High-Contrast Theme */
--st-bg-primary: #ffffff;
--st-bg-secondary: #f5f5f5;
--st-bg-dark: #1a1a1a;

--st-text-primary: #1a1a1a;
--st-text-secondary: #666666;
--st-text-muted: #999999;
--st-text-inverse: #ffffff;

--st-border-light: #e5e5e5;
--st-border-medium: #cccccc;

/* Accent Colors */
--st-accent-primary: #1a1a1a;      /* Primary buttons */
--st-accent-secondary: #666666;    /* Secondary elements */

/* Status Indicators */
--st-status-active: #2563eb;       /* Blue - current step */
--st-status-pending: #eab308;      /* Yellow - upcoming */
--st-status-complete: #22c55e;     /* Green - done */
--st-status-inactive: #d4d4d4;     /* Gray - disabled */

/* Chat Bubbles */
--st-chat-emily: #f5f5f5;
--st-chat-user: #1a1a1a;
```

### Typography

```css
/* Font Stack */
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;

/* Sizes */
--font-xs: 0.75rem;    /* 12px - timestamps, helper text */
--font-sm: 0.875rem;   /* 14px - form labels, secondary text */
--font-base: 1rem;     /* 16px - body text, inputs */
--font-lg: 1.125rem;   /* 18px - section headers */
--font-xl: 1.25rem;    /* 20px - chat title */
--font-2xl: 1.5rem;    /* 24px - main title */

/* Weights */
--font-normal: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

### Spacing

```css
--space-1: 0.25rem;    /* 4px */
--space-2: 0.5rem;     /* 8px */
--space-3: 0.75rem;    /* 12px */
--space-4: 1rem;       /* 16px */
--space-5: 1.25rem;    /* 20px */
--space-6: 1.5rem;     /* 24px */
--space-8: 2rem;       /* 32px */
--space-10: 2.5rem;    /* 40px */
```

### Component Specifications

#### Buttons

**Primary Button:**
- Background: `--st-accent-primary` (#1a1a1a)
- Text: White
- Padding: 12px 24px
- Border-radius: 6px
- Font-weight: 500
- Hover: Opacity 0.9

**Secondary Button:**
- Background: Transparent
- Border: 1px solid `--st-border-medium`
- Text: `--st-text-primary`
- Padding: 12px 24px
- Border-radius: 6px
- Hover: Background `--st-bg-secondary`

**Pill Button (Quick Actions):**
- Background: Transparent
- Border: 1px solid `--st-border-light`
- Padding: 8px 16px
- Border-radius: 20px
- Font-size: 14px
- Hover: Background `--st-bg-secondary`

#### Form Inputs

- Height: 44px
- Padding: 12px
- Border: 1px solid `--st-border-light`
- Border-radius: 6px
- Background: White
- Focus: Border-color `--st-accent-primary`

#### Cards/Sections

- Background: White
- Border: 1px solid `--st-border-light`
- Border-radius: 8px
- Padding: 16px-24px

---

## Responsive Design

### Desktop (1200px+)

- Full three-column layout
- All sidebars visible
- Chat area centered

### Tablet (768px - 1199px)

- Two-column layout
- Left sidebar collapsed to icons only
- Right sidebar as slide-out panel
- Chat area takes primary focus

### Mobile (<768px)

- Single column layout
- Bottom tab navigation for steps
- Full-screen chat
- Forms as modal/sheets
- Swipe gestures for panels

---

## Interactions & Animations

### Chat

- New messages animate in (fade + slide up)
- Typing indicator while Emily "thinks"
- Smooth scroll to new messages

### Forms

- Auto-save on field blur (in production)
- Real-time validation feedback
- Success animations on save

### Navigation

- Step transitions animate
- Active step highlighted
- Progress tracked visually

---

## Technical Implementation

### File Structure

```
/
├── index.html                    # Emily AI home page
├── _layouts/
│   └── emily.html               # Emily chat layout
├── _includes/
│   ├── emily-header.html        # Emily-specific header
│   ├── emily-sidebar-left.html  # Navigation sidebar
│   ├── emily-chat.html          # Chat interface
│   ├── emily-sidebar-right.html # Forms sidebar
│   └── emily-footer.html        # Footer with disclaimer
├── assets/
│   ├── css/
│   │   └── emily.css            # Emily-specific styles
│   └── js/
│       └── emily-chat.js        # Chat interactions (demo)
```

### Data Flow (Production)

```
User Input → Emily AI → Form Auto-population → Lead Qualification
                ↓
         Trip Details Form → Broker Packet Generation
                ↓
         Send to Broker (Email/CRM/API)
```

### API Endpoints (Future)

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/chat` | POST | Send message to Emily AI |
| `/api/leads` | POST | Save qualified lead |
| `/api/trips` | POST | Save trip details |
| `/api/packets` | POST | Generate broker packet |
| `/api/brokers/send` | POST | Send packet to brokers |

---

## Accessibility

### Requirements

- WCAG 2.1 AA compliance
- Keyboard navigation for all interactions
- Screen reader compatible
- Focus indicators visible
- Sufficient color contrast (4.5:1 minimum)
- Touch targets 44px minimum

### ARIA Labels

- Chat messages with role="log"
- Form fields with descriptive labels
- Buttons with aria-label where needed
- Live regions for dynamic content

---

## SEO Considerations

- **Title:** "SwiftTail | Emily - Your AI Private Flight Advisor"
- **Description:** "Get instant private jet charter estimates with Emily AI. Tell her your trip details and receive quotes from licensed brokers."
- **Keywords:** private jet charter, charter flight booking, private flight advisor, jet charter quotes

---

## Open Questions

1. **AI Backend:** What powers Emily's responses? (OpenAI, Claude, custom?)
2. **Broker Integration:** How are packets delivered? (Email, API, CRM?)
3. **SMS Verification:** What service for phone verification?
4. **Airport Data:** Source for airport autocomplete? (FlightAware, custom DB?)
5. **Pricing Estimates:** How are rough prices calculated?
6. **User Accounts:** Is auth required before chatting?
7. **Multi-language:** Any internationalization requirements?

---

## Implementation Phases

### Phase 1: Static POC
- Three-column layout
- Static chat UI with sample messages
- Form mockups (non-functional)
- Responsive breakpoints

### Phase 2: Interactive Demo
- Chat input functionality (canned responses)
- Form validation
- Local storage for state
- Copy-to-clipboard for packets

### Phase 3: Production MVP
- AI backend integration
- Form submissions to backend
- Broker delivery system
- User authentication
- Real airport search

### Phase 4: Enhancement
- SMS verification
- Real-time pricing estimates
- Booking flow completion
- Analytics and tracking

---

## Metrics & Success Criteria

### Primary KPIs

- Lead qualification completion rate
- Chat-to-lead conversion rate
- Broker packet send rate
- Time to complete flow

### Secondary KPIs

- Chat engagement (messages per session)
- Form completion rate
- Return visitor rate
- Mobile vs desktop usage

---

*Specification created for SwiftTail Emily AI home screen redesign. Ready for review and implementation planning.*
