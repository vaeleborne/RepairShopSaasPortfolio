# Repair Shop SaaS – Data Model Documentation

## System Overview

This system is a multi-tenant, multi-location SaaS platform for repair shops focused on cellphones, tablets, computers, and similar devices.

Core workflows it supports:

- Customer intake & device check-in
- Repair ticket management (parts/labor/notes/photos)
- Customer communication & notifications
- Inventory, purchasing, and vendor management
- Sales of accessories, subscriptions, and refurbished devices
- Store credit, payments, invoices, and promotions
- Multi-store, multi-role access control and auditing
- Customer portal for appointments, ticket tracking, and feedback

Each **Tenant** represents a repair business or franchise.  
Each **Location** represents a physical store for that tenant.  
Users, customers, inventory, tickets, and orders are scoped by tenant and/or location.

---

## Module 1: SaaS, Tenants, Locations & Users

### Purpose
Provide multi-tenant, multi-location structure with user accounts, roles, permissions, and auditing.

### Key Entities

**Tenant**
- id
- name
- legal_name
- primary_contact_name
- primary_contact_email
- default_timezone_id (FK → Timezone)
- created_at
- tenant_status_id (FK → TenantStatus)
- tenant_plan_id (FK → TenantPlan)
- billing_external_id (e.g., Stripe)

**TenantPlan**
- id
- name (Standard, Pro, Multi-Store, etc)
- code
- monthly_price
- annual_price
- max_locations
- max_users
- max_sms_per_month
- is_active

**TenantStatus**
- id
- name (Active, Trial, Suspended, etc)
- code

**Timezone**
- id
- abbr
- name
- utc_offset

**Location**
- id
- tenant_id (FK)
- name ("Mall Kiosk", "Downtown", etc)
- code
- phone_number
- email
- address_line1
- address_line2
- zipcity_id (FK → ZipCity)
- state_id (FK → State)
- country_id (FK → Country)
- timezone_id (FK → Timezone)
- is_active

**User**
- id
- tenant_id (FK)
- email
- first_name
- last_name
- is_active
- created_at
- last_login_at

**UserLocationRole**
- user_id (FK → User)
- location_id (FK → Location)
- role_id (FK → Role)
- is_primary

**Role**
- id
- tenant_id (FK)
- name (Owner, Manager, Technician, FrontDesk, etc)
- code (OWNER, MNGR, TECH, etc)
- is_system_role (built-in/admin)
- is_active

**Permission**
- id
- code (VIEW_REPORTS, EDIT_INVENTORY, REFUND_INVOICE, etc)
- description

**RolePermission**
- id
- role_id (FK → Role)
- permission_id (FK → Permission)

**LocationHours**
- id
- tenant_id (FK)
- location_id (FK)
- day_of_week (0–6)
- open_time
- close_time
- is_closed

**LocationClosure**
- id
- tenant_id (FK)
- location_id (FK)
- start_date
- end_date
- reason (Holiday, Staff Meeting, etc)

**AuditLog**
- id
- tenant_id (FK)
- user_id (FK)
- entity_type (RepairTicket, Invoice, Part, etc)
- entity_id
- action (CREATE, UPDATE, DELETE, STATUS_CHANGE, etc)
- field_name (nullable)
- old_value (nullable)
- new_value (nullable)
- created_at
- ip_address (optional)

### Key Relationships

- Tenant 1─* Location  
- Tenant 1─* User  
- Tenant 1─* Role  
- Role *─* Permission (via RolePermission)  
- User *─* Location (via UserLocationRole)  
- TenantPlan 1─* Tenant  
- TenantStatus 1─* Tenant  

---

## Module 2: Customers, Contact & Portal

### Purpose
Manage customers, how to contact them, their addresses, accounts for the customer portal, and feedback.

### Key Entities

**Customer**
- id
- tenant_id (FK)
- first_name
- last_name
- notes
- marketing_opt_in (bool)
- marketing_opt_in_at (datetime)
- marketing_opt_out_at (nullable datetime)
- marketing_source_id (FK → MarketingSource)

**CustomerContactMethod**
- id
- customer_id (FK → Customer)
- contact_method_type_id (FK → ContactMethodType)
- value (phone/email/etc)
- is_primary

