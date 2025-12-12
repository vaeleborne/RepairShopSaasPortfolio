# Repair Shop SaaS – Architecture & Data Model Portfolio

## Overview

**Repair Shop SaaS** is a multi-tenant platform concept designed for independent and franchise repair shops specializing in:

- Cell phones
- Tablets
- Laptops & desktops
- Game consoles and similar consumer electronics

This repository is a **portfolio and architecture showcase** demonstrating system design, database modeling, and domain-driven thinking for a production-grade SaaS application.

> **Note:**  
> This repository contains **architecture and data model documentation only**.  
> The production source code is intentionally not included and remains proprietary.

---

## Why This Project Exists

Before pursuing software engineering, I worked as a repair technician in consumer electronics repair shops that used established POS, inventory, and ticketing systems.

While these systems were functional and far better than ad-hoc solutions, day-to-day work still exposed gaps between how software models repairs and how repairs actually happen. Real workflows often involve edge cases such as partial repairs, warranty work, special orders tied to specific tickets, inter-location transfers, refurbished device sales, and customer approvals that evolve over time.

This project was created to explore how a repair shop SaaS could be designed from the ground up with those realities in mind, emphasizing clear domain boundaries, normalized data modeling, and multi-tenant scalability.

In addition to data modeling, the system is designed around thoughtful, role-aware APIs that serve the distinct needs of owners, managers, technicians, front-desk staff, and customers, while maintaining clear access boundaries and auditability.

Rather than focusing on UI or implementation details, this repository documents the system design and data model needed to support complex, real-world operations in a maintainable and extensible way. The goal is to demonstrate how practical domain experience can inform thoughtful backend and system architecture.

## What This Project Is Not

This project is not a finished commercial product or a production-ready SaaS application.

It does not include a user interface, live backend services, or deployment infrastructure. Instead, it focuses on the underlying system design, domain modeling, and data architecture that would support such implementations.

This repository is also not an attempt to recreate or compete directly with any existing repair shop POS or management software. The goal is not feature parity, but thoughtful exploration of how complex, real-world repair workflows can be modeled cleanly and extensibly.

Finally, this project is not opinionated about frontend frameworks or API technologies. While the data model is intentionally designed to support well-defined APIs for technicians, managers, owners, and customers, the implementation of those interfaces is outside the scope of this repository.



## Goals of This Project

This project was designed with two primary goals:

1. **Professional Portfolio Demonstration**
   - Showcase enterprise-level data modeling
   - Demonstrate SaaS multi-tenancy
   - Highlight real-world repair shop workflows
   - Show thoughtful separation of concerns across modules

2. **Future Commercial Viability**
   - Designed so the system *could* be offered as a SaaS product
   - Attention paid to extensibility, auditing, permissions, and compliance
   - Intellectual property intentionally protected

---

## Target Users

- Independent repair shops
- Multi-location repair chains
- Franchise owners
- Repair technicians
- Front-desk staff
- Store managers
- Customers (via optional customer-facing portal)

---

## Core Functional Areas

The Repair Shop SaaS is organized into the following functional modules:

### 1. Multi-Tenant SaaS Foundation
- Tenant-based isolation
- Multiple locations per tenant
- Tenant subscription plans & limits
- Role-based access control (RBAC)

### 2. Customer & Device Intake
- Customer profiles with multiple contact methods
- Device tracking (IMEI, serial, model, ownership)
- Repair history per device and per customer
- Optional customer portal accounts

### 3. Repair Ticketing System
- Repair tickets with lifecycle states
- Multiple repairs per ticket
- Warranty and diagnostic workflows
- Technician assignment & time tracking
- Attachments, notes, and audit history

### 4. Inventory Management
- Parts, accessories, tools, and consumables
- Per-location stock tracking
- Stock adjustments with full audit trail
- Part compatibility by device model

### 5. Purchasing & Vendor Management
- Vendors with per-location accounts
- Purchase orders and receipts
- Vendor price comparison & history
- Returns, buybacks, and recycling workflows

### 6. Sales, Invoicing & Payments
- Unified invoice system for repairs and retail sales
- Line-item tax handling by category and location
- Multiple payment methods & processors
- Store credit accounts and transactions

### 7. Inter-Location Transfers
- Device transfers between locations
- Part transfers between stores
- Status tracking and condition logging

### 8. Scheduling & Customer Portal
- Online and in-store appointments
- Repair selection during booking
- Notifications and reminders
- Customer feedback collection

### 9. Marketing & Promotions
- Email lists and campaigns
- Promotions and discounts
- Subscription plans (e.g., device protection plans)
- Opt-in marketing compliance

### 10. Management & Auditing
- Employee roles and specialties
- Location hours & closures
- Full audit logging for sensitive actions
- Permission-based access controls

---

## Database Design Philosophy

This system is designed with:

- **Third Normal Form (3NF)** for transactional integrity
- Explicit junction tables for many-to-many relationships
- Immutable history tables for auditing and compliance
- Clear separation between:
  - Operational data
  - Reference data
  - Configuration data
- Location-aware pricing, inventory, and taxation
- Tenant-safe foreign key enforcement

The schema intentionally reflects **real-world operational complexity** rather than simplified CRUD examples.

---

## Repository Contents

### Included
- System architecture documentation
- Module breakdowns
- Database schema definitions
- Entity-Relationship Diagrams (ERDs)
- Design rationale and trade-offs

### Not Included
- Production source code
- Deployment configurations
- Secrets or credentials
- Proprietary business logic

---

## Technology Assumptions (Conceptual)

While implementation-agnostic, the design assumes:

- Relational database (PostgreSQL / Oracle)
- REST or GraphQL API layer
- Role-based authorization
- External payment processors (e.g., Stripe, Square)
- Email/SMS notification services
- Object storage for attachments

---

## Intended Audience

This repository is intended for:

- Hiring managers
- Technical interviewers
- Software engineers reviewing system design
- Architects evaluating data modeling skills

It is **not intended** as an open-source starter kit or production deployment.

---

## Intellectual Property Notice

© 2025 Dylan Hawke. All rights reserved.

This repository is provided for **demonstration and educational purposes only**.  
No permission is granted to use this material for commercial purposes.

---

## Contact

If you are reviewing this repository as part of a hiring process and would like to discuss implementation details, trade-offs, or design decisions, I am happy to walk through them in detail.
