

Haland Petcare UI/UX Guidelines

Version: 1.0.0

---

Design Philosophy

Haland Petcare is an enterprise veterinary clinic management system.

The UI must prioritize:

- Speed
- Clarity
- Consistency
- Readability
- Productivity

This is NOT a marketing website.

Avoid decorative design.

Avoid excessive animations.

Avoid unnecessary visual effects.

Users should complete tasks with the fewest clicks possible.

---

Design Principles

Every screen should be:

Simple

Clean

Modern

Professional

Predictable

Responsive

Accessible

Fast

Minimal

Every page should look like it belongs to the same application.

---

Inspiration

Use these products as inspiration.

Linear

Stripe Dashboard

GitHub

Notion

Supabase Dashboard

Vercel Dashboard

Avoid designs similar to consumer social media applications.

---

Layout Structure

Every page must use the same layout.

Top Navigation

Sidebar

Breadcrumb

Page Header

Action Toolbar

Main Content

Footer (optional)

Never change navigation position.

Consistency is more important than creativity.

---

Sidebar

Sidebar must contain:

Dashboard

Appointments

Customers

Pets

Medical Records

Inventory

Pharmacy

POS

Grooming

Pet Hotel

Vaccination

Inpatient

Reports

Users

Settings

Collapsed sidebar should be supported.

Active page should always be highlighted.

---

Top Navigation

Contains:

Search

Notifications

Clinic Switch (future)

Profile Menu

Theme Switch

Never overload the top bar.

---

Page Header

Every page should include:

Title

Subtitle

Breadcrumb

Primary Action Button

Secondary Actions

Example

Customer Management

Manage customer information and pets.

[ Add Customer ]

---

Cards

Use cards consistently.

Cards should contain:

Title

Optional Description

Body

Optional Footer

Avoid deep shadows.

Use subtle borders.

---

Tables

Tables are primary UI components.

Every table should support:

Search

Sorting

Filtering

Pagination

Column Visibility (future)

CSV Export

Bulk Actions (when necessary)

Row Actions

Never overload tables.

---

Forms

Forms should be clean.

Prefer one-column layouts.

Group related fields.

Always display labels.

Never rely on placeholders as labels.

Display validation messages below fields.

Disable submit button while saving.

Show loading indicators.

---

Buttons

Button hierarchy

Primary

Secondary

Outline

Ghost

Danger

Avoid too many button styles.

Use one primary action per page.

---

Modal

Use modal only for:

Confirmation

Small Forms

Delete Confirmation

Quick Edit

Never place large forms inside modal.

---

Drawer

Use drawers for:

Details

Preview

History

Timeline

Avoid full-screen drawers unless necessary.

---

Tabs

Use tabs for complex modules.

Medical Record

SOAP

Vital Signs

Diagnosis

Prescription

History

Pet Profile

General

Vaccination

Medical History

Attachments

---

Dashboard

Dashboard should prioritize information.

Cards

Charts

Recent Activity

Quick Actions

Alerts

Never hide important metrics.

---

Dashboard Widgets

Owner

Revenue Today

Revenue Month

Appointments Today

Low Stock

Active Inpatients

Best Selling Services

Doctor Productivity

Staff

Today's Queue

Walk In

Pending Payments

Low Stock

Doctor

Today's Queue

Current Patient

Completed Today

Upcoming Appointments

Customer

Upcoming Appointment

Medical History

Vaccination Reminder

Invoices

---

Colors

Primary

Blue

Success

Green

Warning

Orange

Danger

Red

Neutral

Gray

Background

White

Avoid excessive colors.

Status colors should remain consistent.

---

Typography

Use Inter.

Hierarchy

H1

H2

H3

Body

Caption

Use readable font sizes.

Avoid small text.

---

Icons

Use Heroicons only.

Never mix icon libraries.

Icons should always accompany navigation items.

---

Spacing

Use an 8px spacing system.

Examples

8

16

24

32

48

64

Avoid inconsistent spacing.

---

Border Radius

Small

Medium

Never use exaggerated rounded corners.

---

Shadows

Minimal.

Prefer borders over shadows.

---

Empty States

Every module should have an empty state.

Include:

Illustration (optional)

Description

Primary Action

Example

No appointments found.

Create the first appointment.

[ Create Appointment ]

---

Loading States

Never display blank pages.

Use:

Skeleton Loader

Loading Spinner

Progress Bar (when appropriate)

---

Notifications

Use toast notifications.

Success

Error

Warning

Info

Auto dismiss after a few seconds.

Critical errors should require user acknowledgement.

---

Status Badges

Use consistent badge colors.

Scheduled

Blue

Checked In

Purple

Examining

Orange

Completed

Green

Cancelled

Red

Never invent new badge colors.

---

Timeline

Use timelines for:

Medical History

Audit Log

Vaccination

Hospitalization

Appointment History

Timeline should always be chronological.

---

POS Layout

POS must use two-column layout.

Left

Services

Products

Medicine

Search

Categories

Right

Invoice

Payment

Discount

Tax

Total

Process Payment

The cashier should complete payment with minimal clicks.

---

Medical Record Layout

Patient Summary

Tabs

SOAP

Vitals

Diagnosis

Treatment

Prescription

History

Always display previous records.

---

Accessibility

Keyboard navigation.

Visible focus.

Proper labels.

Proper contrast.

ARIA attributes where appropriate.

Never rely only on color.

---

Responsive Design

Desktop First.

Tablet supported.

Mobile supported.

Customer Portal must work perfectly on mobile.

Admin modules optimized for desktop.

---

Dark Mode

Support future dark mode.

Use semantic color tokens.

Never hardcode colors.

---

Component Library

Always reuse components.

Button

Input

Textarea

Select

Checkbox

Radio

Switch

Card

Badge

Alert

Toast

Table

Pagination

Modal

Drawer

Tabs

Avatar

Dropdown

Breadcrumb

Page Header

Stats Card

Chart Card

Timeline

Every component should have consistent spacing and styling.

---

Animations

Animations should be subtle.

Fade

Slide

Loading

Avoid bounce effects.

Avoid decorative animations.

Performance is more important than animation.

---

UX Principles

Minimize clicks.

Reduce typing.

Use autocomplete where possible.

Prefill known data.

Use inline validation.

Show clear success feedback.

Prevent user mistakes.

Never require duplicate input.

---

Golden Rule

The interface exists to help clinic staff complete work quickly and accurately.

Every UI decision must improve efficiency, reduce cognitive load, and support the clinical workflow.

Consistency is always more important than visual creativity.
