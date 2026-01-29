# USER STORIES — E-commerce MVP (Products + Services)

## Roles
- Customer: manages cart and views own orders
- Provider / Admin (single): manages products, inventory, and orders

---

## Core functionality — Authentication & Security
- As a user, I want to register and log in so I can access private features.
- As the system, I want to store passwords securely using hashing so user data is protected.
- As the system, I want to authenticate users using stateless tokens so requests can be authorized.
- As a user, I want to recover my password using a code sent by email.
- As the system, I want to enforce role-based access so users can only perform allowed actions.

---

## Core functionality — Catalog & Search
- As a user, I want to browse the product catalog so I can see available items.
- As a user, I want to search products by name so I can find items quickly.
- As the system, I want to return catalog-ready data only so the frontend remains lightweight.
- As the system, I want to allow only active products to appear in the catalog.

---

## Core functionality — Shopping Cart
- As a customer, I want to add products to a cart so I can prepare a purchase.
- As the system, I want to consolidate quantities for the same product and variant.
- As the system, I want to treat product variants as separate cart items.
- As the system, I want to validate stock when products are added to the cart.
- As the system, I want the cart to be scoped to the user or guest session and persist until checkout or manual cleanup.

---

## Core functionality — Checkout & Payments
- As a customer, I want to checkout and pay so my order can be confirmed.
- As the system, I want to delegate payment processing to a secure payment gateway.
- As a customer, I want to choose a delivery method or pickup option before completing the order.
- As a guest, I want to purchase without an account by providing only the minimum required information.

---

## Core functionality — Orders
- As the system, I want to create orders in a `pending_payment` state before payment confirmation.
- As the system, I want to confirm an order only after payment is successfully completed.
- As the system, I want to ensure no stock is deducted if payment fails.
- As a customer, I want to view my order history so I can track purchases.
- As a customer, I want to view order details so I can see the current status.
- As the system, I want to restrict order access to the owning user only.

---

## Core functionality — Inventory
- As the system, I want to prevent purchasing products that are out of stock.
- As the system, I want to deduct stock only after a successful payment.
- As the system, I want to validate stock both when adding items to the cart and during checkout.

---

## Core functionality — Order Management (Provider / Admin)
- As the provider, I want to view all orders so I can manage fulfillment.
- As the provider, I want to update order states following a strict workflow.
- As the system, I want to record order state changes with timestamps for traceability.

---

## Business Rules (backend-enforced)
- Products cannot be purchased if available stock is insufficient.
- Stock is deducted only after payment confirmation.
- Customers can only view and manage their own carts and orders.
- Only the provider/admin can manage products, inventory, and order states.
- Orders follow a strict state flow:
  - `pending_payment → confirmed`
  - `confirmed → shipping`
  - `shipping → delivered`
  - `delivered → closed`
  - `pending_payment → canceled`

---

## Out of Scope (MVP)
- Chatbot
- Discounts, coupons, or promotions
- Notifications
- Multi-provider support
- Advanced UI features
