# MongoDB Schema

## Collections Overview

```
event-boost/
├── businesses      # Business profiles, menu, inventory, staff
├── events          # Event schedule, venue, expected attendance
├── customers       # Customer signups, pre-orders, contact info
├── promotions      # QR codes, promo codes, deals
├── predictions     # Demand forecasts, actual outcomes
└── recommendations # Agent's suggestions, user feedback
```

---

## 1. `businesses` Collection

```json
{
  "_id": ObjectId,
  "name": "The Sports Bar",
  "owner": "John Doe",
  "email": "john@sportsbar.com",
  "phone": "+62812345678",
  "location": {
    "address": "Jl. Sudirman No. 123",
    "city": "Jakarta",
    "country": "Indonesia",
    "coordinates": {
      "lat": -6.2088,
      "lng": 106.8456
    }
  },
  "capacity": 150,
  "hours": {
    "monday": { "open": "11:00", "close": "23:00" },
    "tuesday": { "open": "11:00", "close": "23:00" },
    "wednesday": { "open": "11:00", "close": "23:00" },
    "thursday": { "open": "11:00", "close": "23:00" },
    "friday": { "open": "11:00", "close": "01:00" },
    "saturday": { "open": "11:00", "close": "01:00" },
    "sunday": { "open": "11:00", "close": "23:00" }
  },
  "menu": [
    {
      "id": "item_001",
      "name": "Chicken Wings (1 kg)",
      "category": "food",
      "price": 50000,
      "currency": "IDR",
      "cost": 25000
    },
    {
      "id": "item_002",
      "name": "Bintang Beer (bottle)",
      "category": "drink",
      "price": 35000,
      "currency": "IDR",
      "cost": 15000
    },
    {
      "id": "item_003",
      "name": "Nachos",
      "category": "food",
      "price": 40000,
      "currency": "IDR",
      "cost": 18000
    }
  ],
  "inventory": [
    { "itemId": "item_001", "current": 20, "unit": "kg" },
    { "itemId": "item_002", "current": 50, "unit": "bottles" },
    { "itemId": "item_003", "current": 30, "unit": "portions" }
  ],
  "staff": [
    {
      "id": "staff_001",
      "name": "Alice",
      "role": "bartender",
      "available_days": ["monday", "tuesday", "wednesday", "thursday", "friday", "saturday"],
      "hourly_rate": 25000
    },
    {
      "id": "staff_002",
      "name": "Bob",
      "role": "cook",
      "available_days": ["monday", "wednesday", "friday", "saturday", "sunday"],
      "hourly_rate": 30000
    }
  ],
  "created_at": ISODate,
  "updated_at": ISODate
}
```

**Indexes:**
- `location.coordinates` — 2dsphere (geospatial queries)
- `location.city` — text search
- `name` — text search

---

## 2. `events` Collection

```json
{
  "_id": ObjectId,
  "name": "Argentina vs Brazil",
  "type": "sports",
  "sport": "football",
  "tournament": "FIFA World Cup 2026",
  "stage": "knockout",
  "date": ISODate("2026-06-15T20:00:00Z"),
  "timezone": "Asia/Jakarta",
  "venue": {
    "name": "Lusail Stadium",
    "city": "Lusail",
    "country": "Qatar",
    "capacity": 80000
  },
  "teams": [
    { "name": "Argentina", "code": "ARG" },
    { "name": "Brazil", "code": "BRA" }
  ],
  "expected_attendance": 80000,
  "popularity_score": 95,
  "importance_multiplier": 1.5,
  "broadcast": ["TV", "streaming"],
  "tags": ["football", "world-cup", "knockout", "rivalry"],
  "created_at": ISODate,
  "updated_at": ISODate
}
```

**Indexes:**
- `date` — ascending (sort by date)
- `type` — filter by event type
- `teams.name` — search by team
- `tags` — search by tags
- `venue.city` — filter by location

---