**ContactMethodType**
- id
- tenant_id (FK)
- code (PHONE, EMAIL, SMS, WHATSAPP, OTHER, etc)
- display_name
- is_active

**CustomerAddress**
- id
- tenant_id (FK)
- customer_id (FK)
- line1
- line2
- unit
- zipcity_id (FK → ZipCity)
- state_id (FK → State)
- country_id (FK → Country)
- address_type (Billing, Shipping, Both, Other)
- is_primary
- notes

**ZipCity**
- id
- zip_code
- city

**State**
- id
- name
- abbr

**Country**
- id
- name
- abbr

**CustomerAccount**
- id
- tenant_id (FK)
- customer_id (FK)
- auth_provider (LOCAL, GOOGLE, etc)
- username_or_email
- password_hash
- is_active
- created_at
- last_login_at

**CustomerFeedback**
- id
- tenant_id (FK)
- customer_id (FK)
- ticket_id (FK → RepairTicket)
- location_id (FK → Location)
- rating (1–5)
- comment
- would_recommend (bool)
- created_at

### Key Relationships

- Tenant 1─* Customer  
- Customer 1─* CustomerContactMethod  
- Customer 1─* CustomerAddress  
- Customer 1─0..1 CustomerAccount  
- Customer 1─* CustomerFeedback  
- RepairTicket 1─* CustomerFeedback  

---

## Module 3: Devices, Repairs & Tech Resources

### Purpose
Track devices, their acquisition and refurb status, repair tickets, repair catalog, and technician resources (notes, photos, guides, links).

### Key Entities

**DeviceType**
- id
- tenant_id (FK)
- name (Phone, Tablet, Laptop, Console, etc)
- sort_order

**Brand**
- id
- tenant_id (FK)
- name
- sort_order

**DeviceModel**
- id
- brand_id (FK → Brand)
- device_type_id (FK → DeviceType)
- model_name
- released_year

**RefurbStatus**
- id
- status (None, NeedsEvaluation, InRefurb, ReadyForSale, Sold)

**DeviceAcquisitionType**
- id
- code
- name (CustomerTradeIn, Wholesale, Other...)

**Device**
- id
- tenant_id (FK)
- customer_id (FK → Customer, nullable if tenant-owned)
- device_model_id (FK → DeviceModel)
- serial_number
- imei
- color
- capacity
- has_passcode
- notes
- ownership_type (Customer, Tenant)
- acquired_at
- acquisition_type_id (FK → DeviceAcquisitionType)
- acquisition_cost
- refurb_status_id (FK → RefurbStatus)
- for_sale (bool)

**ConditionGrade**
- id
- grade (A, B, C, PartsOnly, etc)

**TradeIn**
- id
- tenant_id (FK)
- location_id (FK)
- customer_id (FK → Customer)
- device_id (FK → Device)
- inspected_by_user_id (FK → User)
- condition_grade_id (FK → ConditionGrade)
- agreed_value (decimal)
- payout_method_id (FK → PayoutMethod)
- store_credit_transaction_id (FK → StoreCreditTransaction, nullable)
- payout_payment_id (FK → Payment, nullable)
- created_at
- notes

**RepairTicket**
- id
- tenant_id (FK)
- location_id (FK)
- work_location_id (FK → Location, nullable)
- customer_id (FK → Customer)
- device_id (FK → Device, nullable if sales only)
- status_id (FK → TicketStatus)
- created_at
- closed_at (nullable)
- last_customer_contact_at (nullable)
- summary
- assigned_tech_id (FK → User)
- diagnosis
- priority (Low, Normal, High, Rush)
- promise_time (nullable)
- ticket_source_type (CustomerRepair, InternalRepair, Warranty, Other)

**TicketStatus**
- id
- tenant_id (FK)
- name (New, Received, WaitingApproval, InProgress, Completed, WaitingForPickup, etc)
- sort_order
- is_active

**TicketStatusHistory**
- id
- tenant_id (FK)
- ticket_id (FK → RepairTicket)
- from_status_id (FK → TicketStatus)
- to_status_id (FK → TicketStatus)
- changed_by_user_id (FK → User)
- changed_at
- reason (optional text)

**TicketNote**
- id
- tenant_id (FK)
- ticket_id (FK → RepairTicket)
- created_by_user_id (FK → User)
- note_type (Internal, CustomerVisible)
- body
- created_at

