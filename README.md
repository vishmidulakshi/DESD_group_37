# Bristol Food Network Marketplace

## Project Overview

This project models a food marketplace that allows customers to purchase products from multiple producers through a single order.

Customers can add products from different producers to a basket. The marketplace checks product availability, coordinates delivery or collection arrangements, creates producer-specific order elements, calculates the marketplace's 5% commission, and submits payment to a sandbox or simulated payment service.

Following successful payment, the marketplace confirms the order, updates inventory and notifies the relevant producers. The process must also handle situations such as insufficient stock, declined payments and temporary payment-service unavailability.

## Stakeholders

| Stakeholder | Interest or responsibility |
|---|---|
| **Customer** | Wants to purchase products from different producers through one order and payment, choose delivery or collection, and receive clear information about stock and payment issues. |
| **Producer** | Supplies products and stock information, fulfils their part of a confirmed order, and receives order notifications and the correct attributable amount after commission. |
| **Marketplace platform — Bristol Food Network** | Validates stock, coordinates checkout, creates and splits orders, calculates producer amounts and commission, manages payment outcomes, updates inventory and communicates with customers and producers. |
| **Payment service** | Receives payment requests and returns payment outcomes, such as success or decline. Temporary service unavailability must also be considered. No live payment processing is intended. |
| **Marketplace administrators and operators** | May oversee marketplace operations and support exceptions that cannot be resolved automatically, if this responsibility is included in the agreed process scope. |

## Team and Roles

| Member | Role | Main responsibilities |
|---|---|---|
| **Mohamed** | **Project Manager and Integration Lead** | Coordinate sprints and meetings; maintain the sprint plan, backlog, priorities and task assignments; monitor progress and dependencies; coordinate reviews and demonstrations; support integration and ensure that contributions and project records are accurate. |
| **Wishmi** | **Requirements and Strategic Process Analyst** | Analyse the case study and selected use case; document requirements and process boundaries; identify major enterprise processes, actors and interactions; lead the strategic BPMN model. |
| **Nacer** | **Stakeholder and i* Modelling Lead** | Identify stakeholders, goals, softgoals, tasks, resources and dependencies; lead the i* Strategic Dependency (SD) and Strategic Rationale (SR) models; ensure that stakeholder analysis informs process design. |
| **Lin** | **Operational BPMN and Camunda Lead** | Coordinate detailed operational BPMN modelling; lead Camunda implementation; define user tasks, forms, service tasks, variables, gateways and exception handling; support end-to-end demonstrations. |

## Functional Scope

### Selected Use Case

**Use Case 2: Customer Multi-Vendor Order, Checkout and Payment**

The process begins when a customer browses or searches the marketplace and adds products from one or more producers to a basket.

It covers stock validation, delivery or collection arrangements, customer review and confirmation, marketplace order creation and producer-specific order splitting, commission and payment calculations, payment processing, and the resulting order updates and notifications.

### Major Functionalities

The selected use case is divided into two connected major functionalities.

### A. Multi-Vendor Checkout and Order Preparation

**What it does**

- Allows the customer to review their basket.
- Checks that every selected product is available in the requested quantity.
- Informs the customer when stock is unavailable or insufficient and allows the basket to be amended.
- Identifies the producers involved in the order.
- Determines the applicable delivery or collection arrangements.
- Presents the order for customer review and confirmation.
- Creates the overall marketplace order.
- Splits the order into producer-specific order elements.

**Why we chose it**

Checkout is essential because customers need a way to purchase products from the marketplace. Multi-vendor orders introduce additional complexity because one basket can contain products from several producers. The process must validate availability and organise the order correctly before payment can be completed.

### B. Payment and Order Completion

**What it does**

- Determines the amount payable and calculates the marketplace's 5% commission according to the agreed business rules.
- Submits a payment request to a sandbox or simulated payment service.
- Handles successful payments, declined payments and temporary service unavailability as distinct outcomes.
- Provides an appropriate retry, recovery or cancellation path where required.
- Confirms the order after successful payment.
- Updates inventory and notifies the relevant producers.
- Informs the customer when payment cannot be completed.

**Why we chose it**

Payment is essential to completing the customer transaction and ensuring that the marketplace commission and producer amounts are calculated correctly. It also introduces important alternative and exception paths that can be modelled and tested, including declined payments and temporary service outages.

### Why These Two Functionalities Belong Together

Functionality B depends on the order prepared by Functionality A. The checkout process establishes the products, quantities, producers and order details needed for payment.

## Main Process Outline

