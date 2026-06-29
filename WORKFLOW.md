

Haland Petcare Business Workflow

Version: 1.0.0

---

Overview

This document defines the complete business workflow of Haland Petcare.

Every module MUST follow these workflows.

No module should work independently.

Every action must automatically update the next process.

Avoid duplicate input.

Always think in terms of workflow instead of CRUD.

---

Master Workflow

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

Customer Registration Workflow

Staff creates Customer

↓

System validates data

↓

Customer created

↓

Customer may own multiple pets

↓

Customer may create appointments

Rules

- Email is optional
- Phone number should be unique
- Customer cannot be permanently deleted if related data exists

---

Pet Registration Workflow

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

Rules

- One owner can have many pets
- One pet belongs to one owner
- Pet medical history must never be lost

---

Appointment Workflow

Scheduled

↓

Confirmed

↓

Checked In

↓

Waiting

↓

Called

↓

Examining

↓

Completed

↓

Cancelled

Allowed Transitions

Scheduled → Confirmed

Confirmed → Checked In

Checked In → Waiting

Waiting → Called

Called → Examining

Examining → Completed

Scheduled → Cancelled

Confirmed → Cancelled

Rules

Completed appointments cannot return to previous status.

---

Check-In Workflow

Appointment

↓

Staff Check-In

↓

Queue Number Generated

↓

Queue Status Updated

↓

Doctor Dashboard Updated

---

Queue Workflow

Waiting

↓

Called

↓

Examining

↓

Completed

Rules

Queue order should be chronological.

Doctor can only examine patients marked as Called.

---

Medical Record Workflow

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

Invoice Generated

Rules

Medical Records cannot be deleted.

Medical Records should support version history.

Every modification must be logged.

---

Prescription Workflow

Doctor selects medicine

↓

Select dosage

↓

Select duration

↓

Save prescription

↓

Reduce inventory

↓

Record stock movement

↓

Generate invoice items

↓

Ready for payment

Rules

Medicine cannot be dispensed if stock is insufficient.

Inventory movement must always be recorded.

---

Inventory Workflow

Purchase

↓

Stock In

↓

Available

↓

Prescription

↓

Stock Out

↓

Inventory Ledger

↓

Minimum Stock Alert

↓

Near Expired Alert

Rules

Every stock change must create an inventory movement record.

Stock must never become negative.

---

POS Workflow

Receive Invoice

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

Calculate Change

↓

Payment Success

↓

Invoice Paid

↓

Receipt Generated

Rules

Paid invoices cannot be modified.

Void requires Owner permission.

---

Payment Workflow

Pending

↓

Paid

↓

Refunded

↓

Voided

Rules

Every payment must have an audit log.

---

Grooming Workflow

Booking

↓

Checked In

↓

In Progress

↓

Completed

↓

Invoice Generated

↓

Payment

---

Pet Hotel Workflow

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

Invoice Generated

↓

Payment

---

Vaccination Workflow

Schedule

↓

Reminder

↓

Vaccination

↓

Certificate

↓

Next Schedule Generated

Rules

Automatically calculate next vaccination date.

---

Inpatient Workflow

Admission

↓

Daily Monitoring

↓

Treatment

↓

Medication

↓

Discharge

↓

Discharge Summary

↓

Invoice

↓

Payment

---

Report Workflow

Transactions

↓

Aggregation

↓

Dashboard

↓

Charts

↓

Export CSV

---

User Management Workflow

Invite User

↓

Assign Role

↓

Activate

↓

Use System

↓

Deactivate

Rules

Only Owner can manage users.

---

Audit Log Workflow

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

Save Audit Log

Examples

- Delete
- Update Medical Record
- Stock Adjustment
- Invoice Void
- User Permission Change

---

Customer Portal Workflow

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

---

Notification Workflow (Future)

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

---

Error Handling Rules

Never allow incomplete workflow.

Never skip required status transitions.

Never leave orphan records.

Rollback database transaction if any critical process fails.

Always display meaningful error messages.

---

Integration Rules

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

Golden Rule

Haland Petcare is NOT a collection of CRUD modules.

Haland Petcare is a workflow-driven veterinary clinic management system.

Every feature must support the complete clinic operation from patient arrival until payment without requiring duplicate input.
