# Online Fitness Coaching Platform - ER Diagram

## Overview

This project presents the database design for an online fitness coaching platform where trainers manage clients, offer coaching plans, schedule sessions, and track client progress.

The system is designed to support:

* Trainer and client management
* Coaching plans and enrollments
* Subscription-based billing and payments
* Session scheduling
* Progress tracking through check-ins

---

## Business Context

Fitness influencers often begin by coaching clients through social media and video calls. As their audience grows, they require a structured platform to:

* Onboard and manage clients
* Sell coaching programs
* Schedule consultations and training sessions
* Track client progress
* Handle subscriptions and payments

---

## Core Entities

### Users and Profiles

* **users**: Base entity for all users (trainer, client, admin)
* **trainer_profiles**: Stores trainer-specific details such as bio, specialization, and experience
* **client_profiles**: Stores client-specific details such as fitness goals and health information

---

### Trainer-Client Relationship

* **trainer_client_assignments**

  * Maps trainers to clients
  * Tracks assignment lifecycle (active, paused, ended)

---

### Coaching Plans

* **coaching_plans**

  * Created by trainers
  * Includes plan type (consultation, workout, diet, full coaching)

* **plan_contents**

  * Defines weekly breakdown of plans
  * Supports workout, diet, or both

---

### Enrollment System

* **enrollments**

  * Connects client, trainer, and plan
  * Tracks start date, end date, and status

Key points:

* A client can enroll in multiple plans over time
* A plan can have multiple clients

---

### Payments and Subscriptions

* **subscriptions**

  * Manages billing cycles (one-time, monthly, yearly)
  * Supports recurring payments

* **payments**

  * Stores individual transaction records
  * Includes payment method and status

---

### Sessions and Consultations

* **sessions**

  * Supports consultation, live training, and follow-ups
  * Tracks scheduling, duration, and session status

---

### Progress Tracking

#### Check-ins

* **check_ins**

  * Weekly submissions by clients
  * Includes notes and overall feedback

#### Media

* **check_in_media**

  * Stores photos and videos uploaded during check-ins

#### Metrics

* **progress_entries**

  * Tracks key fitness metrics such as weight and body fat percentage

---

### Trainer Feedback

* **trainer_feedback**

  * Trainers provide feedback on check-ins
  * Includes ratings and visibility control

---

## Relationships Summary

* One trainer can manage multiple clients
* One client can enroll in multiple plans
* One plan can have multiple clients
* One enrollment can have one subscription
* One subscription can have multiple payments
* One check-in can have multiple media entries
* One check-in is associated with one progress entry
* One check-in can have trainer feedback

---

## Design Decisions

### Separation of Concerns

User data, plan data, and progress tracking are separated to maintain clarity and scalability.

### Enrollment as Core Entity

The enrollment table acts as a central link between client, trainer, and plan, enabling flexible business logic.

### Structured Progress Tracking

Progress data is stored separately to allow structured tracking without cluttering check-in data.

### Subscription-Based Billing

Supports both one-time and recurring billing models.

---

## Conclusion

This database design provides a structured and scalable foundation for an online fitness coaching platform. It balances simplicity with real-world requirements, ensuring clear separation between users, plans, sessions, and progress tracking. The model supports both transactional workflows such as enrollments and payments, as well as ongoing coaching interactions like check-ins, feedback, and performance monitoring.






