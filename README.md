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
