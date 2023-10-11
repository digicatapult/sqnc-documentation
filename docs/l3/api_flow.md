# API Flows

This document describes the api flows that can be used to demonstrate the usage of DSCP for the Logistics Living Lab (L3) project. This document walks through the following flows:

1. [Prerequisites](#prerequisites) - contains prerequisites that should be completed prior to attempting any other flows
2. [Match Flow](#match-flow) - contains the flows for creating demands, matching them and forming an agreement on the match
3. [Commenting on demands](#commenting-on-demands) - Flow for commenting additional information onto a demand
4. [Rejecting a match](#rejecting-a-match) - Flow for rejecting a match prior to both demand owners accepting.
5. [Cancelling a match](#cancelling-a-match) - Flow for marking an agreed match as cancelled for unforeseeable circumstances
6. [Re-matching an existing demand](#re-matching-an-existing-demand) - After a match has been agreed a new match can be suggested to replace swap out the `demandB` that will be used to meet a `demandA`. This section covers that flow.

# Prerequisites

## Services

Full set of services required for running:

- dscp-matchmaker-api (+ PostgreSQL)
- dscp-identity-service (+ PostgreSQL)
- dscp-node

Refer [docker-compose-3-persona.yml](https://github.com/digicatapult/dscp-matchmaker-api/blob/main/docker-compose-3-persona.yml) in [dscp-matchmaker-api](https://github.com/digicatapult/dscp-matchmaker-api/tree/main)

## Setting node aliases (dscp-identity-service)

The values set for each persona are your choice, they should provide a recognisable value in response bodies.

Here we have went with:

- “Member A” to represent a persona placing an order ([Member A](http://localhost:8001/v1/swagger/))

- “Member B” to represent a persona offering capacity ([Member B](http://localhost:8011/v1/swagger/))

- “Optimiser” to represent an optimiser ([Optimiser](http://localhost:8021/v1/swagger/))

1. On each node, set `alias` for all node addresses using `PUT /members/{address}`:

`5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY` to “Member A”
i.e. the persona placing an Order

`5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty` to “Member B”
i.e. the persona offering Capacity

`5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y` to “Optimiser”

## Retrieving node aliases

Using `GET /members` on each node returns a list of all aliases as set above:

```
{
    "address": "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty",
    "alias": "Member B"
  },
  {
    "address": "5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y",
    "alias": "Optimiser"
  },
  {
    "address": "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY",
    "alias": "Member A"
}
```

# Match Flow

## Context

In the case of a logistics matching service where one provider has an order to be moved and another has some capacity to move orders, one might represent an order as a `demand_a` and a capacity as a `demand_b`.

### Matchmaker APIs

The matchmaker APIs for the prescribed persona’s:

- [Member A](http://localhost:8000/swagger/)
- [Member B](http://localhost:8010/swagger/)
- [Optimiser](http://localhost:8020/swagger/)

## End-to-End Flow

Parameter files uploaded in `POST /v1/attachment` as Member A & Member B, respectively.

### Member A

```
{
    "on_pallet": true,
    "pallet_size": {
      "max_length": 120,
      "max_height": 180,
      "max_width": 100
    },
...
}
```

### Member B

```
{
    "palletised_dimensions": true,
    "pallet_size": {
      "max_length": 120,
      "max_height": 180,
      "max_width": 100
    },
...
}
```

### Persona - Member A (order)

1. Member A uploads the parameters file for `demand_a` to their local database using `POST /v1/attachment`.

Response:

```
{
  "id": "eab0a41e-bcd2-4500-964a-0e885502d2cc",
  "filename": "attachment_order.json",
  "size": 999,
  "createdAt": "2023-05-19T10:52:54.442Z"
}
```

2. Member A creates `demand_a` by sending a `POST /v1/demandA`, including the `id` for the parameters file in the request body.

Response:

```
{
  "id": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
  "owner": "Member A",
  "state": "pending",
  "parametersAttachmentId": "eab0a41e-bcd2-4500-964a-0e885502d2cc",
  "createdAt": "2023-05-19T10:53:29.223Z",
  "updatedAt": "2023-05-19T10:53:29.223Z"
}
```

3. When Member A is ready for `demand_a` to exist on the blockchain, they send a `POST /v1/demandA/{demandAId}/creation`.

Response:

```
{
  "id": "2b7a7930-5d47-4af8-8262-f159974f8830",
  "state": "submitted",
  "localId": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
  "apiType": "demand_a",
  "transactionType": "creation",
  "submittedAt": "2023-05-19T10:54:12.085Z",
  "updatedAt": "2023-05-19T10:54:12.085Z"
}
```

4. Member A receives a transaction id for the `demand_a` creation transaction, which they can use to get the details of the transaction using `GET /v1/demandA/{demandAId}/creation/{creationId}`. They can also use `GET /v1/transaction` to get all transactions of any type.

#### Request - GET /v1/demandA/{demandAId}/creation/{creationId}

Inputting the `demand_a`'s identifier as: `eaf24d48-0b96-4f9d-a5d1-ead3885205b2`

Inputting the `demand_a`'s creation ID as: `2b7a7930-5d47-4af8-8262-f159974f8830`

Response:

```
{
  "id": "2b7a7930-5d47-4af8-8262-f159974f8830",
  "state": "finalised",
  "localId": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
  "apiType": "demand_a",
  "transactionType": "creation",
  "submittedAt": "2023-05-19T10:54:12.085Z",
  "updatedAt": "2023-05-19T10:54:31.468Z"
}
```

#### Request - GET /v1/transaction

Inputting the apiType as: `demand_a`

Inputting the status as: `finalised`

URL parameters: `/v1/transaction?apiType=demand_a&status=finalised`

Response:

```
  {
    "id": "2b7a7930-5d47-4af8-8262-f159974f8830",
    "state": "finalised",
    "localId": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
    "apiType": "demand_a",
    "transactionType": "creation",
    "submittedAt": "2023-05-19T10:54:12.085Z",
    "updatedAt": "2023-05-19T10:54:31.468Z"
  }
```

5. When demands are first created locally their state is `pending`, once they are put on chain and the block is finalised the state becomes `created`:

#### When local

```
  {
    "id": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
    "owner": "Member A",
    "state": "pending",
...
  }
```

#### When on-chain

```
  {
    "id": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
    "owner": "Member A",
    "state": "created",
    "parametersAttachmentId": "eab0a41e-bcd2-4500-964a-0e885502d2cc",
    "createdAt": "2023-05-19T10:53:29.223Z",
    "updatedAt": "2023-05-19T10:54:31.514Z"
  }
```

### Viewing demands as another persona

The indexers on Member B and Optimiser's dscp-matchmaker-api will process the new `demand_a` when the block containing it is finalised, and the new `demand_a` will be visible to them using `GET /v1/demandA`.

#### Member B (capacity)

Response:

```
  {
    "id": "a0f10fe6-6e03-408e-b0a8-1abe6416b2f1",
    "owner": "Member A",
    "state": "created",
...
  }
```

#### Optimiser

Response:

```
  {
    "id": "908978fa-7773-44c6-b89a-8a14a3b3ebba",
    "owner": "Member A",
    "state": "created",
...
  }
```

### Persona - Member B (capacity)

1. Member B creates `demand_b` in a similar manner to creating `demand_a`, including the parameters file for `demand_b`.

#### Request - POST /v1/attachment

Response:

```
{
  "id": "1eb9e236-f10c-4e51-851e-6441149c845b",
  "filename": "attachment_capacity.json",
  "size": 1304,
  "createdAt": "2023-05-19T11:13:09.094Z"
}
```

#### Request - POST /v1/demandB

Response:

```
{
  "id": "0ab717f8-bfaf-4dc6-b52c-feaf6a0c5978",
  "owner": "Member B",
  "state": "pending",
  "parametersAttachmentId": "1eb9e236-f10c-4e51-851e-6441149c845b",
  "createdAt": "2023-05-19T11:14:10.655Z",
  "updatedAt": "2023-05-19T11:14:10.655Z"
}
```

2. When Member B is ready for `demand_b` to exist on the blockchain, they send a `POST /v1/demandB/{demandBId}/creation`.

Response:

```
{
  "id": "5c0a90c1-ddd4-4d23-9732-4a0788978065",
  "state": "submitted",
  "localId": "0ab717f8-bfaf-4dc6-b52c-feaf6a0c5978",
  "apiType": "demand_b",
  "transactionType": "creation",
  "submittedAt": "2023-05-19T11:15:25.227Z",
  "updatedAt": "2023-05-19T11:15:25.227Z"
}
```

### Persona - Optimiser (optimiser)

1. Optimiser creates a `match2` by sending a `POST /v1/match2`, including the ids for `demand_a` and `demand_b`. From the optimiser node, send a `GET /v1/demandA` & `GET /v1/demandB` to retrieve the id’s:

```
{
  "demandA": "908978fa-7773-44c6-b89a-8a14a3b3ebba",
  "demandB": "f9a352a8-7481-44fa-b8a0-f535755686af"
}
```

Response:

```
{
  "id": "6009e0b0-2fa8-48c1-8830-8e250502a9c3",
  "state": "pending",
  "optimiser": "Optimiser",
  "memberA": "Member A",
  "memberB": "Member B",
  "demandA": "908978fa-7773-44c6-b89a-8a14a3b3ebba",
  "demandB": "f9a352a8-7481-44fa-b8a0-f535755686af",
  "createdAt": "2023-05-19T11:18:07.939Z",
  "updatedAt": "2023-05-19T11:18:07.939Z"
}
```

2. When Optimiser is ready for the `match2` to exist on the blockchain, they send a `POST /v1/match2/{match2Id}/proposal`.

Response:

```
{
  "id": "d2648206-1e53-40c1-b5af-ec3702b13149",
  "state": "submitted",
  "localId": "6009e0b0-2fa8-48c1-8830-8e250502a9c3",
  "apiType": "match2",
  "transactionType": "proposal",
  "submittedAt": "2023-05-19T11:19:05.361Z",
  "updatedAt": "2023-05-19T11:19:05.361Z"
}
```

The Optimiser can view the new `match2` on-chain when the block containing it is finalised. The status of the transaction can be returned with `GET /v1/match2/{match2Id}/proposal/{proposalId}`:

```
  {
    "id": "d2648206-1e53-40c1-b5af-ec3702b13149",
    "state": "finalised",
    "localId": "6009e0b0-2fa8-48c1-8830-8e250502a9c3",
    "apiType": "match2",
    "transactionType": "proposal",
    "submittedAt": "2023-05-19T11:19:05.361Z",
    "updatedAt": "2023-05-19T11:19:19.853Z"
  }
```

## Accepting the match

Either Member A or Member B can accept the `match2` by sending a `POST /v1/{match2Id}/accept`. It doesn't matter which member accepts first.

### Member A (order)

1. From Member A node, send `GET /v1/match2` to retrieve the `match2` id:

```
  {
    "id": "61f8e3bd-b3cd-4827-854e-0d3f96966517",
    "state": "proposed",
    "optimiser": "Optimiser",
    "memberA": "Member A",
    "memberB": "Member B",
    "demandA": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
    "demandB": "51b1dafe-1abe-481b-9a1d-464bbdb04087",
    "createdAt": "2023-05-19T11:19:19.923Z",
    "updatedAt": "2023-05-19T11:19:19.923Z"
  }
```

2. Accept the match by sending a `POST /v1/{match2Id}/accept`:

```
{
  "id": "99af4d2a-24d2-4e0d-93d2-98092e707c57",
  "state": "submitted",
  "localId": "61f8e3bd-b3cd-4827-854e-0d3f96966517",
  "apiType": "match2",
  "transactionType": "accept",
  "submittedAt": "2023-05-19T11:29:37.320Z",
  "updatedAt": "2023-05-19T11:29:37.320Z"
}
```

Note: Once the second member accepts, the `match2` state changes to `acceptedFinal`, and the states of `demand_a` and `demand_b` change to `allocated`. These demands can no longer be used in a new `match2`.

### Member B (capacity)

1. From Member B node, send `GET /v1/match2` to retrieve the `match2` id:

```
  {
    "id": "3e38c0e5-7442-416c-a166-2605ebfa3e3d",
    "state": "acceptedA",
    "optimiser": "Optimiser",
    "memberA": "Member A",
    "memberB": "Member B",
    "demandA": "a0f10fe6-6e03-408e-b0a8-1abe6416b2f1",
    "demandB": "0ab717f8-bfaf-4dc6-b52c-feaf6a0c5978",
    "createdAt": "2023-05-19T11:19:19.931Z",
    "updatedAt": "2023-05-19T11:29:54.859Z"
  }
```

2. Accept the match by sending a `POST /v1/{match2Id}/accept`:

```
{
  "id": "cd1972c1-ad60-4526-a9dd-624ba9f3cecf",
  "state": "submitted",
  "localId": "3e38c0e5-7442-416c-a166-2605ebfa3e3d",
  "apiType": "match2",
  "transactionType": "accept",
  "submittedAt": "2023-05-19T11:33:29.952Z",
  "updatedAt": "2023-05-19T11:33:29.952Z"
}
```

### Matching state

As previously mentioned, then `match2` state changes to `acceptedFinal`:

```
  {
    "id": "3e38c0e5-7442-416c-a166-2605ebfa3e3d",
    "state": "acceptedFinal",
    "optimiser": "Optimiser",
...
  }
```

### Demand state

Both demands states change to allocated at this point.

For example, with `demand_a`:

```
{
  "id": "eaf24d48-0b96-4f9d-a5d1-ead3885205b2",
  "owner": "Member A",
  "state": "allocated",
  "parametersAttachmentId": "eab0a41e-bcd2-4500-964a-0e885502d2cc",
  "createdAt": "2023-05-19T10:53:29.223Z",
  "updatedAt": "2023-05-19T11:33:44.050Z",
  "comments": []
}
```

# Commenting on demands

Demands can be commented upon by any party at any point in their lifecycle. This can be useful for communicating important information about a demand that does not affect the status of that demand with respect to other process flows. An example might be to update the GPS location of a truck or to provide comment that something about a demand may not be valid.

### Member A

Comment file uploaded in `POST /v1/attachment` as Member A's comment:

```
That's not going to work!
```

#### Persona - Member A (order)

1. Member A uploads the comment file for `demand_b` to their local database using `POST /v1/attachment`:

```
{
  "id": "c6d8b94e-d07f-43d9-8cea-26b36f8b2fc1",
  "filename": "comment_on_capacity.txt",
  "size": 25,
  "createdAt": "2023-05-19T14:21:48.690Z"
}
```

2. Member A attaches a comment on`demand_b` by sending a `POST /v1/demandB/{demandBId}/comment`, including the `id` for the comment file in the request body:

```
{
  "id": "9b6eb832-ad85-4d3b-aba8-2a8cb547bb10",
  "state": "submitted",
  "localId": "c21ba838-01b5-48fd-91be-1b8f9833e5ec",
  "apiType": "demand_b",
  "transactionType": "comment",
  "submittedAt": "2023-05-19T14:27:24.648Z",
  "updatedAt": "2023-05-19T14:27:24.648Z"
}
```

#### Persona - Member B (capacity)

Member B is then able to locate the `id` for the comment file using `GET /v1/demandB/{demandBId}`:

```
{
  "id": "c0eb276a-4164-407f-9526-ccb5c45d081d",
  "owner": "Member B",
  "state": "created",
...
  "comments": [
    {
      "attachmentId": "3fdb1c5e-1f6d-45b2-90d6-8cf7a483c188",
      "createdAt": "2023-05-19T14:27:42.823Z",
      "owner": "Member A"
    }
  ]
}
```

Note: A demand can be commented on multiple times and it does not change the demands state.

# Rejecting a match

The [match flow](#match-flow) covered a 'happy path' where both members accepted the match. In another scenario either member could reject the match proposed to them, after commenting their reasons on the other members demand.

In this example, Member A comments on `demand_b` alerting them of an issue. Subsequently Member A rejects the `match2` on-chain.

## Rejecting match2

1. Viewing `match2` as Member A using `GET /v1/match2`:

```
 {
    "id": "a4c03d12-9e19-47a8-a897-c7c60b39ec6f",
    "state": "acceptedB",
    "optimiser": "Optimiser",
...
  }
```

2. Rejecting `match2` as Member A using `POST /v1/match2/{match2Id}/rejection`:

```
{
  "id": "d1226e13-e638-4cb1-8b23-e2399aa7115e",
  "state": "submitted",
  "localId": "a4c03d12-9e19-47a8-a897-c7c60b39ec6f",
  "apiType": "match2",
  "transactionType": "rejection",
  "submittedAt": "2023-05-19T14:33:39.235Z",
  "updatedAt": "2023-05-19T14:33:39.235Z"
}
```

3. Viewing the rejected `match2` as the Optimiser using `GET /v1/match2`:

```
  {
    "id": "b874a067-2570-45d4-a97c-24d63cc44b42",
    "state": "rejected",
    "optimiser": "Optimiser",
...
  }
```

Note: At any time after a `match2` is proposed, and before it reaches `acceptedFinal` state, any of its members can reject the `match2` using `POST /v1/match2/{id}/rejection`. Once a `match2` is rejected, it is permanently closed.

# Cancelling a match

When a `match2` is in a state of `acceptedFinal` it can be cancelled by either Member A or Member B, if seen fit i.e. order or capacity is no longer available for a specific reason. Note that this is a unilateral cancellation meant to communicate that a `demand` essentially no longer exists for unforeseeable circumstances. It is expected that the terms and conditions of the original match would describe any compensation that may be due. It is mandatory that a comment is attached to detail a reason for cancellation.

1. Comment file uploaded in `POST /v1/attachment` as Member A's comment:

```
The strawberries have gone bad!
```

2. For example, cancelling `match2` as Member A using `POST /v1/match2/{match2Id}]/cancellation`:

```js
{
    "id": "8fb8aef6-0944-4367-bce0-fbbcde48ac54",
    "state": "submitted",
    "localId": "6ad909c6-c06c-463d-bbed-4cdec8781a86",
    "apiType": "match2",
    "transactionType": "cancellation",
    "submittedAt": "2023-09-29T12:49:38.458Z",
    "updatedAt": "2023-09-29T12:49:38.458Z"
}
```

3. Viewing a `match2` cancellation transaction can be performed by Member A or Member B by using `GET /v1/match2/{match2Id}/cancellation`:

```js
    {
        "id": "8fb8aef6-0944-4367-bce0-fbbcde48ac54",
        "state": "finalised",
        "localId": "6ad909c6-c06c-463d-bbed-4cdec8781a86",
        "apiType": "match2",
        "transactionType": "cancellation",
        "submittedAt": "2023-09-29T12:49:38.458Z",
        "updatedAt": "2023-09-29T12:49:55.772Z"
    }
```

4. In order to retrieve a specific transaction for a specific `match2`, use `GET /v1/match2/{match2Id}/cancellation/{cancellationId}`:

```js
{
    "id": "8fb8aef6-0944-4367-bce0-fbbcde48ac54",
    "state": "finalised",
    "localId": "6ad909c6-c06c-463d-bbed-4cdec8781a86",
    "apiType": "match2",
    "transactionType": "cancellation",
    "submittedAt": "2023-09-29T12:49:38.458Z",
    "updatedAt": "2023-09-29T12:49:55.772Z"
}
```

# Re-matching an existing demand

In L3 matches are agreed on a pairwise basis. Whilst this minimises the number of parties that need to reach agreement it also means that the complete set of demands may not be matches optimally, especially if some matches are rejected. To account for this the possibility of re-matching exists so that a previously matched `demand_a` can be rematched with a different available `demand_b`. This enables an iterative improvement in the global optimisation problem.

## Creating the initial match

In order to do a re-match we must first have a match between a `demand_a` and a `demand_b` using the flow described [here](#match-flow).

## Prepare the more optimal demand_b and the replacement match

### Persona member-b (capacity)

A new more optimal `demand_b` must be created using the flow [here](#persona---member-b-capacity).

### Persona - Optimiser (optimiser)

1. Optimiser creates a `match2` by sending a `POST /v1/match2`, including the ids for `demand_a` and `demand_b` and the old match Id in `replaces`.

```
{
  "demandA": "908978fa-7773-44c6-b89a-8a14a3b3ebba",
  "demandB": "f9a352a8-7481-44fa-b8a0-f535755686af",
  "replaces": "6009e0b0-2fa8-48c1-8830-8e250502a9c3"
}
```

Response:

```
{
  "id": "58916d47-85f8-49cf-aafc-a704545bb977",
  "state": "pending",
  "optimiser": "Optimiser",
  "memberA": "Member A",
  "memberB": "Member B",
  "demandA": "908978fa-7773-44c6-b89a-8a14a3b3ebba",
  "demandB": "f9a352a8-7481-44fa-b8a0-f535755686af",
  "createdAt": "2023-05-19T11:18:07.939Z",
  "updatedAt": "2023-05-19T11:18:07.939Z",
  "replaces": "6009e0b0-2fa8-48c1-8830-8e250502a9c3"
}
```

2. When Optimiser is ready for the `match2` to exist on the blockchain, they send a `POST /v1/match2/{match2Id}/proposal`.

Response:

```
{
  "id": "d2648206-1e53-40c1-b5af-ec3702b13149",
  "state": "submitted",
  "localId": "58916d47-85f8-49cf-aafc-a704545bb977",
  "apiType": "match2",
  "transactionType": "proposal",
  "submittedAt": "2023-05-19T11:19:05.361Z",
  "updatedAt": "2023-05-19T11:19:05.361Z"
}
```

The Optimiser can view the new `match2` on-chain when the block containing it is finalised. The status of the transaction can be returned with `GET /v1/match2/{match2Id}/proposal/{proposalId}`:

```
  {
    "id": "d2648206-1e53-40c1-b5af-ec3702b13149",
    "state": "finalised",
    "localId": "6009e0b0-2fa8-48c1-8830-8e250502a9c3",
    "apiType": "match2",
    "transactionType": "proposal",
    "submittedAt": "2023-05-19T11:19:05.361Z",
    "updatedAt": "2023-05-19T11:19:19.853Z"
  }
```

## Accepting the replacement match

The replacement match can now be accepted following the same flow as for the origin match. See [Accepting the match](#accepting-the-match)
