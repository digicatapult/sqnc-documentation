# Token Model Example

[Full example](https://docs.google.com/drawings/d/1PEFb19NgPAOEN-r-pZYUeSWODXd8TMp7SFHr8QLez-8/edit)

## Real world story

This example will look at the simplified process of customers ordering pizzas from restaurants. A token model is only useful if its based on an accurate abstraction of a real world flow of events. An example of the level of detail required about a process before a token model can be made:

- There are two parties involved: customer and restaurant.
- There is a menu of pizzas, defined by the restaurant.
- An order can contain one or more pizzas.
- Orders can be rejected by the restaurant, after which the customer can edit the order.
- Orders can only be cancelled by the customer before submitting.

## Building a token model

### Roles

Roles are consistent across all token types so must be defined first. Part of guard railing transactions is defining which roles can run which transactions. Here there are two types of role, `Customer` and `Restaurant`. `Restaurant` transactions are keyed as `black` and `Customer` as `red`. These are only role keys, not a specific customer or restaurant, any number of accounts on the chain can take the role of a customer or restaurant within a process.

![example roles](../../assets/tokenModels/example-roles.png)

### First token type

A restaurant sets a menu of pizzas - a token type of `PIZZA_RECIPE` with an initial `PIZZA_RECIPE - create` transaction run by a `Restaurant` (note black arrow). This mints a `PIZZA_RECIPE` token in the `created` state. For now, other metadata (e.g. `name`, `toppings`) about the pizza isn't important.

![example pizza create](../../assets/tokenModels/example-pizza-create.png)

### Second token type

Now there are pizzas, a customer can make an order. As an order references an array of pizzas, modelling is more challenging. The first step is keep it simple and define a blank order (0 pizzas). This is very similar to creating a pizza, but the transaction is run by a customer.

![example order create](../../assets/tokenModels/example-order-create.png)

The blank `created` order is a staging state for a customer to build up an order of pizzas before sending the order to a restaurant with `ORDER - submit`.

![example order submit](../../assets/tokenModels/example-order-submit.png)

### Handling arrays

Next add transactions to append and remove pizzas from an order - one at a time. These transactions don't change the state of any tokens and can be run as many times as required.

![example append remove](../../assets/tokenModels/example-append-remove.png)

Each append creates a new `Order-Pizza` token, which is retired if the `Order-Pizza` is later removed from the order.

![example order pizza](../../assets/tokenModels/example-order-pizza.png)

If orders of > 10 pizzas are regularly expected, additional transactions could be added such as `ORDER - Append (10) PIZZA`.

### Shared (hierarchical) states

Orders can be rejected by a restaurant.

![example order reject](../../assets/tokenModels/example-order-reject.png)

Once rejected a customer can then edit (append/remove pizzas) and resubmit the order. Since the transactions are the same as can be run on `created`, the states can be grouped together into `Editable Order`.

![example shared state](../../assets/tokenModels/example-shared-state.png)

Alternatively, it could be argued that having separate `created` and `rejected` states is redundant. They both have the same outgoing transactions so could be combined.

![example simple reject](../../assets/tokenModels/example-simple-reject.png)

The best approach depends on the use case and domain knowledge. Perhaps `rejected` orders have extra metadata that shouldn't be on a `created` order.

### End state

Finally, tokens need an end state so they can be 'retired'. A `PIZZA_RECIPE` can only be retired by the restaurant that designed it.

![example pizza retire](../../assets/tokenModels/example-pizza-retire.png)

An order can be cancelled by a customer before submission, or a order can be completed by a restaurant (there would likely be more steps for completion but this is a simple example).

![example order end](../../assets/tokenModels/example-order-end.png)

Logically the end of life for orders would also be the end of life for `ORDER-PIZZA` tokens.

![example order pizza end](../../assets/tokenModels/example-order-pizza-end.png)

## Transactions

//TODO Deriving transaction inputs/outputs/metadata from token models diagrams

- PIZZA_RECIPE - Create
- PIZZA_RECIPE - Retire
- ORDER - Create
- ORDER - Append (1) PIZZA
- ORDER - Remove (1) PIZZA
- ORDER - Cancel
- ORDER - Submit
- ORDER - Reject
- ORDER - Complete
