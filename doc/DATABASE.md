---
title: Haland Petcare Database Architecture
version: 1.1.0
last_modified: 2026-06-29
owner: Lead Architect
status: Approved
depends_on: [ERD.md]
referenced_by: [PROJECT_SPECIFICATION.md, WORKFLOW.md, ERD.md]
---

# Haland Petcare Database Architecture

## Related Documents
- [PROJECT_SPECIFICATION.md](PROJECT_SPECIFICATION.md) — Master project specification
- [WORKFLOW.md](WORKFLOW.md) — Complete business workflows
- [ERD.md](ERD.md) — Entity relationship diagram and table definitions
- [BUSINESS_RULES.md](BUSINESS_RULES.md) — Centralized business rules catalog
- [GLOSSARY.md](GLOSSARY.md) — Domain terminology

---

## Overview

This document defines the database architecture for Haland Petcare.

Database design must prioritize:

- Normalization
- Data Integrity
- Performance
- Readability
- Scalability
- Maintainability

Every table must represent one business entity.

Avoid duplicate data.

Use foreign keys whenever possible.

---

## Database Engine

- **Engine:** MySQL 8
- **Character Set:** utf8mb4
- **Collation:** utf8mb4_unicode_ci
- **Storage Engine:** InnoDB
- **Timezone:** UTC

---

## Primary Key

Every table must use:

- `id`
- `BIGINT UNSIGNED`
- `AUTO_INCREMENT`
- `PRIMARY KEY`

---

## Timestamp

Every table should contain:

- `created_at`
- `updated_at`

Use soft deletes where appropriate:

- `deleted_at`

Medical records must never be permanently deleted.

---

## Naming Convention

### Tables

Plural.

Examples:

- customers
- pets
- appointments
- medical_records
- prescriptions
- invoice_items
- inventory_movements

### Columns

snake_case.

### Foreign Keys

- customer_id
- pet_id
- doctor_id
- appointment_id
- invoice_id
- inventory_item_id

### Polymorphic Columns

- invoiceable_type
- invoiceable_id
- notifiable_type
- notifiable_id

---

## Relationships

### Customer
- hasMany Pets
- hasMany Appointments
- hasMany Invoices
- hasMany GroomingBookings

### Pet
- belongsTo Customer
- belongsTo Species
- belongsTo Breed
- hasMany Medical Records
- hasMany Vaccinations
- hasMany Appointments
- hasMany InpatientRecords
- hasMany GroomingBookings
- hasMany HotelBookings

### Species
- hasMany Breeds
- hasMany Pets

### Breed
- belongsTo Species
- hasMany Pets

### Appointment
- belongsTo Customer
- belongsTo Pet
- belongsTo Doctor
- hasOne Medical Record
- morphOne Invoice (as invoiceable)

### Medical Record
- belongsTo Appointment
- belongsTo Pet
- belongsTo Doctor
- hasMany Diagnoses
- hasMany Treatments
- hasMany Prescriptions

### Prescription
- belongsTo Medical Record
- hasMany Prescription Items

### Prescription Item
- belongsTo Prescription
- belongsTo Inventory Item

### Inventory Item
- belongsTo Supplier
- belongsTo Inventory Category
- hasMany Prescription Items
- hasMany Inventory Movements
- hasMany Purchase Order Items

### Inventory Category
- hasMany Inventory Items

### Supplier
- hasMany Inventory Items
- hasMany Purchase Orders

### Purchase Order
- belongsTo Supplier
- hasMany Purchase Order Items

### Purchase Order Item
- belongsTo Purchase Order
- belongsTo Inventory Item

### Inventory Movement
- belongsTo Inventory Item
- belongsTo User

### Invoice
- belongsTo Customer
- belongsTo Cashier (User)
- morphTo Invoiceable
- hasMany Invoice Items
- hasMany Payments

### Invoice Item
- belongsTo Invoice

### Payment
- belongsTo Invoice
- belongsTo Cashier (User)

### Grooming Booking
- belongsTo Customer
- belongsTo Pet
- morphOne Invoice (as invoiceable)

### Hotel Room
- hasMany Hotel Bookings

### Hotel Booking
- belongsTo Hotel Room
- belongsTo Pet
- belongsTo Customer
- morphOne Invoice (as invoiceable)

### Inpatient Record
- belongsTo Pet
- belongsTo Doctor
- hasMany Daily Monitorings

### Daily Monitoring
- belongsTo Inpatient Record

### User
- belongsTo Role
- hasMany Appointments
- hasMany Medical Records
- hasMany Audit Logs
- hasMany Inventory Movements
- hasMany Payments

### Role
- hasMany Users
- belongsToMany Permissions

### Permission
- belongsToMany Roles

---

## Core Tables

