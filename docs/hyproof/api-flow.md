## HyProof Api Flow

This document describes the API flow that can be used to demonstrate the usage of the HyProof project.

---

## HyProof Api Flow: Prerequisites

In order to go trough the entire flow, there are a couple of prerequisites required, described below.

### HyProof Api Flow: Prerequisites: Services

In order to be able to reproduce the steps described in this document you need to have a simple setup running ( refer to **[docker-compose.yml](https://github.com/digicatapult/dscp-hyproof-api/blob/main/docker-compose.yml)** or **`dscp-node`** as in **[dscp node repo](https://github.com/digicatapult/dscp-node)** + 2 **Postgres DBs** + **`dscp-identity-service`** as in **[dscp-identity-service](https://github.com/digicatapult/dscp-identity-service)** ), at minimum or at maximum a complex setup that include all the needed services for three personas ( refer to the **[docker-compose-3-personal.yml](https://github.com/digicatapult/dscp-hyproof-api/blob/main/docker-compose-3-personal.yml)** file ).

For the minimum option, all that's needed is to execute the following:

```sh
docker compose up -d && npm i && npm run db:migrate && npm run flows && npm run dev
```

---

### HyProof Api Flow: Prerequisites: Setting Up Aliases

The values set for each persona are your choice, they should provide a recognisable value in response bodies.

1. On each node, set alias for all node addresses w/ **`PUT`** **`/members/{address}`**:

```json
{"alias":"emma"}
```

```sh
# E.g.: Emma, Heidi, Reginald
curl -X 'PUT' http://localhost:3002/v1/members/5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY -H "Content-Type: application/json" -d '{"alias":"emma"}'
curl -X 'PUT' http://localhost:3002/v1/members/5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y -H "Content-Type: application/json" -d '{"alias":"heidi"}'
curl -X 'PUT' http://localhost:3002/v1/members/5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty -H "Content-Type: application/json" -d '{"alias":"reginald"}'
```

---

### HyProof Api Flow: Prerequisites: Retrieving Aliases

Once there are some aliases in the system you can retrieve them with the appropriate endpoint.

1. On each node return a list of all aliases as set above w/ **`GET`** **`/members`**:

```json
[
  { "address": "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty",
    "alias": "reginald" },
  { "address": "5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y",
    "alias": "heidi" },
  { "address": "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY",
    "alias": "emma" }
]
```

```sh
curl http://localhost:3002/v1/members
```

2. Optionally, on each node, return the main **[SS58 Address](https://wiki.polkadot.network/docs/learn-account-advanced)** that is currently in used by your node w/ **`GET`** **`/self`**

3. Optionally, on each node, query the member by alias or address w/ **`GET`** **`/members/{aliasOrAddress}`**

---

## HyProof Api Flow: Tokens Flow

The API flow for managing tokens.

---

### HyProof Api Flow: Tokens Flow: Context

In the case of performing tracking of green hydrogen, one might be able to do the on-chain tracking by allowing a token to be created of the type Initiated associated to it then morph this to a token of the type Issued.

---

### HyProof Api Flow: Tokens Flow: Hydrogen Producer

1. Token inserted into the local db ( and with that commitment created ) w/ **`POST`** **`/v1/certificate`**:

```json
{ "energy_consumed_mwh": 1,
  "production_end_time": "2023-12-05T21:36:42.808Z",
  "production_start_time": "2023-12-05T21:36:42.808Z",
  "energy_owner": "emma",
  "hydrogen_quantity_mwh": 1 }
```

```sh
curl -X POST http://localhost:3000/v1/certificate -H "Content-Type: application/json" \
  -d '{"energy_consumed_mwh":1,"production_end_time":"2023-12-05T21:36:42.808Z","production_start_time": "2023-12-05T21:36:42.808Z","energy_owner":"emma","hydrogen_quantity_mwh":1}'
```

2. The response will include some new elements such as **commitment** and **commitment_salt** and the id ( which is the most important one ). Get the id ( e.g.: **`3cdc813a-562f-4008-9ece-74a058a2bf57`** )

<!--

```json
{ "hydrogen_owner": "emma",
  "energy_owner": "emma",
  "hydrogen_quantity_mwh": 1,
  "original_token_id": null,
  "latest_token_id": null,
  "commitment": "aa48b83252a34ad1541399d95b8bda21",
  "commitment_salt": "f6b0b2246976a33c5c9b3333fc68eec6",
  "production_start_time": "2023-12-05T21:36:42.808Z",
  "production_end_time": "2023-12-05T21:36:42.808Z",
  "energy_consumed_mwh": 1,
  "id": "3cdc813a-562f-4008-9ece-74a058a2bf57",
  "state": "pending",
  "created_at": "2023-12-05T23:52:04.705Z",
  "updated_at": "2023-12-05T23:52:04.705Z",
  "embodied_co2": null }
```

-->

3. Token submitted or initiated on-chain using the id from the previous step ( **`3cdc813a-562f-4008-9ece-74a058a2bf57`** ) w/ POST /v1/certificate/{id}/initiation

```
N/A
```

```sh
curl -X POST http://localhost:3000/v1/certificate/3cdc813a-562f-4008-9ece-74a058a2bf57/initiation -d ''
```

<!--

```json
{ "api_type": "certificate",
  "local_id": "3cdc813a-562f-4008-9ece-74a058a2bf57",
  "hash": "0x73c298b5ada2875801e5fb9b8d8704a75ade97a33aed50c91db08a5900c16fa8",
  "state": "submitted",
  "id": "770e0e82-b4d7-4411-853e-6220fd6596e3",
  "created_at": "2023-12-05T23:58:13.745Z",
  "updated_at": "2023-12-05T23:58:13.745Z" }
```

-->

---
