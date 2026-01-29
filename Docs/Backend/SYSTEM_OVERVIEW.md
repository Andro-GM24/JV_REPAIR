# SYSTEM OVERVIEW — Services and Products E-commerce (MVP)

## 1. Purpose

This project implements a **backend-driven e-commerce platform** for a shop that offers **both physical products and services**.

The system enforces real-world commerce rules, including:
- Stock management (products)
- Appointment lifecycle (services)
- Payments and purchase finalization (products)
- Role-based permissions
- Strict state transitions

The backend is the **single source of truth**. Clients cannot bypass business rules.

---

## 2. Scope

### 2.1 Authentication & Authorization
- User registration and login
- Role-based access control
- Roles:
  - Guest
  - Customer
  - Provider (single provider for the entire system)

### 2.2 Payments (Products)
- Payment is required to complete a **product order**
- Payment confirmation triggers automatic order confirmation
- Payment provider integration is abstracted behind a service boundary (implementation can be added later)

### 2.3 Product & Service Search
- Browse products and services
- Search by name
- Filter by category and attributes
- Filtering logic is enforced server-side

### 2.4 Provider Capabilities
- Create and manage products and services
- Update prices
- Update stock (products only)
- Enable / disable items
- Define the daily service appointment window (same every day in MVP)
- View all orders and appointments
- Update shipping status for orders (manual shipping model)
- Manually close orders when needed

### 2.5 Shopping Cart (Products)
- Customers can manage their own shopping cart
- Cart validates stock availability
- Cart is not considered a purchase until checkout

### 2.6 Customer Capabilities
- Browse catalog
- Manage own shopping cart
- Place product orders
- Book appointments (services)
- View own order and appointment history
- Confirm order delivery when the product arrives

---

## 3. Business Rules

### 3.1 Core Rules — Products
- Customers cannot buy products that are out of stock
- Customers cannot buy quantities greater than available stock
- An order is considered confirmed only after payment is confirmed
- Stock is deducted only on payment confirmation
- Stock validation occurs:
  - When adding items to cart
  - When checking out

### 3.2 Stock Concurrency Rule (MVP)
- If two customers attempt to purchase the last unit at the same time:
  - The first payment confirmation succeeds
  - The other purchase fails due to insufficient stock

### 3.3 Core Rules — Services (Appointments)

A service appointment is a lightweight reservation that informs the Provider that the customer plans to visit within a broad time window.

Characteristics:
- Appointment is booked by selecting a **date**
- The customer provides a short **description**
- There is **no** fine-grained slot system (no 30-minute blocks)
- No capacity limits inside a service window (intentional MVP simplification)

Daily service window:
- A single service window template applies to all days
- Example: 10:00–15:00
- Day-specific exceptions are out of scope for MVP

Payments for services:
- Appointments do not require online payment
- Any service payment happens offline / in person

### 3.4 Money (MVP)
- Currency: COP
- No discounts, coupons, or promotions
- Fixed global shipping fee (if applicable)
- Special shipping costs are defined at the product level

### 3.5 Authorization Rules
- Customers:
  - Cannot manage products or services
  - Can only access their own carts, orders, and appointments
  - Cannot cancel orders once in `shipping`
- Provider:
  - Manages products, services, stock, pricing, orders, and appointments
  - Has global system visibility
  - Cannot act as a customer

---

## 4. State Models

### 4.1 Product Order State Model

Allowed states:
- `pending_payment`
- `confirmed`
- `shipping`
- `delivered`
- `canceled`
- `closed`

Allowed transitions:
- `pending_payment → confirmed`
- `pending_payment → canceled`
- `confirmed → shipping`
- `confirmed → canceled`
- `shipping → delivered`
- `shipping → closed`
- `delivered → closed`

Constraints:
- Orders in `shipping` cannot be canceled by the customer

### 4.2 Service Appointment State Model

Allowed states:
- `pending`
- `confirmed`
- `canceled`

Allowed transitions:
- `pending → confirmed`
- `pending → canceled`
- `confirmed → canceled`

Constraints:
- Customers can cancel appointments only while in `pending`

---

## 5. Shipping (Manual — MVP)

- Provider marks order as `shipping`
- Customer marks order as `delivered`
- Provider can manually `close` orders if needed

---

## 6. Assumptions & Constraints

### Assumptions
- Single provider model
- Manual shipping
- Payment confirmation is authoritative
- Appointments are offline-paid
- Same service window every day
- Single stock quantity per product

### Constraints
- Backend enforces all rules
- Strict state machines
- No shared databases across services
- No real-time notifications
- Simplicity over feature completeness

---

## 7. Non-Goals (Out of Scope)
- Chatbot
- Notifications
- Multi-provider marketplace
- Social login
- Native mobile apps
- Discounts or promotions

---

## 8. Design Principles
- Backend-first architecture
- Explicit business rules
- Clear service boundaries
- Fail fast on invalid operations
- Predictable state transitions

---

# USER STORIES — E-commerce MVP (Products + Services)

## Roles
- Guest
- Customer
- Provider / Admin (single)

---

## Authentication & Security
- As a user, I want to register and log in
- As the system, I want to hash passwords securely
- As the system, I want stateless authentication using tokens
- As a user, I want password recovery via email
- As the system, I want role-based authorization

---

## Catalog & Search
- As a user, I want to browse products and services
- As a user, I want to search by name
- As the system, I want to expose only active items
- As the system, I want to return catalog-optimized data

---

## Shopping Cart
- As a customer, I want to add products to a cart
- As the system, I want to consolidate quantities
- As the system, I want variants treated independently
- As the system, I want to validate stock on add
- As the system, I want carts scoped to user or session

---

## Checkout & Payments
- As a customer, I want to checkout and pay
- As the system, I want to delegate payment processing
- As a guest, I want to purchase with minimal data
- As a customer, I want to choose delivery or pickup

---

## Orders
- As the system, I want to create orders as `pending_payment`
- As the system, I want to confirm orders only after payment
- As the system, I want to avoid stock deduction on failed payments
- As a customer, I want to view order history
- As the system, I want to restrict order access by ownership

---

## Inventory
- As the system, I want to prevent purchases with insufficient stock
- As the system, I want to deduct stock only after payment
- As the system, I want to validate stock at cart and checkout

---

## Order Management (Provider)
- As the provider, I want to view all orders
- As the provider, I want to update order states
- As the system, I want to track state transitions with timestamps

---

## Business Rules Summary
- Stock cannot go below zero
- Payment confirmation is mandatory
- Customers access only their own data
- Provider controls inventory and orders
- Orders follow strict state flows

---

## Out of Scope (MVP)
- Discounts and coupons
- Notifications
- Multi-provider support
- Advanced UI features
