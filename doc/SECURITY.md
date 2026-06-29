---
title: Haland Petcare Security Specification
version: 1.0.0
last_modified: 2026-06-29
owner: Lead Architect
status: Draft
depends_on: [PROJECT_SPECIFICATION.md, WORKFLOW.md, DATABASE.md, ERD.md, BUSINESS_RULES.md]
referenced_by: [PROJECT_SPECIFICATION.md, TESTING_STRATEGY.md, DEPLOYMENT.md]
---

# Haland Petcare Security Specification

## Related Documents
- [PROJECT_SPECIFICATION.md](PROJECT_SPECIFICATION.md) — Master project specification
- [WORKFLOW.md](WORKFLOW.md) — Complete business workflows
- [DATABASE.md](DATABASE.md) — Database architecture and rules
- [ERD.md](ERD.md) — Entity relationship diagram
- [BUSINESS_RULES.md](BUSINESS_RULES.md) — Centralized business rules catalog
- [GLOSSARY.md](GLOSSARY.md) — Domain terminology

---

## Overview

This document defines the security architecture, access control, authentication, authorization, and data protection requirements for Haland Petcare.

Security is a first-class concern for any enterprise system handling PII, PHI, and financial data.

---

## Authentication

### Primary Mechanism
- Laravel Starter Kit (Livewire) provides the default authentication scaffolding.
- Session-based authentication for web users.
- Password hashing using bcrypt.

### Password Policy
- Minimum length: 12 characters
- Must contain uppercase, lowercase, number, and special character
- Cannot reuse the last 5 passwords
- Must be changed every 90 days for Owner, Doctor, and Staff roles
- Customers: minimum 8 characters, complexity recommended

### Session Management
- Session timeout after 30 minutes of inactivity
- Single session per user per device (optional configurable)
- Regenerate session ID after login
- Invalidate all sessions on password change
- Secure and HttpOnly cookies

### Account Lockout
- Lock account for 15 minutes after 5 failed login attempts
- Notify Owner of repeated failed login attempts

### Multi-Factor Authentication (Future)
- Support TOTP-based MFA for Owner and Doctor roles in future releases.

---

## Authorization

### Role-Based Access Control (RBAC)
- Users are assigned to Roles.
- Roles are granted Permissions.
- Permissions are enforced through Laravel Policies and Gates.

### Roles
- Owner
- Doctor
- Staff
- Customer

### Permission Naming Convention
- `{module}.{action}`

Examples:
- `revenue.view`
- `medical_record.create`
- `inventory.manage`
- `invoice.void`
- `user.manage`

---

## Access Control Matrix

| Permission | Owner | Doctor | Staff | Customer |
|---|---|---|---|---|
| **Dashboard** |
| dashboard.owner | ✅ | ❌ | ❌ | ❌ |
| dashboard.doctor | ✅ | ✅ | ❌ | ❌ |
| dashboard.staff | ✅ | ❌ | ✅ | ❌ |
| dashboard.customer | ✅ | ❌ | ❌ | ✅ |
| **Customer Management** |
| customer.view_all | ✅ | ✅ | ✅ | ❌ |
| customer.create | ✅ | ✅ | ✅ | ❌ |
| customer.update | ✅ | ✅ | ✅ | ❌ |
| customer.delete | ✅ | ❌ | ❌ | ❌ |
| customer.view_own | ❌ | ❌ | ❌ | ✅ |
| **Pet Management** |
| pet.view_all | ✅ | ✅ | ✅ | ❌ |
| pet.create | ✅ | ✅ | ✅ | ❌ |
| pet.update | ✅ | ✅ | ✅ | ❌ |
| pet.delete | ✅ | ❌ | ❌ | ❌ |
| pet.view_own | ❌ | ❌ | ❌ | ✅ |
| **Appointments** |
| appointment.view_all | ✅ | ✅ | ✅ | ❌ |
| appointment.create | ✅ | ✅ | ✅ | ❌ |
| appointment.update | ✅ | ✅ | ✅ | ❌ |
| appointment.cancel | ✅ | ✅ | ✅ | ❌ |
| appointment.book_online | ❌ | ❌ | ❌ | ✅ |
| appointment.view_own | ❌ | ❌ | ❌ | ✅ |
| **Queue** |
| queue.view | ✅ | ✅ | ✅ | ❌ |
| queue.call | ✅ | ✅ | ❌ | ❌ |
| queue.examine | ✅ | ✅ | ❌ | ❌ |
| **Medical Records** |
| medical_record.view_all | ✅ | ✅ | ❌ | ❌ |
| medical_record.create | ✅ | ✅ | ❌ | ❌ |
| medical_record.update | ✅ | ✅ | ❌ | ❌ |
| medical_record.view_own | ❌ | ❌ | ❌ | ✅ |
| **Prescriptions** |
| prescription.create | ✅ | ✅ | ❌ | ❌ |
| prescription.view | ✅ | ✅ | ❌ | ❌ |
| **Inventory** |
| inventory.view | ✅ | ❌ | ✅ | ❌ |
| inventory.manage | ✅ | ❌ | ✅ | ❌ |
| inventory.adjust | ✅ | ❌ | ✅ | ❌ |
| supplier.manage | ✅ | ❌ | ✅ | ❌ |
| purchase_order.manage | ✅ | ❌ | ✅ | ❌ |
| **POS / Invoicing** |
| invoice.view_all | ✅ | ❌ | ✅ | ❌ |
| invoice.create | ✅ | ❌ | ✅ | ❌ |
| invoice.void | ✅ | ❌ | ❌ | ❌ |
| payment.process | ✅ | ❌ | ✅ | ❌ |
| invoice.view_own | ❌ | ❌ | ❌ | ✅ |
| **Grooming** |
| grooming.view_all | ✅ | ❌ | ✅ | ❌ |
| grooming.book | ✅ | ❌ | ✅ | ❌ |
| grooming.complete | ✅ | ❌ | ✅ | ❌ |
| **Pet Hotel** |
| hotel.view_all | ✅ | ❌ | ✅ | ❌ |
| hotel.book | ✅ | ❌ | ✅ | ❌ |
| hotel.check_in | ✅ | ❌ | ✅ | ❌ |
| hotel.check_out | ✅ | ❌ | ✅ | ❌ |
| **Vaccination** |
| vaccination.view_all | ✅ | ✅ | ❌ | ❌ |
| vaccination.create | ✅ | ✅ | ❌ | ❌ |
| vaccination.view_own | ❌ | ❌ | ❌ | ✅ |
| **Inpatient** |
| inpatient.view_all | ✅ | ✅ | ❌ | ❌ |
| inpatient.create | ✅ | ✅ | ❌ | ❌ |
| inpatient.discharge | ✅ | ✅ | ❌ | ❌ |
| **Reports** |
| report.view_revenue | ✅ | ❌ | ❌ | ❌ |
| report.view_inventory | ✅ | ❌ | ✅ | ❌ |
| report.view_doctor_productivity | ✅ | ✅ | ❌ | ❌ |
| report.export | ✅ | ❌ | ✅ | ❌ |
| **User Management** |
| user.manage | ✅ | ❌ | ❌ | ❌ |
| role.manage | ✅ | ❌ | ❌ | ❌ |
| permission.manage | ✅ | ❌ | ❌ | ❌ |
| **Settings** |
| settings.manage | ✅ | ❌ | ❌ | ❌ |
| **Audit Log** |
| audit_log.view | ✅ | ❌ | ❌ | ❌ |

