

Haland Petcare Entity Relationship Diagram

Version: 1.0.0

---

Overview

This document defines the complete Entity Relationship Diagram (ERD) for Haland Petcare.

The ERD is the single source of truth for:

- Database structure
- Table relationships
- Foreign keys
- Cardinality
- Delete behavior
- Index strategy
- Business rules

Every migration, model, relationship, and query must follow this document.

---

Database Modules

Identity

Customer

Pet

Appointment

Medical

Pharmacy

Inventory

POS

Grooming

Pet Hotel

Vaccination

Inpatient

Reporting

Audit

Settings

---

Identity Module

users

PK

id

Relationships

belongsTo role

hasMany appointments

hasMany medical_records

hasMany audit_logs

Soft Delete

YES

---

roles

PK

id

Relationships

hasMany users

Seed Data

Owner

Doctor

Staff

Customer

---

Customer Module

customers

PK

id

Relationships

hasMany pets

hasMany appointments

hasMany invoices

Important Fields

name

phone

email

address

emergency_contact

Soft Delete

YES

---

pets

PK

id

FK

customer_id

species_id

breed_id

Relationships

belongsTo customer

belongsTo species

belongsTo breed

hasMany appointments

hasMany medical_records

hasMany vaccinations

hasMany inpatient_records

Important Fields

name

gender

birth_date

weight

microchip_number

photo

Soft Delete

YES

---

species

PK

id

Relationships

hasMany pets

Seed

Dog

Cat

Rabbit

Bird

Others

---

breeds

PK

id

FK

species_id

Relationships

belongsTo species

hasMany pets

---

Appointment Module

appointments

PK

id

FK

customer_id

pet_id

doctor_id

Relationships

belongsTo customer

belongsTo pet

belongsTo doctor

hasOne medical_record

hasOne invoice

Important Fields

appointment_date

appointment_time

status

notes

Indexes

appointment_date

doctor_id

status

---

Medical Module

medical_records

PK

id

FK

appointment_id

pet_id

doctor_id

Relationships

belongsTo appointment

belongsTo pet

belongsTo doctor

hasMany diagnoses

hasMany treatments

hasMany prescriptions

Important Fields

chief_complaint

soap

vital_sign

doctor_note

follow_up_date

Soft Delete

NO

---

diagnoses

PK

id

FK

medical_record_id

Relationships

belongsTo medical_record

---

treatments

PK

id

FK

medical_record_id

Relationships

belongsTo medical_record

---

Pharmacy Module

medicines

PK

id

FK

supplier_id

Relationships

belongsTo supplier

hasMany prescription_items

hasMany inventory_movements

Important Fields

sku

barcode

unit

purchase_price

selling_price

minimum_stock

expired_date

Soft Delete

YES

---

prescriptions

PK

id

FK

medical_record_id

Relationships

belongsTo medical_record

hasMany prescription_items

---

prescription_items

PK

id

FK

prescription_id

medicine_id

Relationships

belongsTo prescription

belongsTo medicine

Fields

dosage

frequency

duration

quantity

---

Inventory Module

suppliers

PK

id

Relationships

hasMany medicines

hasMany purchase_orders

---

purchase_orders

PK

id

FK

supplier_id

Relationships

belongsTo supplier

hasMany purchase_order_items

---

purchase_order_items

PK

id

FK

purchase_order_id

medicine_id

Relationships

belongsTo purchase_order

belongsTo medicine

---

inventory_movements

PK

id

FK

medicine_id

performed_by

Relationships

belongsTo medicine

belongsTo user

Fields

movement_type

quantity

reference_type

reference_id

balance_after

Soft Delete

NO

---

POS Module

invoices

PK

id

FK

appointment_id

customer_id

Relationships

belongsTo appointment

belongsTo customer

hasMany invoice_items

hasMany payments

Fields

invoice_number

subtotal

discount

tax

grand_total

status

Indexes

invoice_number

status

---

invoice_items

PK

id

FK

invoice_id

Relationships

belongsTo invoice

Fields

item_type

item_id

description

quantity

unit_price

subtotal

---

payments

PK

id

FK

invoice_id

cashier_id

Relationships

belongsTo invoice

belongsTo user

Fields

payment_method

amount

reference_number

paid_at

status

Soft Delete

NO

---

Grooming Module

grooming_bookings

PK

id

FK

customer_id

pet_id

Relationships

belongsTo customer

belongsTo pet

hasOne invoice

---

Pet Hotel Module

hotel_rooms

PK

id

Relationships

hasMany hotel_bookings

---

hotel_bookings

PK

id

FK

room_id

pet_id

Relationships

belongsTo hotel_room

belongsTo pet

hasOne invoice

---

Vaccination Module

vaccinations

PK

id

FK

pet_id

doctor_id

Relationships

belongsTo pet

belongsTo doctor

Fields

vaccine_name

vaccination_date

next_due_date

certificate_number

---

Inpatient Module

inpatient_records

PK

id

FK

pet_id

doctor_id

Relationships

belongsTo pet

belongsTo doctor

hasMany daily_monitorings

---

daily_monitorings

PK

id

FK

inpatient_record_id

Relationships

belongsTo inpatient_record

Fields

temperature

heart_rate

weight

notes

---

Audit Module

audit_logs

PK

id

FK

user_id

Relationships

belongsTo user

Fields

action

model

model_id

old_values

new_values

ip_address

user_agent

created_at

Soft Delete

NO

---

Settings Module

settings

Single Record Table

Fields

clinic_name

address

phone

email

timezone

currency

business_hours

payment_methods

branding

---

Cardinality

Customer

1 → N Pets

Customer

1 → N Appointments

Customer

1 → N Invoices

Pet

1 → N Appointments

Pet

1 → N Medical Records

Pet

1 → N Vaccinations

Pet

1 → N Hotel Bookings

Pet

1 → N Grooming Bookings

Appointment

1 → 1 Medical Record

Appointment

1 → 1 Invoice

Medical Record

1 → N Diagnoses

Medical Record

1 → N Treatments

Medical Record

1 → 1 Prescription

Prescription

1 → N Prescription Items

Medicine

1 → N Inventory Movements

Supplier

1 → N Medicines

Supplier

1 → N Purchase Orders

Invoice

1 → N Invoice Items

Invoice

1 → N Payments

Inpatient Record

1 → N Daily Monitorings

Role

1 → N Users

User

1 → N Audit Logs

---

Delete Rules

Customer

RESTRICT

Pet

RESTRICT

Medical Record

RESTRICT

Invoice

RESTRICT

Payment

RESTRICT

Audit Log

RESTRICT

Supplier

SOFT DELETE

Medicine

SOFT DELETE

User

SOFT DELETE

---

Index Strategy

Index every:

Foreign Key

Appointment Date

Invoice Number

Phone

Email

Barcode

SKU

Medicine Name

Payment Date

Status Columns

---

Future Expansion

The database must support future modules without structural redesign.

- Multi Clinic
- Multi Tenant
- Laboratory
- Imaging
- WhatsApp Notification
- REST API
- Mobile Application
- Accounting
- Loyalty Program
- Membership
- AI Assistant

The current architecture must remain extensible while preserving backward compatibility.
