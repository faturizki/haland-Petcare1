---
title: Haland Petcare Business Workflow
version: 1.1.0
last_modified: 2026-06-29
owner: Lead Architect
status: Approved
depends_on: [PROJECT_SPECIFICATION.md]
referenced_by: [PROJECT_SPECIFICATION.md, DATABASE.md, ERD.md]
---

# Haland Petcare Business Workflow

## Related Documents
- [PROJECT_SPECIFICATION.md](PROJECT_SPECIFICATION.md) — Master project specification
- [DATABASE.md](DATABASE.md) — Database architecture and rules
- [ERD.md](ERD.md) — Entity relationship diagram
- [BUSINESS_RULES.md](BUSINESS_RULES.md) — Centralized business rules catalog
- [GLOSSARY.md](GLOSSARY.md) — Domain terminology

---

## Overview

This document defines the complete business workflow of Haland Petcare.

Every module MUST follow these workflows.

No module should work independently.

Every action must automatically update the next process.

Avoid duplicate input.

Always think in terms of workflow instead of CRUD.

---

## Master Workflow

Customer

↓

Pet

↓

Appointment

↓

Check-in

↓

Queue

↓

Doctor Examination

↓

Medical Record

↓

Prescription

↓

Inventory Update

↓

Invoice Generation

↓

Payment

↓

Completed

↓

Customer Portal Updated

---

## Customer Registration Workflow

Staff creates Customer

↓

System validates data

↓

Customer created

↓

Customer may own multiple pets

↓

Customer may create appointments

### Rules
- Email is optional
- Phone number should be unique
- Customer cannot be permanently deleted if related data exists

---

## Pet Registration Workflow

Select Owner

↓

Create Pet

↓

Species

↓

Breed

↓

Weight

↓

Birth Date

↓

Medical History

↓

Ready for Appointment

### Rules
- One owner can have many pets
- One pet belongs to one owner
- Pet medical history must never be lost

---

## Appointment Workflow

### Walk-In Path

Customer Arrives

↓

Staff Creates Appointment

↓

Immediate Check-in

↓

Queue

### Online Booking Path

Customer Books via Portal

↓

Scheduled

↓

Confirmed

↓

Checked In (on arrival)

↓

Waiting

↓

Called

↓

Examining

↓

Completed

↓

Cancelled (alternative terminal)

### Allowed Transitions

Scheduled → Confirmed

Confirmed → CheckedIn

CheckedIn → Waiting

Waiting → Called

Called → Examining

Examining → Completed

Scheduled → Cancelled

Confirmed → Cancelled

### Rules
- Completed appointments cannot return to previous status.
- Cancelled appointments cannot be reactivated; a new appointment must be created.
- Walk-in appointments skip the Scheduled and Confirmed states.
- Online bookings require confirmation before check-in.

---

## Appointment Reschedule Workflow

Existing Appointment

↓

Request Reschedule

↓

Validate New Slot Available

↓

New Slot Selected

↓

Confirm Reschedule

↓

Old Slot Released

↓

Notifications Sent

↓

Appointment Updated

### Rules
- Rescheduling is only allowed for appointments not in Examining or Completed status.
- The original slot must be released immediately after confirmation.
- The customer must be notified of the new schedule.

---

## Check-In Workflow

Appointment

↓

Staff Check-In

↓

Queue Number Generated

↓

Queue Status Updated

↓

Doctor Dashboard Updated

### Rules
- Check-in is only allowed for Confirmed appointments or Walk-ins.
- A pet must be registered before check-in.

---

## Queue Workflow

Waiting

↓

Called

↓

Examining

↓

Completed

### Rules
- Queue order should be chronological.
- Doctor can only examine patients marked as Called.
- Called patients must be examined before the next patient can be Called.

---

## Medical Record Workflow

Doctor opens patient

↓

View previous history

↓

Input SOAP

↓

Input Vital Signs

↓

Diagnosis

↓

Treatment

↓

Prescription

↓

Save Record

↓

Timeline Updated

↓

Invoice Generated (Draft)

↓

Follow-up Appointment Generated (if follow_up_date set)

### Rules
- Medical Records cannot be deleted.
- Medical Records should support version history.
- Every modification must be logged.
- A follow-up appointment is automatically created when follow_up_date is provided.

---

## Prescription Workflow

Doctor selects inventory item

↓

Select dosage

↓

Select frequency

↓

Select duration

↓

Save prescription

↓

Validate stock sufficient

↓

Reserve or reduce inventory

↓

Record stock movement

↓

Generate invoice items

↓

Ready for payment

### Rules
- Medicine cannot be dispensed if stock is insufficient.
- Inventory movement must always be recorded.
- Prescription items reference inventory_items, not a separate medicine table.

---

## Inventory Workflow

Purchase Order Created

↓

Send to Supplier

↓

Items Received

↓

Verify Quantity and Quality

↓

Stock In

↓

Inventory Item Available

↓

Prescription / Sale / Adjustment

↓

Stock Out / Stock Adjustment

↓

Inventory Ledger Updated

↓

Minimum Stock Alert

↓

Near Expired Alert

### Rules
- Every stock change must create an inventory movement record.
- Stock must never become negative.
- Inventory items can be medicine, product, or supply.

---

## Stock Adjustment Workflow

Identify Discrepancy

↓

Request Adjustment

↓

Supervisor / Owner Approval

↓

Adjust Stock

↓

Record Inventory Movement

↓

Audit Log Created

### Rules
- Adjustments must always be justified and approved.
- Every adjustment creates an inventory_movement record with type Adjustment.
- Negative stock is never allowed.

