# Components

For now the model is generated using [Google Drawing](https://docs.google.com/drawings/d/1PEFb19NgPAOEN-r-pZYUeSWODXd8TMp7SFHr8QLez-8/edit).

A token model is made of various components:

## Start of life/end of life

![start end nodes](../../assets/tokenModels/start-end-nodes.png)

## States

Every transaction has a fixed output state.

![token state](../../assets/tokenModels/token-state.png)

## Transactions

A transaction moving a single token from one state to another.

![status transaction](../../assets/tokenModels/status-transaction.png)

A transaction moving a single token from one state to another, as well as array(s) of related tokens.

![array transaction](../../assets/tokenModels/array-transaction.png)

## Shared (hierarchical) states

Used to group tokens with different states that can be treated as the same state in a subsequent transaction e.g. both a `submitted` and a `amended` order is `acceptable` and can be the input of an `ORDER - accept` transaction.

![shared state](../../assets/tokenModels/shared-state.png)
