DATABASE.md

Haland Petcare Database Architecture

Version: 1.0.0

---

Overview

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

Database Engine

MySQL 8

Character Set

utf8mb4

Collation

utf8mb4_unicode_ci

Storage Engine

InnoDB

Timezone

UTC

---

Primary Key

Every table must use:

id

BIGINT UNSIGNED

AUTO INCREMENT

PRIMARY KEY

---

Timestamp

Every table should contain

created_at

updated_at

Use soft deletes where appropriate

deleted_at

Medical records must never be permanently deleted.

---

Naming Convention

Tables

Plural

Examples

customers

pets

appointments

medical_records

prescriptions

invoice_items

inventory_movements

Columns

snake_case

Foreign Keys

customer_id

pet_id

doctor_id

appointment_id

invoice_id

---

Relationships

Customer

hasMany Pets

hasMany Appointments

hasMany Invoices

Pet

belongsTo Customer

hasMany Medical Records

hasMany Vaccinations

hasMany Appointments

Appointment

belongsTo Customer

belongsTo Pet

belongsTo Doctor

hasOne Medical Record

hasOne Invoice

Medical Record

belongsTo Appointment

belongsTo Pet

belongsTo Doctor

hasMany Prescriptions

Prescription

belongsTo Medical Record

hasMany Prescription Items

Invoice

belongsTo Appointment

hasMany Invoice Items

hasMany Payments

Inventory Item

belongsTo Supplier

hasMany Inventory Movements

Supplier

hasMany Inventory Items

User

hasMany Medical Records

hasMany Audit Logs

Role

hasMany Users

---

Core Tables

users

roles

permissions

customers

pets

species

breeds

appointments

appointment_statuses (Enum)

medical_records

diagnoses

treatments

prescriptions

prescription_items

medicines

medicine_categories

suppliers

purchase_orders

purchase_order_items

inventory_items

inventory_movements

invoices

invoice_items

payments

grooming_bookings

pet_hotel_rooms

pet_hotel_bookings

vaccinations

inpatients

daily_monitorings

reports_cache

audit_logs

settings

notifications

---

Status Management

Never store status as arbitrary strings.

Use PHP Enums.

Examples

AppointmentStatus

Scheduled

Confirmed

CheckedIn

Waiting

Called

Examining

Completed

Cancelled

InvoiceStatus

Draft

Pending

Paid

Voided

Refunded

PaymentStatus

Pending

Paid

Failed

Refunded

InventoryMovementType

StockIn

StockOut

Adjustment

Purchase

Prescription

Return

---

Database Rules

Every foreign key must exist.

Never store duplicated customer information.

Never duplicate pet information.

Medical records are immutable.

Inventory movement is append-only.

Payments cannot modify historical data.

Invoices cannot be edited after payment.

---

Inventory Ledger

Inventory must never be updated directly.

Every stock change creates:

inventory_movements

Fields

inventory_item_id

movement_type

quantity

reference_type

reference_id

performed_by

notes

created_at

Current stock is calculated from movements or updated transactionally.

---

Medical Records

Medical Records are permanent.

Never hard delete.

Every update should create audit history.

Relationships

Pet

Doctor

Appointment

Prescription

Diagnosis

Treatment

Attachments

---

Invoice

Invoice Header

Customer

Appointment

Subtotal

Discount

Tax

Grand Total

Status

Invoice Items

Service

Medicine

Product

Quantity

Price

Subtotal

---

Payment

Invoice

Payment Method

Amount

Paid At

Cashier

Reference Number

Status

Notes

---

Audit Log

Capture

User

Action

Model

Model ID

Old Value

New Value

IP Address

User Agent

Timestamp

Never delete audit logs.

---

Clinic Settings

Store only one record.

Clinic Name

Address

Phone

Business Hours

Timezone

Payment Methods

Branding

Currency

---

Indexing

Always index

Foreign Keys

Email

Phone

Appointment Date

Invoice Number

Payment Date

Medicine Name

SKU

Barcode

Frequently searched fields.

---

Transactions

Always wrap critical workflows.

Examples

Create Appointment

Medical Record

Prescription

Invoice

Payment

Inventory Update

If any step fails

Rollback everything.

---

Constraints

Stock cannot be negative.

Invoice total cannot be negative.

Payment cannot exceed invoice total.

Pet must always belong to customer.

Appointment requires pet.

Prescription requires medical record.

---

Soft Deletes

Allowed

Customers

Pets

Medicines

Inventory Items

Suppliers

Users

Not Allowed

Medical Records

Payments

Audit Logs

Inventory Movements

---

UUID

Not required.

Use BIGINT AUTO_INCREMENT for all primary keys.

Future API may expose ULID or UUID publicly if needed.

---

Performance

Always eager load relationships.

Avoid N+1 queries.

Paginate large datasets.

Use indexes.

Cache configuration data.

---

Security

Never trust client IDs.

Always validate ownership.

Use Policies.

Use Foreign Keys.

Prevent orphan records.

---

Future Expansion

Database must support future features without major redesign.

Multi Clinic

Multi Tenant

Laboratory

Imaging

REST API

Mobile App

Accounting

AI Assistant

Business Intelligence

---

Golden Rule

The database is the single source of truth.

Every workflow must preserve referential integrity, prevent duplicate data, and guarantee consistency across all related modules.
