# EventBoost 🎯

**Turn any event into your biggest sales day.**

AI-powered event marketing platform for local businesses. Automatically integrate events (World Cup, concerts, festivals) into your business strategy with QR code promotions, demand prediction, inventory management, and smart scheduling.

Built for the [Google Cloud Rapid Agent Hackathon](https://rapid-agent.devpost.com/) — MongoDB track.

---

## The Problem

Local businesses miss out on event-driven demand because:
- They don't know which events are coming
- They don't know how to promote around them
- They can't predict demand or prepare inventory
- They scramble last-minute and lose sales

## The Solution

**Business + Event + AI Agent = Increased Profit**

EventBoost is a platform that helps local businesses seamlessly integrate events into their business strategy. The AI agent does the heavy lifting: predicting demand, managing inventory, scheduling staff, and creating promotions.

---

## How It Works

### Step 1: Business Onboards
- Upload menu, prices, inventory
- Set location, capacity, hours
- Connect POS (optional, can be mock for demo)

### Step 2: Select an Event
- World Cup match, concert, festival, local event
- Agent pulls event data (date, expected attendance, location)

### Step 3: Agent Creates Promotion
- Generates QR code / promo code for event-specific deal
- Example: "Watch Argentina vs Brazil LIVE! Scan for 20% off wings + free first beer"
- Business customizes or approves

### Step 4: Customers Engage
- Scan QR code → landing page
- See event details + promo
- Enter name, phone/email
- Pre-order combo (wings + beer = $15 instead of $22)
- Pay online (or pay at venue)

### Step 5: Agent Does the Work
- Analyzes pre-orders → predicts total demand
- Adjusts inventory: "Order 200 more beer bottles"
- Schedules staff: "Add 2 bartenders for 6-11pm"
- Creates social media post: "Watch the World Cup here! Scan for deals"
- Sends reminders to customers who signed up

### Step 6: Post-Event Analysis
- Agent compares prediction vs actual
- "You predicted 150 customers, actual was 130. Adjusting for next Brazil game."
- Shows ROI: "This event generated $2,400 extra revenue"

---

## Key Features

### Business Dashboard
- Upcoming events (auto-detected)
- Promotions created (QR codes)
- Pre-orders and customer signups
- Agent recommendations (inventory, staff, marketing)
- ROI report per event

### Event Manager
- Select event → agent generates promo
- Customize deal (discount %, combo items)
- Preview QR code / landing page
- Schedule social media posts

### Customer Landing Page (mobile-friendly)
- Event details + promo
- Name, phone/email form
- Menu / combo selection
- Payment (mock for demo)
- Confirmation + reminder

### Agent Chat
- "What should I do for the World Cup match?"
- "How many customers should I expect?"
- "Create a promo for the concert next week"

### Phoenix Integration (Self-Improvement)
- View agent traces
- Self-improvement metrics
- Prediction accuracy over time

### Settings
- Business profile
- Menu / products
- Staff list
- Location

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Agent | Google ADK (Python) |
| Data Layer | MongoDB Atlas (free tier) |
| MCP | MongoDB MCP Server |
| Observability | Arize Phoenix (optional) |
| Frontend | V0/Cursor vibecoding |
| Deploy | Cloud Run |
| License | MIT |

---

## What Makes This Different

**For businesses:**
- One-click event integration (no manual research)
- Automated promotions (QR code generated instantly)
- Demand prediction (never run out or over-order)
- Staff scheduling (right people at the right time)
- Measurable ROI (see exactly how much the event earned)

**For customers:**
- Easy access to event deals (scan QR, done)
- Pre-order (no waiting in line)
- Reminders (don't forget the game)

**For the agent (Arize/Phoenix):**
- Traces every decision
- Evaluates outcomes (did prediction match reality?)
- Improves over time (learns from each event)

---

## Catchphrases

- "Turn events into revenue."
- "Your business + any event = more sales."
- "Stop guessing. Start profiting from events."
- "One QR code. One event. Big results."
- "AI-powered event marketing for local businesses."

---

## Project Structure

```
event-boost/
├── README.md                          # This file
├── LICENSE                            # MIT
├── docs/
│   ├── concept.md                     # Detailed concept and pitch
│   ├── architecture.md                # Technical architecture
│   ├── user-flow.md                   # User flow for demo video
│   ├── features.md                    # Feature list with details
│   ├── mongodb-schema.md             # MongoDB data schema
│   ├── agent-tools.md                # Agent tool definitions
│   ├── arize-integration.md          # Arize/Phoenix self-improvement
│   ├── demo-script.md                # 3-minute demo video script
│   └── hackathon-requirements.md     # What the hackathon expects
├── .gitignore
└── (future: src/, frontend/, etc.)
```

---

## Hackathon Info

- **Event:** Google Cloud Rapid Agent Hackathon
- **Track:** MongoDB
- **Deadline:** June 11, 2026 @ 2:00 PM PDT (June 12 @ 4:00 AM GMT+7)
- **Prize:** $5,000 (1st place in MongoDB track)
- **Submission:** Devpost

---

## License

MIT

---

## Contact

Built by [Mika](https://github.com/mikaelaldy) for the Google Cloud Rapid Agent Hackathon.
