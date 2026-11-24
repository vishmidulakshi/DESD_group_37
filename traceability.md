# Traceability: i* → Strategic BPMN → Operational BPMN

**Project:** Bristol Food Network – Use Case 2: Customer Multi-Vendor Order, Checkout and Payment

**Module:** DESD UFCFTR-30-3 · **Group 37** · **Sprint 2**

## 1. Purpose

This document shows that every detailed operational task can be traced back to a stakeholder need (i*) and to the strategic process (Strategic BPMN). Each row follows one need from the stakeholder view, to the high-level process step, to the executable Camunda model.

| Model | Owner | File |
|---|---|---|
| i* Strategic Dependency (SD) and Strategic Rationale (SR) | Nacer | `istar/SD-SR-FINAL.svg` (earlier SD-only files: `istar/SD-Diagram.svg`, `istar/SD-Diagram.json`) |
| Strategic BPMN (Use Case 2 master) | Wishmi (admin exception lane added by Mohamed) | `diagrams/strategic/Strategic_BPMN.png` and `diagrams/strategic/Strategic_BPMN.bpmn` (final version).  |
| Operational BPMN A – Checkout and Order Preparation | Wishmi | `diagrams/Operational/Operational BPMN A - Checkout and Order Preparation.bpmn` and `...Preparation-3.png` |
| Operational BPMN B – Payment and Outcome | Lin | `diagrams/Operational/Operational BPMN model B, Payment and Outcome` |

## 2. i* element types 

| Shape | Meaning |
|---|---|
| Circle | Actor |
| Rounded rectangle | Goal |
| Hexagon | Task |
| Rectangle | Resource |
| Cloud | Softgoal (quality) |

## 3. Operational BPMN A – Checkout and Order Preparation

| # | i* element (SD) | i* type | i* element (SR – Actor & Internal Rationale) | Strategic BPMN element | Operational BPMN A element | Camunda detail |
|---|---|---|---|---|---|---|
| A1 | Multi-vendor order placed | Goal | **Customer:** `Place multi-vendor order` (Task), `Multi-vendor order placed` (Goal)<br>**Platform:** `Run multi-vendor checkout` (Task), `Multi-vendor order placed` (Goal) | Whole Use Case 2 flow, from "Customer starts an order" | Whole process, from "Customer opens basket" to "Order prepared, ready for payment" | Pool: Bristol Food Network platform |
| A2 | Review and Confirm Order | Task | **Customer:** `Browse Items` (Task), `Add Items to Basket` (Task), `Review and Confirm Order` (Task) | "Items added to basket"; "Proceed to checkout and delivery options" | User task "Review basket" | Form `review-basket`; variable `basketSummary` |
| A3 | Clear Stock Information | Resource | **Platform:** `Check stock availability` (Task), `Stock information accurate` (Softgoal) | "Check stock availability"; gateway "Item in stock?"; data store "Product & Stock Data" | Service task "Check stock for all items"; gateway "All items in stock?" | Job type `check-stock`; variable `stockAvailable` |
| A4 | Clear Stock Information | Resource | **Platform:** `Notify customer: items unavailable` (Task)<br>**Customer:** `Amend Basket` (Task), `Clear stock and payment information` (Softgoal) | "Notify customer 'Items unavailable'"; gateway "Amend order?"; end "Order cancelled (out of stock)" | "Notify customer: items unavailable"; "Amend basket or cancel"; gateway "Amend basket?"; end "Order cancelled" | Job type `notify-customer`; form `amend-basket-or-cancel`; variable `amendBasket` |
| A5 | Products & Stock Information | Resource | **Producer:** `Update product stock information` (Task), `Stock information up to date` (Softgoal)<br>**Platform:** `Identify producers in the order` (Task) | Data store "Product & Stock Data"; Producer lane "Update product stock information" | Service task "Identify producers in the order" | Job type `identify-producers` |
| A6 | Delivery or Collection | Resource / Goal | **Customer:** `Provide Delivery Address / Arrange Collection` (Task)<br>**Platform:** `Delivery or collection arranged` (Goal), `Arrange delivery or collection` (Task) | Gateway "Delivery or collection?" | User task "Choose delivery or collection"; gateway "Delivery or collection?" | Form `choose-delivery-or-collection`; variable `deliveryOption` |
| A7 | Delivery or Collection | Resource / Goal | **Customer:** `Provide Delivery Address / Arrange Collection` (Task)<br>**Platform:** `Arrange home delivery` (Task) | "Enter delivery address"; "Arrange home delivery" | "Enter delivery address"; "Arrange home delivery" | Form `enter-delivery-address`; variable `deliveryAddress`; job type `arrange-home-delivery` |
| A8 | *(no direct SD element; exception path)* | – | **Platform:** `Arrange home delivery` (Task)<br>**Customer:** `Clear stock and payment information` (Softgoal) | Error "Delivery not available" → "Notify customer delivery is not available" → end "Delivery not arranged" | Error boundary event "Delivery not available" → "Notify customer: delivery not available" → end "Delivery not arranged" | Error code `DELIVERY_NOT_AVAILABLE`; job type `notify-customer` |
| A9 | Delivery or Collection | Resource / Goal | **Customer:** `Provide Delivery Address / Arrange Collection` (Task)<br>**Platform:** `Arrange collection from each producer` (Task) | "Choose collection time slot (min. 48 hours ahead)"; "Arrange collection from each producer" | "Choose collection time slot (min. 48 hours ahead)"; "Arrange collection from each producer" | Form `choose-collection-time-slot`; variable `collectionSlot`; job type `arrange-collection` |
| A10 | Order Notification | Resource | **Platform:** `Arrange collection from each producer` (Task)<br>**Producer:** `Receive order notification` (Task) | Message event "Collection confirmed" | Message flow "Collection details" to Producer pool; message event "Collection confirmed" | Message name `Collection confirmed`; correlation key `=orderId` |
| A11 | Multi-vendor order placed | Goal | **Platform:** `Create marketplace order` (Task), `Minimal manual intervention` (Softgoal) | "Create marketplace order" | Service task "Create marketplace order" | Job type `create-marketplace-order` |
| A12 | Multi-vendor order placed | Goal | **Platform:** `Split order by producer` (Task)<br>**Producer:** `Record producer-specific order elements` (Task) | "Split order by producer"; Producer lane "Record producer-specific order elements" | Service task "Split order by producer" | Job type `split-order` |
| A13 | Review and Confirm Order | Task | **Customer:** `Review and Confirm Order` (Task) | "Review order and pay (sandbox)" (the review part; payment is in B) | User task "Review and confirm order"; gateway "Order confirmed?"; end "Order cancelled by customer" (added operational detail) | Form `review-and-confirm-order`; variable `orderConfirmed` |

