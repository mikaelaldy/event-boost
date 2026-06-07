# Agent Tools

## Tool Overview

The EventBoost agent uses tools to interact with MongoDB, generate promotions, predict demand, and manage business operations. All tools are implemented as ADK tools that the Gemini agent can call.

---

## Event Tools

### `query_events`

**Purpose:** Find upcoming events by location, date, and type.

**Parameters:**
```python
{
  "city": str,           # City name (e.g., "Jakarta")
  "date_from": str,      # Start date (ISO format)
  "date_to": str,        # End date (ISO format)
  "event_type": str,     # "sports", "concert", "festival", "local"
  "limit": int           # Max results (default: 10)
}
```

**Returns:**
```python
{
  "events": [
    {
      "id": str,
      "name": str,
      "type": str,
      "date": str,
      "venue": str,
      "teams": [str],
      "popularity_score": float,
      "importance_multiplier": float
    }
  ],
  "total": int
}
```

**Example usage:**
```
User: "What events are coming up in Jakarta this weekend?"
Agent: calls query_events(city="Jakarta", date_from="2026-06-14", date_to="2026-06-15")
```

---

### `get_event_details`

**Purpose:** Get detailed information about a specific event.

**Parameters:**
```python
{
  "event_id": str  # Event ID
}
```

**Returns:**
```python
{
  "id": str,
  "name": str,
  "type": str,
  "tournament": str,
  "stage": str,
  "date": str,
  "timezone": str,
  "venue": {
    "name": str,
    "city": str,
    "capacity": int
  },
  "teams": [str],
  "expected_attendance": int,
  "popularity_score": float,
  "importance_multiplier": float,
  "tags": [str]
}
```

---

## Business Tools

### `get_business_profile`

**Purpose:** Get business profile including menu, inventory, and staff.

**Parameters:**
```python
{
  "business_id": str  # Business ID
}
```

**Returns:**
```python
{
  "id": str,
  "name": str,
  "location": dict,
  "capacity": int,
  "hours": dict,
  "menu": [dict],
  "inventory": [dict],
  "staff": [dict]
}
```

---

### `update_inventory`

**Purpose:** Update inventory levels for a business.

**Parameters:**
```python
{
  "business_id": str,
  "items": [
    {
      "item_id": str,
      "quantity": int,
      "operation": str  # "set", "add", "subtract"
    }
  ]
}
```

**Returns:**
```python
{
  "success": bool,
  "updated_items": [dict],
  "message": str
}
```

---

### `get_staff_schedule`

**Purpose:** Get current staff schedule for a business.

**Parameters:**
```python
{
  "business_id": str,
  "date": str  # Date to check schedule
}
```

**Returns:**
```python
{
  "date": str,
  "staff": [
    {
      "id": str,
      "name": str,
      "role": str,
      "shift_start": str,
      "shift_end": str,
      "hourly_rate": float
    }
  ]
}
```

---

### `schedule_staff`

**Purpose:** Add or modify staff schedule for a specific date.

**Parameters:**
```python
{
  "business_id": str,
  "date": str,
  "staff_changes": [
    {
      "staff_id": str,
      "shift_start": str,
      "shift_end": str,
      "action": str  # "add", "remove", "modify"
    }
  ]
}
```

**Returns:**
```python
{
  "success": bool,
  "updated_schedule": [dict],
  "message": str
}
```

---

## Customer Tools

### `add_customer_signup`

**Purpose:** Register a customer for an event promotion.

**Parameters:**
```python
{
  "business_id": str,
  "event_id": str,
  "promotion_id": str,
  "name": str,
  "phone": str,
  "email": str,  # optional
  "pre_order": {
    "items": [
      { "item_id": str, "quantity": int }
    ],
    "payment_method": str  # "online", "at_venue"
  }
}
```

**Returns:**
```python
{
  "success": bool,
  "customer_id": str,
  "pre_order_total": float,
  "currency": str,
  "confirmation_message": str
}
```

---

### `get_pre_orders`

**Purpose:** Get all pre-orders for an event at a business.

**Parameters:**
```python
{
  "business_id": str,
  "event_id": str
}
```

**Returns:**
```python
{
  "event_id": str,
  "business_id": str,
  "total_signups": int,
  "total_pre_orders": int,
  "total_revenue": float,
  "currency": str,
  "items_summary": [
    { "item_id": str, "name": str, "total_quantity": int }
  ]
}
```

---

### `send_reminder`

**Purpose:** Send reminder to customers about upcoming event.

**Parameters:**
```python
{
  "business_id": str,
  "event_id": str,
  "message": str,  # custom message (optional)
  "hours_before": int  # how many hours before event (default: 2)
}
```

**Returns:**
```python
{
  "success": bool,
  "reminders_sent": int,
  "message": str
}
```

---

## Promotion Tools

