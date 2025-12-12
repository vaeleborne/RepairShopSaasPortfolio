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
> The production source code is proprietary and intentionally not included.

---

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

The platform is organized into the following functional modules:

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