- users
- roles
- permissions
- role_permission
- customers
- pets
- species
- breeds
- appointments
- medical_records
- diagnoses
- treatments
- prescriptions
- prescription_items
- inventory_categories
- inventory_items
- suppliers
- purchase_orders
- purchase_order_items
- inventory_movements
- invoices
- invoice_items
- payments
- grooming_bookings
- hotel_rooms
- hotel_bookings
- vaccinations
- inpatient_records
- daily_monitorings
- reports_cache
- audit_logs
- notifications
- settings

---

## Status Management

Never store status as arbitrary strings.

Use PHP Enums.

### AppointmentStatus
- Scheduled
- Confirmed
- CheckedIn
- Waiting
- Called
- Examining
- Completed
- Cancelled

### QueueStatus
- Waiting
- Called
- Examining
- Completed

### InvoiceStatus
- Draft
- Pending
- Paid
- Voided
- Refunded

### PaymentStatus
- Pending
- Paid
- Failed
- Refunded
- Voided

### InventoryMovementType
- StockIn
- StockOut
- Adjustment
- Purchase
- Prescription
- Return

### GroomingStatus
- Booked
- CheckedIn
- InProgress
- Completed
- Cancelled

### HotelBookingStatus
- Reserved
- CheckedIn
- Active
- CheckedOut
- Cancelled

### InpatientStatus
- Admitted
- UnderTreatment
- Discharged

---

## Database Rules

Every foreign key must exist.

Never store duplicated customer information.

Never duplicate pet information.

Medical records are immutable.

Inventory movement is append-only.

Payments cannot modify historical data.

Invoices cannot be edited after payment.

Polymorphic references must always resolve to a valid source record.

Inventory item type must be validated at the application level.

---

## Inventory Ledger

Inventory must never be updated directly.

Every stock change creates:

- inventory_movements

Fields:

- inventory_item_id
- movement_type
- quantity
- reference_type
- reference_id
- balance_after
- performed_by
- notes
- created_at

Current stock is calculated from movements or updated transactionally.

---

## Medical Records

Medical Records are permanent.

Never hard delete.

Every update should create audit history.

Relationships:

- Pet
- Doctor
- Appointment
- Prescription
- Diagnosis
- Treatment
- Attachments

---

## Invoice

Invoice Header:

- Customer
- Cashier
- Invoiceable source (Appointment, Grooming Booking, or Hotel Booking)
- Invoice Number
- Subtotal
- Discount
- Tax
- Grand Total
- Status

Invoice Items:

- Item Type (service | inventory_item)
- Item ID
- Description
- Quantity
- Price
- Subtotal

---

## Payment

- Invoice
- Payment Method
- Amount
- Paid At
- Cashier
- Reference Number
- Status
- Notes

---

## Audit Log

Capture:

- User
- Action
- Model
- Model ID
- Old Value
- New Value
- IP Address
- User Agent
- Timestamp

Never delete audit logs.

---

## Clinic Settings

Store only one record.

- Clinic Name
- Address
- Phone
- Email
- Business Hours
- Timezone
- Payment Methods
- Branding
- Currency
- Tax Rate

---

## Indexing

Always index:

- Foreign Keys
- Polymorphic columns
- Email
- Phone
- Appointment Date
- Invoice Number
- Payment Date
- Inventory Item Name
- SKU
- Barcode
- item_type
- Status Columns
- Frequently searched fields

---

## Transactions

Always wrap critical workflows.

Examples:

- Create Appointment
- Medical Record
- Prescription
- Invoice
- Payment
- Inventory Update

If any step fails:

- Rollback everything.

---

## Constraints

- Stock cannot be negative.
- Invoice total cannot be negative.
- Payment cannot exceed invoice total.
- Pet must always belong to customer.
- Appointment requires pet.
- Prescription requires medical record.
- Invoice must have a valid invoiceable source.
- Inventory item type must be one of: medicine, product, supply.
- Non-medicine inventory items must not require an expired_date.

---

## Soft Deletes

### Allowed
- Customers
- Pets
- Inventory Items
- Suppliers
- Users
- Inventory Categories

### Not Allowed
- Medical Records
- Payments
- Audit Logs
- Inventory Movements
- Invoices
- Prescriptions

---

## UUID

Not required.

Use BIGINT AUTO_INCREMENT for all primary keys.

Future API may expose ULID or UUID publicly if needed.

---

## Performance

- Always eager load relationships.
- Avoid N+1 queries.
- Paginate large datasets.
- Use indexes.
- Cache configuration data.

---

## Security

- Never trust client IDs.
- Always validate ownership.
- Use Policies.
- Use Foreign Keys.
- Prevent orphan records.
- Validate polymorphic types against a whitelist.

---

## Future Expansion

Database must support future features without major redesign.

- Multi Clinic
- Multi Tenant
- Laboratory
- Imaging
- REST API
- Mobile App
- Accounting
- AI Assistant
- Business Intelligence

---

## Golden Rule

The database is the single source of truth.

Every workflow must preserve referential integrity, prevent duplicate data, and guarantee consistency across all related modules.