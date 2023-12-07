## HyProof Api Flow

This document describes the API flow that can be used to demonstrate the usage of the HyProof project.

---

## HyProof Api Flow: Prerequisites

In order to go trough the entire flow, there are a couple of prerequisites required, described below.

### HyProof Api Flow: Prerequisites: Services

In order to be able to reproduce the steps described in this document you need to have the three personas setup ( a complex setup that include all the needed services for three personas ). For that, please refer to the **[docker-compose-3-personal.yml](https://github.com/digicatapult/dscp-hyproof-api/blob/main/docker-compose-3-personal.yml)** file found in the **[dscp-hyproof-api](https://github.com/digicatapult/dscp-hyproof-api)** repository.

To run this, a command similar to the next one can be used:

```sh
docker-compose -f docker-compose-3-personal.yml down -v && docker-compose -f docker-compose-3-personal.yml up -d && \
npm i && npm run db:migrate && \
npm run flows
```

The Swagger GUI for both the Main DSCP API sys and the Identity API sys for all three personas can be accessed using:

* **Persona 01** - **`heidi`**:

  - http://localhost:8000/swagger/#/

  - http://localhost:9000/v1/swagger/#/

* **Persona 02** - **`emma`**:

  - http://localhost:8010/swagger/#/

  - http://localhost:9010/v1/swagger/#/

* **Persona 03** - **`reginald`**:

  - http://localhost:8020/swagger/#/

  - http://localhost:9020/v1/swagger/#/

---

### HyProof Api Flow: Prerequisites: Setting Up Aliases

The values set for each persona are your choice but, they should provide a recognisable value in response bodies.

<!-- 1. Set the alias for self and for persona 02 in the using the first Swagger w/ /self and /members/{address} : -->

1. Get the address for self using the 1st, 2nd and 3rd Swagger w/ /self:

```sh
curl -s -X 'GET' 'http://localhost:9000/v1/self' -H 'accept: application/json' | jq -r .address
# 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
heidi=5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
curl -s -X 'GET' 'http://localhost:9010/v1/self' -H 'accept: application/json' | jq -r .address
# 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
emma=5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
curl -s -X 'GET' 'http://localhost:9020/v1/self' -H 'accept: application/json' | jq -r .address
# 5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y
reginald=5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y
```

2. Set the alias for all the personas using the 1st Swagger w/ POST /members/{address}:

```sh
curl -X 'PUT' http://localhost:9000/v1/members/${heidi} \
  -H 'Content-Type: application/json' \
  -d '{"alias":"heidi"}'

curl -X 'PUT' http://localhost:9000/v1/members/${emma} \
  -H 'Content-Type: application/json' \
  -d '{"alias":"emma"}'

curl -X 'PUT' http://localhost:9000/v1/members/${reginald} \
  -H 'Content-Type: application/json' \
  -d '{"alias": "reginald"}'
```

3. Set the alias for all the personas using the 2nd Swagger w/ POST /members/{address}:

```sh
curl -X 'PUT' http://localhost:9010/v1/members/${heidi} \
  -H 'Content-Type: application/json' \
  -d '{"alias":"heidi"}'

curl -X 'PUT' http://localhost:9010/v1/members/${emma} \
  -H 'Content-Type: application/json' \
  -d '{"alias":"emma"}'

curl -X 'PUT' http://localhost:9010/v1/members/${reginald} \
  -H 'Content-Type: application/json' \
  -d '{"alias": "reginald"}'
```

4. Set the alias for all the personas using the 3rd Swagger w/ POST /members/{address}:

```sh
curl -X 'PUT' http://localhost:9020/v1/members/${heidi} \
  -H 'Content-Type: application/json' \
  -d '{"alias":"heidi"}'

curl -X 'PUT' http://localhost:9020/v1/members/${emma} \
  -H 'Content-Type: application/json' \
  -d '{"alias":"emma"}'

curl -X 'PUT' http://localhost:9020/v1/members/${reginald} \
  -H 'Content-Type: application/json' \
  -d '{"alias": "reginald"}'
```

<!--
```sh
# E.g.: Emma, Heidi, Reginald
curl -X 'PUT' http://localhost:3002/v1/members/5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY -H "Content-Type: application/json" -d '{"alias":"emma"}'
curl -X 'PUT' http://localhost:3002/v1/members/5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y -H "Content-Type: application/json" -d '{"alias":"heidi"}'
curl -X 'PUT' http://localhost:3002/v1/members/5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty -H "Content-Type: application/json" -d '{"alias":"reginald"}'
```
-->

Note that you can check everything with GET /members.

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
