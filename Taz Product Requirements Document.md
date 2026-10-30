# Taz: Product Requirements Document

Version 0.1 (draft) · 29 October 2026 · Working name: Taz (earlier called QStatus)

## 1. Summary

Taz turns a food stall's QR code into a live order board on customers' phones. Customers scan, order and pay without installing an app or logging in, then watch their order number move from **In progress** to **Ready**. Stall staff run the kitchen from a realtime dashboard. Taz earns a fixed commission on larger orders, plus an optional subscription.

The product has four roles: **Customer**, **Stall staff**, **Stall owner** and **Admin** (the Taz team). A clickable prototype already demonstrates most of the flows described here. This document defines what the real product must do.

## 2. Problem and opportunity

- Small stalls, food trucks, market operators and festival vendors manage orders by shouting numbers, paper tickets or WhatsApp. Queues are slow, orders get mixed up and customers don't know when food is ready.
- Existing restaurant ordering systems assume a dine-in venue, a login and a monthly cost that small stalls will not pay.
- A QR code is already familiar to customers (UPI payments), so "scan to order and see your number" needs no new behaviour.

## 3. Goals and non-goals

### Goals

1. A customer can go from scan to paid order in under 60 seconds, with no app and no login.
2. A stall owner can set up a stall and go live in under 15 minutes without help.
3. Staff see new orders instantly and can move them through the kitchen with one tap.
4. Taz can see every stall's orders and commission, and can control access.
5. Commission is collected accurately and cannot be changed by stall owners.

### Non-goals (version 1)

- Delivery, rider tracking or table reservations.
- Full point-of-sale replacement (inventory, supplier management, accounting).
- A native mobile app for customers.
- Loyalty programmes and marketing campaigns.

## 4. Users and roles