## 3. `customers` Collection

```json
{
  "_id": ObjectId,
  "business_id": ObjectId,
  "event_id": ObjectId,
  "promotion_id": ObjectId,
  "name": "Jane Smith",
  "phone": "+62812345679",
  "email": "jane@email.com",
  "pre_order": {
    "items": [
      { "itemId": "item_001", "quantity": 2, "price": 50000 },
      { "itemId": "item_002", "quantity": 3, "price": 35000 }
    ],
    "total": 205000,
    "currency": "IDR",
    "paid": true,
    "payment_method": "online"
  },
  "reminder_sent": false,
  "reminder_time": ISODate("2026-06-15T18:00:00Z"),
  "attended": null,
  "rating": null,
  "feedback": null,
  "created_at": ISODate,
  "updated_at": ISODate
}
```

**Indexes:**
- `business_id` — filter by business
- `event_id` — filter by event
- `phone` — lookup by phone
- `email` — lookup by email
- `reminder_sent` + `reminder_time` — find customers to remind

---

## 4. `promotions` Collection

```json
{
  "_id": ObjectId,
  "business_id": ObjectId,
  "event_id": ObjectId,
  "title": "Watch Argentina vs Brazil LIVE!",
  "description": "20% off wings + free first beer",
  "discount": {
    "type": "percentage",
    "value": 20,
    "applies_to": ["item_001"]
  },
  "free_items": [
    { "itemId": "item_002", "quantity": 1, "condition": "first_order" }
  ],
  "combo": {
    "name": "Match Day Combo",
    "items": ["item_001", "item_002"],
    "original_price": 85000,
    "combo_price": 65000,
    "currency": "IDR"
  },
  "qr_code": {
    "url": "https://eventboost.app/promo/abc123",
    "image_url": "https://storage.googleapis.com/eventboost/qr/abc123.png",
    "scans": 45,
    "conversions": 30
  },
  "promo_code": "MATCHDAY20",
  "valid_from": ISODate("2026-06-14T00:00:00Z"),
  "valid_until": ISODate("2026-06-15T23:59:00Z"),
  "max_redemptions": 100,
  "current_redemptions": 30,
  "status": "active",
  "social_media_posts": [
    {
      "platform": "instagram",
      "post_id": "ig_123456",
      "scheduled_at": ISODate("2026-06-15T16:00:00Z"),
      "posted_at": ISODate("2026-06-15T16:00:00Z"),
      "engagement": { "likes": 45, "comments": 12, "shares": 8 }
    }
  ],
  "created_at": ISODate,
  "updated_at": ISODate
}
```

**Indexes:**
- `business_id` — filter by business
- `event_id` — filter by event
- `promo_code` — lookup by code
- `status` — filter active/inactive
- `valid_until` — find expired promos

---

## 5. `predictions` Collection

```json
{
  "_id": ObjectId,
  "business_id": ObjectId,
  "event_id": ObjectId,
  "predicted_attendance": {
    "min": 120,
    "max": 150,
    "expected": 135,
    "confidence": 0.85
  },
  "predicted_revenue": {
    "min": 1800000,
    "max": 2250000,
    "expected": 2025000,
    "currency": "IDR"
  },
  "factors": {
    "event_type": "knockout",
    "team_popularity": 0.95,
    "historical_data": 0.80,
    "current_signups": 45,
    "weather": "clear",
    "day_of_week": "sunday"
  },
  "inventory_recommendations": [
    { "itemId": "item_001", "current": 20, "recommended": 35, "unit": "kg" },
    { "itemId": "item_002", "current": 50, "recommended": 250, "unit": "bottles" }
  ],
  "staff_recommendations": [
    { "role": "bartender", "current": 2, "recommended": 4 },
    { "role": "cook", "current": 1, "recommended": 2 }
  ],
  "actual_attendance": null,
  "actual_revenue": null,
  "accuracy": null,
  "created_at": ISODate,
  "updated_at": ISODate
}
```

