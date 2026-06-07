# Features

## Core Features

### 1. Event Discovery

**What it does:**
- Automatically discovers upcoming events (World Cup, concerts, festivals, local events)
- Filters by location, date, and relevance to business type
- Shows expected attendance and event importance

**How it works:**
- Agent queries event APIs (World Cup schedule, Ticketmaster, local event listings)
- Stores events in MongoDB `events` collection
- Matches events to business location and type

**User story:**
> As a bar owner, I want to see upcoming events near my location so I can prepare my business.

---

### 2. Promotion Generator

**What it does:**
- Creates event-specific promotions (QR code, promo code)
- Generates compelling copy based on event type and business
- Customizable discount %, combo items, and terms

**How it works:**
- Agent uses Gemini to generate promo copy
- Generates unique QR code linked to promotion
- Stores promotion in MongoDB `promotions` collection
- QR code links to customer landing page

**User story:**
> As a bar owner, I want to create a promotion for the World Cup match in one click so I don't have to design it myself.

**Example promo:**
- "Watch Argentina vs Brazil LIVE! Scan for 20% off wings + free first beer 🍺⚽"
- QR code links to: `eventboost.app/promo/abc123`

---

### 3. Demand Prediction

**What it does:**
- Predicts how many customers will attend based on event type, historical data, and current signups
- Provides confidence interval (e.g., "120-150 customers, 85% confidence")
- Adjusts predictions based on weather, day of week, and event importance

**How it works:**
- Agent queries MongoDB for historical data (past events, actual attendance)
- Analyzes current pre-orders and signups
- Uses Gemini to predict demand
- Stores prediction in MongoDB `predictions` collection
- Compares prediction vs actual post-event

**User story:**
> As a bar owner, I want to know how many customers to expect so I can order the right amount of inventory.

**Prediction factors:**
- Event type (World Cup final > group stage)
- Team popularity (Brazil > Switzerland)
- Historical data (past similar events)
- Current pre-orders
- Weather forecast
- Day of week (weekend > weekday)

---

### 4. Inventory Management

**What it does:**
- Recommends inventory adjustments based on predicted demand
- Calculates optimal stock levels for each item
- Alerts when current stock is insufficient

**How it works:**
- Agent queries MongoDB for current inventory
- Compares with predicted demand
- Calculates needed stock per item (beer, wings, etc.)
- Recommends orders with quantities
- Stores recommendation in MongoDB `recommendations` collection

**User story:**
> As a bar owner, I want to know exactly what to order for the World Cup match so I don't run out or waste money.

**Example recommendation:**
- "Order 200 more beer bottles (current: 50, predicted need: 250)"
- "Order 15 kg more wings (current: 20 kg, predicted need: 35 kg)"
- "No change needed for nachos (current stock sufficient)"

---

### 5. Staff Scheduling

**What it does:**
- Recommends staff scheduling based on predicted demand
- Calculates optimal staff count per shift
- Suggests specific staff members based on availability

**How it works:**
- Agent queries MongoDB for current staff schedule
- Compares with predicted demand and hours
- Calculates needed staff per role (bartender, cook, server)
- Recommends schedule adjustments
- Stores recommendation in MongoDB `recommendations` collection

**User story:**
> As a bar owner, I want to schedule the right number of staff for the World Cup match so I'm not over-staffed or under-staffed.

**Example recommendation:**
- "Add 2 bartenders for 6pm-11pm shift (predicted peak: 8pm-10pm)"
- "Add 1 cook for 5pm-11pm shift"
- "No change needed for security (current coverage sufficient)"

---

### 6. Marketing Automation

**What it does:**
- Creates social media posts for the event
- Schedules posts for optimal timing
- Generates email/SMS campaigns for customer signups

**How it works:**
- Agent uses Gemini to generate post copy
- Creates posts for Instagram, Facebook, Twitter
- Schedules posts 4 hours before kickoff (optimal timing)
- Stores posts in MongoDB `promotions` collection

