# DSCP Lang Flows For HyProof

DSCP is a _domain-specific-language_ ( DSL ) for designing **[token process flows](https://github.com/digicatapult/dscp-documentation/blob/main/docs/tokenModels/language.md)**. This new way of doing flows has a compiler for automatically generating process **[guard rails](https://github.com/digicatapult/dscp-documentation/blob/main/docs/tokenModels/guardRails.md)** using the previously mentioned purpose built compiler.

The **`dscp-lang`**, tool we use for parsing token flows, can be found in the **[dscp-node](https://github.com/digicatapult/dscp-node)** repository.

To differentiate documents with code that has a high-level of abstraction from other things, let's consider using the custom file extension **`*.dscp`**.

## DSCP Lang Flows For HyProof: Overview

In terms of how the information that needs to be persisted in on-chain looks like, it is important explain the big picture.

The process is currently designed in such a way so that certain users can mint out of nothing, burn-and-create and burn tokens. There are two token types - (1) the first one represents NFT tokens that have no Embodied CO2 data attached to them yet and only Hydrogen amount in whatever unit we decide to use here while (2) the second one has Embodied CO2 while the hydrogen amount is removed to avoid being redundant.

Assuming a simple user flow made out of two steps (1) token **A** gets created from nothingness; (2) token **B** gets spawned from the previously one:

* Token **A** will have two key-value pairs: (A1) hydrogen owner where the owner will be an address like the one owned by Heidi the H producer, (A2) the proposed owner where the owner will be an address like the one owner by the Emma the E producer and (A3) the respective H amount.

* Token **B** will have three key-value pairs: (B1) which is basically a clone of A1, (B2) energy owner where the owner will be an address like the one owned by Emma the energy maker or any other address different than the A1 address and (B3) the embodied CO2.

## DSCP Lang Flows For HyProof: Preparing and Testing

To compile the final _token flow json_ using the _token dscp code_ as an input the **[dscp-lang](https://github.com/digicatapult/dscp-node/tree/main/tools/lang)** needs to be used, therefore a command like the following:

```sh
dscp-lang -- build -v ./hyproof-token-flows.dscp -o hyproof-token-flows.json
```

To create, as in, deploy the new token flows ( described in the json ) into the node's _processValidation_ set, something like the following can be used ( make sure the chain is running first ):

```sh
process-management create -h localhost -p 9944 -u //Alice -f hyproof-token-flows.json # OR 127.0.0.1
```

---
