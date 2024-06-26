# SQNC Lang Flows For HyProof

SQNC is a _domain-specific-language_ ( DSL ) for designing **[token process flows](https://github.com/digicatapult/sqnc-documentation/blob/main/docs/tokenModels/language.md)**. This new way of doing flows has a compiler for automatically generating process **[guard rails](https://github.com/digicatapult/sqnc-documentation/blob/main/docs/tokenModels/guardRails.md)** using the previously mentioned purpose built compiler.

The **`sqnc-lang`**, tool we use for parsing token flows, can be found in the **[sqnc-node](https://github.com/digicatapult/sqnc-node)** repository.

To differentiate documents with code from others, we use the custom file extension **`*.sqnc`**.

---

## SQNC Lang Flows For HyProof: Overview

In terms of how the information that needs to be persisted in on-chain looks like, it is important explain the big picture.

The process is currently designed in such a way so that certain users can mint out of nothing, burn-and-create and burn tokens. There are two token types - (1) the first one represents NFT tokens that have no Embodied CO2 data attached to them yet and only Hydrogen amount in whatever unit we decide to use here while (2) the second one has Embodied CO2 while the hydrogen amount is removed to avoid being redundant.

Assuming a simple user flow made out of two steps (1) token **A** gets created from nothingness; (2) token **B** gets spawned from the previously one:

* Token **A** will have two key-value pairs: (A1) hydrogen owner where the owner will be an address like the one owned by Heidi the H producer, (A2) the proposed owner where the owner will be an address like the one owner by the Emma the E producer and (A3) the respective H amount.

* Token **B** will have three key-value pairs: (B1) which is basically a clone of A1, (B2) energy owner where the owner will be an address like the one owned by Emma the energy maker or any other address different than the A1 address and (B3) the embodied CO2.

---

## SQNC Lang Flows For HyProof: The Token Flows SQNC Code

The token model has been written originally in our Domain Specific Language ( SQNC DSL Lang ) and added to the API repo, as in **`sqnc-hyproof-api`**, specifically, in **[processFlows.sqnc](https://github.com/digicatapult/sqnc-hyproof-api/blob/main/processFlows.sqnc)**.

The final JSON file containing all the flow guardrail restrictions has been compiled from that file. The file has been added to the same repo, more exactly, in **[processFlows.json](https://github.com/digicatapult/sqnc-hyproof-api/blob/main/processFlows.json)**.

---

## SQNC Lang Flows For HyProof: Preparing and Testing

To compile the final _token flow json_ using the _token sqnc code_ as an input the **[sqnc-lang](https://github.com/digicatapult/sqnc-node/tree/main/tools/lang)** needs to be used, therefore a command like the following:

```sh
sqnc-lang -- build -v ./processFlows.sqnc -o processFlows.json
```

To create, as in, deploy the new token flows ( described in the json ) into the node's _processValidation_ set, something like the following can be used ( make sure the chain is running first ):

```sh
process-management create -h localhost -p 9944 -u //Alice -f processFlows.json # OR 127.0.0.1
```

---
