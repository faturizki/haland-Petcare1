---
title: Haland Petcare Testing Strategy
version: 1.0.0
last_modified: 2026-06-29
owner: Lead Architect
status: Draft
depends_on: [PROJECT_SPECIFICATION.md, WORKFLOW.md, DATABASE.md, ERD.md, BUSINESS_RULES.md, SECURITY.md]
referenced_by: [PROJECT_SPECIFICATION.md, DEPLOYMENT.md, DEVELOPMENT_STANDARDS.md]
---

# Haland Petcare Testing Strategy

## Related Documents
- [PROJECT_SPECIFICATION.md](PROJECT_SPECIFICATION.md) — Master project specification
- [WORKFLOW.md](WORKFLOW.md) — Complete business workflows
- [DATABASE.md](DATABASE.md) — Database architecture and rules
- [ERD.md](ERD.md) — Entity relationship diagram
- [BUSINESS_RULES.md](BUSINESS_RULES.md) — Centralized business rules catalog
- [SECURITY.md](SECURITY.md) — Security specification
- [GLOSSARY.md](GLOSSARY.md) — Domain terminology

---

## Overview

This document defines the testing strategy for Haland Petcare.

The goal is to ensure the system is reliable, secure, and meets all business requirements before deployment.

Testing is not optional. Every critical workflow must be covered by automated tests.

---

## Test Pyramid

| Level | Percentage | Tools |
|---|---|---|
| Unit Tests | 30% | PHPUnit |
| Feature Tests | 50% | PHPUnit with Laravel TestCase |
| Integration Tests | 15% | PHPUnit, Database transactions |
| E2E Tests | 5% | Laravel Dusk or Playwright (future) |

---

## Testing Principles

- Tests must be deterministic.
- Tests must run in isolation.
- Use database transactions to reset state between tests.
- Never depend on external services in unit or feature tests.
- Mock external APIs and notifications.
- Every bug fix must include a regression test.

---

## Critical Workflow Test Cases

### TC-001: Complete Patient Visit
**Objective:** Verify a patient can be registered, examined, prescribed, invoiced, and paid without duplicate input.

**Steps:**
1. Create customer and pet.
2. Create appointment.
3. Check in appointment.
4. Doctor examines patient and creates medical record.
5. Doctor adds diagnosis, treatment, and prescription.
6. System reduces inventory.
7. System generates invoice.
8. Staff processes payment.
9. Verify customer portal is updated.

**Expected Result:** All records linked; no duplicate data; inventory movement recorded; invoice paid.

---

### TC-002: Walk-In Appointment
**Objective:** Verify walk-in appointments skip Scheduled and Confirmed states.

**Steps:**
1. Customer arrives without appointment.
2. Staff creates walk-in appointment.
3. Staff checks in immediately.
4. Patient enters queue.

**Expected Result:** Appointment status transitions directly to CheckedIn.

---

### TC-003: Online Booking Confirmation
**Objective:** Verify online bookings require confirmation.

**Steps:**
1. Customer books via portal.
2. Appointment status is Scheduled.
3. Staff confirms appointment.
4. Customer checks in on arrival.

**Expected Result:** Status transitions: Scheduled → Confirmed → CheckedIn.

---

### TC-004: Prescription Stock Validation
**Objective:** Verify medicine cannot be dispensed with insufficient stock.

**Steps:**
1. Create prescription for inventory item with zero stock.
2. Attempt to save prescription.

**Expected Result:** System rejects with meaningful error; no inventory movement created.

---

### TC-005: Invoice Void Reverses Inventory
**Objective:** Verify voiding an invoice reverses consumed inventory.

**Steps:**
1. Create paid invoice with prescription items.
2. Owner requests void.
3. System voids invoice.
4. System creates reversing inventory movement.

**Expected Result:** Inventory balance restored; invoice status Voided; audit log created.

---

### TC-006: Refund Workflow
**Objective:** Verify refund requires Owner approval and updates invoice status.

**Steps:**
1. Create paid invoice.
2. Request refund.
3. Non-owner attempts approval (should fail).
4. Owner approves refund.
5. Process refund payment.

**Expected Result:** Invoice status Refunded; audit log created.

---

### TC-007: Stock Adjustment Approval
**Objective:** Verify stock adjustments require approval and create audit logs.

**Steps:**
1. Identify inventory discrepancy.
2. Staff requests adjustment.
3. Supervisor approves.
4. System adjusts stock and records movement.

**Expected Result:** Stock updated; movement record created; audit log captured.

---

### TC-008: Purchase Order Receipt
**Objective:** Verify PO receipt creates stock-in movement.

**Steps:**
1. Create PO with inventory items.
2. Receive partial quantity.
3. Verify stock increased.
4. Verify movement record created.