**TicketAttachment**
- id
- tenant_id (FK)
- ticket_id (FK → RepairTicket)
- device_id (FK → Device, nullable)
- uploaded_by_user_id (FK → User)
- file_name
- file_size_bytes
- storage_url
- is_customer_visible (bool)
- created_at
- caption (optional)

**RepairType**
- id
- tenant_id (FK)
- name (Screen Repair, Battery, Diagnostics, etc)
- code (SCREEN, BATTERY, DIAG, etc)
- device_type_id (FK → DeviceType)
- is_diagnostic (bool)
- is_warranty_eligible (bool)
- default_minutes (nullable)
- default_service_definition_id (FK → ServiceDefinition, nullable)
- is_active

**DeviceModelRepairType**
- id
- tenant_id (FK)
- device_model_id (FK → DeviceModel)
- repair_type_id (FK → RepairType)
- is_common (bool)
- base_price
- base_minutes
- is_active

**LocationRepairPrice**
- id
- tenant_id (FK)
- location_id (FK)
- device_model_repair_type_id (FK → DeviceModelRepairType)
- override_price (nullable)
- override_minutes (nullable)
- is_active

**TicketRepair**
- id
- tenant_id (FK)
- location_id (FK)
- ticket_id (FK → RepairTicket)
- device_id (FK → Device)
- device_model_id (FK → DeviceModel)
- repair_type_id (FK → RepairType)
- device_model_repair_type_id (FK → DeviceModelRepairType)
- is_warranty (bool)
- warranty_type_id (FK → WarrantyType, nullable)
- status (Planned, WaitingApproval, Approved, InProgress, etc)
- quoted_price
- approved_price
- approved_at (nullable)
- approval_method (InPerson, Phone, Email, etc)
- notes
- created_at

**WarrantyType**
- id
- tenant_id (FK)
- name
- description
- is_active

**TicketLaborEntry**
- id
- tenant_id (FK)
- ticket_id (FK → RepairTicket)
- tech_user_id (FK → User)
- service_definition_id (FK → ServiceDefinition, nullable)
- minutes
- description
- created_at

**TicketPartUsage**
- id
- tenant_id (FK)
- ticket_id (FK → RepairTicket)
- part_id (FK → Part)
- quantity
- used_at
- notes

**RepairGuide**
- id
- tenant_id (FK)
- device_model_id (FK → DeviceModel, nullable)
- repair_type_id (FK → RepairType)
- device_model_repair_type_id (FK → DeviceModelRepairType)
- title
- body_markdown
- is_public_for_all_locations (bool)
- created_by_user_id (FK → User)
- created_at
- updated_at

**RepairResourceLink**
- id
- tenant_id (FK)
- device_model_id (FK → DeviceModel, nullable)
- repair_type_id (FK → RepairType, nullable)
- device_model_repair_type_id (FK → DeviceModelRepairType, nullable)
- title
- url
- resource_type_id (FK → ResourceType)
- is_official (bool)
- sort_order
- created_by_user_id (FK → User)
- created_at

**ResourceType**
- id
- abbr
- name (IFIXIT, VIDEO, PDF, etc)

### Key Relationships

- DeviceType 1─* DeviceModel  
- Brand 1─* DeviceModel  
- DeviceModel 1─* Device  
- Customer 1─* Device  
- Device 1─* RepairTicket  
- RepairTicket 1─* TicketNote, TicketAttachment, TicketRepair, TicketLaborEntry, TicketPartUsage  
- RepairType scoped to DeviceType  
- DeviceModel *─* RepairType via DeviceModelRepairType  
- LocationRepairPrice overrides pricing per location  
- TicketRepair ties together ticket, device, model, and repair type  
- TradeIn ties customer → device → payout (Payment / StoreCredit)  

---

## Module 4: Inventory, Vendors, Purchasing & Inter-location Transfers

### Purpose
Track parts and stock levels, vendor relationships, purchase orders & receipts, returns, and transfers between locations.

### Key Entities

**PartCategory**
- id
- name (Screen, Battery, Case, SIM, RefurbDevice, etc)

