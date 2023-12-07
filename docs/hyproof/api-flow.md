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

1. Get the address for self using the 1st, 2nd and 3rd Swagger w/ **`GET`** **`/self`** ( save the **`.address`** field ):

```sh
curl -s http://localhost:9000/v1/self -H 'accept: application/json' | jq -r .address
# 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
heidi=5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
curl -s http://localhost:9010/v1/self -H 'accept: application/json' | jq -r .address
# 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
emma=5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
curl -s http://localhost:9020/v1/self -H 'accept: application/json' | jq -r .address
# 5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y
reginald=5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y
```

2. Set the alias for all the personas using the 1st Swagger w/ **`POST`** **`/members/{address}`**:

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

```js
// Payloads:
'{"alias":"heidi"}'
'{"alias":"emma"}'
'{"alias": "reginald"}'
```

3. Set the alias for all the personas using the 2nd Swagger w/ **`POST`** **`/members/{address}`**:

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

```js
// Payloads:
'{"alias":"heidi"}'
'{"alias":"emma"}'
'{"alias": "reginald"}'
```

4. Set the alias for all the personas using the 3rd Swagger w/ **`POST`** **`/members/{address}`**:

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

```js
// Payloads:
'{"alias":"heidi"}'
'{"alias":"emma"}'
'{"alias": "reginald"}'
```

Note that you can check everything with GET /members.

---

### HyProof Api Flow: Prerequisites: Retrieving Aliases

Once there are some aliases in the system you can retrieve them with the appropriate endpoint.

1. On each node return a list of all aliases as set above w/ **`GET`** **`/members`**:

```sh
curl http://localhost:3002/v1/members
```

```js
// Response:
[
  { "address": "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty", "alias": "emma" },
  { "address": "5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y", "alias": "reginald" },
  { "address": "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY", "alias": "heidi" }
]
```

2. Optionally, on each node, return the main **[SS58 Address](https://wiki.polkadot.network/docs/learn-account-advanced)** that is currently in used by your node w/ **`GET`** **`/self`** and, last but not least, optionally, on each node, query the member by alias or address w/ **`GET`** **`/members/{aliasOrAddress}`**.

---

## HyProof Api Flow: Tokens Flow

The API flow for managing tokens.

---

### HyProof Api Flow: Tokens Flow: Context

In the case of performing tracking of green hydrogen, one might be able to do the on-chain tracking by allowing a token to be created of the type Initiated associated to it then morph this to a token of the type Issued.

---

### HyProof Api Flow: Tokens Flow: Hydrogen Producer

1. The new token need to be inserted into the local db prior to being added to the chain w/ **`POST`** **`/v1/certificate`** ( save the **`id`** and **`commitment_salt`** fields ):

```sh
response=$(
curl -s -X 'POST' \
  'http://localhost:8000/v1/certificate' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "energy_consumed_mwh": 2,
  "production_end_time": "2023-12-07T08:56:41.116Z",
  "production_start_time": "2023-12-07T08:56:41.116Z",
  "energy_owner": "emma",
  "hydrogen_quantity_mwh": 3
}'
)

id=$(echo $response | jq -r .id)
# 10b3dbc5-46d8-4b2b-a51c-eebcbfd76f91
commitment_salt=$(echo $response | jq -r .commitment_salt)
# 14d3f9f753f4296a214623877a0ca53b
```

```js
// Payload:
{
  "energy_consumed_mwh": 2,
  "production_end_time": "2023-12-07T08:56:41.116Z",
  "production_start_time": "2023-12-07T08:56:41.116Z",
  "energy_owner": "emma",
  "hydrogen_quantity_mwh": 3
}
```

2. The new token needs to be added to the chain w/ **`POST`** **`/v1/certificate/{id}/initiation`**:

```sh
curl -X POST http://localhost:8000/v1/certificate/${id}/initiation -H 'accept: application/json' -d ''
```

```js
// Payload:
""
```

3. After a period of time the token will be minted and marked as finalised and this can be checked w/ **`GET`** **`/v1/certificate/{id}/initiation`**:

```sh
curl http://localhost:8000/v1/certificate/${id}/initiation
# "state": "finalised"
```

---

### HyProof Api Flow: Tokens Flow: Energy Producer

1. The same token needs to be found using the 2nd persona, ergo the 2nd Swagger, and the its id needs to be grabbed ( with a blank createdAt ) w/ **`GET`** **`/v1/certificate`** ( save the **`id`** field ):

```sh
curl -s -X http://localhost:8010/v1/certificate -H 'accept: application/json' | jq -r .[1].id
# 0841d34e-0c61-419c-96fb-e77d2eca4d50
id=0841d34e-0c61-419c-96fb-e77d2eca4d50
```

2. After that, prior to the last step, the secret information that has been hashed need to be added in w/ **`PUT`** **`/v1/certificate/{id}`**:

```sh
curl -X 'PUT' http://localhost:8010/v1/certificate/0841d34e-0c61-419c-96fb-e77d2eca4d50 \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "commitment_salt": "'${commitment_salt}'",
  "energy_consumed_mwh": 2,
  "production_end_time": "2023-12-07T08:56:41.116Z",
  "production_start_time": "2023-12-07T08:56:41.116Z"
}'
```

3. Finally, the last token spawned from the previous one, needs to be added to the chain w/ **`PUT`** **`/v1/certificate/{id}/issuance`**:

```sh
curl -X 'POST' http://localhost:8010/v1/certificate/${id}/issuance \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "embodied_co2": 13
}'
```

```js
// Payload:
{ "embodied_co2": 13 }
```

4. Optionally, because after a small period of time the tx will eventually be included in the chain, you can eventually check that w/ **`GET`** **`/v1/certificate/{id}/issuance`**:

```sh
curl -X http://localhost:8010/v1/certificate/${id}/issuance -H 'accept: application/json'
```

---

### HyProof Api Flow: Tokens Flow: Regulator

In a similar way, the regulator can burn / revoke tokens.

---
