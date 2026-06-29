---
title: Haland Petcare Glossary
version: 1.0.0
last_modified: 2026-06-29
owner: Lead Architect
status: Draft
depends_on: [PROJECT_SPECIFICATION.md, WORKFLOW.md, DATABASE.md, ERD.md]
referenced_by: [PROJECT_SPECIFICATION.md, WORKFLOW.md, DATABASE.md, ERD.md, BUSINESS_RULES.md, SECURITY.md, TESTING_STRATEGY.md]
---

# Haland Petcare Glossary

## Related Documents
- [PROJECT_SPECIFICATION.md](PROJECT_SPECIFICATION.md) — Master project specification
- [WORKFLOW.md](WORKFLOW.md) — Complete business workflows
- [DATABASE.md](DATABASE.md) — Database architecture and rules
- [ERD.md](ERD.md) — Entity relationship diagram
- [BUSINESS_RULES.md](BUSINESS_RULES.md) — Centralized business rules catalog

---

## Domain Terms

### Appointment
A scheduled interaction between a customer, a pet, and a doctor. Appointments can be created by staff (walk-in) or by customers through the portal (online booking).

### Audit Log
An immutable record of sensitive actions including the user, timestamp, before/after values, IP address, and user agent.

### Breed
A specific animal breed (e.g., Golden Retriever, Persian Cat) linked to a Species.

### Business Rule
A constraint or behavior that governs how the system operates. All business rules are cataloged in [BUSINESS_RULES.md](BUSINESS_RULES.md).

### Check-In
The process of confirming a customer's arrival and placing the appointment into the queue. Check-in is a pre-queue state.

### Chief Complaint
The primary reason the customer brought the pet to the clinic, recorded at the start of the medical record.

### Customer
The person who owns or is responsible for one or more pets. Customers may book appointments and view their data through the portal.

### Customer Portal
A self-service interface for customers to view pets, appointments, medical history, vaccination history, and invoices.

### Daily Monitoring
A record of a hospitalized pet's vital signs and condition taken at least once per day during inpatient care.

### Diagnosis
A clinical determination of a pet's condition recorded in the medical record. Can be primary or secondary.

### Doctor
A veterinary professional responsible for medical examinations, diagnoses, prescriptions, and inpatient care.

### Drawer
A UI panel that slides in from the side of the screen, used for details, previews, history, and timelines.

### Eager Loading
A database query optimization technique that loads related records in a single query to avoid N+1 query problems.

### Enum
A PHP enumerated type used to represent a fixed set of named values, such as AppointmentStatus or PaymentStatus.

### Failed Payment
A payment attempt that was rejected by the payment processor or system. Failed payments can be retried.

### Follow-Up Appointment
An automatically generated future appointment created when a doctor sets a follow_up_date in the medical record.

### Grooming Booking
A reservation for grooming services. Grooming bookings generate invoices through the polymorphic invoice system.

### Hotel Booking
A reservation for a pet hotel room. Hotel bookings generate invoices through the polymorphic invoice system.

### Inventory Category
A classification for inventory items, replacing the previous medicine_categories concept.

### Inventory Item
A generic stock item that can be a medicine, product, or supply. Inventory items are tracked through inventory_movements.

### Inventory Ledger
The complete history of stock changes for an inventory item, stored as append-only inventory_movement records.

### Inventory Movement
An append-only record of every stock change, including stock in, stock out, adjustment, purchase, prescription, and return.

### Invoice
A bill generated from an appointment, grooming booking, or hotel booking. Invoices are linked to their source through a polymorphic relationship.

### Invoice Item
A line item on an invoice representing a service or inventory item.

### Invoice Void
The cancellation of an invoice after creation. Voiding requires Owner approval and may reverse inventory consumption.

### Livewire
A Laravel full-stack framework used for dynamic UI components without writing JavaScript.

### Medical Record
A permanent clinical document containing SOAP notes, vital signs, diagnoses, treatments, and prescriptions for a pet visit.

### Modal
A small overlay dialog used for confirmations, small forms, delete confirmations, and quick edits.

### N+1 Query
A performance anti-pattern where an application executes one query for a list and then one additional query per item. Must be avoided.

### Online Booking
An appointment created by a customer through the Customer Portal, requiring confirmation before check-in.

### Owner
The clinic owner with full system access, including revenue, reports, user management, and sensitive operations.

### Payment
A financial transaction applied to an invoice. Payments can be pending, paid, failed, refunded, or voided.

### Permission
A granular access right that can be assigned to roles. Permissions are managed through the role_permission pivot.

