# Token Model Example

[Full example](https://docs.google.com/drawings/d/1A7XaDMwlpOjIGasTgv61KOS0sE9tFrBO5sXeuKMN5ag/edit)

## Key

![key](../../assets/l3/key.png)

## Roles

![#38761d](https://placehold.co/15x15/38761d/38761d.png) <b>MEMBER_A</b>

![#0000ff](https://placehold.co/15x15/0000ff/0000ff.png) <b>MEMBER_B</b>

![#ff0000](https://placehold.co/15x15/ff0000/ff0000.png) <b>OPTIMISER</b>

## Token types

Represents either a member's order or a member's capacity.
![demand](../../assets/l3/demand.png)

Represents the proposal and allocation of one member's order to a different member's capacity.
![match2](../../assets/l3/match2.png)

## States

| Token    | State            | Represents                                                                                                                                                                                                                                                                                                                                                                                         |
| -------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DEMAND` | `created`        | An order/capacity that a member has made available to be matched. If the demand is no longer required, the token can be burned.                                                                                                                                                                                                                                                                    |
|          | `allocated`      | An order/capacity that's allocated to a corresponding capacity/order in a `MATCH`. Allocation can't be cancelled, the token can no longer be burned.                                                                                                                                                                                                                                               |
| `MATCH2` | `proposed`       | A proposed pairing of an order (`DemandA`) and a capacity (`DemandB`). `DemandA` could be an order `DEMAND` and `DemandB` a capacity, as long as there's one of each, not two orders or two capacities. The first accept of a `proposed` `MATCH2` can be made by either member. `DemandA` and `DemandB` must come from different members. Only references the demands, doesn't change their state. |
|          | `accepted_a`     | A proposed `MATCH2` that has been accepted by the owner of `DemandA`. Still only references the demands, doesn't change their state.                                                                                                                                                                                                                                                               |
|          | `accepted_b`     | A proposed `MATCH2` that has been accepted by the owner of `DemandB`. Still only references the demands, doesn't change their state.                                                                                                                                                                                                                                                               |
|          | `accepted_final` | A proposed `MATCH2` that has been accepted by both members. At this point the demands change state to `allocated`.                                                                                                                                                                                                                                                                                 |

## Transactions

### Demand

Created by a member.
![demand transactions](../../assets/l3/demand-transactions.png)

### Match2

An optimiser proposes the matching of a single order from some `MEMBER_A` to a single capacity of a different `MEMBER_B`.

![match2 create](../../assets/l3/match2-create.png)

Accept flow if `MEMBER_A` accepts first, then `MEMBER_B`.

![match2 ab](../../assets/l3/match-ab.png)

Accept flow if `MEMBER_B` accepts first, then `MEMBER_A`.

![match2 ba](../../assets/l3/match-ba.png)

A match can be rejected by any party before it is fully accepted.

![match2 reject](../../assets/l3/match-reject.png)
