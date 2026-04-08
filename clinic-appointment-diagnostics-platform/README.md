# Clinic Appointment and Diagnostics Platform

## Overview

This database schema represents a **Clinic Appointment and Diagnostics Platform** that manages the complete lifecycle of patient care — from booking appointments to consultations, diagnostic tests, report generation, and payments.

It is designed to reflect real-world healthcare workflows while maintaining scalability, normalization, and flexibility.

---

## Features

- User management (patients & doctors)
- Appointment booking system
- Consultation tracking (with or without appointments)
- Diagnostic test prescription and completion
- Report generation system
- Flexible payment handling

---

## Database Schema

### 1. Users

Stores common user information.

| Field        | Type         | Description                  |
|--------------|-------------|------------------------------|
| id           | serial (PK) | Unique identifier            |
| first_name   | varchar     | First name                   |
| last_name    | varchar     | Last name                    |
| email        | varchar     | Unique email                 |
| phone        | varchar     | Unique phone number          |
| password     | varchar     | Hashed password              |
| dob          | date        | Date of birth                |
| gender       | varchar     | Gender                       |
| created_at   | timestamp   | Record creation time         |

---

### 2. Patients

Extends users for patient-specific data.

| Field               | Type         | Description                  |
|--------------------|-------------|------------------------------|
| id                 | serial (PK) | Unique identifier            |
| user_id            | int (FK)    | References users.id          |
| height_cm          | numeric     | Height                       |
| current_weight_kg  | numeric     | Weight                       |
| created_at         | timestamp   | Created time                 |
| updated_at         | timestamp   | Updated time                 |

---

### 3. Doctors

Extends users for doctor-specific data.

| Field               | Type         | Description                  |
|--------------------|-------------|------------------------------|
| id                 | serial (PK) | Unique identifier            |
| user_id            | int (FK)    | References users.id          |
| experience_years   | int         | Years of experience          |
| consultation_fee   | numeric     | Fee per consultation         |
| created_at         | timestamp   | Created time                 |
| updated_at         | timestamp   | Updated time                 |

---

### 4. Specialisations

Stores different medical specializations.

| Field      | Type         | Description        |
|------------|-------------|--------------------|
| id         | serial (PK) | Unique identifier  |
| name       | varchar     | Specialisation     |
| created_at | timestamp   | Created time       |

---

### 5. Doctor Specialisation

Maps doctors to their specialisations (many-to-many).

| Field              | Type         | Description                      |
|-------------------|-------------|----------------------------------|
| id                | serial (PK) | Unique identifier                |
| doctor_id         | int (FK)    | References doctors.id            |
| specialisation_id | int (FK)    | References specialisations.id    |

---

### 6. Appointments

Represents scheduled bookings.

| Field         | Type         | Description                                  |
|--------------|-------------|----------------------------------------------|
| id           | serial (PK) | Unique identifier                            |
| patient_id   | int (FK)    | References patients.id                       |
| doctor_id    | int (FK)    | References doctors.id                        |
| scheduled_at | timestamp   | Appointment time                             |
| status       | varchar     | booked / cancelled / completed / no_show     |
| created_at   | timestamp   | Created time                                 |

---

### 7. Consultations

Represents actual visits.

| Field          | Type         | Description                          |
|----------------|-------------|--------------------------------------|
| id             | serial (PK) | Unique identifier                    |
| appointment_id | int (FK)    | References appointments.id (nullable)|
| patient_id     | int (FK)    | References patients.id               |
| doctor_id      | int (FK)    | References doctors.id                |
| visit_time     | timestamp   | Visit time                           |
| notes          | text        | Doctor notes                         |
| created_at     | timestamp   | Created time                         |

---

### 8. Tests

Stores available diagnostic tests.

| Field | Type         | Description        |
|------|-------------|--------------------|
| id   | serial (PK) | Unique identifier  |
| name | varchar     | Test name          |
| price| numeric     | Cost               |

---

### 9. Consultation Tests

Links tests to consultations.

| Field           | Type         | Description                     |
|----------------|-------------|---------------------------------|
| id             | serial (PK) | Unique identifier               |
| consultation_id| int (FK)    | References consultations.id     |
| test_id        | int (FK)    | References tests.id             |
| status         | varchar     | prescribed / completed          |

---

### 10. Reports

Stores generated diagnostic reports.

| Field                  | Type         | Description                          |
|------------------------|-------------|--------------------------------------|
| id                     | serial (PK) | Unique identifier                    |
| consultation_test_id   | int (FK)    | References consultation_tests.id     |
| report_url             | text        | Report location                      |
| generated_at           | timestamp   | Generation time                      |

---

### 11. Payments

Handles financial transactions.

| Field          | Type         | Description                                  |
|----------------|-------------|----------------------------------------------|
| id             | serial (PK) | Unique identifier                            |
| patient_id     | int (FK)    | References patients.id                       |
| consultation_id| int (FK)    | References consultations.id (nullable)       |
| appointment_id | int (FK)    | References appointments.id (nullable)        |
| amount         | numeric     | Payment amount                               |
| status         | varchar     | pending / success / failed / refunded        |
| method         | varchar     | upi / card / cash / netbanking               |
| paid_at        | timestamp   | Payment time                                 |
| created_at     | timestamp   | Created time                                 |

---

## Relationships Summary

- `users → patients` (1:1)
- `users → doctors` (1:1)
- `doctors ↔ specialisations` (M:N)
- `patients → appointments` (1:N)
- `doctors → appointments` (1:N)
- `appointments → consultations` (1:1 or 1:N optional)
- `consultations → consultation_tests` (1:N)
- `consultation_tests → reports` (1:1)
- `patients → payments` (1:N)

---

## Design Highlights

- **Flexible consultations**: supports both appointment-based and walk-in visits  
- **Decoupled diagnostics**: multiple tests per consultation with independent tracking  
- **Scalable payments**: supports multiple billing scenarios  
- **Normalized schema**: reduces redundancy and improves consistency  

---

## Possible Improvements

- Add indexing on frequently queried fields
- Introduce soft delete (`deleted_at`)
- Add constraints to ensure only one of `appointment_id` or `consultation_id` is used in payments
- Add scheduling/slot management system
- Implement audit logs for tracking changes

---

## Conclusion

This schema provides a strong and scalable foundation for building a clinic management system. It balances real-world healthcare requirements with clean database design principles, ensuring flexibility, maintainability, and future extensibility.