# Technical Architecture

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      EventBoost Platform                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │   Frontend   │    │   Backend    │    │   Database   │  │
│  │  (Web App)   │◄──►│   (ADK)      │◄──►│  (MongoDB)   │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
│         │                   │                   │           │
│         │                   │                   │           │
│         ▼                   ▼                   ▼           │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │  Customer    │    │   Gemini 3   │    │   Phoenix    │  │
│  │  Landing     │    │  (Reasoning) │    │  (Tracing)   │  │
│  │  Page        │    └──────────────┘    └──────────────┘  │
│  └──────────────┘                                           │
└─────────────────────────────────────────────────────────────┘
```

## Component Breakdown

### 1. Frontend (Web App)

**Technology:** V0/Cursor vibecoding (React/Next.js or similar)

**Pages:**
- **Dashboard** — Upcoming events, recommendations, ROI
- **Event Manager** — Select event, create promo, generate QR
- **Customer Landing** — Mobile-friendly, event details, pre-order
- **Agent Chat** — Ask agent questions, see recommendations
- **Settings** — Business profile, menu, staff, location
- **Phoenix Viewer** — View traces, self-improvement metrics

**Responsibilities:**
- Render UI
- Call backend API
- Display agent recommendations
- Generate QR codes
- Show customer landing page

### 2. Backend (Agent)

**Technology:** Google ADK (Python) + Gemini 3

**Agent Structure:**
```python
# Root agent (orchestrator)
root_agent = Agent(
    model="gemini-3-pro",
    name="eventboost_agent",
    instruction="You are an AI assistant for local businesses...",
    tools=[
        # Event tools
        query_events,
        get_event_details,
        
        # Business tools
        get_business_profile,
        update_inventory,
        get_staff_schedule,
        schedule_staff,
        
        # Customer tools
        add_customer_signup,
        get_pre_orders,
        send_reminder,
        
        # Promotion tools
        create_promotion,
        generate_qr_code,
        create_social_media_post,
        
        # Prediction tools
        predict_demand,
        store_prediction,
        compare_outcome,
        
        # MongoDB MCP tools
        mongodb_query,
        mongodb_insert,
        mongodb_update,
    ]
)
```

**Agent Workflow:**
1. Business selects an event
2. Agent queries MongoDB for event details
3. Agent predicts demand based on historical data
4. Agent generates promotion (QR code, promo code)
5. Agent recommends inventory adjustments
6. Agent recommends staff scheduling
7. Agent creates social media post
8. Agent monitors pre-orders
9. Agent sends reminders to customers
10. Post-event: Agent compares prediction vs actual
11. Agent updates prediction model based on outcomes

### 3. Database (MongoDB)

**Technology:** MongoDB Atlas (free tier M0)

**Collections:**
- `businesses` — Business profiles, menu, inventory, staff
- `events` — Event schedule, venue, expected attendance
- `customers` — Customer signups, pre-orders, contact info
- `promotions` — QR codes, promo codes, deals
- `predictions` — Demand forecasts, actual outcomes
- `recommendations` — Agent's suggestions, user feedback

**Why MongoDB:**
- Flexible schema (different business types)
- Atlas Vector Search (semantic event matching)
- Atlas Search (full-text queries)
- MCP server (agent can query/update directly)
- Free tier (no credit card)

### 4. Reasoning (Gemini 3)

**Technology:** Gemini 3 via Vertex AI

**Used for:**
- Demand prediction (analyze historical data + event type)
- Promotion generation (create compelling copy)
- Inventory recommendations (calculate optimal stock levels)
- Staff scheduling (predict needed coverage)
- Self-improvement (analyze traces, adjust model)

### 5. Observability (Arize Phoenix)

**Technology:** Arize Phoenix (optional, for self-improvement)

**Used for:**
- Trace every agent decision
- Evaluate prediction accuracy
- Compare outcomes vs predictions
- Self-improve over time

**Integration:**
- OpenInference instrumentation (auto-trace ADK agent)
- Phoenix MCP (agent queries its own traces)
- LLM-as-Judge (evaluate prediction quality)

---

## Data Flow

### Event Selection Flow
```
User (Business) → Select Event → Backend → MongoDB (query events) → Return event details
```

### Promotion Creation Flow
```
Backend → Gemini (generate promo copy) → Backend → MongoDB (store promotion) → Generate QR code → Return to user
```

### Customer Signup Flow
```
Customer → Scan QR → Landing Page → Enter info → Backend → MongoDB (store signup) → Return confirmation
```

### Demand Prediction Flow
```
Backend → MongoDB (get pre-orders + historical data) → Gemini (predict demand) → MongoDB (store prediction) → Return to user
```

### Inventory Recommendation Flow
```
Backend → MongoDB (get current inventory + predicted demand) → Gemini (calculate needed stock) → Return recommendation
```

### Post-Event Analysis Flow
```
Backend → MongoDB (get prediction + actual sales) → Gemini (compare, analyze) → MongoDB (store outcome) → Update prediction model
```

---

## API Endpoints

### Business APIs
- `POST /api/business` — Create business profile
- `GET /api/business/:id` — Get business profile
- `PUT /api/business/:id` — Update business profile

### Event APIs
- `GET /api/events` — List upcoming events
- `GET /api/events/:id` — Get event details
- `POST /api/events/:id/promo` — Create promotion for event

### Customer APIs
- `POST /api/customers/signup` — Customer signup for event
- `GET /api/customers/:eventId` — Get signups for event
- `POST /api/customers/:id/remind` — Send reminder

### Agent APIs
- `POST /api/agent/chat` — Chat with agent
- `POST /api/agent/predict` — Get demand prediction
- `POST /api/agent/recommend` — Get recommendations

### QR Code APIs
- `GET /api/qr/:promoId` — Generate QR code
- `GET /api/landing/:promoId` — Customer landing page

---

## Deployment

### Local Development
```bash
# Backend
cd backend
uv sync
uv run uvicorn main:app --reload

# Frontend
cd frontend
npm install
npm run dev
```

### Production (Cloud Run)
```bash
# Build and deploy backend
gcloud run deploy eventboost-backend \
  --source ./backend \
  --region us-central1 \
  --allow-unauthenticated

# Build and deploy frontend
gcloud run deploy eventboost-frontend \
  --source ./frontend \
  --region us-central1 \
  --allow-unauthenticated
```

---

## Security Considerations

- MongoDB Atlas: Use IP whitelist + authentication
- API keys: Store in Google Secret Manager
- Customer data: Encrypt at rest, hash PII
- QR codes: Time-limited, single-use for sensitive promos
- Rate limiting: Prevent abuse of signup endpoint

---

## Scalability

### Current (Hackathon)
- MongoDB Atlas M0 (free tier, 512 MB)
- Cloud Run (auto-scaling, pay-per-use)
- Gemini 3 (API, pay-per-token)

### Production
- MongoDB Atlas M10+ (dedicated cluster)
- Cloud Run (min instances for cold start)
- Gemini 3 (batch prediction for cost optimization)
- CDN for static assets
- Redis for caching
