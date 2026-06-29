---
title: Haland Petcare Business Rules
version: 1.0.0
last_modified: 2026-06-29
owner: Lead Architect
status: Draft
depends_on: [PROJECT_SPECIFICATION.md, WORKFLOW.md, DATABASE.md, ERD.md]
referenced_by: [PROJECT_SPECIFICATION.md, WORKFLOW.md, DATABASE.md, ERD.md, SECURITY.md, TESTING_STRATEGY.md]
---

# Haland Petcare Business Rules

## Related Documents
- [PROJECT_SPECIFICATION.md](PROJECT_SPECIFICATION.md) — Master project specification
- [WORKFLOW.md](WORKFLOW.md) — Complete business workflows
- [DATABASE.md](DATABASE.md) — Database architecture and rules
- [ERD.md](ERD.md) — Entity relationship diagram
- [GLOSSARY.md](GLOSSARY.md) — Domain terminology

---

## Overview

This document is the single source of truth for all business rules in Haland Petcare.

Every validation, workflow guard, and system behavior must be traceable to a rule in this document.

Rules are organized by domain. Each rule has a unique ID, description, severity, and source document reference.

---

## Rule Format

| Field | Description |
|---|---|
| **ID** | Unique identifier (e.g., BR-CUST-001) |
| **Domain** | Business area |
| **Rule** | Clear, testable statement |
| **Severity** | Critical / High / Medium / Low |
| **Source** | Document and section where the rule originated |

---

## Customer Management

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-CUST-001 | Email is optional for customers. | Medium | WORKFLOW.md Customer Registration |
| BR-CUST-002 | Phone number should be unique across customers. | High | WORKFLOW.md Customer Registration |
| BR-CUST-003 | A customer cannot be permanently deleted if related data (pets, appointments, invoices) exists. | Critical | WORKFLOW.md Customer Registration |
| BR-CUST-004 | Customers can own multiple pets. | High | PROJECT_SPECIFICATION.md |
| BR-CUST-005 | Customers can view only their own data in the Customer Portal. | Critical | WORKFLOW.md Customer Portal |
| BR-CUST-006 | Customer PII must be protected according to data classification rules. | Critical | SECURITY.md |

---

## Pet Management

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-PET-001 | One owner can have many pets. | High | WORKFLOW.md Pet Registration |
| BR-PET-002 | One pet belongs to exactly one owner. | High | WORKFLOW.md Pet Registration |
| BR-PET-003 | Pet medical history must never be lost. | Critical | WORKFLOW.md Pet Registration |
| BR-PET-004 | A pet must have a species and may have a breed. | High | ERD.md Pet Module |
| BR-PET-005 | Pet weight should be recorded per visit, not only on the pet profile. | High | Audit finding |
| BR-PET-006 | A pet cannot be permanently deleted if related medical records exist. | Critical | DATABASE.md Soft Deletes |

---

## Appointment Management

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-APT-001 | Appointments can be created as walk-in or online booking. | High | PROJECT_SPECIFICATION.md |
| BR-APT-002 | Walk-in appointments skip Scheduled and Confirmed states. | High | WORKFLOW.md Appointment Workflow |
| BR-APT-003 | Online bookings require confirmation before check-in. | High | WORKFLOW.md Appointment Workflow |
| BR-APT-004 | Allowed transitions: Scheduled→Confirmed, Confirmed→CheckedIn, CheckedIn→Waiting, Waiting→Called, Called→Examining, Examining→Completed, Scheduled→Cancelled, Confirmed→Cancelled. | Critical | WORKFLOW.md Appointment Workflow |
| BR-APT-005 | Completed appointments cannot return to previous status. | Critical | WORKFLOW.md Appointment Workflow |
| BR-APT-006 | Cancelled appointments cannot be reactivated; a new appointment must be created. | High | WORKFLOW.md Appointment Workflow |
| BR-APT-007 | Rescheduling is only allowed for appointments not in Examining or Completed status. | High | WORKFLOW.md Reschedule Workflow |
| BR-APT-008 | The original slot must be released immediately after reschedule confirmation. | High | WORKFLOW.md Reschedule Workflow |
| BR-APT-009 | The customer must be notified of appointment reschedule. | Medium | WORKFLOW.md Reschedule Workflow |
| BR-APT-010 | Double-booking a doctor in the same time slot is not allowed. | Critical | Audit finding |
| BR-APT-011 | Check-in is only allowed for Confirmed appointments or Walk-ins. | High | WORKFLOW.md Check-In Workflow |
| BR-APT-012 | A pet must be registered before check-in. | High | WORKFLOW.md Check-In Workflow |
| BR-APT-013 | Multi-pet appointments are not supported; one appointment serves one pet. | Medium | Audit finding |
| BR-APT-014 | Emergency visits follow the walk-in path with priority queue handling. | Medium | Audit finding |