1. **Add items to basket:** The customer selects products from one or more producers.
2. **Check stock:** The marketplace checks whether all selected products remain available in the requested quantities.
3. **Handle stock problems:** If an item is unavailable or insufficient, the customer is informed and can amend the basket.
4. **Choose delivery or collection:** The customer selects an available arrangement for the order.
5. **Review and confirm:** The customer reviews the prepared order and confirms the purchase.
6. **Create and split order:** The marketplace creates the overall order and its producer-specific order elements.
7. **Calculate amounts and commission:** The marketplace determines the payment amount, each producer's attributable amount and the 5% commission according to the agreed rules.
8. **Process payment:** The marketplace submits a request to the sandbox or simulated payment service.
9. **Handle payment outcomes:** Successful payment proceeds to order completion. A declined payment follows the agreed retry or cancellation path. Temporary service unavailability follows a suitable exception or recovery path.
10. **Complete order:** Following successful payment, the marketplace confirms the order, updates inventory and notifies the relevant producers.

## In Scope

- Customer basket and checkout interactions.
- Product availability and quantity validation.
- Basket amendment when stock is insufficient.
- Producer identification and delivery or collection arrangements.
- Customer review and order confirmation.
- Overall marketplace order creation and producer-specific order splitting.
- Calculation of producer amounts and the 5% marketplace commission.
- Sandbox or simulated payment processing.
- Payment success, decline, retry and service-unavailability paths.
- Order confirmation, inventory updates and producer notifications after successful payment.
- Relevant BPMN pools, lanes, tasks, gateways, events, message flows and exception paths.
- Process data and business logic required for the agreed workflows.

## Out of Scope

- Live payment processing or handling real payment-card data.
- Building the entire marketplace or implementing all use cases in the case study.
- Standalone producer inventory-management features, such as creating listings or editing product details independently of checkout.
- Producer fulfilment and delivery operations beyond the arrangements represented in the agreed process models.
- Refund processing, unless required by the module team.
- Delivery-provider integrations beyond those explicitly included in the agreed prototype.

## Modelling and Implementation Approach

The project will use stakeholder analysis and business process modelling to establish the requirements and responsibilities of the marketplace.

The planned modelling and implementation work includes:

- **i* Strategic Dependency (SD) model:** Represents stakeholders and their dependencies on one another.
- **i* Strategic Rationale (SR) model:** Explores stakeholder goals, tasks, resources, softgoals and the reasoning behind decisions.
- **Strategic-level BPMN model:** Represents the marketplace at an enterprise level, showing its main processes, participants and interactions.
- **Two operational BPMN models:** Represent the agreed major functionalities in greater detail, including normal, alternative and exception paths.
- **Camunda implementation:** Implements the agreed processes using appropriate user tasks, service tasks, forms, variables, gateways and error handling.
- **Demonstration evidence:** Shows the models and implemented behaviour required for the relevant assessment stage.

## Quality and Design Considerations

- Keep the strategic BPMN model at an enterprise-level abstraction.
- Make operational BPMN models detailed enough to explain the agreed end-to-end workflows.
- Use pools, lanes, sequence flows and message flows consistently.
- Distinguish customer interaction from automated platform activities.
- Represent stock problems, payment declines and service outages as distinct outcomes.
- Use the sandbox or simulated payment service for development and demonstrations.
- Ensure the two operational processes have clear boundaries, inputs, outputs and handoffs.
- Maintain consistency between stakeholder goals, strategic BPMN, operational BPMN and implementation.
- Consider privacy, security, accessibility, fairness and professional responsibilities when designing data handling and user interactions.
- Maintain meaningful version-control history and accurate evidence of individual contributions.

## Development Workflow

1. Record requirements, assumptions and unresolved questions.
2. Break the work into sprint-sized backlog items with an owner, priority and acceptance criteria.
3. Develop and review models incrementally.
4. Maintain the sprint plan and backlog as work progresses.
5. Use version control and meaningful commit messages.
6. Record design decisions, blockers, reviews and demonstration evidence.
7. Check traceability between requirements, models and implementation.
8. Update the documentation as the group confirms requirements and makes design decisions.

## Open Decisions

- Confirm the two major functionalities and their boundaries with the module team.
- Confirm the final allocation of responsibilities among group members.
- Agree where the 5% commission is calculated and which charges are included in the calculation.
- Agree how payment-service unavailability, retry limits and recovery should behave.
- Agree how delivery or collection arrangements are represented in the prototype.
- Confirm the Camunda version and implementation approach.
- Record any additional assumptions required for the demonstration.
