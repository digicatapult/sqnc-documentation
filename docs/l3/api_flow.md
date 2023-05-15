# Prerequisites

## Services

Full set of services required for running:

- dscp-matchmaker-api (+ PostgreSQL)
- dscp-identity-service (+ PostgreSQL)
- dscp-node

Refer [docker-compose-3-persona.yml](https://github.com/digicatapult/dscp-matchmaker-api/blob/main/docker-compose-3-persona.yml) in [dscp-matchmaker-api](https://github.com/digicatapult/dscp-matchmaker-api/tree/main)

## Setting node aliases 

The values set for each persona are your choice, they should provide a recognisable value in response bodies.

Here we have went with:

- “Member A” to represent a persona placing an order

- “Member B” to represent a persona offering capacity

- “Optimiser” to represent an optimiser

1. For each node, set node address `5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY` to “Member A” i.e. the persona placing an Order, using `PUT /members/{address}`

2. For each node, set node address `5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty` to “Member B” i.e. the persona offering Capacity, using `PUT /members/{address}`

3. For each node, set node address `5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y` to “Optimiser” using `PUT /members/{address}`

## Retrieving node aliases

Using `GET /members` on each node returns a list of all node aliases as set above:
```
[
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
]
```
# Match Creation API Flow (dscp-matchmaker-api)

## Context

In the case of a logistics matching service where one provider has an order to be moved and another has some capacity to move orders, one might represent an order as a `demand_a` and a capacity as a `demand_b`.

## End-to-End Flow

Parameter files uploaded in `POST /v1/attachment` as Member A & Member B, respectively.

#### Member A
```
{
    "on_pallet": true,
    "pallet_size": {
      "max_length": 120,
      "max_height": 180,
      "max_width": 100
    },
    "item_dimensions": {
      "length": 80,
      "height": 50,
      "width": 40
    },
    "item_weight": 25,
    "pickup_location": {
      "geo_location": {
        "latitude": 51.5074,
        "longitude": -0.1278
      },
      "timed_window": {
        "start_time": "2023-05-10T10:00:00Z",
        "end_time": "2023-05-10T12:00:00Z"
      },
      "expected_time_for_loading": 30
    },
    "dropoff_location": {
      "geo_location": {
        "latitude": 52.5200,
        "longitude": 13.4050
      },
      "estimated_time": "2023-05-11T15:30:00Z"
    },
    "mixing_exclusion_criteria": {
      "critical": [
        "toxic",
        "flammable"
      ],
      "user_defined": [
        "organic",
        "inorganic"
      ]
    },
    "specialist_requirements": [
      "refrigerated",
      "hazardous"
    ]
  }
```
#### Member B
{
    "palletised_dimensions": true,
    "pallet_size": {
      "max_length": 120,
      "max_height": 180,
      "max_width": 100
    },
    "max_weight": 500,
    "wheel_position": "front",
    "pickup_locations": [
      {
        "geo_location": {
          "latitude": 51.5074,
          "longitude": -0.1278
        },
        "estimated_time": "2023-05-10T10:00:00Z"
      },
      {
        "geo_location": {
          "latitude": 52.5200,
          "longitude": 13.4050
        },
        "estimated_time": "2023-05-11T15:30:00Z"
      }
    ],
    "required_buffer_time": 30,
    "driver_breaks": {
      "interval": 240,
      "duration": 30
    },
    "latest_end_time": "2023-05-12T18:00:00Z",
    "latest_end_time_buffer": 60,
    "worked_time_so_far": 300,
    "breaks_so_far": 2,
    "vehicle_type": "van",
    "vehicle_specialism": "refrigerated",
    "existing_item_exception_criteria": [
      {
        "name": "fragile",
        "weight_threshold": 10
      },
      {
        "name": "flammable",
        "weight_threshold": 5
      }
    ],
    "distance_travelled_so_far": 150,
    "current_vehicle_location": {
      "latitude": 51.5074,
      "longitude": -0.1278
    },
    "priority_value": 5,
    "timeout_cutoff": 120
  }

Member A uploads the parameters file for `demand_a` to their local database using `POST /v1/attachment`.

Response:
```
{
  "id": "78b6a81c-5ef3-4549-82a9-e9459ca0cc93",
  "filename": "attachment_order.json",
  "size": 999,
  "createdAt": "2023-05-09T14:54:14.708Z"
}
```

Member A creates `demand_a` by sending a `POST request to /v1/demandA`, including the `id` for the parameters file in the request body. 

Response:
```
{
  "id": "18f832d2-162f-486c-bbc6-d4c436df0d63",
  "owner": "Member A",
{
  "id": "18f832d2-162f-486c-bbc6-d4c436df0d63",
  "owner": "Member A",
  "state": "pending",
  "parametersAttachmentId": "78b6a81c-5ef3-4549-82a9-e9459ca0cc93",
  "createdAt": "2023-05-09T14:58:17.408Z",
  "updatedAt": "2023-05-09T14:58:17.408Z"
}
```

When Member A is ready for `demand_a` to exist on the blockchain, they send a `POST request to /v1/demandA/{demandAId}/creation`. 

Response:
```
{
  "id": "8c783421-eb61-4bdb-b719-aee945d02121",
  "state": "submitted",
  "localId": "18f832d2-162f-486c-bbc6-d4c436df0d63",
  "apiType": "demand_a",
  "transactionType": "creation",
  "submittedAt": "2023-05-09T15:04:19.422Z",
  "updatedAt": "2023-05-09T15:04:19.422Z"
}
```

Member A receives a transaction id for the `demand_a` creation transaction, which they can use to get the details of the transaction using `GET /v1/demandA/{demandAId}/creation/{creationId}`. They can also use `GET /v1/transaction` to get all transactions of any type. 

#### GET /v1/demandA/{demandAId}/creation/{creationId}

Inputting the `demand_a`'s identifier as: `18f832d2-162f-486c-bbc6-d4c436df0d63`

Inputting the `demand_a`'s creation ID as: `8c783421-eb61-4bdb-b719-aee945d02121`

Response:
```
{
  "id": "8c783421-eb61-4bdb-b719-aee945d02121",
  "state": "finalised",
  "localId": "18f832d2-162f-486c-bbc6-d4c436df0d63",
  "apiType": "demand_a",
  "transactionType": "creation",
  "submittedAt": "2023-05-09T15:04:19.422Z",
  "updatedAt": "2023-05-09T15:04:37.227Z"
}
```

#### GET /v1/transaction

Inputting the apiType as: `demand_a`

Inputting the status as: `finalised`

Response:
```
[
  {
    "id": "8c783421-eb61-4bdb-b719-aee945d02121",
    "state": "finalised",
    "localId": "18f832d2-162f-486c-bbc6-d4c436df0d63",
    "apiType": "demand_a",
    "transactionType": "creation",
    "submittedAt": "2023-05-09T15:04:19.422Z",
    "updatedAt": "2023-05-09T15:04:37.227Z"
  }
]
```

Enhancement: The local id changes to `pending` at this point. For example, with Member A:
```
{
  "id": "18f832d2-162f-486c-bbc6-d4c436df0d63",
  "owner": "Member A",
  "state": "pending",
  "parametersAttachmentId": "78b6a81c-5ef3-4549-82a9-e9459ca0cc93",
  "createdAt": "2023-05-09T14:58:17.408Z",
  "updatedAt": "2023-05-09T14:58:17.408Z"
}
```

The indexers on Member B and Optimiser's dscp-matchmaker-api will process the new `demand_a` when the block containing it is finalised, and the new `demand_a` will be visible to them using `GET /v1/demandA`.

#### Member B
Inputting the `updatedSince` as: `2023-05-09T14:04:37.227Z`

Response:
```
[
  {
    "id": "d51277a0-1e23-4d09-aceb-c10ecc147560",
    "owner": "Member A",
    "state": "created",
    "parametersAttachmentId": "da003d33-3f0f-4c18-9ccb-5d122a7176ff",
    "createdAt": "2023-05-09T15:04:37.279Z",
    "updatedAt": "2023-05-09T15:04:37.279Z"
  }
]
```

#### Optimiser

Inputting the `updatedSince` as: `2023-05-09T14:04:37.227Z`

Response:
```
[
  {
    "id": "d51277a0-1e23-4d09-aceb-c10ecc147560",
    "owner": "Member A",
    "state": "created",
    "parametersAttachmentId": "da003d33-3f0f-4c18-9ccb-5d122a7176ff",
    "createdAt": "2023-05-09T15:04:37.279Z",
    "updatedAt": "2023-05-09T15:04:37.279Z"
  }
]
```

Member B creates `demand_b` in a similar manner to creating `demand_a`, including the parameters file for `demand_b`.

#### POST /v1/attachment

Response:
```
{
  "id": "18d3b77a-ded6-4fb5-ae16-edd8c1ba1a92",
  "filename": "attachment_capacity.json",
  "size": 1304,
  "createdAt": "2023-05-09T15:32:43.664Z"
}
```

#### POST request to /v1/demandB

Response:
```
{
  "id": "566f6819-eb37-4102-9cfb-1c79c03cdb9b",
  "owner": "Member B",
  "state": "created",
  "parametersAttachmentId": "18d3b77a-ded6-4fb5-ae16-edd8c1ba1a92",
  "createdAt": "2023-05-09T15:43:45.427Z",
  "updatedAt": "2023-05-09T15:43:45.427Z"
}
```

When Member B is ready for `demand_b` to exist on the blockchain, they send a `POST request to /v1/demandB/{demandBId}/creation`.

Response:
```
{
  "id": "cce98ffb-9e88-4876-ab99-b4ae45f589a9",
  "state": "submitted",
  "localId": "566f6819-eb37-4102-9cfb-1c79c03cdb9b",
  "apiType": "demand_b",
  "transactionType": "creation",
  "submittedAt": "2023-05-09T15:45:32.313Z",
  "updatedAt": "2023-05-09T15:45:32.313Z"
}
```

Optimiser creates a `match2` by sending a `POST request to /v1/match2/{match2Id}/proposal`, including the ids for `demand_a` and `demand_b`. From the optimiser node, send a `GET /v1/demandA` & `GET /v1/demandB` to retrieve the id’s:
```
{
  "demandA": "7c1134ba-f735-4f8f-9935-b8aca7d38e6b",
  "demandB": "3fdf525c-20d5-43dd-b387-27c0eac0f73a"
}
```

Response:
```
{
  "id": "3be18c6e-367f-479e-a5c2-a52aafc6a376",
  {
    "id": "3be18c6e-367f-479e-a5c2-a52aafc6a376",
    "state": "pending",
  "optimiser": "Optimiser",
  "memberA": "Member A",
  "memberB": "Member B",
  "demandA": "7c1134ba-f735-4f8f-9935-b8aca7d38e6b",
  "demandB": "3fdf525c-20d5-43dd-b387-27c0eac0f73a",
  "createdAt": "2023-05-09T16:10:53.921Z",
  "updatedAt": "2023-05-09T16:10:53.921Z"
}
```

When Optimiser is ready for the `match2` to exist on the blockchain, they send a `POST request to /v1/{match2Id}/proposal`.

Response:
```
{
  "id": "606ea493-d761-4446-b456-3e92ecf28931",
  "state": "submitted",
  "localId": "3be18c6e-367f-479e-a5c2-a52aafc6a376",
  "apiType": "match2",
  "transactionType": "proposal",
  "submittedAt": "2023-05-09T16:22:57.259Z",
  "updatedAt": "2023-05-09T16:22:57.259Z"
}
```

Either Member A or Member B can accept the `match2` by sending a `POST request to /v1/{match2Id}/accept`. It doesn't matter which member accepts first. 

From Member A node, send `GET /v1/match2` to retrieve the `match2` id:
```
[
  {
    "id": "3be18c6e-367f-479e-a5c2-a52aafc6a376",
    "state": "proposed",
    "optimiser": "Optimiser",
    "memberA": "Member A",
    "memberB": "Member B",
    "demandA": "7c1134ba-f735-4f8f-9935-b8aca7d38e6b",
    "demandB": "3fdf525c-20d5-43dd-b387-27c0eac0f73a",
    "createdAt": "2023-05-09T16:10:53.921Z",
    "updatedAt": "2023-05-09T16:23:13.646Z"
  }
]
```

Accept the match by sending a `POST request to /v1/{match2Id}/accept`:
```
{
  "id": "f21d36fe-c2ba-4a60-b38d-ec3615d670ca",
  "state": "submitted",
  "localId": "2ece25be-1a66-4aff-b606-952e35c2ecab",
  "apiType": "match2",
  "transactionType": "accept",
  "submittedAt": "2023-05-09T16:29:03.904Z",
  "updatedAt": "2023-05-09T16:29:03.904Z"
}
```

Once the second member accepts, the `match2` state changes to `acceptedFinal`, and the states of `demand_a` and `demand_b` change to `allocated`. These demands can no longer be used in a new `match2`. 

From Member B node, send `GET /v1/match2` to retrieve the `match2` id:
```
[
  {
    "id": "89d26441-2ae8-4967-bbff-e7f642674b6e",
    "state": "acceptedA",
    "optimiser": "Optimiser",
    "memberA": "Member A",
    "memberB": "Member B",
    "demandA": "d51277a0-1e23-4d09-aceb-c10ecc147560",
    "demandB": "566f6819-eb37-4102-9cfb-1c79c03cdb9b",
    "createdAt": "2023-05-09T16:23:13.650Z",
    "updatedAt": "2023-05-09T16:29:19.259Z"
  }
]
```

Accept the match by sending a `POST request to /v1/{match2Id}/accept`:
```
{
  "id": "564964a7-2bae-4555-bf85-8127933ce262",
  "state": "submitted",
  "localId": "89d26441-2ae8-4967-bbff-e7f642674b6e",
  "apiType": "match2",
  "transactionType": "accept",
  "submittedAt": "2023-05-09T16:33:19.848Z",
  "updatedAt": "2023-05-09T16:33:19.848Z"
}
```

Finally, the `match2` state changes to `acceptedFinal`:
```
[
  {
    "id": "3be18c6e-367f-479e-a5c2-a52aafc6a376",
    "state": "acceptedFinal",
    "optimiser": "Optimiser",
    "memberA": "Member A",
    "memberB": "Member B",
    "demandA": "7c1134ba-f735-4f8f-9935-b8aca7d38e6b",
    "demandB": "3fdf525c-20d5-43dd-b387-27c0eac0f73a",
    "createdAt": "2023-05-09T16:10:53.921Z",
    "updatedAt": "2023-05-09T16:33:37.847Z"
  }
]
```

Both demands states change to allocated at this point. 

For example, with `demand_a`:
```
[
  {
    "id": "8c783421-eb61-4bdb-b719-aee945d02121",
    "state": "allocated",
    "localId": "18f832d2-162f-486c-bbc6-d4c436df0d63",
    "apiType": "demand_a",
    "transactionType": "creation",
    "submittedAt": "2023-05-09T15:04:19.422Z",
    "updatedAt": "2023-05-09T16:33:37.847Z"
  }
]
```