**Part**
- id
- tenant_id (FK)
- sku
- sn (optional)
- name
- category_id (FK → PartCategory)
- cost_price
- default_retail_price
- is_active
- tier (Economy, Premium, OEM, etc)
- is_sellable (bool)
- is_stock_tracked (bool)
- is_consumable (bool)
- is_tool (bool)

**PartCompatibleModel**
- id
- part_id (FK → Part)
- device_model_id (FK → DeviceModel)

**InventoryStock**
- id
- tenant_id (FK)
- location_id (FK)
- part_id (FK → Part)
- quantity_on_hand
- quantity_available
- last_counted_at

**StockAdjustment**
- id
- tenant_id (FK)
- location_id (FK)
- part_id (FK → Part)
- quantity_delta (positive or negative)
- adjustment_type (TicketUsage, VendorReturn, VendorBuyback, Recycling, etc)
- source_ticket_id (FK → RepairTicket, nullable)
- source_vendor_return_id (FK → VendorReturn, nullable)
- source_purchase_receipt_line_id (FK → PurchaseReceiptLine, nullable)
- created_at
- created_by_user_id (FK → User)
- notes

**Vendor**
- id
- tenant_id (FK)
- name
- vendor_code
- website_url
- notes
- vendor_type (Supplier, Recycler, Buyback, Mixed)
- is_active

**VendorAddress**
- id
- vendor_id (FK → Vendor)
- line1
- line2
- unit
- zipcity_id (FK → ZipCity)
- state_id (FK → State)
- country_id (FK → Country)
- type (Billing, Shipping, Both)

**VendorContactMethod**
- id
- vendor_id (FK → Vendor)
- contact_method_type_id (FK → ContactMethodType)
- value
- is_primary

**VendorLocationAccount**
- id
- vendor_id (FK → Vendor)
- location_id (FK → Location)
- account_number
- notes

**VendorPart**
- id
- tenant_id (FK)
- vendor_id (FK → Vendor)
- part_id (FK → Part)
- vendor_sku
- display_name
- cost_price
- currency_code
- pack_size
- min_order_qty
- lead_time_days
- is_preferred
- is_active

**VendorPriceHistory**
- id
- vendor_part_id (FK → VendorPart)
- effective_from
- effective_to (nullable)
- cost_price

**PurchaseOrder**
- id
- tenant_id (FK)
- location_id (FK)
- vendor_id (FK → Vendor)
- po_number (unique per tenant)
- status_id (FK → PurchaseOrderStatus)
- created_by_user_id (FK → User)
- created_at
- ordered_at
- expected_at
- received_at
- notes

**PurchaseOrderLine**
- id
- purchase_order_id (FK → PurchaseOrder)
- vendor_part_id (FK → VendorPart)
- part_id (FK → Part)
- ticket_id (FK → RepairTicket, nullable)
- description
- ordered_qty
- unit_cost
- line_total
- received_qty
- backordered_qty
- cancelled_qty
- sort_order

**PurchaseReceipt**
- id
- tenant_id (FK)
- location_id (FK)
- purchase_order_id (FK → PurchaseOrder)
- received_at
- received_by_user_id (FK → User)
- packing_slip_number
- notes

**PurchaseReceiptLine**
- id
- purchase_order_line_id (FK → PurchaseOrderLine)
- part_id (FK → Part)
- quantity_received
- quantity_accepted
- quantity_rejected
- rejection_reason (enum/text)

**VendorReturn**
- id
- tenant_id (FK)
- location_id (FK)
- vendor_id (FK → Vendor)
- return_type_id (FK → VendorReturnType)
- related_po_id (FK → PurchaseOrder, nullable)
- rma_number
- status_id (FK → VendorReturnStatus)
- created_by_user_id (FK → User)
- created_at
- shipped_at
- vendor_received_at
- expected_credit_amount
- actual_credit_amount
- notes

**VendorReturnLine**
- id
- vendor_return_id (FK → VendorReturn)
- part_id (FK → Part)
- purchase_order_line_id (FK → PurchaseOrderLine)
- purchase_receipt_line_id (FK → PurchaseReceiptLine)
- ticket_id (FK → RepairTicket)
- return_reason_id (FK → ReturnReason)
- quantity
- unit_cost_basis
- expected_unit_credit
- actual_unit_credit (nullable)
- line_expected_credit
- line_actual_credit
- notes

