# Smart Elevator System – Database Design

## Overview

This project defines the database schema for a smart elevator management system designed for multi-building environments. It supports real-time elevator tracking, ride requests, assignment handling, access control, and detailed logging.

---

## Core Concepts

### Building-Based Structure
- Each building contains multiple floors and elevators.
- Elevators operate independently within a building.

### Smart Elevator Control
- Elevators track:
  - Current floor
  - Direction (up/down)
  - Load vs capacity
  - Operational status

### Ride Lifecycle
1. User creates a `ride_request`
2. System assigns an elevator (`ride_assignments`)
3. Elevator executes the ride
4. Events are logged (`ride_logs`)

### Access Control
- `elevator_floors` defines which floors an elevator can access.
- Supports restricted floors (VIP, maintenance, etc.)

---

## Tables

### buildings

Stores building information.

| Column     | Type        | Description        |
|------------|------------|--------------------|
| id         | serial     | Primary key        |
| name       | varchar    | Building name      |
| city       | varchar    | City               |
| district   | varchar    | District           |
| pincode    | numeric    | Postal code        |
| created_at | timestamp  | Creation time      |
| updated_at | timestamp  | Last update        |

---

### floors

Represents floors inside a building.

| Column       | Type     | Description        |
|--------------|----------|--------------------|
| id           | serial   | Primary key        |
| building_id  | int      | Building reference |
| number       | varchar  | Floor number       |
| created_at   | timestamp| Creation time      |

---

### elevators

Stores elevator state and configuration.

| Column            | Type     | Description                |
|-------------------|----------|----------------------------|
| id                | serial   | Primary key                |
| building_id       | int      | Building reference         |
| status            | varchar  | Operational status         |
| current_floor_id  | int      | Current floor              |
| direction         | varchar  | up / down / idle           |
| capacity          | int      | Max capacity               |
| current_load      | int      | Current load               |
| created_at        | timestamp| Creation time              |
| updated_at        | timestamp| Last update                |

---

### elevator_floors

Defines which floors an elevator can access.

| Column       | Type    | Description                  |
|--------------|---------|------------------------------|
| elevator_id  | int     | Elevator reference (PK)      |
| floor_id     | int     | Floor reference (PK)         |
| access_type  | varchar | Type of access               |
| is_active    | boolean | Availability flag            |
| created_at   | timestamp| Creation time               |

Composite Primary Key: `(elevator_id, floor_id)`

---

### ride_requests

Represents user ride requests.

| Column              | Type      | Description              |
|---------------------|----------|--------------------------|
| id                  | serial   | Primary key              |
| ride_assignment_id  | int      | Assigned ride            |
| source_floor_id     | int      | Source floor             |
| dest_floor_id       | int      | Destination floor        |
| status              | varchar  | Request status           |
| requested_at        | timestamp| Request time             |

---

### ride_assignments

Handles elevator assignment to requests.

| Column        | Type      | Description              |
|---------------|----------|--------------------------|
| id            | serial   | Primary key              |
| elevator_id   | int      | Assigned elevator        |
| status        | varchar  | Assignment status        |
| assigned_at   | timestamp| Assignment time          |
| completed_at  | timestamp| Completion time          |

---

### ride_logs

Stores logs for debugging and tracking.

| Column           | Type      | Description              |
|------------------|----------|--------------------------|
| id               | serial   | Primary key              |
| elevator_id      | int      | Elevator reference       |
| ride_request_id  | int      | Ride request reference   |
| log_type         | varchar  | Log type                 |
| message          | text     | Log message              |
| created_at       | timestamp| Log time                 |
| updated_at       | timestamp| Last update              |

---

## Relationships

### Building Structure
- buildings.id → floors.building_id
- buildings.id → elevators.building_id

### Elevator Mapping
- elevators.id → elevator_floors.elevator_id
- floors.id → elevator_floors.floor_id
- floors.id → elevators.current_floor_id

### Ride Flow
- elevators.id → ride_assignments.elevator_id
- ride_assignments.id → ride_requests.ride_assignment_id

### Floor Mapping in Requests
- floors.id → ride_requests.source_floor_id
- floors.id → ride_requests.dest_floor_id

### Logging
- elevators.id → ride_logs.elevator_id
- ride_requests.id → ride_logs.ride_request_id

---

## Design Highlights

- Modular structure separating requests, assignments, and logs
- Supports restricted floor access per elevator
- Tracks real-time elevator state (direction, load, floor)
- Designed for scalability across multiple buildings
- Enables detailed logging for monitoring and debugging

---

## Conclusion

This schema provides a structured and scalable foundation for building intelligent elevator systems with real-time tracking, efficient ride handling, and flexible access control across complex multi-building environments.