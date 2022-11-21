# Best Practice

When defining token models, there are a number of best practices to follow:

1. Avoid arrays and lists
   It can be difficult to generate guard rails for variable size transactions e.g. adding X a list of items (each with their own token) to an order. If a transaction can involve a variable number of inputs and outputs, the guard rails need to cover every variation. It's simpler to define a single transaction that can be run X number of times. For example if there were a transaction for adding an item to an order, you only need to define `AddItemToOrder` once and it can be run several times over a list of items. The alternative would be defining `Add1ItemToOrder`, `Add2ItemsToOrder`... It isn't possible to define `AddListOfItemsToOrder`.

2. Prefer FILE metadata over LITERAL metadata
   Tokens should contain as minimal metadata as possible. The `LITERAL` metadata type is for storing data that is (or possibly will be) referred to in a process guardrails e.g. token `TYPE`. All entity-specific data, even something as small as the ID of an order, should be stored as a `FILE`. The only other time `LITERAL` should be used is if there is a clear requirement that a value be available at all times.

3. All tokens have `type`, `state` and `version` literals.
   These are the predominant way in which workflows are guarded. If the problem set requires a hierarchical state machine across a large number of tokens, it is acceptable to group them using an additional 'sub-state' literal e.g. `subState: 'acceptableOrder'`, otherwise `or` can be used in guard rails to define multiple acceptable states.

4. Assigned roles should consistently describe the persona of an actor with respect to that token `type`.
   This means an account may be a `supplier` for one purchase order but a `buyer` for another. Also this largely means guardrails should always be in place to require that roles are copied over from one token to the next equivalent state.

5. The splitting up of data across multiple metadata `FILE` should be based on how visibility should be assigned.
   Only split metadata into multiple files if there is a use case where only some of the information in that file should be released. This is important when encrypting file contents and verifiably sharing data.

6. When a token array (for example an item/row in a purchase order) is associated with a container token (in this case a purchase order) a mechanism for requiring these match up should be implemented.
   This will likely be in the form of a `TOKEN_ID` metadata item on the item/row token associated with the initial PO token.

## Versioning

All tokens have a `version` metadata key with an integer value e.g. `version: '5'`. `version` makes it possible to change the structure of an in-use token type without invalidating that type's historical tokens on the chain. For example, to add an additional `role_key` to a token that's existed for some time at `version: '0'`:

- Create a `version: '1'` model with the new `role_key`.
- Create a migration transaction for updating a token from `version: 0` -> `version: 1`.
- Create migration transactions for any other token types that are affected by the version bump.
- Run migrations.