**VendorReturnType**
- id
- tenant_id (FK)
- name (Defective, Buyback, etc)
- code
- is_active

**VendorReturnStatus**
- id
- tenant_id (FK)
- name (Draft, PendingShipment, Shipped, Closed, Cancelled, etc)
- sort_order
- is_active

**ReturnReason**
- id
- tenant_id (FK)
- code (defective, damaged_in_shipping, core_buyback, recycle, etc)
- description
- is_defective
- is_buyback
- is_recycling
- is_active

**PurchaseOrderStatus**
- id
- tenant_id (FK)
- name (Draft, Sent, PartiallyReceived, Complete, Cancelled)
- sort_order
- is_active

**DeviceTransfer**
- id
- tenant_id (FK)
- from_location_id (FK → Location)
- to_location_id (FK → Location)
- status_id (FK → TransferStatus)
- created_by_user_id (FK → User)
- created_at
- shipped_at
- received_at
- promise_time
- contact_name
- contact_phone
- contact_email
- tracking_number
- notes

**TransferStatus**
- id
- tenant_id (FK)
- status (Pending, InTransit, Received, Cancelled)

**DeviceTransferItem**
- id
- device_transfer_id (FK → DeviceTransfer)
- device_id (FK → Device)
- ticket_id (FK → RepairTicket)
- condition_notes

**PartTransfer**
- id
- tenant_id (FK)
- from_location_id (FK → Location)
- to_location_id (FK → Location)
- transfer_status_id (FK → TransferStatus)
- created_by_user_id (FK → User)
- created_at
- shipped_at
- expected_at
- received_at
- notes

**PartTransferLine**
- id
- part_transfer_id (FK → PartTransfer)
- part_id (FK → Part)
- quantity
- notes

**TicketShipment**
- id
- tenant_id (FK)
- ticket_id (FK → RepairTicket)
- direction (Inbound, Outbound)
- carrier_id (FK → Carrier)
- tracking_number
- service_level
- shipped_at
- delivered_at
- expected_at
- from_location_id (FK → Location, nullable)
- to_location_id (FK → Location, nullable)
- customer_address_id (FK → CustomerAddress)
- notes

**Carrier**
- id
- tenant_id (FK)
- carrier_name (UPS, FedEx, USPS, etc)

### Key Relationships

- Tenant 1─* Part  
- Part 1─* InventoryStock (per location)  
- StockAdjustment always references Part + Location  
- Vendor 1─* PurchaseOrder  
- PurchaseOrder 1─* PurchaseOrderLine  
- PurchaseOrder 1─* PurchaseReceipt  
- PurchaseReceipt 1─* PurchaseReceiptLine  
- VendorReturn 1─* VendorReturnLine  
- TransferStatus 1─* DeviceTransfer / PartTransfer  

---

## Module 5: Billing, Payments, Store Credit, Subscriptions & Promotions

### Purpose
Handle invoices, payments, store credit, subscriptions/memberships, and promotions/discounts.

### Key Entities

**TaxCategory**
- id
- tenant_id (FK)
- name (Goods, Labor, Subscription, Nontaxable, etc)
- is_taxable

**LocationTaxRate**
- id
- location_id (FK → Location)
- tax_category_id (FK → TaxCategory)
- rate_percent
- effective_from
- effective_to (nullable)

**Invoice**
- id
- tenant_id (FK)
- location_id (FK)
- customer_id (FK → Customer)
- invoice_number
- status (Draft, Issued, Paid, Void, Refunded)
- issued_at
- due_at (nullable)
- subtotal_amount
- tax_amount
- total_amount
- amount_paid
- tip_amount (nullable)
- balance_due
- notes (optional, customer-facing)

**InvoiceLine**
- id
- invoice_id (FK → Invoice)
- line_type (Part, Labor, Accessory, Device, ServicePlan, Fee, Discount, etc)
- ticket_id (FK → RepairTicket, nullable)
- part_id (FK → Part, nullable)
- service_definition_id (FK → ServiceDefinition, nullable)
- device_id (FK → Device, nullable)
- description
- quantity
- unit_price
- tax_category_id (FK → TaxCategory)
- tax_rate_percent
- tax_amount
- line_total
- sort_order

