# desd-food-network

## Stakeholders (Use Case 2)

- **Customer:** Customer wants to buy from several producers in one order with one checkout and one payment, and clear delivery or collection options.
- **Producer:** Producer wants to receive orders for their own products and get paid the correct amount (minus commission).
- **Marketplace (Bristol Food Network):** wants to take 5% commission on each order and keep orders and payments accurate and reliable.
- **Payment service (sandbox):** wants to receive clear payment requests from the system and reply with success or decline, so the order can continue.


## Main Processes (Use Case 2)

1. **Add items to basket:** The customer adds products from one or more producers to a single basket.
2. **Check stock availability:** The system checks that every item in the basket is still in stock.
3. **Select delivery or collection:** The customer selects home delivery or collection, and the system arranges the chosen option.
4. **Confirm order and split by producer:** The customer confirms the order, and the system splits it into separate parts for each producer.
5. **Process payment:** The customer pays once through the sandbox payment service. The system calculates each producer's share and the 5% marketplace commission.
6. **Handle failed payment:** If the payment is declined, the customer can try again.
7. **Handle payment service outage:** If the payment service is unavailable, the system tells the customer and the order is not completed.
8. **Complete order:** When payment succeeds, the order is confirmed, stock is updated and the producers are notified.

9. ## Functional Scope (Use Case 2)

We focus on two core functionalities that together cover the full journey of a customer buying from several producers in one order.

### Functionality A: Checkout and order creation
**What it does:** The customer reviews their basket, the platform checks stock, the customer chooses home delivery or collection and confirms the order. The platform then splits the order by producer.

**Why we chose it:** Checkout is the point where a basket becomes a real order. Without it, customers cannot buy anything from the network. It is also where multi-vendor complexity first appears (one customer, several producers), so it needs to be designed carefully before payment can be built on top of it.

### Functionality B: Payment and commission
**What it does:** The platform sends a payment request to the (sandbox) payment service, handles success, decline and service-unavailable cases, calculates the 5% network commission and the amount owed to each producer, then updates inventory and notifies producers.

**Why we chose it:** Payment is the most business-critical step, because the network only earns revenue through the 5% commission, and producers only get paid correctly if the split is right. It also contains the main error paths (declined card, payment service down), which makes it a good candidate for careful modelling and testing.

### Why these two together
A depends on nothing else in this use case, and B depends on A's output (the confirmed, producer-split order). Building A first and B second gives a clear sprint order and a working end-to-end flow by the end of the project.

### Out of scope (for now)
Real payment processing (sandbox only), producer-side order fulfilment, and refunds.
