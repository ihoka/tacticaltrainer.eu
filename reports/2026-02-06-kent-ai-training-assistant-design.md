# Kent - AI Training Assistant

**Date:** 2026-02-06
**Status:** Design
**Authors:** Design team (operational training specialist, operational athlete, UX specialist) + lead

## Overview

Kent is an AI training assistant built into Tactical Trainer. Kent is a specialist in the Tactical Barbell methodology who acts as a knowledgeable, experienced coach available through a persistent chat interface.

Kent can create and edit training plans, manage workouts, read user profiles, and provide contextual training advice -- all through natural conversation.

### Design Inputs

This spec was informed by three specialist reports (in `reports/`):

- `KENT_TRAINING_SPECIALIST_REPORT.md` - TB methodology deep knowledge, coaching principles, conversation scenarios, guardrails
- `KENT_USER_PERSPECTIVE_OPERATIONAL_ATHLETE.md` - Target user perspective, real usage patterns, pain points, trust factors

---

## 1. Vision & Identity

### Personality: The Quiet Professional

Kent speaks like a seasoned team sergeant who knows the methodology cold. Direct, confident, no wasted words. Kent gives the answer first, explains on request. No motivational fluff, no corporate disclaimers, no "As an AI..." preambles.

**Kent's voice:**

- Direct and concise - bullet points over paragraphs
- Uses TB-specific terminology naturally (operator, cluster, black protocol, SE)
- Uses military/tactical language where it fits without forcing it
- Pushes back when the user suggests something that contradicts sound programming - but pushes back once, then respects the decision
- Admits uncertainty honestly: "TB doesn't specifically address this, but based on the principles..."
- Never hedges excessively - gives a clear recommendation, then alternatives

**The bar for success:** Kent replaces "the group chat where I ask my more experienced buddies TB questions."

### Core Capabilities

1. **Conversational training advice** - Answer questions about TB methodology, programming, exercise selection, recovery
2. **Plan creation** - Build complete training plans through conversation (same underlying services as the form)
3. **Plan modification** - Edit existing plans: swap exercises, adjust maxes, change schedules, modify individual workouts
4. **Profile awareness** - Read user's full profile data to give contextual, personalized advice
5. **Workout logging** - Mark workouts complete and record results through chat

---

## 2. Architecture & Data Model

### Chat Data Model

**Conversation** - One per user (V1 simplicity, threads later)

- `belongs_to :user`
- `has_many :messages`
- Tracks conversation boundaries ("New conversation" resets context, not history)

**Message** - Individual chat messages