**InvoiceTicket**
- id
- invoice_id (FK → Invoice)
- ticket_id (FK → RepairTicket)
- relation_type (Primary, Supplemental, etc)

**PaymentMethod**
- id
- tenant_id (FK, nullable)
- code (CASH, CARD, VENMO, ZELLE, STORECREDIT, etc)
- display_name
- is_external (bool)
- is_digital_wallet (bool)

**PaymentProcessor**
- id
- tenant_id (FK)
- location_id (FK → Location, nullable)
- name (Square POS, Stripe, etc)
- type (POS_Terminal, Online_Gateway, Wallet, etc)
- external_key (optional)
- is_active

**CardType**
- id
- tenant_id (FK)
- code (VISA, MC, AMEX, etc)
- display_name

**Payment**
- id
- tenant_id (FK)
- location_id (FK)
- customer_id (FK → Customer)
- amount
- payment_method_id (FK → PaymentMethod)
- payment_processor_id (FK → PaymentProcessor, nullable)
- currency_code
- received_at
- reference
- card_type_id (FK → CardType)
- card_last4 (nullable)
- processor_transaction_id (nullable)

**PaymentApplication**
- id
- payment_id (FK → Payment)
- invoice_id (FK → Invoice)
- amount_applied

**PayoutMethod**
- id
- tenant_id (FK)
- location_id (FK)
- display_name
- type (Cash, StoreCredit, etc)

**StoreCreditAccount**
- id
- tenant_id (FK)
- customer_id (FK → Customer)
- balance
- last_updated_at

**StoreCreditReason**
- id
- tenant_id (FK)
- location_id (FK)
- code
- name (Refund for store credit, Gift Card Purchase, etc)

**StoreCreditTransaction**
- id
- account_id (FK → StoreCreditAccount)
- invoice_id (FK → Invoice, nullable)
- payment_id (FK → Payment, nullable)
- amount
- store_credit_reason_id (FK → StoreCreditReason)
- created_at

**ServiceDefinition**
- id
- tenant_id (FK)
- name
- default_price
- tax_category_id (FK → TaxCategory)

**SubscriptionPlan**
- id
- tenant_id (FK)
- name
- code
- description
- device_type_id (FK → DeviceType, nullable)
- monthly_price
- annual_price
- billing_frequency (Monthly, Annually)
- tax_category_id (FK → TaxCategory)
- is_active

**SubscriptionStatus**
- id
- name (Active, Paused, Cancelled, Expired, etc)
- code

**CustomerSubscription**
- id
- tenant_id (FK)
- customer_id (FK → Customer)
- subscription_plan_id (FK → SubscriptionPlan)
- device_id (FK → Device, nullable)
- start_date
- end_date
- subscription_status_id (FK → SubscriptionStatus)
- cancel_reason (nullable)
- notes (nullable)

**Promotion**
- id
- tenant_id (FK)
- location_id (FK)
- name
- code
- description
- discount_type (Percent, FixedAmount)
- discount_value
- start_date
- end_date
- is_active

**PromotionCondition**
- id
- tenant_id (FK)
- location_id (FK)
- promotion_id (FK → Promotion)
- device_type_id (FK → DeviceType, nullable)
- repair_type_id (FK → RepairType, nullable)
- part_category_id (FK → PartCategory, nullable)
- min_invoice_total (nullable)

**PromotionApplication**
- id
- promotion_id (FK → Promotion)
- invoice_id (FK → Invoice)
- invoice_line_id (FK → InvoiceLine, nullable)
- discount_amount

### Key Relationships

- Customer 1─* Invoice  
- Invoice 1─* InvoiceLine  
- Payment 1─* PaymentApplication  
- Invoice 1─* PaymentApplication  
- Customer 1─0..1 StoreCreditAccount  
- StoreCreditAccount 1─* StoreCreditTransaction  
- SubscriptionPlan 1─* CustomerSubscription  

---

## Module 6: Marketing, Email & Notifications

### Purpose
Handle marketing attribution, email lists and campaigns, as well as operational notifications (ticket updates, invoices, appointments).

### Key Entities

**MarketingSource**
- id
- tenant_id (FK)
- location_id (FK)
- source (InStore, Online, TicketChecking, etc)