## 4. Operational BPMN B – Payment and Outcome 


| # | i* element (SD) | i* type | i* element (SR – Actor & Internal Rationale) | Strategic BPMN element | Operational BPMN B element |
|---|---|---|---|---|---|
| B1 | Payment Details | Resource | **Customer:** `Provide Payment Information` (Task), `Secure payment` (Softgoal)<br>**Platform:** `Complete payment and order` (Task), `Compile payment request and order summary` (Task) | "Compile payment request and order summary" | "Prepare payment request" |
| B2 | Process Payment | Task | **Platform:** `Send payment request to sandbox` (Task)<br>**Payment service:** `Check service availability` (Task), `Process payment` (Task) | "Process payment (sandbox)" | "Send payment request to sandbox"; Sandbox Payment Service pool |
| B3 | Payment Result | Resource | **Payment service:** `Payment outcome returned` (Goal), `Return payment result` (Task), `Return payment success` (Task), `Return payment declined` (Task), `Return service unavailable` (Task)<br>**Platform:** `Receive payment result` (Task) | Gateway "Payment successful?" | "Receive payment result"; gateway "Payment outcome?" |
| B4 | Payment processing reliable | Softgoal | **Platform & Payment service:** `Payment processing reliable` (Softgoal)<br>**Platform:** `Notify customer: payment service unavailable` (Task) | "Why did payment fail?" → "Service unavailable"; "Retry Payment?" | "Notify customer: payment service unavailable"; "Retry requested" message event |
| B5 | Payment Issue Update | Resource | **Platform:** `Customer informed of payment issue` (Goal), `Notify customer: payment declined` (Task)<br>**Customer:** `Receive Payment Issue Update` (Task), `Retry Payment or Cancel` (Task), `Clear stock and payment information` (Softgoal) | "Notify customer: payment declined"; "Notify customer: payment service unavailable"; "Retry or Cancel" | "Notify customer: payment declined"; Customer pool "Customer decides: retry or cancel" |
| B6 | Payments accurate and transparent | Softgoal | **Platform:** `Calculate marketplace 5% commission` (Task), `Payments accurate and transparent` (Softgoal) | "Calculate marketplace 5% commission"; "Compile payment request and order summary" | "Calculate order total"; "Calculate 5% marketplace commission" |
| B7 | Producer paid correct share | Goal | **Platform:** `Calculate amount attributable to each producer` (Task), `Producer paid correct share` (Goal)<br>**Producer:** `Producer paid correct share` (Goal), `Payments accurate and transparent` (Softgoal) | "Calculate amount attributable to each producer" | "Calculate producer share" |
| B8 | Order Notification | Resource | **Platform:** `Confirm marketplace order` (Task), `Update inventory` (Task), `Notify relevant producers` (Task)<br>**Producer:** `Receive order notification` (Task) | "Confirm marketplace order (post-payment)"; "Update inventory"; "Notify relevant producers" | "Confirm marketplace order"; "Update inventory"; "Notify relevant producers" |
| B9 | Multi-vendor order placed | Goal | **Producer:** `Prepare and fulfil order` (Task), `Order fulfilled` (Goal) | End "Order Complete"; Producer lane "Prepare and fulfil order" / "Order fulfilled" | End "Order complete"; Producer pool "Prepare and fulfil order" |
| B10 | Support Exceptions | Task | **Platform:** `Escalate service exception` (Task)<br>**Marketplace admins and operators:** `Exceptions resolved` (Goal), `Support exceptions` (Task), `Review and log service exception` (Task), `Exception log` (Resource), `Exceptions handled promptly` (Softgoal) | Admin lane "Oversee & log service exception" → end "Exception logged" | **Not yet modelled in B (gap, to agree with Lin)** |