---

## Queue Management

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-QUE-001 | Queue states are: Waiting, Called, Examining, Completed. | Critical | WORKFLOW.md Queue Workflow |
| BR-QUE-002 | Queue order should be chronological. | High | WORKFLOW.md Queue Workflow |
| BR-QUE-003 | Doctor can only examine patients marked as Called. | Critical | WORKFLOW.md Queue Workflow |
| BR-QUE-004 | Called patients must be examined before the next patient can be Called. | High | WORKFLOW.md Queue Workflow |
| BR-QUE-005 | Checked In is a pre-queue state, not a queue state. | High | Audit finding |

---

## Medical Records

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-MED-001 | Medical records cannot be deleted. | Critical | WORKFLOW.md Medical Record Workflow |
| BR-MED-002 | Medical records should support version history. | High | WORKFLOW.md Medical Record Workflow |
| BR-MED-003 | Every modification to a medical record must be logged in the audit log. | Critical | WORKFLOW.md Medical Record Workflow |
| BR-MED-004 | A medical record is created for every completed appointment. | High | WORKFLOW.md Medical Record Workflow |
| BR-MED-005 | A follow-up appointment is automatically created when follow_up_date is provided. | High | WORKFLOW.md Medical Record Workflow |
| BR-MED-006 | SOAP notes must include Subjective, Objective, Assessment, and Plan. | High | PROJECT_SPECIFICATION.md |
| BR-MED-007 | Vital signs must include temperature, heart rate, respiratory rate, weight, and body condition score. | High | GLOSSARY.md |
| BR-MED-008 | Diagnoses can be primary or secondary. | Medium | ERD.md Medical Module |
| BR-MED-009 | Treatments recorded in medical records can generate invoice line items. | High | WORKFLOW.md Medical Record Workflow |

---

## Prescription and Pharmacy

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-RX-001 | A medical record can have multiple prescriptions. | Critical | ERD.md Cardinality |
| BR-RX-002 | Prescription items reference inventory_items, not a separate medicine table. | Critical | Audit finding |
| BR-RX-003 | Medicine cannot be dispensed if stock is insufficient. | Critical | WORKFLOW.md Prescription Workflow |
| BR-RX-004 | Inventory movement must always be recorded when medicine is dispensed. | Critical | WORKFLOW.md Prescription Workflow |
| BR-RX-005 | Prescription items must include dosage, frequency, duration, quantity, and instructions. | High | ERD.md Pharmacy Module |
| BR-RX-006 | Drug interactions and known allergies should be checked before dispensing. | High | Audit finding |
| BR-RX-007 | Prescriptions require a valid medical record. | High | DATABASE.md Constraints |

---

## Inventory Management

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-INV-001 | Inventory items can be medicine, product, or supply. | Critical | Audit finding |
| BR-INV-002 | Inventory must never be updated directly; every change creates an inventory_movement record. | Critical | DATABASE.md Inventory Ledger |
| BR-INV-003 | Stock must never become negative. | Critical | WORKFLOW.md Inventory Workflow |
| BR-INV-004 | Every stock change creates an inventory movement record. | Critical | WORKFLOW.md Inventory Workflow |
| BR-INV-005 | Inventory movements are append-only and cannot be deleted. | Critical | DATABASE.md Soft Deletes |
| BR-INV-006 | Current stock is calculated from movements or updated transactionally. | High | DATABASE.md Inventory Ledger |
| BR-INV-007 | Minimum stock alerts must be generated when stock falls below minimum_stock. | High | PROJECT_SPECIFICATION.md |
| BR-INV-008 | Near-expired alerts must be generated for medicines approaching expiration. | High | PROJECT_SPECIFICATION.md |
| BR-INV-009 | Non-medicine inventory items do not require an expired_date. | Medium | DATABASE.md Constraints |
| BR-INV-010 | Inventory item type must be validated at the application level. | High | DATABASE.md Database Rules |