**EmailList**
- id
- tenant_id (FK)
- name (General Promos, Repair Tips, etc)
- code (PROMO_GENERAL, NEWSLETTER, etc)
- description
- is_default_promotions_list (bool)
- is_active

**EmailListSubscription**
- id
- tenant_id (FK)
- email_list_id (FK → EmailList)
- customer_id (FK → Customer)
- email_address
- is_subscribed
- subscribed_at
- unsubscribed_at
- marketing_source_id (FK → MarketingSource)

**EmailCampaign**
- id
- tenant_id (FK)
- email_list_id (FK → EmailList)
- name
- subject
- body_html
- scheduled_send_at
- status (enum)

**EmailCampaignRecipient**
- id
- tenant_id (FK)
- campaign_id (FK → EmailCampaign)
- customer_id (FK → Customer)
- email_address
- send_status
- sent_at

**NotificationPreference**
- id
- tenant_id (FK)
- customer_id (FK → Customer)
- contact_method_type_id (FK → ContactMethodType)
- is_enabled
- purpose (StatusUpdate, Marketing, Invoices, etc)

**NotificationLog**
- id
- tenant_id (FK)
- customer_id (FK → Customer)
- ticket_id (FK → RepairTicket, nullable)
- invoice_id (FK → Invoice, nullable)
- appointment_id (FK → Appointment, nullable)
- channel (SMS, Email, Push, Call, etc)
- contact_value
- subject (optional)
- body (summary)
- status (Sent, Failed, Delivered)

### Key Relationships

- Customer 1─* EmailListSubscription  
- EmailList 1─* EmailListSubscription  
- EmailCampaign 1─* EmailCampaignRecipient  
- Customer 1─* NotificationPreference  
- Customer 1─* NotificationLog  

---

## Module 7: Appointments & Scheduling

### Purpose
Allow customers to schedule appointments for diagnostics/repairs, choose preferred contact methods, and model status and channel.

### Key Entities

**AppointmentChannels**
- id
- tenant_id (FK)
- name (InStore, Online, Phone, etc)

**AppointmentStatus**
- id
- tenant_id (FK)
- name

**Appointment**
- id
- tenant_id (FK)
- location_id (FK)
- customer_id (FK → Customer)
- device_id (FK → Device)
- device_model_id (FK → DeviceModel)
- preferred_contact_method_id (FK → CustomerContactMethod)
- scheduled_start
- scheduled_end
- appt_status_id (FK → AppointmentStatus)
- channel_id (FK → AppointmentChannels)
- created_at
- created_by_customer_account_id (FK → CustomerAccount, nullable)
- notes

**AppointmentRepair**
- id
- appointment_id (FK → Appointment)
- repair_type_id (FK → RepairType)
- device_model_repair_type_id (FK → DeviceModelRepairType)
- is_primary (bool)

### Key Relationships

- Customer 1─* Appointment  
- Appointment 1─* AppointmentRepair  
- AppointmentChannels / AppointmentStatus are per-tenant catalogs  

---

## Module 8: Employees & Specialties

### Purpose
Model employees as users with HR-lite attributes and technical specialties to help with staffing and routing repairs.

### Key Entities

**EmployeeProfile**
- id
- tenant_id (FK)
- user_id (FK → User)
- primary_location_id (FK → Location)
- employment_type (FullTime, PartTime, Contract)
- pay_tier (Tech I, Tech II, etc)
- hire_date
- termination_date (nullable)
- separation_reason_id (FK → EmploymentSeparationReason, nullable)
- is_eligible_for_rehire (bool)
- rehire_flag (bool)
- is_active
- notes

**EmploymentSeparationReason**
- id
- tenant_id (FK)
- code (Performance, Layoff, Seasonal, Voluntary, etc)
- name
- is_active

**Specialty**
- id
- tenant_id (FK)
- name (Microsoldering, Board Repair, etc)
- description
- is_active

**UserSpecialty**
- id
- user_id (FK → User)
- specialty_id (FK → Specialty)
- proficiency_level (1–5 or similar)
- notes

### Key Relationships

- Tenant 1─* EmployeeProfile  
- User 1─0..1 EmployeeProfile  
- EmploymentSeparationReason 1─* EmployeeProfile  
- Tenant 1─* Specialty  
- User *─* Specialty via UserSpecialty  

---
