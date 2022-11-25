# Token Model Diagram Components

A token model is made of various components:

## Roles

A key showing which colours represent which roles.

![example roles](../../assets/tokenModels/example-roles.png)

## States

Tokens always have a state. Every transaction has a fixed output state.

![token state](../../assets/tokenModels/token-state.png)

### Start of life/end of life

The start and end point for a token type, effectively the `null` state.

![start end nodes](../../assets/tokenModels/start-end-nodes.png)

## Transactions

The colour of a transaction arrow indicates which role will run the transaction.

//TODO How to display transactions that can be run by more than one role

### Status transaction

A transaction moving a single token from one state to another.

![status transaction](../../assets/tokenModels/status-transaction.png)

A transaction that appends/removes related tokens to a token. State is unchanged.

![array transaction](../../assets/tokenModels/array-transaction.png)

## Shared (hierarchical) states

Groups tokens with different states that can be treated as the same state in a subsequent transaction e.g. both a `created` and a `rejected` order is `editable` and can be the input of an `ORDER - append` transaction.

![shared state](../../assets/tokenModels/shared-state.png)