---

## Stock Adjustment

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-ADJ-001 | Adjustments must always be justified and approved by a supervisor or owner. | High | WORKFLOW.md Stock Adjustment Workflow |
| BR-ADJ-002 | Every adjustment creates an inventory_movement record with type Adjustment. | Critical | WORKFLOW.md Stock Adjustment Workflow |
| BR-ADJ-003 | Negative stock is never allowed after adjustment. | Critical | WORKFLOW.md Stock Adjustment Workflow |

---

## Purchase Orders

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-PO-001 | Partial receipts must be supported. | High | WORKFLOW.md Purchase Order Workflow |
| BR-PO-002 | Received quantity cannot exceed ordered quantity without approval. | High | WORKFLOW.md Purchase Order Workflow |
| BR-PO-003 | Every receipt creates a StockIn movement. | Critical | WORKFLOW.md Purchase Order Workflow |
| BR-PO-004 | Suppliers with active purchase orders cannot be deleted. | High | WORKFLOW.md Supplier Management |

---

## POS and Invoicing

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-POS-001 | Invoices can be generated from appointments, grooming bookings, or hotel bookings via polymorphic invoiceable. | Critical | Audit finding |
| BR-POS-002 | Invoice items can represent services or inventory items. | High | ERD.md POS Module |
| BR-POS-003 | Paid invoices cannot be modified. | Critical | WORKFLOW.md POS Workflow |
| BR-POS-004 | Draft invoices can be modified before payment. | High | WORKFLOW.md POS Workflow |
| BR-POS-005 | Void requires Owner permission. | Critical | WORKFLOW.md POS Workflow |
| BR-POS-006 | Invoice total cannot be negative. | High | DATABASE.md Constraints |
| BR-POS-007 | Discount and tax must be calculated before grand total. | High | WORKFLOW.md POS Workflow |
| BR-POS-008 | Receipts are generated only after successful payment. | High | WORKFLOW.md POS Workflow |

---

## Payments

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-PAY-001 | Payment statuses are: Pending, Paid, Failed, Refunded, Voided. | Critical | DATABASE.md Status Management |
| BR-PAY-002 | Every payment must have an audit log. | Critical | WORKFLOW.md Payment Workflow |
| BR-PAY-003 | Failed payments can be retried. | High | WORKFLOW.md Payment Workflow |
| BR-PAY-004 | Payment cannot exceed invoice total. | Critical | DATABASE.md Constraints |
| BR-PAY-005 | Refunds require Owner approval. | Critical | WORKFLOW.md Refund Workflow |
| BR-PAY-006 | Refund amount cannot exceed the original payment amount. | High | WORKFLOW.md Refund Workflow |
| BR-PAY-007 | Voided payments require Owner approval and must reverse the invoice status. | Critical | WORKFLOW.md Payment Workflow |
| BR-PAY-008 | Refunded invoices cannot be modified. | High | WORKFLOW.md Refund Workflow |

---

## Invoice Void

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-VOID-001 | Void requires Owner permission. | Critical | WORKFLOW.md Void Invoice Workflow |
| BR-VOID-002 | Voided invoices must reverse related inventory consumption. | High | WORKFLOW.md Void Invoice Workflow |
| BR-VOID-003 | Voided invoices cannot be reactivated. | High | WORKFLOW.md Void Invoice Workflow |

---

## Grooming

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-GRM-001 | Grooming bookings generate invoices through the polymorphic invoice system. | High | WORKFLOW.md Grooming Workflow |
| BR-GRM-002 | Services must be defined in the service catalog before booking. | High | WORKFLOW.md Grooming Workflow |
| BR-GRM-003 | Grooming statuses are: Booked, CheckedIn, InProgress, Completed, Cancelled. | High | DATABASE.md Status Management |

---

## Pet Hotel

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-HOT-001 | Hotel bookings generate invoices through the polymorphic invoice system. | High | WORKFLOW.md Pet Hotel Workflow |
| BR-HOT-002 | Daily charges are accumulated during the stay. | High | WORKFLOW.md Pet Hotel Workflow |
| BR-HOT-003 | Hotel booking statuses are: Reserved, CheckedIn, Active, CheckedOut, Cancelled. | High | DATABASE.md Status Management |
| BR-HOT-004 | A room cannot be double-booked for overlapping dates. | Critical | Audit finding |