### Pet
An animal registered under a customer. A pet has species, breed, weight, birth date, microchip, and medical history.

### Polymorphic Relationship
A database relationship where a record can belong to more than one other type of record. Invoices use polymorphic relationships to belong to appointments, grooming bookings, or hotel bookings.

### POS (Point of Sale)
The interface where staff process invoices, apply discounts, calculate tax, receive payments, and generate receipts.

### Prescription
A doctor's instruction for medication, including dosage, frequency, duration, and quantity. A medical record can have multiple prescriptions.

### Prescription Item
A single line in a prescription referencing an inventory item.

### Purchase Order (PO)
A document sent to a supplier to request inventory items. POs support partial receipts and stock-in movements.

### Queue
The ordered list of checked-in patients waiting to be examined. Queue states: Waiting, Called, Examining, Completed.

### Refund
A return of payment to the customer. Refunds require Owner approval and update the invoice status to Refunded.

### Role
A named collection of permissions assigned to users. Seed roles: Owner, Doctor, Staff, Customer.

### Service Layer
An architectural pattern where business logic is encapsulated in dedicated service classes, keeping controllers thin.

### SOAP
A structured medical documentation format:
- **Subjective**: What the owner reports
- **Objective**: Observable clinical findings
- **Assessment**: Doctor's evaluation/diagnosis
- **Plan**: Treatment and follow-up plan

### Soft Delete
A deletion strategy that marks a record as deleted without physically removing it, preserving referential integrity and history.

### Species
A broad animal classification (e.g., Dog, Cat, Rabbit, Bird, Others). Species have many breeds.

### Staff
A clinic employee responsible for daily operations such as registration, appointments, check-in, POS, grooming, hotel, and inventory.

### State Machine
A formal model of allowed status transitions. All major workflows use state machines.

### Stock Adjustment
A correction to inventory quantity due to discrepancy, damage, or recount. Adjustments require approval and create audit logs.

### Stock In
An inventory movement that increases stock, typically from purchase order receipts.

### Stock Out
An inventory movement that decreases stock, typically from prescriptions or sales.

### Supplier
A vendor that provides inventory items. Suppliers can be soft deleted if no active purchase orders exist.

### Treatment
A medical intervention or procedure recorded in the medical record.

### User
Any person with system access, including Owner, Doctor, Staff, and Customer roles.

### Vaccination
An immunization event recorded for a pet, including vaccine name, date, next due date, and certificate number.

### Vital Signs
Clinical measurements recorded during examination:
- Temperature
- Heart Rate
- Respiratory Rate
- Weight
- Body Condition Score

### Voided Payment
A payment that was intentionally cancelled after being recorded. Voided payments require Owner approval.

### Walk-In
An appointment created when the customer arrives at the clinic, skipping the Scheduled and Confirmed states.

---

## Technical Terms

### Action Class
A single-responsibility class that performs one business operation, often used in Laravel applications.

### Blade
Laravel's templating engine used for server-side rendering.

### CSRF (Cross-Site Request Forgery)
An attack where unauthorized commands are submitted from a trusted user. Prevented by Laravel CSRF tokens.

### Foreign Key
A database constraint that links a column to the primary key of another table, ensuring referential integrity.

### Index
A database structure that improves query performance on frequently searched or joined columns.

### PII (Personally Identifiable Information)
Data that can identify an individual, such as name, phone, email, and address.

### PHI (Pet Health Information)
Medical data about a pet, including diagnoses, treatments, prescriptions, and vaccination records.

### PSR-12
The PHP coding style standard followed by the project.

### SOLID
A set of object-oriented design principles: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion.

### Tailwind CSS
A utility-first CSS framework used for styling.

### Thin Controller
A controller that delegates business logic to service or action classes, containing minimal code.

### Transaction
A database operation sequence that is atomic: all steps succeed or all are rolled back.

---

## Abbreviations

| Abbreviation | Meaning |
|---|---|
| API | Application Programming Interface |
| CI/CD | Continuous Integration / Continuous Deployment |
| CSV | Comma-Separated Values |
| ERD | Entity Relationship Diagram |
| FK | Foreign Key |
| HIS | Hospital Information System |
| ERP | Enterprise Resource Planning |
| PII | Personally Identifiable Information |
| PHI | Pet Health Information |
| PK | Primary Key |
| PO | Purchase Order |
| POS | Point of Sale |
| REST | Representational State Transfer |
| RTM | Requirements Traceability Matrix |
| SOAP | Subjective, Objective, Assessment, Plan |
| UI | User Interface |
| UX | User Experience |
| VAT | Value Added Tax |