| Role | Who | What they can do | What they cannot do |
| --- | --- | --- | --- |
| **Customer** | Anyone who scans the stall's QR code | See the menu and prices, place and pay for an order, edit it before preparation starts, watch their order status | See other orders or any stall data; see amounts on the order status page |
| **Stall staff** | Cook or cashier at the stall | See incoming orders (items only, no prices), move orders through statuses, cancel an order, add a walk-up number, mark items sold out | Edit the menu, prices, stall settings or commission |
| **Stall owner** | Person who runs the stall | Everything staff can do, plus edit the stall name, menu, categories, prices and item order; download the QR sign; set the staff PIN; connect payments; go live, close and reopen the stall | Change the commission; see other stalls |
| **Admin** | Taz team | See all stalls, orders and commission; suspend or reactivate stalls; set the standard commission rule and per-stall custom rules; download reports; read the activity log | (Admin edits to a stall's menu are an open question, see section 14) |

Permissions must be enforced on the server for every request, not only hidden in the screen.

## 5. Core user journeys

### 5.1 Customer: scan to collect

1. Customer scans the stall's QR code and opens the stall's page in the phone browser.
2. If the stall is Live, they see the menu grouped by category, with item prices.
3. They add items. The footer shows items, service fee (if any) and total.
4. They choose how to pay: UPI, card or pay at the counter.
5. They receive an order number and see **In progress**. The page updates by itself.
6. When staff mark it ready, the page shows **Ready** and "Collect it at the counter."

The order status page shows only the order number, status and items. It does not show any amount.

### 5.2 Customer: editing an order

- While the order is **Awaiting payment** or **New**, the customer sees an **Edit order** button.
- Their current items are pre-filled. The same order number is kept. At least one item must remain.
- The bill is recalculated, including the service fee. The page shows the new total, what was already paid and the difference. If the new total is higher the customer pays the difference; if lower the difference is refunded.
- Once staff tap **Start preparing**, editing is locked with a clear message. If the lock happens while the customer is editing, the save is rejected and nothing changes.
- Staff see the order flagged **Edited** and hear the new-order alert.

### 5.3 Stall owner: activation and go-live

1. Admin creates the stall. It starts as **Draft** and gets a permanent link and QR code plus a **one-time activation code**.
2. Anyone scanning a Draft stall's code sees "This stall isn't open yet." The owner sees an activation screen.
3. The owner enters the activation code. It works once.
4. A checklist appears: stall name, at least one menu item, a 4-digit staff PIN and connected payments.
5. When every step is done, **Go live** unlocks. The same QR code now opens the real ordering page.
6. The owner can later **Close** and **Reopen** the stall without changing the QR code.

### 5.4 Staff: running the kitchen

- Columns: Awaiting payment, New, Preparing, Ready. Collected orders leave the board.
- One-tap actions: Confirm payment, Start preparing, Mark ready, Collected.
- **Cancel order** needs a second confirmation. A cancelled order disappears from the board; the customer sees "Cancelled" and any payment is refunded; no commission is charged.
- **Add walk-up number** gives a cash customer a number on the counter board.
- **Order alert sound**: a ding (and vibration where supported) for each new or edited order, with a highlighted **New** or **Edited** tag until staff act on it. Browsers need one tap to enable sound.
- Staff never see prices.

### 5.5 Counter board

A fullscreen display for a TV or tablet at the counter shows the stall name and two lists: **In progress** and **Ready**. It shows order numbers only, never names or amounts. It is fed by a read-only public channel.

### 5.6 Admin: oversight

Admin sees a table of all stalls with status, orders, billed orders and commission for Today, Last 7 days and Last 30 days. Clicking a stall opens its detail page (section 8.4).

## 6. Functional requirements: customer experience

| ID | Requirement | Priority |
| --- | --- | --- |
| C-1 | The stall page loads from the QR link with no app and no login | Must |
| C-2 | Menu is grouped by owner-defined categories, in the owner's chosen order | Must |
| C-3 | Sold-out items are visible but cannot be added | Must |
| C-4 | Place-order page shows item prices, service fee and total before payment | Must |
| C-5 | Payment by UPI or card, or choose pay at counter | Must |
| C-6 | Order status page shows number, status and items only, with no amounts | Must |
| C-7 | Status updates in realtime, with a polling fallback for weak connectivity | Must |
| C-8 | Customer can edit an order until preparation starts | Must |
| C-9 | Closed, suspended and draft stalls show a clear message instead of a menu | Must |
| C-10 | The customer's order is remembered in the browser so reopening the page still shows their number | Should |
| C-11 | Notification when the order is ready (vibration or browser notification, later SMS or WhatsApp) | Should |
| C-12 | Veg and non-veg marks, allergen tags | Should |
| C-13 | Estimated wait time | Could |
| C-14 | Reorder in one tap; language switch (English, Hindi, Kannada) | Could |

## 7. Functional requirements: stall operations

### 7.1 Stall owner

| ID | Requirement | Priority |
| --- | --- | --- |
| O-1 | Edit stall name; it appears on the customer menu, counter board and QR sign | Must |
| O-2 | Create, rename, remove and drag-reorder categories; at least one category must exist | Must |
| O-3 | Add, rename, reprice, remove and drag-reorder items; assign each item to a category | Must |
| O-4 | Mark items sold out (also available to staff) | Must |
| O-5 | Generate and download a printable QR sign (SVG) showing the stall name and "Scan to order" | Must |
| O-6 | Set a 4-digit staff PIN | Must |
| O-7 | Connect payments and complete the go-live checklist | Must |
| O-8 | Close and reopen the stall | Must |
| O-9 | View the commission rule applied to the stall; it is read-only | Must |
| O-10 | Owner earnings view: sales, fees and orders by day | Should |
| O-11 | Opening hours that open and close the stall automatically; busy mode that pauses orders or raises wait time | Could |

### 7.2 Stall staff

| ID | Requirement | Priority |
| --- | --- | --- |
| S-1 | Order board with the four columns and one-tap status changes | Must |
| S-2 | Items only, no prices | Must |
| S-3 | Cancel order with confirmation | Must |
| S-4 | Add walk-up number | Must |
| S-5 | New and edited order alert (sound, highlight tag) | Must |
| S-6 | Staff login by PIN | Should |
| S-7 | Order timers with colour warning for long waits; optional repeat alert until acknowledged | Could |

## 8. Functional requirements: admin

### 8.1 Stall lifecycle

Stall states: **Draft** (created, waiting for activation or go-live), **Live**, **Closed** (owner closed it), **Suspended** (Taz blocked it). A suspended stall cannot take orders and its staff and owner see a suspension notice. Suspension and reactivation need confirmation and are logged.

### 8.2 Commission

- **Standard rule**: a fee of ₹5 is added to the customer's bill on every order above ₹50. Orders of ₹50 or less carry no fee. A walk-up number with no amount carries no fee.
- Admin can change the standard rule. Admin can set a **custom rule per stall** (for example, a lower fee for a large festival operator) and revert to standard.
- A rule change applies to new orders only. The fee is stored on each order when it is paid, so past records never change.
- Fees are not charged on orders awaiting payment or cancelled. If an edit moves an order across the threshold, the fee is recalculated on the new total.
- Stall owners cannot see or change the rule beyond a read-only notice.

### 8.3 Reporting and alerts

- Admin table: stall, status, orders, billed orders, commission, for Today, 7 days or 30 days.
- CSV export of the all-stalls report and a CSV invoice per stall.
- Alerts: activation code unused; unusually high cancellation share; suspended stalls; a stall whose orders today are far below its 7-day average.

### 8.4 Stall detail page

Headline numbers (orders, billed orders, commission); setup status; stall access (suspend or reactivate); commission rule for this stall; commission by day; commission invoice download; recent orders; and an activity log.

### 8.5 Activity log

Every significant action is recorded with time, role and description: activation, go-live, close and reopen, price changes, adding or removing items, renaming the stall, cancelling or editing orders, commission rule changes, suspension and reactivation. The log is append-only.

### 8.6 Not yet specified

Stall approval before activation, a refund and dispute log, per-stall payout statements, and tax lines on invoices.

## 9. Pricing and business model

- **Per-order commission**: ₹5 on orders above ₹50, added to the customer's bill (section 8.2). Customers see it as a service fee on the place-order page.
- **Subscription (optional)**: an earlier plan used Stripe subscriptions in a €9–29 per month range plus a per-order fee. Indian plan prices are not yet decided.
- Payment gateway charges (UPI, card) are separate from the Taz commission. The gateway fee must be checked against the ₹5 fee to confirm the unit economics work.

## 10. Payments and money flow

Two separate money flows must be kept apart:

1. **Guest payments**: the customer pays for food. The stall owner receives this money. The provider is behind a single internal interface (`createPayment`, `handleWebhook`, `refund`) so providers can be added or swapped.
2. **Taz fees**: the commission (and any subscription) paid to Taz.

**Decision needed:** whether customers pay the stall's own gateway account (Taz then invoices the stall monthly) or pay through a Taz-held account with automatic split settlement. The first is simpler but risks unpaid fees; the second removes collection risk but brings extra compliance and regulatory work. See section 14.

Rules that apply either way:

- The payment **webhook**, not the browser redirect, moves an order to Paid. Webhooks are processed idempotently (deduplicated by event ID).
- Refunds and edits create new ledger entries; nothing is overwritten.
- Test orders during stall setup never count towards commission.

## 11. Order lifecycle

`Awaiting payment → New → Preparing → Ready → Collected`, with `Cancelled` possible before collection.

- Pay-at-counter orders start as **Awaiting payment**; staff confirm payment to move them to New.
- Online-paid orders start as **New** once the payment webhook confirms.
- Customers can edit only in Awaiting payment and New.
- Order numbers are short, per stall, and restart each day.

## 12. Data model (minimum)

| Entity | Key fields |
| --- | --- |
| **Organization** (optional) | name; owns several stalls (market or festival operator) |
| **Stall** | name, permanent slug, status (draft, live, closed, suspended), activation code and used flag, staff PIN hash, payment provider and encrypted credentials, commission rule override |
| **Category** | stall, name, sort order |
| **Menu item** | stall, category, name, price, sold-out flag, sort order, dietary tags |
| **Order** | stall, daily number, items, subtotal, fee, total, status, payment status, edited flag, timestamps |
| **Fee ledger entry** | stall, order, amount, fee, rule applied, type (charge or reversal), timestamp. Append-only |
| **User** | role, stall (for owner and staff), login method |
| **Audit log entry** | time, user and role, stall, action description. Append-only |
| **Commission rule** | standard rule, plus optional per-stall override with effective date |

Every stall-owned record carries a `stall_id`. Database row-level security must stop one stall's users from ever reading another stall's data.

## 13. Technical approach

- **Customer and staff apps**: a mobile-first web app (PWA), for example Next.js or SvelteKit. The staff view is installable on a tablet.
- **Backend**: a small API with PostgreSQL. A managed service such as Supabase provides database, auth and realtime in one place.
- **Realtime**: WebSocket or managed realtime subscriptions for status changes and new orders, with a polling fallback because festival and market networks are unreliable.
- **Counter board**: subscribes to a read-only public feed with order numbers and statuses only.
- **QR codes**: generated from the stall's permanent short link; one code per stall in version 1. Per-table codes (a table number in the link) are planned after.
- **Audio alerts**: the Web Audio API, unlocked by one tap on the staff device. The staff page must stay open with the screen awake, so kiosk mode is recommended on a dedicated tablet.
- **Security**: server-side role checks on every request; row-level security; encrypted payment credentials; rate limits on public endpoints; activation codes single-use and stored hashed.

## 14. Open questions and decisions

1. **Launch market.** Early material mentions both Europe (Teya, Worldline Saferpay, Stripe, euros) and India (UPI, Razorpay, rupees). The market decides the payment provider, currency, tax rules and receipts. This is the biggest open decision.
2. **Money flow.** Stall-owned gateway with monthly invoicing, or Taz-held account with split settlement (section 10).
3. **Tax.** How GST (or the local equivalent) applies to the platform fee on the customer's bill and to Taz invoices.
4. **Showing the fee.** Customers see the service fee on the place-order page. Confirm this meets local consumer-price rules.
5. **Admin editing rights.** Whether admin can edit a stall's menu or settings to help an owner, always logged.
6. **Activation code delivery.** Printed on the sign, given verbally, or sent by SMS, plus whether a phone OTP is added.
7. **Staff authentication.** PIN only, or individual staff accounts.
8. **Subscription pricing** in the chosen market, and whether commission alone is enough to start.
9. **Who prints the QR signs**, and whether Taz offers physical stands or stickers.
10. **Anti-tampering.** How owners check that no one has stuck a fake QR code over theirs.

## 15. Non-functional requirements

| Area | Requirement |
| --- | --- |
| Performance | Customer page usable on a 3G connection; initial load as light as possible |
| Availability | The ordering flow stays available during peak events; clear recovery if realtime drops |
| Concurrency | A few hundred simultaneous orders per event without degradation |
| Accessibility | Keyboard and screen-reader support, colour contrast, text labels in addition to colour, reduced-motion respect |
| Privacy | No customer login; collect the minimum personal data; the public board shows numbers only |
| Auditability | Money-affecting and permission-affecting actions are logged and cannot be edited |
| Browsers | Current Chrome and Safari on Android and iOS |

## 16. Success metrics

| Metric | Target to agree |
| --- | --- |
| Scan-to-paid-order time | Under 60 seconds for most customers |
| Share of customers who complete an order after scanning | To set after the pilot |
| Stall time from activation to go-live | Under 15 minutes |
| Order mistakes or disputes per 100 orders | Falling over time |
| Commission collected versus commission expected | Reconciles to within the gateway's reporting |
| Stall retention after first month | To set after the pilot |

## 17. Risks

- **Payment and compliance**: handling funds on behalf of stalls may need licences or a licensed partner. Confirm early.
- **Weak connectivity** at markets and festivals can break realtime. Mitigated by polling fallback and a light page.
- **Fake QR stickers** can divert customers to a fraudulent page. Mitigate with tamper-resistant stickers and a visible stall name on the order page.
- **Thin margins**: gateway fees plus a ₹5 commission on small orders may not be profitable. Model this before fixing prices.
- **Owner adoption**: small stalls may resist setup. The activation checklist and sample menu are meant to reduce friction.
- **Hidden prices on the status page** are fine for order tracking only because prices are shown before payment.

## 18. Release plan

| Phase | Scope |
| --- | --- |
| **0. Prototype (done)** | Clickable single-page prototype: customer, staff, owner and admin views; activation flow; commission rules; QR sign; alert sound; admin reports (simulated payments and no shared backend) |
| **1. Core pilot** | Real backend and realtime; one payment provider in sandbox; activation, menu, ordering, staff board, counter board; fee ledger; admin table |
| **2. Money and controls** | Live payments, refunds, edits with payment differences, invoices and CSV reports, audit log, suspension, alerts |
| **3. Growth features** | Per-table QR codes, owner earnings view, staff PIN login, ready notifications, veg and non-veg marks, busy mode, opening hours |
| **4. Scale** | Organizations with several stalls, second payment provider, refund and dispute log, stall approval, language support |

## 19. What the prototype covers today

The prototype demonstrates, with simulated payments and in-page state only:

- Role switcher for Customer, Stall staff, Stall owner and Admin
- One-time activation, setup checklist, Go live, Close and Reopen
- Menu with categories, prices, drag-and-drop ordering and sold-out toggles
- Order placement with service fee, editing before preparation, staff cancellation
- Staff board with new and edited order alerts and sound
- Counter TV board and a downloadable QR sign
- Admin table, per-stall detail page, suspension, standard and custom commission rules, alerts, activity log and CSV downloads

It does not demonstrate shared data across devices, real payments, real logins, or permanent storage.
