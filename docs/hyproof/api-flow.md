## HyProof Api Flow

This document describes the API flow that can be used to demonstrate the usage of the HyProof project.

---

## HyProof Api Flow: Prerequisites

In order to go trough the entire flow, there are a couple of prerequisites required, described below.

### HyProof Api Flow: Prerequisites: Services

In order to be able to reproduce the steps described in this document you need to have the three personas setup ( a complex setup that include all the needed services for three personas ). For that, please refer to the **[docker-compose-3-persona.yml](https://github.com/digicatapult/dscp-hyproof-api/blob/main/docker-compose-3-personal.yml)** file found in the **[dscp-hyproof-api](https://github.com/digicatapult/dscp-hyproof-api)** repository.

Use the following command to build and run the 3-persona testnet:

```sh
docker-compose -f docker-compose-3-persona.yml up --build -d
```

The Swagger GUI for both the Main DSCP API sys and the Identity API sys for all three personas can be accessed using:

* **Persona 01** - **`Heidi (the Hydrogen Producer)`**:

  - **[localhost:8000/swagger](http://localhost:8000/swagger/#/)**

  - **[localhost:9000/v1/swagger](http://localhost:9000/v1/swagger/#/)**

* **Persona 02** - **`Emma (the Energy Owner)`**:

  - **[localhost:8010/swagger](http://localhost:8010/swagger/#/)**

  - **[localhost:9010/v1/swagger](http://localhost:9010/v1/swagger/#/)**

* **Persona 03** - **`Reginald (the Regulator)`**:

  - **[localhost:8020/swagger](http://localhost:8020/swagger/#/)**

  - **[localhost:9020/v1/swagger](http://localhost:9020/v1/swagger/#/)**

---

### HyProof Api Flow: Prerequisites: Setting Up Aliases

The values set for each persona are your choice but, they should provide a recognisable value in response bodies.

1. Get the address for self using the 1st, 2nd and 3rd Swagger w/ **`GET`** **`/self`** ( save the **`.address`** field ):

```sh
heidi=$(curl -s http://localhost:9000/v1/self -H 'accept: application/json' | jq -r .address)
emma=$(curl -s http://localhost:9010/v1/self -H 'accept: application/json' | jq -r .address)
reginald=$(curl -s http://localhost:9020/v1/self -H 'accept: application/json' | jq -r .address)
```

These commands should populate the following environment variables:
```sh
heidi = 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
emma = 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
reginald = 5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y
```

2. Set the aliases for all the personas using the 1st Swagger w/ **`POST`** **`/members/{address}`**:

```sh
curl -X 'PUT' http://localhost:9000/v1/members/$heidi \
  -H 'Content-Type: application/json' \
  -d '{"alias":"Heidi"}'

curl -X 'PUT' http://localhost:9000/v1/members/$emma \
  -H 'Content-Type: application/json' \
  -d '{"alias": "Emma"}'

curl -X 'PUT' http://localhost:9000/v1/members/$reginald \
  -H 'Content-Type: application/json' \
  -d '{"alias": "Reginald"}'
```

3. Set the aliases for all the personas using the 2nd Swagger w/ **`POST`** **`/members/{address}`**:

```sh
curl -X 'PUT' http://localhost:9010/v1/members/$heidi \
  -H 'Content-Type: application/json' \
  -d '{"alias":"Heidi"}'

curl -X 'PUT' http://localhost:9010/v1/members/$emma \
  -H 'Content-Type: application/json' \
  -d '{"alias": "Emma"}'

curl -X 'PUT' http://localhost:9010/v1/members/$reginald \
  -H 'Content-Type: application/json' \
  -d '{"alias": "Reginald"}'
```

4. Set the aliases for all the personas using the 3rd Swagger w/ **`POST`** **`/members/{address}`**:

```sh
curl -X 'PUT' http://localhost:9020/v1/members/$heidi \
  -H 'Content-Type: application/json' \
  -d '{"alias":"Heidi"}'

curl -X 'PUT' http://localhost:9020/v1/members/$emma \
  -H 'Content-Type: application/json' \
  -d '{"alias": "Emma"}'

curl -X 'PUT' http://localhost:9020/v1/members/$reginald \
  -H 'Content-Type: application/json' \
  -d '{"alias": "Reginald"}'
```

---

### HyProof Api Flow: Prerequisites: Retrieving Aliases

Once there are some aliases in the system you can retrieve them by passing a `GET` to the `/members` endpoint.

1. On each node return a list of all aliases as set above:

```sh
curl http://localhost:9000/v1/members
# OR curl http://localhost:9010/v1/members 
# OR curl http://localhost:9020/v1/members
```

```js
// Response:
[
  { "address": "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty", "alias": "Emma" },
  { "address": "5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y", "alias": "Reginald" },
  { "address": "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY", "alias": "Heidi" }
]
```

2. Optionally, on each node, return the main **[SS58 Address](https://wiki.polkadot.network/docs/learn-account-advanced)** that is currently in used by your node w/ **`GET`** **`/self`** and, last but not least, optionally, on each node, query the member by alias or address w/ **`GET`** **`/members/{aliasOrAddress}`**.

---

## HyProof Api Flow: Token Flow

The API flow for managing tokens.

---

### HyProof Api Flow: Token Flow: Context

To create a valid hydrogen certificate, we need the cooperation of 2 parties: `Heidi the Hydrogen Producer` and `Emma the Energy Owner`.

1. `Heidi` first creates a token in her local database (`initiated`) and submits it to the chain. This token contains a commitment to the secret time and energy data she has used. She passes the information used to create the commitment to `Emma` out-of-band.
2. `Emma` performs a calculation and loads the eCO2 to the token on-chain along with another commitment to the energy mix used at the time. The token/certificate is now `issued`.

---

### HyProof Api Flow: Token Flow: Hydrogen Producer

1. The new token needs to be inserted into the local db prior to being added to the chain w/ **`POST`** **`/v1/certificate`** 

```sh
response=$(
curl -s -X 'POST' \
  'http://localhost:8000/v1/certificate' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{ 
  "energy_consumed_mwh": 2,
  "production_end_time": "2023-12-07T08:56:41.116Z",
  "production_start_time": "2023-12-07T07:56:41.116Z",
  "energy_owner": "Emma",
  "regulator": "Reginald",
  "hydrogen_quantity_mwh": 3
}'
)
```

2. Save the **`database id`** and **`commitment_salt`** fields locally:

```sh
database_id=$(echo $response | jq -r .id) \
commitment_salt=$(echo $response | jq -r .commitment_salt)
```

3. The new token now needs to be added to the chain w/ **`POST`** **`/v1/certificate/{id}/initiation`**:

```sh
curl -X POST http://localhost:8000/v1/certificate/$database_id/initiation -H 'accept: application/json' -d ''
```

```js
// Response payload:
// Note local_id == database_id from previous steps
// local_id, hash & id are all non-deterministic
{
  "api_type":"certificate",
  "transaction_type":"initiate_cert",
  "local_id":"44ca54da-28e8-4cfe-8e0b-7e668045b630",
  "hash":"0x63a3e244681a3850a1b44e6df70b41c17fd4090f286c8ce08fbd855f8d4fb385",
  "state":"submitted",
  "id":"779cb38f-d5e1-4deb-93b9-4c476d639cf6",
  "created_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "updated_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)"
}
```

4. After a short period of time the token will be marked as `inBlock` and then `finalised`. This can be checked w/ **`GET`** **`/v1/certificate/{id}/initiation`**:

```sh
curl http://localhost:8000/v1/certificate/$database_id/initiation
```

```js
// Response payload:
// Note local_id, hash, id will be identical to previous step
{
  "api_type":"certificate",
  "transaction_type":"initiate_cert",
  "local_id":"44ca54da-28e8-4cfe-8e0b-7e668045b630",
  "hash":"0x63a3e244681a3850a1b44e6df70b41c17fd4090f286c8ce08fbd855f8d4fb385",
  "state":"finalised",
  "id":"779cb38f-d5e1-4deb-93b9-4c476d639cf6",
  "created_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "updated_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)"
}
```

---

### HyProof Api Flow: Token Flow: Energy Producer 

1. The same token needs to be found using the 2nd persona (`Emma the Energy Owner`), ergo the 2nd Swagger, and its **id** needs to be grabbed ( with a blank createdAt ) w/ **`GET`** **`/v1/certificate`**:

```sh
response=$(
  curl -s -X 'GET' http://localhost:8010/v1/certificate \
  -H 'accept: application/json'
  )
```

2. Save the **`id`** field:

```sh
database_id=$(echo $response | jq -r '.[] | .id')
```

3. After that, the secret information transmitted out of band between the Hydrogen Producer `(Heidi)` and the Energy Owner `(Emma)` needs to be added using **`PUT`** **`/v1/certificate/{id}`**:

```sh
curl -X 'PUT' http://localhost:8010/v1/certificate/$database_id \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "commitment_salt": $commitment_salt,
  "energy_consumed_mwh": 2,
  "production_end_time": "2023-12-07T08:56:41.116Z",
  "production_start_time": "2023-12-07T07:56:41.116Z"
}'
```

4. The final step is to load the embodied CO2 data into the token and issue it on chain as a complete certificate w/ **`PUT`** **`/v1/certificate/{id}/issuance`**:

```sh
curl -X 'POST' http://localhost:8010/v1/certificate/$database_id/issuance \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "embodied_co2": 135
}'
```

5. After a short period to finalise the transaction on chain, you can retrieve the complete certificate w/ **`GET`** **`/v1/certificate/{id}/issuance`**:

```sh
curl -X 'GET' http://localhost:8010/v1/certificate \
  -H 'accept: application/json'
```

```sh
# Final certificate payload:
{
  "hydrogen_owner":"Heidi",
  "energy_owner":"Emma",
  "regulator":"Reginald",
  "hydrogen_quantity_mwh":3,
  "original_token_id":1,
  "latest_token_id":2,
  "commitment":"d1b90d43c87e761b2373d8a3faa6cd2f",
  "commitment_salt":"0233f6fa708fdd2af20124854e473533",
  "production_start_time":"2023-12-07T07:56:41.116Z",
  "production_end_time":"2023-12-07T08:56:41.116Z",
  "energy_consumed_mwh":2,
  "id":"82d31f2f-416f-40bd-ac77-6b9cb8713537",
  "state":"issued",
  "created_at":"2023-12-11T14:06:12.822Z",
  "updated_at":"2023-12-11T14:14:08.059Z",
  "embodied_co2":135
}
```
---

### HyProof Api Flow: Token Flow: Regulator

`Reginald the Regulator` has the power to revoke invalid certificates, but to prevent foul play there must always be an explanation given. 

The explanation will be given on a document stored in `IPFS` that is indelibly linked to the revoked certificate.

---
