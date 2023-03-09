# Token Model Example

[Full example](https://docs.google.com/drawings/d/1A7XaDMwlpOjIGasTgv61KOS0sE9tFrBO5sXeuKMN5ag/edit)

## Roles

<span style="font-weight:bold; color:#38761d">MEMBER_A</span>

<span style="font-weight:bold; color:#0000ff">MEMBER_B</span>

<span style="font-weight:bold; color:#ff0000">OPTIMISER</span>

## Token types

Represents either a member's order or a member's capacity.
![demand](../../assets/l3/demand.png)

Represents the proposal and allocation of one member's order to a different member's capacity.
![match2](../../assets/l3/match2.png)

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