## 5. i* Softgoals (Qualities) – Process Design & LESP Traceability
Every softgoal (quality cloud) modelled in the i* Strategic Rationale (SR) diagram (`istar/SD-SR-FINAL.svg`) directly informs both the operational BPMN design decisions and the Legal, Ethical, Social, and Professional (LESP) evaluation required for Sprint 3:
| # | i* Softgoal (Quality) | Actor(s) in i* SR | Operational & Architectural Design Decision | Legal, Ethical, Social & Professional (LESP) Implication |
|---|---|---|---|---|
| Q1 | `Clear stock and payment information` | Customer | Operational BPMN A provides explicit notification (`Notify customer: items unavailable`) and an amendment loop (`Amend basket or cancel`) rather than silently dropping items; Operational BPMN B provides distinct notifications for declined payments vs. service unavailability. | **Legal & Ethical (Consumer Rights Act 2015 / Transparency):** Customers must receive accurate pre-contractual information about item availability, delivery feasibility, and total cost before being charged. |
| Q2 | `Secure payment` | Customer | Payment processing is delegated to a separate `Sandbox Payment Service` pool via message flows (`Send payment request to sandbox` / `Receive payment result`), ensuring the marketplace platform does not directly process or store raw payment card details. | **Legal & Professional (UK GDPR / Data Protection Act 2018 / PCI-DSS):** Minimises sensitive financial data handling on the marketplace platform and prevents unauthorized exposure of customer payment credentials. |
| Q3 | `Stock information accurate` | Bristol Food Network Platform | Operational BPMN A validates all basket quantities (`Check stock for all items`, job type `check-stock`) before allowing delivery selection or order creation, and re-runs stock validation whenever a customer amends their basket (`=amendBasket = true`). | **Professional & Ethical (Fairness / System Integrity):** Prevents overselling across multi-vendor orders and ensures customers do not pay for items that cannot be fulfilled. |
| Q4 | `Stock information up to date` | Producer | Producers update stock (`Update product stock information` in SR) and inventory is automatically decremented immediately upon payment confirmation (`Update inventory` in Operational BPMN B) before producer notifications are sent. | **Social & Ethical (Producer Protection):** Protects small local food producers in the Bristol Food Network from receiving unfulfillable orders or reputational damage caused by stale inventory records. |
| Q5 | `Payments accurate and transparent` | Bristol Food Network Platform, Producer | Operational BPMN B separates `Calculate order total`, `Calculate 5% marketplace commission`, and `Calculate producer share` into explicit, auditable steps before payment request compilation. | **Ethical & Legal (Fair Commercial Practice / Auditability):** Ensures local producers transparently receive their full 95% attributable share and that the platform's 5% commission is deterministically calculated and auditable. |
| Q6 | `Payment processing reliable` | Bristol Food Network Platform, Payment service | Operational BPMN B distinguishes between a business rejection (`Declined`) and a technical outage (`Service unavailable`), providing a customer-controlled retry loop (`Retry requested`) rather than losing the prepared multi-vendor order. | **Professional (BCS Code of Conduct / ISO/IEC 25010 Fault Tolerance):** Enterprise distributed systems must handle external third-party service failures gracefully without corrupting order state or double-charging customers. |
| Q7 | `Minimal manual intervention` | Bristol Food Network Platform | Core validation, producer identification, order creation, producer order splitting, and notifications are implemented as automated Camunda service tasks (`check-stock`, `identify-producers`, `create-marketplace-order`, `split-order`, `notify-customer`), reserving user tasks strictly for customer choices and confirmations. | **Social & Professional (Scalability & Accessibility):** Reduces operational bottlenecks for community food networks while keeping customer interaction steps simple, structured, and accessible via Camunda Forms. |
| Q8 | `Exceptions handled promptly` | Marketplace admins and operators | In the i* SR model and Strategic BPMN, `Escalate service exception` hands off unrecoverable payment service outages to `Marketplace admins and operators` to `Review and log service exception` into an `Exception log` (tracked under row `B10` for Operational BPMN B integration). | **Professional (Accountability & Observability):** Ensures technical outages with external payment providers are logged and monitored by human operators rather than failing silently. |

## 6. Handoff between A and B

| From | To | Link |
|---|---|---|
| A end event "Order prepared, ready for payment" | B start event "Order prepared" | Same milestone: the order is created and split by producer, so payment can start |

Variables passed from A to B (proposed, to confirm with Lin): `orderId`, `producerOrders`, `orderTotal`.
