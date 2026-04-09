# Comic-Con Parking System – Database Design

## Overview

This project defines the database schema for a scalable parking management system designed for high-traffic events like Comic-Con. It supports vehicle categorization, parking allocation, reservations, sessions, ticketing, payments, and dynamic pricing.

---

## Core Concepts

### Unified Categories Table
A single `categories` table is used to manage:
- Vehicle types (car, bike, etc.)
- Parking spot types (compact, large, etc.)
- Access levels (VIP, staff, general)

### Reservation vs Usage
- `spot_reservations` → future bookings
- `parking_sessions` → actual parking usage

### Flexible Pricing
- Pricing is handled via `rate_cards` based on:
  - Vehicle type
  - Spot type
  - Access level (optional)

---

## Tables

### categories

Stores all category types.

| Column        | Type        | Description                          |
|--------------|------------|--------------------------------------|
| id           | int        | Primary key                          |
| name         | varchar    | Category name                        |
| category_type| varchar    | vehicle \| spot \| access            |

Constraint:
- Unique `(name, category_type)`

---

### vehicles

Stores vehicle details.

| Column          | Type        | Description             |
|-----------------|------------|-------------------------|
| id              | int        | Primary key             |
| license_plate   | varchar    | Unique vehicle number   |
| category_id     | int        | Vehicle category        |
| created_at      | timestamp  | Creation time           |
| updated_at      | timestamp  | Last update             |

---

### vehicle_access

Defines access level per vehicle.

| Column       | Type | Description              |
|--------------|------|--------------------------|
| id           | int  | Primary key              |
| vehicle_id   | int  | Unique vehicle reference |
| category_id  | int  | Access category          |

---

### parking_zones

Represents parking areas.

| Column | Type | Description     |
|--------|------|-----------------|
| id     | int  | Primary key     |
| name   | varchar | Zone name   |
| level  | int  | Floor/level     |

---

### parking_spots

Represents individual parking spots.

| Column              | Type     | Description                |
|---------------------|----------|----------------------------|
| id                  | int      | Primary key                |
| zone_id             | int      | Zone reference             |
| spot_number         | varchar  | Unique spot number         |
| category_id         | int      | Spot category              |
| vehicle_category_id | int      | Allowed vehicle type       |
| is_active           | boolean  | Availability flag          |

---

### spot_reservations

Handles advance reservations.

| Column      | Type      | Description                         |
|-------------|----------|-------------------------------------|
| id          | int      | Primary key                         |
| vehicle_id  | int      | Vehicle reference                   |
| spot_id     | int      | Spot reference                      |
| start_time  | timestamp| Reservation start                   |
| end_time    | timestamp| Reservation end                     |
| status      | varchar  | reserved \| used \| cancelled \| expired |
| created_at  | timestamp| Creation time                       |

---

### parking_sessions

Tracks actual parking usage.

| Column      | Type      | Description              |
|-------------|----------|--------------------------|
| id          | int      | Primary key              |
| vehicle_id  | int      | Vehicle                  |
| spot_id     | int      | Spot                     |
| entry_time  | timestamp| Entry time               |
| exit_time   | timestamp| Exit time                |
| status      | varchar  | active \| completed      |
| created_at  | timestamp| Creation time            |

---

### parking_tickets

Generated per parking session.

| Column        | Type      | Description              |
|---------------|----------|--------------------------|
| id            | int      | Primary key              |
| session_id    | int      | Unique session reference |
| ticket_number | varchar  | Unique ticket number     |
| issued_at     | timestamp| Issue time               |

---

### payments

Handles payments for parking sessions.

| Column          | Type      | Description                 |
|-----------------|----------|-----------------------------|
| id              | int      | Primary key                 |
| session_id      | int      | Session reference           |
| amount          | decimal  | Payment amount              |
| payment_status  | varchar  | pending \| paid \| failed   |
| payment_method  | varchar  | cash \| upi \| card         |
| paid_at         | timestamp| Payment time                |

---

### rate_cards

Defines pricing rules.

| Column              | Type      | Description               |
|---------------------|----------|---------------------------|
| id                  | int      | Primary key               |
| vehicle_category_id | int      | Vehicle type              |
| spot_category_id    | int      | Spot type                 |
| access_category_id  | int      | Optional access type      |
| base_rate           | decimal  | Base charge               |
| hourly_rate         | decimal  | Hourly charge             |
| free_minutes        | int      | Free duration             |
| max_daily_cap       | decimal  | Daily max limit           |
| created_at          | timestamp| Creation time             |

---

## Relationships

### Category Mapping
- vehicles.category_id → categories.id
- vehicle_access.category_id → categories.id
- parking_spots.category_id → categories.id
- parking_spots.vehicle_category_id → categories.id
- rate_cards.*_category_id → categories.id

### Vehicle Flow
- vehicle_access.vehicle_id → vehicles.id
- spot_reservations.vehicle_id → vehicles.id
- parking_sessions.vehicle_id → vehicles.id

### Parking Flow
- parking_spots.zone_id → parking_zones.id
- spot_reservations.spot_id → parking_spots.id
- parking_sessions.spot_id → parking_spots.id

### Session Flow
- parking_tickets.session_id → parking_sessions.id
- payments.session_id → parking_sessions.id

---

## Design Highlights

- Single categories table for flexibility
- Separation of reservation and actual usage
- One-to-one constraints where required
- Extensible pricing system via rate_cards

---

## Conclusion

This schema provides a flexible and scalable foundation for managing complex parking systems by unifying categories, cleanly separating reservations from real usage, and supporting dynamic pricing, making it well-suited for high-traffic, real-world scenarios.