**User story:**
> As a bar owner, I want to automatically post about the World Cup match on social media so customers know we're showing it.

**Example post:**
- "⚽ Watch Argentina vs Brazil LIVE at [Bar Name]! 🍺 Scan the QR code for 20% off wings + free first beer. See you at 8pm! #WorldCup #WatchParty"

---

### 7. Customer Management

**What it does:**
- Collects customer signups via QR code landing page
- Stores customer info (name, phone, email)
- Manages pre-orders and combos
- Sends reminders before the event

**How it works:**
- Customer scans QR code → landing page
- Enters info → stored in MongoDB `customers` collection
- Selects combo → stored as pre-order
- Agent sends reminder 2 hours before event

**User story:**
> As a customer, I want to scan a QR code, pre-order my combo, and get a reminder before the match.

---

### 8. QR Code Generation

**What it does:**
- Generates unique QR codes for each promotion
- Links to customer landing page
- Tracks scans and conversions

**How it works:**
- Agent generates unique promo ID
- Creates QR code: `eventboost.app/promo/{promoId}`
- Stores QR code in MongoDB `promotions` collection
- Business can print/display QR code

**User story:**
> As a bar owner, I want a QR code I can print and display so customers can easily access the promotion.

---

### 9. ROI Tracking

**What it does:**
- Tracks revenue generated per event
- Compares prediction vs actual
- Shows improvement over time

**How it works:**
- Agent stores predictions in MongoDB `predictions` collection
- After event, compares with actual sales
- Calculates ROI: (Event revenue - Normal revenue) / Normal revenue
- Shows improvement graph over time

**User story:**
> As a bar owner, I want to see exactly how much revenue each event generated so I know if EventBoost is working.

**Example ROI:**
- "Argentina vs Brazil: $2,400 extra revenue (30% increase)"
- "10 events tracked: Average 25% revenue increase"

---

### 10. Agent Chat

**What it does:**
- Allows business owners to ask questions in natural language
- Agent provides recommendations, answers, and takes actions
- Shows agent's reasoning and traces

**How it works:**
- User types question → Backend → Gemini → Response
- Agent can query MongoDB, generate promos, make recommendations
- Responses include actionable suggestions
- Phoenix traces show agent's reasoning

**User story:**
> As a bar owner, I want to ask "What should I do for the World Cup match?" and get actionable recommendations.

**Example questions:**
- "What should I do for the Germany game?"
- "How many customers should I expect?"
- "Create a promo for the concert next week"
- "What's my ROI for the last 5 events?"

---

## Optional Features (Post-Hackathon)

### POS Integration
- Connect to Square, Toast, or other POS systems
- Auto-import inventory and sales data
- Real-time stock tracking

### Multi-Location Support
- Manage multiple business locations
- Centralized dashboard
- Location-specific recommendations

### Event Partnerships
- Connect with event organizers
- Official watch party listings
- Cross-promotion opportunities

### Customer Loyalty
- Track repeat customers
- Personalized recommendations
- Loyalty rewards

### Analytics Dashboard
- Advanced analytics and reporting
- Export data to CSV/PDF
- Custom date ranges

---

## Feature Priority Matrix

| Feature | Priority | Complexity | Impact |
|---------|----------|------------|--------|
| Event Discovery | P0 | Low | High |
| Promotion Generator | P0 | Medium | High |
| Demand Prediction | P0 | Medium | High |
| QR Code Generation | P0 | Low | High |
| Customer Landing Page | P0 | Medium | High |
| Inventory Management | P1 | Medium | Medium |
| Staff Scheduling | P1 | Medium | Medium |
| Marketing Automation | P1 | Medium | Medium |
| ROI Tracking | P1 | Low | High |
| Agent Chat | P2 | Medium | Medium |
| Phoenix Integration | P2 | High | Medium |

**P0:** Must have for hackathon
**P1:** Should have for hackathon
**P2:** Nice to have for hackathon