---

## Purchase Order Workflow

Create Purchase Order

↓

Select Supplier

↓

Add Inventory Items

↓

Send to Supplier

↓

Receive Items

↓

Verify Against PO

↓

Stock In

↓

Update Inventory

↓

Record Movement

↓

Close PO

### Rules
- Partial receipts must be supported.
- Received quantity cannot exceed ordered quantity without approval.
- Every receipt creates a StockIn movement.

---

## POS Workflow

Receive Invoice (Draft or Final)

↓

Verify Items

↓

Apply Discount

↓

Calculate Tax

↓

Choose Payment Method

↓

Receive Payment

↓

Validate Payment

↓

Calculate Change

↓

Payment Success

↓

Invoice Paid

↓

Receipt Generated

↓

Customer Portal Updated

### Rules
- Paid invoices cannot be modified.
- Draft invoices can be modified before payment.
- Void requires Owner permission.

---

## Payment Workflow

Pending

↓ Paid

Pending → Failed

Paid → Refunded

Paid → Voided

### Rules
- Every payment must have an audit log.
- Failed payments can be retried.
- Voided payments require Owner approval and must reverse the invoice status.
- Refunded payments require Owner approval.

---

## Refund Workflow

Paid Invoice

↓

Request Refund

↓

Owner Approval

↓

Process Refund Payment

↓

Update Invoice Status to Refunded

↓

Audit Log Created

↓

Customer Portal Updated

### Rules
- Refunds require Owner approval.
- Refund amount cannot exceed the original payment amount.
- Refunded invoices cannot be modified.

---

## Void Invoice Workflow

Invoice

↓

Request Void

↓

Owner Approval

↓

Void Invoice

↓

Audit Log Created

↓

Reverse Inventory Movements (if medicine dispensed)

↓

Invoice Status Updated to Voided

### Rules
- Void requires Owner permission.
- Voided invoices must reverse related inventory consumption.
- Voided invoices cannot be reactivated.

---

## Grooming Workflow

Booking

↓

Checked In

↓

In Progress

↓

Completed

↓

Invoice Generated (polymorphic)

↓

Payment

### Rules
- Grooming bookings generate invoices through the polymorphic invoice system.
- Services must be defined in the service catalog before booking.

---

## Pet Hotel Workflow

Reservation

↓

Check In

↓

Active Stay

↓

Daily Charge

↓

Check Out

↓

Invoice Generated (polymorphic)

↓

Payment

### Rules
- Hotel bookings generate invoices through the polymorphic invoice system.
- Daily charges are accumulated during the stay.

---

## Vaccination Workflow

Schedule

↓

Reminder

↓

Vaccination

↓

Certificate

↓

Next Schedule Generated

### Rules
- Automatically calculate next vaccination date based on vaccine protocol.
- Vaccination history is linked to the pet.

---

## Inpatient Workflow

Admission

↓

Daily Monitoring

↓

Treatment

↓

Medication

↓

Discharge (when clinical criteria met)

↓

Discharge Summary

↓

Invoice Generated

↓

Payment

### Rules
- Discharge is only allowed when clinical criteria are met.
- Daily monitoring must be recorded at least once per day.
- Medications dispensed during inpatient care reduce inventory.

---

## Supplier Management Workflow

Register Supplier

↓

Verify Supplier

↓

Active

↓

Create Purchase Orders

↓

Deactivate (if no active POs)

### Rules
- Suppliers with active purchase orders cannot be deleted.
- Supplier status affects availability for new purchase orders.

---

## Report Workflow

Transactions

↓

Aggregation

↓

Dashboard

↓

Charts

↓

Export CSV

### Rules
- Reports are generated from immutable transaction data.
- Cached reports must expire and refresh automatically.

---

## User Management Workflow

Invite User

↓

Assign Role

↓

Assign Permissions

↓

Activate

↓

Use System

↓

Deactivate

### Rules
- Only Owner can manage users.
- Deactivated users cannot log in but their historical actions remain in audit logs.

---

## Audit Log Workflow

Sensitive Action

↓

Capture User

↓

Capture Timestamp

↓

Capture Before Data

↓

Capture After Data

↓

Capture IP Address and User Agent

↓

Save Audit Log

### Examples
- Delete
- Update Medical Record
- Stock Adjustment
- Invoice Void
- User Permission Change
- Payment Refund

---

## Customer Portal Workflow

Customer Login

↓

View Pets

↓

View Appointments

↓

View Medical History

↓

View Vaccination History

↓

View Invoice

↓

Create Appointment

### Rules
- Customers can only view their own data.
- Online bookings follow the same appointment workflow as staff-created bookings.

---

## Notification Workflow (Future)

Appointment Created

↓

Confirmation

↓

Reminder H-1

↓

Reminder H-1 Hour

↓

Vaccination Reminder

↓

Invoice Paid

↓

Thank You Message

### Rules
- Notifications are triggered by workflow events.
- Customers can configure notification preferences.

---

## Error Handling Rules

Never allow incomplete workflow.

Never skip required status transitions.

Never leave orphan records.

Rollback database transaction if any critical process fails.

Always display meaningful error messages.

---

## Integration Rules

Every module must integrate automatically.

Medical Record

↓

Prescription

↓

Inventory

↓

Invoice

↓

POS

↓

Reports

↓

Dashboard

One action should trigger all related processes.

Avoid duplicate data entry.

---

## Golden Rule

Haland Petcare is NOT a collection of CRUD modules.

Haland Petcare is a workflow-driven veterinary clinic management system.

Every feature must support the complete clinic operation from patient arrival until payment without requiring duplicate input.