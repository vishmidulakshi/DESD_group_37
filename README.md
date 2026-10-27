# desd-food-network

## Stakeholders (Use Case 2)

- **Customer:** wants to buy from different producers in one order, with one payment, and choose delivery or collection.
- **Producer:** wants to get orders for their products and be paid the right amount after commission.
- **Marketplace (Bristol Food Network):** takes 5% commission on each order and wants orders and payments to be correct.
- **Payment service (sandbox):** receives the payment request and replies success or decline.

## Main Processes (Use Case 2)

1. **Add items to basket:** the customer adds products from one or more producers.
2. **Check stock:** the system checks that all items are still available.
3. **Choose delivery or collection:** the customer picks home delivery or collection.
4. **Confirm order and split by producer:** the customer confirms, and the system splits the order for each producer.
5. **Process payment:** the customer pays once. The system works out each producer's share and the 5% commission.
6. **Failed payment:** if the payment is declined, the customer can try again.
7. **Payment service down:** the customer is told and the order is not completed.
8. **Complete order:** the order is confirmed, stock is updated and producers are told.

## Functional Scope (Use Case 2)

We will build two main functions.

### A. Checkout and order creation
**What it does:** the customer checks their basket, the system checks stock, the customer picks delivery or collection and confirms. The system then splits the order by producer.

**Why we chose it:** without checkout, customers cannot buy anything. It is also where one customer buys from many producers, so we need to design it carefully first.

### B. Payment and commission
**What it does:** the system sends the payment request, handles success, decline and service down, works out the 5% commission and each producer's share, updates stock and tells the producers.

**Why we chose it:** this is how the network earns money and how producers get paid correctly. It also has the main error cases, like a declined payment, so it is good to model and test.

### Why these two together
B needs the order from A. So we build A first, then B, and we get a full working flow.

### Out of scope (for now)
Real payments (sandbox only), producers packing and sending orders, and refunds.ox only), producer-side order fulfilment, and refunds.