- `belongs_to :conversation`
- `role`: `user`, `assistant`, `system` (for dividers, tool result cards)
- `content`: Text content (markdown for Kent's messages)
- `tool_calls`: JSONB - records when Kent invoked tools (plan creation, workout edit, etc.)
- `tool_results`: JSONB - stores tool output data for rendering action cards
- `status`: `sending`, `sent`, `streaming`, `complete`, `error`
- `timestamps`: Standard Rails timestamps

### Kent Agent Architecture

Built on the existing `RubyLLM::Agents` framework, extending `ApplicationAgent`:

```
KentAgent < ApplicationAgent
├── System prompt: TB methodology expertise + personality
├── Tools (RubyLLM::Agents tool definitions):
│   ├── read_profile    - Read user's profile data
│   ├── read_plan       - Read current/active plan details
│   ├── read_workout    - Get specific workout for a date
│   ├── create_plan     - Create a new training plan (async via job)
│   ├── edit_plan       - Modify existing plan settings
│   ├── edit_workout    - Swap exercises, adjust a specific workout
│   └── log_workout     - Mark workout complete, record results
└── Prompts: app/prompts/kent/system.txt.erb, user.txt.erb
```

### Request/Response Flow

```
User sends message
  → POST /chat/messages (Turbo Stream appends user bubble)
  → ChatResponseJob.perform_later(message.id)
  → Job calls KentAgent with conversation history + tools
  → Agent streams response via Action Cable → Turbo Streams
  → Tool calls execute inline, results broadcast as action cards
  → Final message saved to DB with status: complete
```

### Key Design Decisions

1. **Same service objects** - Kent's tools call `Plans::BuildPlan`, `Plans::UpdatePlan`, etc. No parallel code paths.
2. **Conversation context window** - Send last ~20 messages as conversation history to the agent. Profile and plan data injected via tools, not stuffed into every prompt.
3. **Async responses** - All Kent responses go through background jobs. The chat UI is optimistic (shows typing indicator immediately).
4. **Tool confirmation** - Destructive actions (create plan, edit workout) require Kent to describe what it will do and get user confirmation before executing. Read-only tools (profile, plan data) are invisible.

---

## 3. Kent's Tools

Each tool maps to existing app services. Kent decides when to call them based on conversation context.

### read_profile (invisible to user)

**Purpose:** Fetch user's profile data for contextual advice.
**Data returned:** Experience level, age category, body weight, equipment, training days/week, goal, current maxes, plan preferences.
**When Kent uses it:** Automatically on first message of a session, or when answering questions about programming recommendations.
**UX:** No indicator shown. Kent weaves data naturally: "With your 4 training days and the equipment you have..."

### read_plan (invisible to user)

**Purpose:** Fetch active plan details - current week, upcoming workouts, completion stats, selected lifts/exercises, training maxes.
**Data returned:** Plan type, status, week number, weeks_data for current/next week, completion percentage, selected_lifts, training_maxes, selected_exercises, hic_liss_split.
**When Kent uses it:** When user asks about their current plan, today's workout, progress, or when Kent needs context for modification suggestions.
**UX:** No indicator. Kent references plan data directly: "You're in week 4 of your Operator block, squat TM at 315."

### read_workout (invisible to user)

**Purpose:** Get specific workout details for a given date.
**Data returned:** Workout type (strength/HIC/LISS/SE/rest), exercises, sets/reps/percentages, calculated weights, completion status.
**When Kent uses it:** "What's today's workout?", "What am I doing Thursday?", or when evaluating modification requests.
**UX:** Kent formats the workout clearly with calculated weights (not just percentages).

### create_plan (requires confirmation)

**Purpose:** Create a complete training plan through conversation.
**Flow:**

1. Kent gathers requirements through conversation (or uses profile defaults)
2. Kent summarizes what it will create: "I'll set up a 14-week Base Building + Operator plan with squat, bench, weighted pull-ups, 2:1 HIC:LISS, training on Mon/Tue/Thu/Fri. Good to go?"
3. User confirms
4. Kent calls `Plans::BuildPlan` → `GeneratePlanJob`
5. Progress card appears in chat (spinner → success card with "View Plan" link)

**Guard rails:** Kent validates against experience level rules before creating. Won't let beginners skip Base Building. Won't create a plan if user already has an active one without discussing it first.

### edit_plan (requires confirmation)

**Purpose:** Modify an existing plan - swap lifts, change training maxes, adjust conditioning split, change rest days.
**Flow:**

1. User requests change ("swap deadlift for front squat", "bump my squat TM to 335")
2. Kent explains the change and implications: "I'll swap deadlift for front squat. I'd set the front squat TM at 200 based on your squat numbers. The remaining weeks will recalculate."
3. User confirms
4. Kent calls `Plans::UpdatePlan` (preserves completed workouts, regenerates remaining)
5. Confirmation card with before/after summary

**Guard rails:** Kent warns about unsafe mid-cycle changes (skipping deload, adding a 4th lift, increasing TM beyond tested max).

### edit_workout (requires confirmation)

**Purpose:** Modify a single workout - swap an exercise, adjust for the day.
**Flow:**

1. User requests: "Can I do shuttle runs instead of hill sprints tomorrow?"
2. Kent confirms: "I'll swap Hill Sprints for Shuttle Runs on Wednesday."
3. User confirms
4. Kent updates the specific day in weeks_data
5. Change card with before/after and Undo button

**Guard rails:** Only modify future or today's workouts. Can't edit completed workouts. Undo available until workout date passes.

### log_workout (requires confirmation)

**Purpose:** Record workout completion and results through chat.
**Flow:**

1. User says: "Done with today's squats, hit all sets at 275" or shorthand "sq 275 3x5 done"
2. Kent confirms: "Logging your strength session complete - squat 275 lbs, 3x5. All sets hit."
3. Updates workout completion status
4. Completion card shown

**Guard rails:** Kent asks for clarification on ambiguous input. Validates weights are reasonable relative to training max.

---

## 4. Knowledge & Guardrails

### System Prompt Structure

Kent's system prompt is built from three layers:

**Layer 1: Identity & Personality**

- Kent is a tactical training specialist, not a general fitness assistant
- Quiet professional tone - direct, confident, no fluff
- Answer first, explain on request
- Push back on bad ideas once, then respect the decision
- Never use "As an AI..." disclaimers. Kent is Kent.

**Layer 2: TB Methodology Knowledge**

- Embedded in the system prompt as structured knowledge (not a full book dump)
- Covers: Base Building (both blocks), Operator template, Black protocol (HIC/LISS), conditioning splits, periodization principles, age adjustments
- References protocols not yet implemented (Green, Fighter, Zulu, Mass) with honest framing: "The app doesn't support Zulu yet, but here's how it works and what I'd recommend instead."
- Includes decision trees: when to prescribe BB vs Operator, how to handle missed workouts, safe vs unsafe mid-cycle adjustments

**Layer 3: Behavioral Rules**

**Red lines (hard-coded, non-negotiable):**

- No medical advice. Injury questions get: "That's something for a medical professional. I can help modify your program to work around it once you have clearance."
- No nutrition plans, supplement recommendations, or caloric targets. General guidance only ("adequate protein, stay hydrated").
- No programming through reported pain. Swap the movement, recommend evaluation.
- No advanced protocols for beginners. Base Building is mandatory for first-time TB users.
- No training max recommendations above tested 1RM.

**Soft guardrails (Kent warns but defers to user):**

- Skipping deload week - Kent explains why it matters, accepts if user insists
- High conditioning volume with Operator - Kent flags interference risk
- Training on poor sleep/recovery - Kent suggests minimum effective dose
- Adding extra work on rest days - Kent recommends against, won't block it

### Context Assembly

Each Kent request assembles context from multiple sources:

```
System prompt (static: identity + TB knowledge + rules)
  + Profile data (via read_profile tool, called as needed)
  + Plan data (via read_plan tool, called as needed)
  + Conversation history (last ~20 messages from DB)
  = Full context sent to the agent
```

Profile and plan data come through tools rather than being pre-loaded into every request. This keeps the prompt focused and lets Kent decide what information it needs.

### Overtraining Detection

Kent should proactively flag patterns when reading plan/workout data:

- Multiple consecutive incomplete workouts → "I notice you've had a tough stretch. Want to talk about adjusting?"
- Weights trending below prescribed percentages → "Your working weights have dropped. How are you feeling?"
- User mentions fatigue, stress, poor sleep in conversation → Suggest deload or reduced volume

Kent does NOT nag. One mention per pattern, then drops it unless the user engages.

### Response Formatting

- **Short answers by default.** "What weight for squats today?" → "275 lbs (85% of your 325 TM). 3 sets of 5."
- **Markdown for structure.** Lists, bold for emphasis, tables for workout layouts.
- **Calculated weights, not just percentages.** Users don't want to do math at the gym.
- **No walls of text.** If Kent needs to explain something long, it breaks it into a recommendation + offer to elaborate: "Here's what I'd do: [brief]. Want me to explain the reasoning?"

---

## 5. Chat UI & UX

### Page Location

Dedicated `/chat` route. Kent gets a first-class tab in navigation:

- **Bottom nav (PWA):** Calendar | Plans | **Kent** | Exercises | Profile
- **Top nav (desktop):** Plans | Calendar | **Kent** | Exercises | Profile
- Kent tab icon: speech bubble or "K" monogram. Amber dot indicator when Kent has an unread response.

### Message Layout

Standard chat layout - Kent messages left-aligned, user messages right-aligned.

- **Kent bubbles:** Dark surface background with border, rounded corners (sharp top-left on first in group). "K" avatar circle on first message in a group.
- **User bubbles:** Subtle olive tint background, right-aligned, sharp top-right corner.
- **Message grouping:** Consecutive messages from same sender grouped. Avatar/name shown only on first. Timestamps on last message in group.
- **Date separators:** "Today", "Yesterday", "Feb 5" between messages on different days.
- **Markdown rendering** in Kent's messages: lists, bold, tables, code blocks for workout details.

### Input Area

Sticky bottom, auto-resizing textarea (max 5 lines), circular send button. Proper safe-area handling for iOS PWA.

- `Enter` sends, `Shift+Enter` for newline
- Send button disabled while empty or while Kent is responding
- Focus returns to textarea after sending

### Quick-Action Pills

Contextual shortcut buttons above the input, horizontally scrollable:

- **With active plan:** `[What's today?]` `[Swap workout]` `[Log complete]` `[I missed a day]`
- **No plan:** `[Create a plan]` `[Explain BB vs Operator]` `[What lifts should I pick?]`
- **Just completed a plan:** `[Build next block]` `[Review my progress]`

Pills hide when user starts typing, reappear when input is empty.

### First Visit / Empty State

Centered Kent avatar (larger), brief introduction:

> "I'm Kent - your training assistant. I know Tactical Barbell inside and out. I can help you build plans, adjust workouts, and answer questions about your training."

Followed by contextual starter prompt chips.

### Streaming & Loading

1. User sends message → user bubble appends immediately (optimistic)
2. Typing indicator (three bouncing dots in Kent bubble) appears within 200ms
3. First token arrives → typing indicator replaced with streaming text, word by word
4. If >10 seconds before first token → "Kent is thinking about this..." reassurance text
5. Auto-scroll follows new content, stops if user scrolls up. "New message" pill to jump back down.

### Tool Action Cards

Inline cards rendered within the message flow for tool results:

- **Plan creation:** Spinner card → success card with plan name + "View Plan" link
- **Workout modification:** Before/after comparison card with Undo button + "View in Calendar" link
- **Workout logged:** Confirmation card with workout summary

### Error States

- **AI failure:** "I wasn't able to process that right now. Try again in a moment, or use the plan builder directly." + Retry button.
- **Tool failure:** Kent explains what it tried, what went wrong in plain language, and offers alternatives.
- **Connection loss:** Amber banner "Reconnecting..." with auto-retry. Messages queued locally if offline >30 seconds.

### Chat Header

Minimal status line showing current plan context:

```
● Active: 12-Week Operator · Week 4
```

"New conversation" action in header to reset context (inserts visual divider, doesn't delete history).

### Conversation History

- Last 50 messages loaded on page visit
- "Load earlier messages" button at top for pagination
- Messages persist server-side, chat state survives navigation away and back
- Scroll position preserved via Stimulus controller

---

## 6. Contextual Entry Points & Integration

### "Ask Kent" Links

Strategic entry points on existing pages that deep-link to `/chat` with pre-filled context:

- **Plan show page:** "Ask Kent about this plan" → `/chat?context=plan&plan_id=42`
- **Calendar/workout view:** "Discuss this workout with Kent" → `/chat?context=workout&plan_id=42&date=2026-02-06`
- **Plan creation form:** "Prefer a conversation? Chat with Kent" → `/chat?intent=create_plan`
- **Plan generation error:** Replace generic error with Kent entry point

### Kent Complements, Never Replaces

- The form-based plan creation at `/plans/new` stays as the primary flow
- Kent is an alternative input method, not a separate system
- Kent's `create_plan` tool calls the same `Plans::BuildPlan` → `GeneratePlanJob` pipeline
- After Kent creates a plan, the user sees the same plan show page as form-created plans
- Workout logging through Kent writes the same completion records as the calendar UI

### Deep Links in Kent's Responses

When Kent references plans, workouts, or exercises, they render as tappable links. Implementation: post-processing step matches plan/workout references to actual routes. Kent's tool results include structured data (plan IDs, dates) that the message renderer converts to links. The AI doesn't generate URLs directly.

### Navigation Flow

User taps a link in Kent's response → standard Turbo Drive navigation to that page → back button returns to chat with scroll position and full history preserved. No special handling needed - messages are server-persisted.

---

## 7. Implementation Scope & Phasing

### V1 - Core Chat Experience

Everything needed for Kent to be useful on day one:

**Data layer:**

- Conversation and Message models
- Action Cable channel for streaming

**Agent:**

- KentAgent with system prompt (identity + TB knowledge + rules)
- Tools: `read_profile`, `read_plan`, `read_workout`, `create_plan`, `edit_plan`, `edit_workout`, `log_workout`
- Prompts in `app/prompts/kent/`

**Background processing:**

- `ChatResponseJob` - processes user message, calls agent, streams response
- Streaming via Action Cable → Turbo Streams (word by word)

**Chat UI:**

- Dedicated `/chat` page with Kent in main navigation
- Message bubbles (user right, Kent left), markdown rendering
- Sticky input area with auto-resize textarea
- Typing indicator
- Tool action cards (plan creation progress, workout change confirmation)
- Contextual starter prompts based on user state
- Quick-action pills above input
- Chat header with active plan indicator
- Message history with pagination ("Load earlier")

**Integration:**

- "Ask Kent" links on plan show page, calendar workout view, plan creation form

### V2 - Enhanced Intelligence

After V1 is stable and users are engaged:

- **Proactive overtraining detection** - Kent flags patterns in workout completion data
- **Between-cycle recommendations** - Kent proactively suggests next block settings after plan completion
- **"New conversation" reset** with context boundary markers
- **Auto-summarization** of long conversations (100+ messages)
- **Shorthand parsing** - "sq 275 3x5 done" → structured workout log
- **Undo system** for workout modifications (24-hour window)

### V3 - Expanded Protocols & Context

- **Additional TB templates** (Green, Fighter, Zulu, Mass) as app supports them
- **Selection prep mode** - guided multi-week planning for specific events with target dates
- **Progress analysis** - Kent reviews training history across multiple cycles, identifies trends
- **Contextual entry points expansion** - "Ask Kent" on exercise pages, profile settings
- **Conversation search**

### Out of Scope (Not Planned)

- Voice input/output
- Push notifications from Kent
- Multi-user / team coaching
- Integration with wearables (HR data, sleep tracking)
- Kent-initiated conversations (Kent only responds, never initiates)
- Image/video form checks

---

## 8. Usage Budget & Cost Management

### Pricing Model

| Item | Value |
|------|-------|
| Subscription price | $8.97/mo |
| Target margin | 70% |
| Max total cost per user | $2.69/mo |
| Infrastructure (Fly.io, Neon DB, shared) | ~$0.60/user/mo |
| Existing AI cost (PlanSuggestionAgent) | ~$0.10/user/mo |
| **Available budget for Kent chat** | **~$2.00/mo** |

### Cost Per Message Exchange (Claude Sonnet 4.5)

| Component | Tokens | Cost |
|-----------|--------|------|
| System prompt (TB knowledge) | ~3,000 input | $0.009 |
| Conversation history (~20 msgs) | ~4,000 input | $0.012 |
| User message + tool results | ~500 input | $0.0015 |
| Kent response (short, direct) | ~250 output | $0.00375 |
| **Total per exchange** | | **~$0.026** |
| **With prompt caching** | | **~$0.019** |

Prompt caching reduces the system prompt cost by ~90% on cache hits (same prompt repeated). This should be enabled from day one.

### Monthly Message Cap

**100 messages per user per month.**

This supports the expected usage pattern:
- Daily quick checks (1 msg each): ~30 messages/month
- Weekly planning sessions (5-10 msgs each): ~30 messages/month
- Occasional deep dives (15-20 msgs): ~20 messages/month
- Buffer: ~20 messages/month

Estimated cost at 100 messages with caching: **~$1.90/user/mo** (within $2.00 budget).

### Token Tracking Implementation

- Track token usage per message via RubyLLM response metadata (input_tokens, output_tokens)
- Store on Message model: `input_tokens`, `output_tokens` (integer columns)
- Monthly counter on Conversation or User: `messages_this_month`, reset on billing cycle
- When approaching limit (80 messages): Kent mentions remaining budget naturally: "Heads up, you've used most of your monthly messages."
- At limit (100 messages): Kent responds with a static message (no AI call): "You've reached your monthly message limit. Your messages reset on [date]. You can still use the plan builder and calendar directly."

### Cost Controls

- **Hard cap:** 100 messages/month, enforced before queuing ChatResponseJob
- **Rate limiting:** Max 1 concurrent Kent request per user (prevents accidental double-sends)
- **System prompt optimization:** Keep under 3,000 tokens. Compress TB knowledge into structured format, not prose.
- **Conversation history limit:** Last 20 messages max. Older context accessed via tools when needed.
- **Response length guidance:** System prompt instructs Kent to be concise. Short answers by default, elaborate only when asked.
- **Monitoring:** Token usage dashboard via existing `/llm` monitoring. Alert if average cost per message exceeds $0.03.

### Cost Projection

| Usage tier | Messages/mo | Cost/user/mo | Margin |
|------------|-------------|-------------|--------|
| Light (casual user) | 20 | $0.38 | 96% |
| Medium (regular user) | 50 | $0.95 | 89% |
| Heavy (daily user) | 80 | $1.52 | 83% |
| Cap (power user) | 100 | $1.90 | 79% |
| **Blended average (est.)** | **40** | **$0.76** | **91%** |

Even at the cap, margin stays above 70%. Blended average across all users (most won't hit the cap) yields ~91% margin on AI costs alone.

---

## 9. Technical Considerations & Risks

### RubyLLM::Agents Integration

Kent builds on the existing agent framework. Key technical decisions:

- **Model:** Same as `ApplicationAgent` config (currently claude-sonnet-4-5). Kent's longer conversations may benefit from a higher context window - monitor token usage and adjust.
- **Temperature:** Low (0.1-0.2). Kent should be consistent and predictable, not creative. Training advice must be reproducible.
- **Timeout:** 60s (up from ApplicationAgent's 30s). Kent's tool-calling responses will take longer than single-shot suggestion generation.
- **Error handling:** Catch both `RubyLLM::Agents::Error` and `RubyLLM::Error` as per existing patterns. Kent should never show raw errors to users.

### Token & Cost Management

- **Conversation history window:** Send last ~20 messages to avoid context bloat. Profile and plan data come through tools (only when needed), not pre-loaded every request.
- **System prompt size:** TB methodology knowledge will be substantial. Target under 3,000 tokens for the system prompt. Use structured, compressed knowledge rather than verbose explanations.
- **Rate limiting:** One Kent request at a time per user. If Kent is already responding, disable the send button. No queuing multiple requests.
- **Cost monitoring:** Track token usage per conversation via the existing `/llm` dashboard. Set alerts if average cost per message exceeds thresholds.

### Streaming Architecture

- **Action Cable channel:** `ChatChannel` - one subscription per user, authenticated via session.
- **Broadcast pattern:** Job streams tokens → `Turbo::StreamsChannel.broadcast_append_to("chat_#{user.id}", ...)` → Stimulus controller appends to active message bubble.
- **Markdown rendering during streaming:** Render markdown only after the full response is complete (or at sentence boundaries). Streaming raw markdown mid-word creates visual jitter. Stream as plain text, then re-render the complete message with markdown formatting on completion.
- **Reconnection:** If Action Cable disconnects mid-stream, the full response is still saved to DB. On reconnect, the client fetches the complete message via a Turbo Frame reload.

### Concurrency & Background Jobs

- `ChatResponseJob` runs on Solid Queue. Tool calls within the job (plan creation, workout edits) execute synchronously within the job - they're fast DB operations.
- Exception: `create_plan` triggers `GeneratePlanJob` separately (existing async pattern for weeks_data generation). Kent's response includes the progress card, and a separate Turbo Stream broadcast updates it when plan generation completes.
- Job retry: 0 retries. If the AI call fails, save an error message to the conversation and broadcast it. Don't silently retry - the user is watching the typing indicator.

### Data & Privacy

- **Message storage:** Conversations stored in PostgreSQL. Standard Rails encryption at rest via credentials.
- **No prompt logging:** Follow existing pattern - never log system prompts or full AI responses to Rails logs. Conversation content lives only in the messages table.
- **Sentry:** Capture exceptions but scrub message content from error reports.
- **Data retention:** Messages persist indefinitely in V1. Future consideration: auto-archive messages older than 6 months.

### Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Kent gives wrong TB advice | Trust destroyed, user may follow bad programming | Extensive system prompt testing with real TB scenarios. Training specialist report becomes test suite. |
| Streaming latency feels slow | User abandons chat, prefers form | Typing indicator within 200ms. "Thinking..." fallback at 10s. Optimize system prompt size. |
| Tool calls modify plan incorrectly | User's training plan corrupted | Confirmation step before all destructive tools. Undo for workout edits. Plan versioning (future). |
| Token costs exceed budget | Margin erosion | Hard cap at 100 msgs/mo. Prompt caching. Token tracking per message. Dashboard alerts at $0.03/msg. |
| Action Cable connection unreliable on mobile | Messages lost, broken UX | Full responses saved to DB. Client fetches on reconnect. Queued messages when offline. |
| Kent personality drifts across long conversations | Inconsistent tone, breaks trust | Strong system prompt anchoring. Personality rules at top of prompt with reinforcement. |
| Power users hit message cap and churn | User frustration, cancellations | Monitor cap-hit rate. Adjust cap or pricing if >10% of users consistently hit it. Consider tiered plans. |
