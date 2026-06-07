# Arize Phoenix Integration

## Overview

Arize Phoenix provides observability and self-improvement capabilities for the EventBoost agent. By tracing every agent decision and evaluating outcomes, the agent can learn from each event and improve over time.

**Note:** Arize integration is optional for the hackathon. The primary track is MongoDB. Arize adds a "self-improvement" layer that can be highlighted in the demo.

---

## Why Arize?

### Without Arize
- Agent makes predictions, but doesn't know if they were accurate
- No visibility into agent's reasoning
- No way to improve over time
- "Black box" agent

### With Arize
- Every prediction is traced and compared to actual outcome
- Agent's reasoning is visible in Phoenix dashboard
- Agent learns from mistakes and adjusts
- "Glass box" agent with measurable improvement

---

## How It Works

### 1. Tracing (OpenInference)

Every agent action gets traced:
- Event queries
- Demand predictions
- Inventory recommendations
- Staff scheduling decisions
- Promotion generation
- Customer signups

**Setup:**
```python
from phoenix.otel import register

def setup_tracing():
    register(
        project_name="eventboost",
        auto_instrument=True
    )
```

**What gets traced:**
- LLM calls (Gemini reasoning)
- Tool calls (MongoDB queries, predictions)
- Agent decisions (what to recommend)
- Timestamps and latency

---

### 2. Evaluation (LLM-as-Judge)

After each event, the agent evaluates its own performance:

**Prediction Accuracy:**
```python
# Compare predicted vs actual attendance
accuracy = 1 - abs(predicted - actual) / actual

# Example:
# Predicted: 135 customers
# Actual: 130 customers
# Accuracy: 96.3%
```

**Inventory Effectiveness:**
```python
# Did we have enough stock?
stockout = actual_demand > available_stock
waste = available_stock - actual_demand > threshold

# Example:
# Predicted need: 250 beer bottles
# Ordered: 250 beer bottles
# Actual demand: 230 beer bottles
# Result: No stockout, 20 bottles leftover (acceptable)
```

**Promotion Performance:**
```python
# Did the promo convert?
conversion_rate = signups / qr_scans

# Example:
# QR scans: 45
# Signups: 30
# Conversion: 66.7%
```

---

### 3. Self-Improvement (Phoenix MCP)

The agent reads its own traces via Phoenix MCP to improve:

**Query own traces:**
```
Agent: "In Phoenix, show me the last 3 predictions for Brazil games"
Phoenix MCP: Returns traces with predicted vs actual attendance
```

**Identify patterns:**
```
Agent: "Analyze prediction accuracy for knockout games"
Phoenix MCP: Returns accuracy metrics, identifies underestimation pattern
```

**Adjust model:**
```
Agent: "I've been underestimating Brazil games by 10%. Adjusting multiplier from 1.0 to 1.1"
```

---

## Implementation

### Step 1: Install Dependencies

```bash
pip install arize-phoenix[otel]
pip install openinference-instrumentation-google-adk
```

### Step 2: Configure Tracing

```python
# instrumentation.py
from phoenix.otel import register

def setup_tracing():
    register(
        project_name="eventboost",
        auto_instrument=True,
        endpoint="https://app.phoenix.arize.com/api/v1/phoenix/trace/v1/traces/grpc"
    )
```

### Step 3: Add to Agent

```python
# main.py
from instrumentation import setup_tracing
setup_tracing()

# Rest of agent code...
```

### Step 4: Configure Phoenix MCP

```json
// .gemini/settings.json
{
  "mcpServers": {
    "phoenix": {
      "command": "npx",
      "args": [
        "-y",
        "@arizeai/phoenix-mcp@latest",
        "--baseUrl", "https://app.phoenix.arize.com/s/your-space",
        "--apiKey", "px_live_your_api_key"
      ]
    }
  }
}
```

---

## Evaluation Pipeline

### Post-Event Evaluation

After each event, run evaluation:

```python
def evaluate_event(prediction_id: str, actual_attendance: int, actual_revenue: float):
    """Evaluate prediction accuracy and store results."""
    
    # Get prediction from MongoDB
    prediction = get_prediction(prediction_id)
    
    # Calculate accuracy
    attendance_accuracy = 1 - abs(prediction['predicted_attendance']['expected'] - actual_attendance) / actual_attendance
    revenue_accuracy = 1 - abs(prediction['predicted_revenue']['expected'] - actual_revenue) / actual_revenue
    
    # Store evaluation
    store_evaluation({
        'prediction_id': prediction_id,
        'attendance_accuracy': attendance_accuracy,
        'revenue_accuracy': revenue_accuracy,
        'insights': generate_insights(prediction, actual_attendance, actual_revenue)
    })
    
    # Update prediction model
    update_prediction_model(attendance_accuracy, revenue_accuracy)
```