**Expected Result:** Stock reflects received quantity; PO status updated.

---

### TC-009: Appointment Reschedule
**Objective:** Verify reschedule releases old slot and updates status.

**Steps:**
1. Create confirmed appointment.
2. Reschedule to new slot.
3. Verify old slot is free.
4. Verify new slot is occupied.

**Expected Result:** Appointment updated; notification sent.

---

### TC-010: Follow-Up Appointment Generation
**Objective:** Verify follow-up appointment is auto-generated.

**Steps:**
1. Doctor creates medical record with follow_up_date.
2. Save record.
3. Verify appointment created for follow-up date.

**Expected Result:** Follow-up appointment linked to pet and customer.

---

## Inventory Accuracy Tests

| ID | Test | Expected Result |
|---|---|---|
| INV-001 | Stock cannot become negative. | Rejected with error. |
| INV-002 | Every stock change creates movement. | inventory_movements count increases. |
| INV-003 | Current stock equals sum of movements. | Calculation matches. |
| INV-004 | Soft-deleted inventory item cannot be sold. | Rejected. |
| INV-005 | Expired medicine alert triggers. | Alert generated. |
| INV-006 | Minimum stock alert triggers. | Alert generated. |

---

## Medical Record Integrity Tests

| ID | Test | Expected Result |
|---|---|---|
| MED-001 | Medical record cannot be deleted. | Soft delete not allowed; RESTRICT. |
| MED-002 | Medical record update creates audit log. | audit_logs entry exists. |
| MED-003 | Medical record version history preserved. | Previous version retrievable. |
| MED-004 | Prescription linked to medical record. | FK constraint enforced. |
| MED-005 | Follow-up date generates appointment. | appointments entry exists. |

---

## POS Calculation Tests

| ID | Test | Expected Result |
|---|---|---|
| POS-001 | Subtotal = sum of line items. | Calculation correct. |
| POS-002 | Grand total = subtotal - discount + tax. | Calculation correct. |
| POS-003 | Payment cannot exceed grand total. | Rejected. |
| POS-004 | Paid invoice cannot be modified. | Rejected. |
| POS-005 | Draft invoice can be modified. | Allowed. |
| POS-006 | Tax calculation uses settings tax_rate. | Correct percentage applied. |

---

## Concurrency Tests

| ID | Test | Expected Result |
|---|---|---|
| CON-001 | Two simultaneous check-ins for same appointment. | Only one succeeds. |
| CON-002 | Two simultaneous inventory reductions for last item. | Only one succeeds; stock not negative. |
| CON-003 | Simultaneous payment attempts on same invoice. | Only one succeeds; no overpayment. |

---

## Security Tests

| ID | Test | Expected Result |
|---|---|---|
| SEC-001 | Customer cannot access another customer's data. | 403 Forbidden. |
| SEC-002 | Staff cannot void invoice. | 403 Forbidden. |
| SEC-003 | Doctor cannot access revenue reports. | 403 Forbidden. |
| SEC-004 | CSRF token required for state changes. | 419 Page Expired. |
| SEC-005 | SQL injection attempt blocked. | Input sanitized; no error. |
| SEC-006 | XSS attempt escaped in output. | Script not executed. |
| SEC-007 | Audit log captures sensitive action. | Entry exists. |

---

## Performance Tests

| ID | Test | Target |
|---|---|---|
| PERF-001 | Dashboard loads within 2 seconds. | < 2s |
| PERF-002 | Appointment list with 10,000 records paginates. | < 1s |
| PERF-003 | No N+1 queries on medical record detail. | 0 N+1 |
| PERF-004 | Inventory report generation under 5 seconds. | < 5s |

---

## UAT Criteria

The system is ready for User Acceptance Testing when:

- All critical workflow tests pass.
- All security tests pass.
- All POS calculation tests pass.
- No critical or high-severity bugs remain open.
- UI matches UI_GUIDELINES.md.
- Documentation is updated and consistent.

---

## Test Data Management

- Use factories and seeders for test data.
- Never use production data in tests.
- Reset database between test suites.
- Maintain a set of representative test scenarios:
  - Single-pet customer
  - Multi-pet customer
  - Walk-in emergency
  - Online booking
  - Grooming package
  - Hotel stay
  - Inpatient admission

---

## Regression Policy

- Every reported bug must have a regression test before fix is accepted.
- Regression tests must fail before the fix and pass after the fix.
- Regression tests are added to the permanent test suite.

---

## Continuous Testing

- All tests run on every pull request.
- Feature tests run in CI using SQLite or MySQL test database.
- Security tests run on every deployment candidate.
- Performance tests run before release.

---

## Golden Rule

If a workflow is not tested, it is not trusted. Every critical path must be covered by automated tests before release.