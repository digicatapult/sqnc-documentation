# Logistics Living Lab (L3)

The [Logistics Living Lab](https://digitalsupplychainhub.uk/showcase/logistics-living-lab/) is one of the flagship projects of the Made Smarter Innovation | Digital Supply Chain Hub.

L3 will demonstrate the value of creating a shared public infrastructure for managing delivery vehicle slot filling, routing, and tracking. This will enable logistics companies to collaborate where necessary, and still compete in new and more efficient ways, while enhancing UK manufacturing capability.

Part of the shared infrastructure is built using `DSCP`.

## Token Models and Process flows

The token model design for L3 is available in both the diagrammatic style [here](./tokens.md) and in our token model DSL [here](./l3.dscp).

This design has then also been translated to process flow guard rails whose implementation can be found in [`dscp-matchmaker-api`](https://github.com/digicatapult/dscp-matchmaker-api#process-flows).

## API flows

The API flows for 'dscp-matchmaker-api' can be found in [API flows](api_flow.md).

## Glossary and transaction summary

This section describes some terminology used in the L3 project.

### Main entities and routes

| name        | description                                                                                                                                           | route prefixes               |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| demand      | A demand represents an abstract need for some sort of matchmaking. In L3 this represents either an order or a capacity                                | `/v1/demandA`, `/v1/demandB` |
| demand_a    | Matches in L3 are between heterogeneous demands and a demand_a represents one side of this                                                            | `/v1/demandA`                |
| order       | A demand to have a physical asset moved from one location to another. This is represented by a demand_a in L3                                         | `/v1/demandA`                |
| demand_b    | Matches in L3 are between heterogeneous demands and a demand_b represents the other side of this                                                      | `/v1/demandB`                |
| capacity    | An ability to move physical assets via some sort of transportation mechanism. This represents a demand to have space filled by orders                 | `/v1/demandB`                |
| match       | An agreement to have an order moved by a specified capacity                                                                                           | `/v1/match2`                 |
| re-match    | An agreement to have an order moved by a specified capacity which replaces replaces an existing match for an order. This is a special kind of match   | `/v1/match2`                 |
| transaction | An onchain transaction that communicates a change of state for an order, a capacity and/or a match                                                    |
| attachment  | A file that needs to be communicated over shared infrastructure consistently, but the contents of which will not affect the validity of a transaction | `/v1/attachment`             |

### Transaction types

Transactions communicate via the distributed infrastructure (ledger and file-store) various messages about the above entity types. The ontology of these is as follows:

| transaction type | description                                                                                                                                                                                                                   | route prefix                                                           |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Demand create    | Communicates a new demand                                                                                                                                                                                                     | `/v1/demandA/{demandAId}/creation`, `/v1/demandB/{demandBId}/creation` |
| Match propose    | Communicates a suggestion of a match between an order and a capacity                                                                                                                                                          | `/v1/match2/{match2Id}/proposal`                                       |
| Match Accept     | Communicates the acceptance of a match by either the owner of the order or the capacity. Once Both parties have accepted a match it is allocated                                                                              | `/v1/match2/{match2Id}/accept`                                         |
| Match reject     | Communicates the rejections of a match by either the owner of the order or the capacity. Match rejection from a single party prevents the match from ever being allocated                                                     | `/v1/match2/{match2Id}/reject`                                         |
| Match cancel     | Unilaterally cancels a match that has previously been allocated due to unforeseeable circumstances. This can be triggered by either the order or capacity owner and communicates the demand in question no longer exists      | `/v1/match2/{match2Id}/cancellation`                                   |
| Demand Comment   | Communicates information about a demand that does not affect the validity of other flows. For example this may communicate the location of a demand as part of a fulfilment process. May be called on any demand by any party | `/v1/demandA/{demandAId}/comment`, `/v1/demandB/{demandBId}/comment`   |