### LLM-as-Judge Evaluation

Use Gemini to evaluate agent's reasoning:

```python
def evaluate_reasoning(trace_id: str):
    """Use LLM to evaluate agent's reasoning quality."""
    
    # Get trace from Phoenix
    trace = get_trace(trace_id)
    
    # Evaluate with Gemini
    evaluation = gemini.evaluate(
        prompt="""Evaluate this agent's reasoning:
        
        Trace: {trace}
        
        Criteria:
        1. Did the agent consider all relevant factors?
        2. Was the reasoning logical?
        3. Were the recommendations appropriate?
        4. Was the confidence level justified?
        
        Score 1-10 and explain.""",
        trace=trace
    )
    
    return evaluation
```

---

## Self-Improvement Loop

### Continuous Improvement Cycle

```
┌─────────────────────────────────────────────────────────────┐
│                    Self-Improvement Loop                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. OBSERVE                                                 │
│     └─► Collect trace data from agent interactions          │
│                                                             │
│  2. EVALUATE                                                │
│     └─► Run LLM-as-Judge on traces                         │
│     └─► Compare prediction vs actual                        │
│                                                             │
│  3. ANALYZE                                                 │
│     └─► Identify patterns in failures                       │
│     └─► Hypothesize root causes                            │
│                                                             │
│  4. IMPROVE                                                 │
│     └─► Adjust prediction model                            │
│     └─► Update recommendation logic                         │
│     └─► Refine prompts                                     │
│                                                             │
│  5. TEST                                                    │
│     └─► Run experiments with new logic                      │
│     └─► Compare performance                                │
│                                                             │
│  6. DEPLOY                                                  │
│     └─► Push improved model to production                   │
│     └─► Monitor for regressions                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Example Improvement

**Event 1: Argentina vs Serbia (Group Stage)**
- Predicted: 100 customers
- Actual: 80 customers
- Accuracy: 80%
- Insight: Overestimated by 25%

**Event 2: Brazil vs Switzerland (Group Stage)**
- Predicted: 90 customers
- Actual: 75 customers
- Accuracy: 83%
- Insight: Still overestimating group stage games

**Event 3: Argentina vs Brazil (Knockout)**
- Predicted: 120 customers (adjusted multiplier: 0.85)
- Actual: 130 customers
- Accuracy: 92%
- Insight: Adjustment helped, but knockout games need higher multiplier

**Event 4: France vs Germany (Knockout)**
- Predicted: 140 customers (adjusted multiplier: 1.1)
- Actual: 145 customers
- Accuracy: 97%
- Insight: Model now accurate for knockout games

---

## Phoenix Dashboard

### What to Show in Demo

1. **Traces View**
   - Show agent's reasoning for a prediction
   - Highlight tool calls (MongoDB queries)
   - Show timestamps and latency

2. **Evaluation Results**
   - Show prediction vs actual comparison
   - Show accuracy metrics over time
   - Show improvement graph

3. **Self-Improvement Evidence**
   - Show multiplier adjustments
   - Show before/after accuracy comparison
   - Show "agent learned" insights

---

## Demo Integration

### In the 3-Minute Demo Video

**[2:10-2:40] Self-Improvement Section (30 seconds)**

1. Open Phoenix dashboard
2. Show traces: "Predicted 150 customers for Brazil vs Serbia"
3. Show actual: "Actual: 130 customers"
4. Show adjustment: "Agent learned: Brazil group games = 1.2x multiplier"
5. Show next prediction: "Argentina vs Brazil knockout = 1.5x multiplier"
6. Show improvement graph: "Prediction accuracy improved from 70% to 92% over 10 events"

**Text overlay:** "AI that learns from every event. Gets better every time."

---

## Optional: Advanced Features

### Drift Detection
- Monitor prediction accuracy over time
- Alert if accuracy drops below threshold
- Auto-trigger retraining

### A/B Testing
- Test different prediction models
- Compare performance
- Deploy best performer

### Anomaly Detection
- Detect unusual patterns in signups
- Alert on unexpected demand spikes
- Recommend adjustments

---

## Resources

- [Phoenix Documentation](https://arize.com/docs/phoenix)
- [Phoenix MCP Server](https://arize.com/docs/phoenix/integrations/phoenix-mcp-server)
- [OpenInference Instrumentation](https://github.com/Arize-ai/openinference)
- [Phoenix Cloud](https://app.phoenix.arize.com)
- [Gemini Hackathon Starter](https://github.com/Arize-ai/gemini-hackathon)