### `create_promotion`

**Purpose:** Create a new promotion for an event.

**Parameters:**
```python
{
  "business_id": str,
  "event_id": str,
  "title": str,
  "description": str,
  "discount": {
    "type": str,  # "percentage", "fixed"
    "value": float,
    "applies_to": [str]  # item IDs
  },
  "free_items": [
    { "item_id": str, "quantity": int, "condition": str }
  ],
  "combo": {
    "name": str,
    "items": [str],
    "combo_price": float
  }
}
```

**Returns:**
```python
{
  "success": bool,
  "promotion_id": str,
  "qr_code_url": str,
  "promo_code": str,
  "landing_page_url": str,
  "message": str
}
```

---

### `generate_qr_code`

**Purpose:** Generate QR code for a promotion.

**Parameters:**
```python
{
  "promotion_id": str,
  "size": int,  # pixels (default: 300)
  "format": str  # "png", "svg" (default: "png")
}
```

**Returns:**
```python
{
  "qr_code_url": str,
  "qr_code_image": str,  # base64 encoded image
  "landing_page_url": str
}
```

---

### `create_social_media_post`

**Purpose:** Create social media post for an event promotion.

**Parameters:**
```python
{
  "business_id": str,
  "event_id": str,
  "promotion_id": str,
  "platforms": [str],  # "instagram", "facebook", "twitter"
  "custom_message": str  # optional override
}
```

**Returns:**
```python
{
  "success": bool,
  "posts": [
    {
      "platform": str,
      "content": str,
      "scheduled_at": str,
      "post_id": str
    }
  ],
  "message": str
}
```

---

## Prediction Tools

### `predict_demand`

**Purpose:** Predict demand for an event at a business.

**Parameters:**
```python
{
  "business_id": str,
  "event_id": str
}
```

**Returns:**
```python
{
  "predicted_attendance": {
    "min": int,
    "max": int,
    "expected": int,
    "confidence": float
  },
  "predicted_revenue": {
    "min": float,
    "max": float,
    "expected": float,
    "currency": str
  },
  "factors": {
    "event_type": str,
    "team_popularity": float,
    "historical_data": float,
    "current_signups": int,
    "weather": str,
    "day_of_week": str
  },
  "inventory_recommendations": [dict],
  "staff_recommendations": [dict]
}
```

---

### `store_prediction`

**Purpose:** Store a prediction for later comparison with actual outcome.

**Parameters:**
```python
{
  "business_id": str,
  "event_id": str,
  "predicted_attendance": dict,
  "predicted_revenue": dict,
  "factors": dict,
  "inventory_recommendations": [dict],
  "staff_recommendations": [dict]
}
```

**Returns:**
```python
{
  "success": bool,
  "prediction_id": str,
  "message": str
}
```

---

### `compare_outcome`

**Purpose:** Compare prediction with actual outcome after event.

**Parameters:**
```python
{
  "prediction_id": str,
  "actual_attendance": int,
  "actual_revenue": float,
  "currency": str
}
```

**Returns:**
```python
{
  "success": bool,
  "accuracy": float,
  "attendance_error": float,
  "revenue_error": float,
  "insights": [str],
  "adjustments": [str]
}
```

---

## MongoDB MCP Tools

The agent also uses MongoDB MCP tools directly for advanced queries:

### `mongodb_query`

**Purpose:** Query MongoDB collection with filter.

**Parameters:**
```python
{
  "collection": str,
  "filter": dict,
  "sort": dict,  # optional
  "limit": int   # optional
}
```

---

### `mongodb_insert`

**Purpose:** Insert document into MongoDB collection.

**Parameters:**
```python
{
  "collection": str,
  "document": dict
}
```

---

### `mongodb_update`

**Purpose:** Update documents in MongoDB collection.

**Parameters:**
```python
{
  "collection": str,
  "filter": dict,
  "update": dict,
  "upsert": bool  # optional, default: false
}
```

---

### `mongodb_aggregate`

**Purpose:** Run aggregation pipeline on MongoDB collection.

**Parameters:**
```python
{
  "collection": str,
  "pipeline": [dict]
}
```

---

## Tool Implementation Notes

### ADK Tool Structure

```python
from google.adk.tools import FunctionTool

def query_events(city: str, date_from: str, date_to: str, event_type: str = None, limit: int = 10) -> dict:
    """Find upcoming events by location, date, and type."""
    # Implementation using MongoDB MCP
    pass

# Register as ADK tool
query_events_tool = FunctionTool(func=query_events)
```

### Error Handling

All tools should:
1. Validate input parameters
2. Handle MongoDB connection errors
3. Return meaningful error messages
4. Log errors for debugging

### Rate Limiting

- MongoDB queries: Max 100 per minute
- QR code generation: Max 10 per minute
- Social media posts: Max 5 per hour