---

## Data Ownership Rules

- Customers can only access their own records and their own pets' records.
- Doctors can access medical records of patients they examine or are assigned to.
- Staff can access operational data required for their role but not financial summaries or user management.
- Owners can access all data but must still be logged for sensitive actions.

---

## Data Classification

| Classification | Examples | Handling Rules |
|---|---|---|
| **PII** | Customer name, phone, email, address, emergency contact | Encrypt at rest; restrict access; audit all reads |
| **PHI** | Medical records, diagnoses, treatments, prescriptions, vital signs | Encrypt at rest; never delete; audit all modifications |
| **Financial** | Invoices, payments, revenue, discounts, taxes | Encrypt at rest; only Owner and authorized Staff can modify |
| **Operational** | Appointments, queue, inventory levels, settings | Restrict by role; audit sensitive changes |
| **Authentication** | Passwords, session tokens, MFA secrets | Hash passwords; encrypt secrets; never log credentials |

---

## Data Protection

### Encryption at Rest
- Database backups encrypted.
- Sensitive fields (PII, PHI, financial) should use application-level encryption where required by regulation.

### Encryption in Transit
- TLS 1.2+ for all traffic.
- HSTS enabled.
- Secure cookies.

### Input Validation
- All user input validated through Laravel Form Requests.
- Whitelist validation for polymorphic types.
- Strict type validation for enums.

### Output Escaping
- All Blade output escaped by default.
- Use `{!! !!}` only for trusted, sanitized content.

### CSRF Protection
- CSRF tokens on all state-changing forms.
- Verify CSRF for Livewire actions.

### SQL Injection Prevention
- Use Eloquent or Query Builder with parameter binding.
- Never concatenate user input into raw SQL.

### XSS Prevention
- Escape output.
- Sanitize rich text if supported in future.
- Content Security Policy (CSP) headers.

---

## Audit and Logging

- All sensitive actions logged to audit_logs.
- Log entries include: user_id, action, model, model_id, old_values, new_values, ip_address, user_agent, created_at.
- Audit logs are immutable and cannot be deleted.
- Failed login attempts, permission changes, and data exports must be logged.

---

## API Security (Future)

When REST API is implemented:

- OAuth 2.0 / Laravel Sanctum for token-based authentication.
- Rate limiting per user/IP.
- Scope-based permissions.
- API keys rotated regularly.
- No sensitive data in URL parameters.

---

## Security Testing Requirements

- Authentication bypass tests.
- Authorization matrix tests for every permission.
- CSRF attempt tests.
- SQL injection attempt tests.
- XSS attempt tests.
- Session fixation/hijacking tests.
- Audit log completeness tests.
- Data isolation tests (Customer A cannot see Customer B data).

---

## Incident Response

- Security incidents must be reported to the Owner immediately.
- Compromised accounts must be deactivated and sessions invalidated.
- Database backups must be available for forensic recovery.
- Security fixes must be deployed through the CI/CD pipeline with approval.

---

## Compliance

- Follow applicable local data protection laws.
- Maintain records required for tax and accounting.
- Medical records retention must meet veterinary practice regulations.

---

## Golden Rule

Security is not a feature; it is a foundation. Every design decision must protect patient data, customer privacy, and financial integrity.