# Traceability: i* → Strategic BPMN → Operational BPMN

**Project:** Bristol Food Network – Use Case 2: Customer Multi-Vendor Order, Checkout and Payment

**Module:** DESD UFCFTR-30-3 · 
      
**Group 37** · **Sprint 2**

## 1. Purpose

This document shows that every detailed operational task can be traced back to a stakeholder need (i*) and to the strategic process (Strategic BPMN). Each row follows one need from the stakeholder view, to the high-level process step, to the executable Camunda model.

| Model | Owner | File |
|---|---|---|
| i* Strategic Dependency (SD)/ i* Strategic Rationale (SR | Nacer | `istar/SD-Diagram.svg` and `istar/SD-Diagram.json` | 
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

| # | i* element (SD) | i* type | Strategic BPMN element | Operational BPMN A element | Camunda detail |
|---|---|---|---|---|---|
| A1 | Multi-vendor order placed | Goal | Whole Use Case 2 flow, from "Customer starts an order" | Whole process, from "Customer opens basket" to "Order prepared, ready for payment" | Pool: Bristol Food Network platform |
| A2 | Review and Confirm Order | Task | "Items added to basket"; "Proceed to checkout and delivery options" | User task "Review basket" | Form `review-basket`; variable `basketSummary` |
| A3 | Clear Stock Information | Resource | "Check stock availability"; gateway "Item in stock?"; data store "Product & Stock Data" | Service task "Check stock for all items"; gateway "All items in stock?" | Job type `check-stock`; variable `stockAvailable` |
| A4 | Clear Stock Information | Resource | "Notify customer 'Items unavailable'"; gateway "Amend order?"; end "Order cancelled (out of stock)" | "Notify customer: items unavailable"; "Amend basket or cancel"; gateway "Amend basket?"; end "Order cancelled" | Job type `notify-customer`; form `amend-basket-or-cancel`; variable `amendBasket` |
| A5 | Products & Stock Information | Resource | Data store "Product & Stock Data"; Producer lane "Update product stock information" | Service task "Identify producers in the order" (added operational detail) | Job type `identify-producers` |
| A6 | Delivery or Collection | Goal | Gateway "Delivery or collection?" | User task "Choose delivery or collection"; gateway "Delivery or collection?" | Form `choose-delivery-or-collection`; variable `deliveryOption` |
| A7 | Delivery or Collection | Goal | "Enter delivery address"; "Arrange home delivery" | "Enter delivery address"; "Arrange home delivery" | Form `enter-delivery-address`; variable `deliveryAddress`; job type `arrange-home-delivery` |
| A8 | *(no direct SD element; exception path)* | – | Error "Delivery not available" → "Notify customer delivery is not available" → end "Delivery not arranged" | Error boundary event "Delivery not available" → "Notify customer: delivery not available" → end "Delivery not arranged" | Error code `DELIVERY_NOT_AVAILABLE`; job type `notify-customer` |
| A9 | Delivery or Collection | Goal | "Choose collection time slot (min. 48 hours ahead)"; "Arrange collection from each producer" | "Choose collection time slot (min. 48 hours ahead)"; "Arrange collection from each producer" | Form `choose-collection-time-slot`; variable `collectionSlot`; job type `arrange-collection` |
| A10 | Order Notification | Resource | Message event "Collection confirmed" | Message flow "Collection details" to Producer pool; message event "Collection confirmed" | Message name `Collection confirmed`; correlation key `=orderId` |
| A11 | Multi-vendor order placed | Goal | "Create marketplace order" | Service task "Create marketplace order" | Job type `create-marketplace-order` |
| A12 | Multi-vendor order placed | Goal | "Split order by producer"; Producer lane "Record producer-specific order elements" | Service task "Split order by producer" | Job type `split-order` |
| A13 | Review and Confirm Order | Task | "Review order and pay (sandbox)" (the review part; payment is in B) | User task "Review and confirm order"; gateway "Order confirmed?"; end "Order cancelled by customer" (added operational detail) | Form `review-and-confirm-order`; variable `orderConfirmed` |

## 4. Operational BPMN B – Payment and Outcome 


| # | i* element (SD) | i* type | Strategic BPMN element | Operational BPMN B element |
|---|---|---|---|---|
| B1 | Payment Details | Resource | "Compile payment request and order summary" | "Prepare payment request" |
| B2 | Process Payment | Task | "Process payment (sandbox)" | "Send payment request to sandbox"; Sandbox Payment Service pool |
| B3 | Payment Result | Resource | Gateway "Payment successful?" | "Receive payment result"; gateway "Payment outcome?" |
| B4 | Payment processing reliable | Softgoal | "Why did payment fail?" → "Service unavailable"; "Retry Payment?" | "Notify customer: payment service unavailable"; "Retry requested" message event |
| B5 | Payment Issue Update | Resource | "Notify customer: payment declined"; "Notify customer: payment service unavailable"; "Retry or Cancel" | "Notify customer: payment declined"; Customer pool "Customer decides: retry or cancel" |
| B6 | Payments accurate and transparent | Softgoal | "Calculate marketplace 5% commission"; "Compile payment request and order summary" | "Calculate order total"; "Calculate 5% marketplace commission" |
| B7 | Producer paid correct share | Goal | "Calculate amount attributable to each producer" | "Calculate producer share" |
| B8 | Order Notification | Resource | "Confirm marketplace order (post-payment)"; "Update inventory"; "Notify relevant producers" | "Confirm marketplace order"; "Update inventory"; "Notify relevant producers" |
| B9 | Multi-vendor order placed | Goal | End "Order Complete"; Producer lane "Prepare and fulfil order" / "Order fulfilled" | End "Order complete"; Producer pool "Prepare and fulfil order" |
| B10 | Support Exceptions | Task | Admin lane "Oversee & log service exception" → end "Exception logged" |


## 5. Handoff between A and B

| From | To | Link |
|---|---|---|
| A end event "Order prepared, ready for payment" | B start event "Order prepared" | Same milestone: the order is created and split by producer, so payment can start |

Variables passed from A to B (proposed, to confirm with Lin): `orderId`, `producerOrders`, `orderTotal`.