**Indexes:**
- `business_id` — filter by business
- `event_id` — filter by event
- `accuracy` — find predictions to improve
- `created_at` — sort by date

---

## 6. `recommendations` Collection

```json
{
  "_id": ObjectId,
  "business_id": ObjectId,
  "event_id": ObjectId,
  "prediction_id": ObjectId,
  "type": "inventory",
  "action": "order",
  "items": [
    {
      "itemId": "item_002",
      "name": "Bintang Beer (bottle)",
      "current": 50,
      "recommended": 250,
      "quantity_to_order": 200,
      "estimated_cost": 3000000,
      "currency": "IDR"
    }
  ],
  "total_estimated_cost": 3000000,
  "status": "pending",
  "approved_by": null,
  "approved_at": null,
  "executed_at": null,
  "feedback": null,
  "created_at": ISODate,
  "updated_at": ISODate
}
```

**Indexes:**
- `business_id` — filter by business
- `event_id` — filter by event
- `type` — filter by recommendation type
- `status` — find pending/approved/executed

---

## Vector Search Indexes

### Event Matching (Atlas Vector Search)

```json
{
  "name": "event_vector_index",
  "definition": {
    "fields": [
      {
        "type": "vector",
        "path": "embedding",
        "numDimensions": 768,
        "similarity": "cosine"
      },
      {
        "type": "filter",
        "path": "type"
      },
      {
        "type": "filter",
        "path": "venue.city"
      }
    ]
  }
}
```

**Use case:** "Find events similar to Argentina vs Brazil" → semantic search over event descriptions

### Business Matching (Atlas Vector Search)

```json
{
  "name": "business_vector_index",
  "definition": {
    "fields": [
      {
        "type": "vector",
        "path": "embedding",
        "numDimensions": 768,
        "similarity": "cosine"
      },
      {
        "type": "filter",
        "path": "location.city"
      }
    ]
  }
}
```

**Use case:** "Find sports bars near me" → semantic search over business descriptions

---

## Atlas Search Indexes

### Full-Text Search on Events

```json
{
  "name": "event_search_index",
  "definition": {
    "mappings": {
      "dynamic": false,
      "fields": {
        "name": { "type": "text", "analyzer": "standard" },
        "tournament": { "type": "text", "analyzer": "standard" },
        "tags": { "type": "string", "analyzer": "standard" },
        "teams.name": { "type": "text", "analyzer": "standard" }
      }
    }
  }
}
```

**Use case:** "World Cup" → search across event names, tournaments, tags

---

## Sample Data

### Sample Business
```json
{
  "name": "The Sports Bar Jakarta",
  "owner": "Budi Santoso",
  "location": {
    "address": "Jl. Sudirman No. 123",
    "city": "Jakarta",
    "country": "Indonesia",
    "coordinates": { "lat": -6.2088, "lng": 106.8456 }
  },
  "capacity": 100,
  "menu": [
    { "id": "wings", "name": "Chicken Wings (1 kg)", "price": 50000 },
    { "id": "beer", "name": "Bintang Beer", "price": 35000 },
    { "id": "nachos", "name": "Nachos", "price": 40000 }
  ]
}
```

### Sample Event
```json
{
  "name": "Argentina vs Brazil",
  "type": "sports",
  "tournament": "FIFA World Cup 2026",
  "stage": "knockout",
  "date": "2026-06-15T20:00:00+07:00",
  "teams": ["Argentina", "Brazil"],
  "popularity_score": 95,
  "importance_multiplier": 1.5
}
```

### Sample Promotion
```json
{
  "title": "Watch Argentina vs Brazil LIVE!",
  "description": "20% off wings + free first beer",
  "discount": { "type": "percentage", "value": 20 },
  "combo": {
    "name": "Match Day Combo",
    "items": ["wings", "beer"],
    "combo_price": 65000
  }
}
```
