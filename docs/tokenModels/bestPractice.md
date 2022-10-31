# Best Practice

When defining token models, there are a number of best practices to follow:

1. Avoid arrays and lists
   It can be difficult to generate guard rails for variable size transactions e.g. adding X a list of items (each with their own token) to an order. If a transaction can involve a variable number of inputs and outputs, the guard rails need to cover every variation. It's simpler to define a single transaction that can be run X number of times. For example if there were a transaction for adding an item to an order, you only need to define `AddItemToOrder` once and it can be run several times over a list of items. The alternative would be defining `Add1ItemToOrder`, `Add2ItemsToOrder`... It isn't possible to define `AddListOfItemsToOrder`.

2. Prefer FILE metadata over LITERAL metadata
   Tokens should contain as minimal metadata as possible. The `LITERAL` metadata type is for storing data that is (or possibly will be) referred to in a process guardrails e.g. token `TYPE`. All entity-specific data, even something as small as the ID of an order, should be stored as a `FILE`. The only other time `LITERAL` should be used is if there is a clear requirement that a value be available at all times.

3. Avoid the `owner` role
   The `owner` role defines who can next interact with a token and is set automatically based on token flows. It shouldn't be set by the user when creating a token model.

4. All tokens should have `type` and `state` literals.
   These are the predominant way in which we guard workflows. If the problem set requires a very clearly hierarchical state machine across a large number of tokens, it is acceptable to have additional "sub-state" literals, otherwise `or` can be used in guard rails to define multiple acceptable states.

5. Assigned roles should consistently describe the persona of an actor with respect to that token `type`.
   This means an account may be a `supplier` for one purchase order but a `buyer` for another. Also this largely means guardrails should always be in place to require that roles are copied over from one token to the next equivalent state.

6. The splitting up of data across multiple metadata `FILE` should be based on how visibility should be assigned.
   Only split metadata into multiple files if there is a use case where only some of the information in that file should be released. This is important when encrypting file contents and verifiably sharing data.

7. When a token array (for example an item/row in a purchase order) is associated with a container token (in this case a purchase order) a mechanism for requiring these match up should be implemented.
   This will likely be in the form of a `TOKEN_ID` metadata item on the item/row token associated with the initial PO token.

## Versioning

??