---

## Vaccination

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-VAC-001 | Next vaccination date is automatically calculated based on vaccine protocol. | High | WORKFLOW.md Vaccination Workflow |
| BR-VAC-002 | Vaccination history is linked to the pet. | High | WORKFLOW.md Vaccination Workflow |
| BR-VAC-003 | Vaccination certificates must include certificate number and batch number. | Medium | ERD.md Vaccination Module |
| BR-VAC-004 | Vaccination schedules should consider species and breed-specific protocols. | Medium | Audit finding |

---

## Inpatient

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-INP-001 | Discharge is only allowed when clinical criteria are met. | High | WORKFLOW.md Inpatient Workflow |
| BR-INP-002 | Daily monitoring must be recorded at least once per day. | High | WORKFLOW.md Inpatient Workflow |
| BR-INP-003 | Medications dispensed during inpatient care reduce inventory. | High | WORKFLOW.md Inpatient Workflow |
| BR-INP-004 | Inpatient statuses are: Admitted, UnderTreatment, Discharged. | High | DATABASE.md Status Management |

---

## User Management

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-USR-001 | Only Owner can manage users. | Critical | WORKFLOW.md User Management |
| BR-USR-002 | Roles are: Owner, Doctor, Staff, Customer. | High | PROJECT_SPECIFICATION.md |
| BR-USR-003 | Each role has separate permissions. | High | PROJECT_SPECIFICATION.md |
| BR-USR-004 | Deactivated users cannot log in but their historical actions remain in audit logs. | High | WORKFLOW.md User Management |
| BR-USR-005 | Permissions are managed through the role_permission pivot. | High | ERD.md Identity Module |

---

## Audit Log

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-AUD-001 | Audit logs must never be deleted. | Critical | DATABASE.md Soft Deletes |
| BR-AUD-002 | Sensitive actions must capture user, timestamp, before/after data, IP address, and user agent. | Critical | WORKFLOW.md Audit Log Workflow |
| BR-AUD-003 | Audit log examples include delete, update medical record, stock adjustment, invoice void, user permission change, and payment refund. | High | WORKFLOW.md Audit Log Workflow |

---

## Data Retention and Compliance

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-DAT-001 | Medical records must be retained permanently. | Critical | DATABASE.md Medical Records |
| BR-DAT-002 | Audit logs must be retained permanently. | Critical | DATABASE.md Audit Log |
| BR-DAT-003 | Customer PII retention must comply with applicable privacy regulations. | High | Audit finding |
| BR-DAT-004 | Financial records must be retained according to local tax and accounting laws. | High | Audit finding |

---

## Concurrency and Integrity

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-CON-001 | Critical workflows must be wrapped in database transactions. | Critical | DATABASE.md Transactions |
| BR-CON-002 | If any step in a critical workflow fails, the entire transaction must roll back. | Critical | DATABASE.md Transactions |
| BR-CON-003 | Concurrent check-in of the same appointment must be prevented. | High | Audit finding |
| BR-CON-004 | Race conditions in inventory reduction must be prevented. | Critical | Audit finding |

---

## Error Handling

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-ERR-001 | Never allow incomplete workflows. | Critical | WORKFLOW.md Error Handling |
| BR-ERR-002 | Never skip required status transitions. | Critical | WORKFLOW.md Error Handling |
| BR-ERR-003 | Never leave orphan records. | Critical | WORKFLOW.md Error Handling |
| BR-ERR-004 | Always display meaningful error messages. | High | WORKFLOW.md Error Handling |

---

## Integration

| ID | Rule | Severity | Source |
|---|---|---|---|
| BR-INT-001 | Every module must integrate automatically. | Critical | WORKFLOW.md Integration Rules |
| BR-INT-002 | One action should trigger all related processes. | High | WORKFLOW.md Integration Rules |
| BR-INT-003 | Avoid duplicate data entry across modules. | Critical | WORKFLOW.md Integration Rules |

---

## Golden Rule

Every feature must support the complete clinic operation from patient arrival until payment without requiring duplicate input.