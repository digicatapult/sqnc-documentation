## HyProof API Flow

This document describes the API flow that can be used to demonstrate the usage of the HyProof project.

---

## Prerequisites

In order to go trough the entire flow, there are a couple of prerequisites required, described below.

### Prerequisites: Services

In order to be able to reproduce the steps described in this document you need to have the three personas setup ( a complex setup that include all the needed services for three personas ). For that, please refer to the **[docker-compose-3-persona.yml](https://github.com/digicatapult/sqnc-hyproof-api/blob/main/docker-compose-3-personal.yml)** file found in the **[sqnc-hyproof-api](https://github.com/digicatapult/sqnc-hyproof-api)** repository.

Use the following command to build and run the 3-persona testnet:

```sh
docker-compose -f docker-compose-3-persona.yml up --build -d
```

The Swagger GUI for both the Main SQNC API sys and the Identity API sys for all three personas can be accessed using:

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

### Prerequisites: Setting Up Aliases

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

### Prerequisites: Retrieving Aliases

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

## Token Flow

The remaining part of this document details the API workflow for demonstrating how HyProof manages tokens representing certified batches of hydrogen.

---

### Token Flow: Outline

To create a valid hydrogen certificate, we need the cooperation of 2 parties: `Heidi the Hydrogen Producer` and `Emma the Energy Owner`.

1. `Heidi` first creates a token in her local database (`initiated`) and submits it to the chain. This token contains a commitment to the secret time and energy data she has used. She passes the information used to create the commitment to `Emma` out-of-band.
2. `Emma` performs a calculation and loads the eCO2 to the token on-chain along with another commitment to the energy mix used at the time. The token/certificate is now `issued`.
3. `Reginald` finds the certificate to be invalid, and attaches evidence/reasoning to the certificate while transitioning it from `issued` to `revoked` status.

---

### Persona 1: Heidi the Hydrogen Producer

1. The new token needs to be inserted into the local db prior to being added to the chain w/ **`POST`** **`/v1/certificate`** 

```sh
heidi_response=$(
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

2. Save the local **`id`** and the **`commitment_salt`** fields for later:

```sh
heidi_local_id=$(echo $heidi_response | jq -r .id) \
commitment_salt=$(echo $heidi_response | jq -r .commitment_salt)
```

3. The new token now needs to be added to the chain w/ **`POST`** **`/v1/certificate/{id}/initiation`**:

```sh
curl -X POST http://localhost:8000/v1/certificate/$heidi_local_id/initiation -H 'accept: application/json' -d ''
```

```js
// Response payload:
// Note local_id == heidi_local_id from previous steps
// local_id, hash & id are all non-deterministic
{
  "api_type":"certificate",
  "transaction_type":"initiate_cert",
  "local_id":"UUID",
  "hash":"HASH",
  "state":"submitted",
  "id":"UUID",
  "created_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "updated_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)"
}
```

4. After a short period of time the token will be marked as `inBlock` and then `finalised`. This can be checked w/ **`GET`** **`/v1/certificate/{id}/initiation`**:

```sh
curl http://localhost:8000/v1/certificate/$heidi_local_id/initiation
```

```js
// Response payload:
// Note local_id, hash, id will be identical to previous step
{
  "api_type":"certificate",
  "transaction_type":"initiate_cert",
  "local_id":"UUID",
  "hash":"HASH",
  "state":"finalised",
  "id":"UUID",
  "created_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "updated_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)"
}
```

---

### Persona 2: Emma the Energy Owner

1. The same token needs to be found using the 2nd persona (`Emma the Energy Owner`), ergo the 2nd Swagger, and its **id** needs to be grabbed ( with a blank createdAt ) w/ **`GET`** **`/v1/certificate`**:

```sh
emma_response=$(
  curl -s -X 'GET' http://localhost:8010/v1/certificate \
  -H 'accept: application/json'
  )
```

2. Save the **`id`** field:

```sh
emma_local_id=$(echo $emma_response | jq -r '.[] | .id')
```

3. After that, the secret information transmitted out of band between the Hydrogen Producer `(Heidi)` and the Energy Owner `(Emma)` needs to be added using **`PUT`** **`/v1/certificate/{id}`**:

```sh
curl -X 'PUT' http://localhost:8010/v1/certificate/$emma_local_id \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "commitment_salt": "'"$commitment_salt"'",
  "energy_consumed_mwh": 2,
  "production_end_time": "2023-12-07T08:56:41.116Z",
  "production_start_time": "2023-12-07T07:56:41.116Z"
}'
```

4. The final step is to load the embodied CO2 data into the token and issue it on chain as a complete certificate w/ **`POST`** **`/v1/certificate/{id}/issuance`**:

```sh
curl -X 'POST' http://localhost:8010/v1/certificate/$emma_local_id/issuance \
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
  "id":"UUID",
  "state":"issued",
  "created_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "updated_at":"ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "embodied_co2":135
}
```

---

### Persona 3: Reginald the Regulator

`Reginald the Regulator` has the power to revoke invalid certificates, but to prevent foul play there must always be an explanation given.

The explanation will be given on a document stored in `IPFS` that is indelibly linked to the revoked certificate.

6. First retrieve the certificate to be revoked with w/ **`GET`** **`/v1/certificate/`**:

```sh
reggie_response=$(
  curl -s -X 'GET' http://localhost:8020/v1/certificate \
  -H 'accept: application/json'
  )
```

7. Save the **`id`** field locally:

```sh
reggie_local_id=$(echo $reggie_response | jq -r '.[] | .id')
```

8. Now load a `pdf` document containing the reason for revocation to `IPFS` and save the `file_id` w/ **`POST`** **`/v1/attachment`**:


```sh
file_id=$(
  curl -s -X 'POST' http://localhost:8020/v1/attachment \
  -H 'accept: application/json' \
  -H 'Content-Type: multipart/form-data' \
  -F 'file=@Revocation_Reason.pdf;type=application/pdf' | jq -r .id
  )
```

9. The final step is to submit the revocation transaction, referencing the `local_id` and the `file_id` w/ **`POST`** **`/v1/certificate/{id}/revocation`**:


```sh
curl -s -X 'POST' http://localhost:8020/v1/certificate/$reggie_local_id/revocation \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "reason": "'"$file_id"'"
}'
```

```sh
# Response payload
{
  "api_type":"certificate",
  "transaction_type":"revoke_cert",
  "local_id":"UUID",
  "hash":"HASH",
  "state":"submitted",
  "id":"UUID",
  "created_at": "ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "updated_at": "ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)"
}
```

10. After a short time to allow the transaction to finalise on-chain, the certificate can be retrieved and viewed w/ **`GET`** **`/v1/certificate/{id}`**:

```sh
curl -s http://localhost:8020/v1/certificate/$reggie_local_id -H 'accept: application/json' | jq -r
```

```sh
# Final revoked certificate:
{
  "hydrogen_owner": "Heidi",
  "energy_owner": "Emma",
  "regulator": "Reginald",
  "hydrogen_quantity_mwh": 3,
  "original_token_id": 1,
  "latest_token_id": 3,
  "commitment": "cb9dee10df30d05390f25f67fbbdf920",
  "commitment_salt": null,
  "production_start_time": null,
  "production_end_time": null,
  "energy_consumed_mwh": null,
  "id": "UUID",
  "state": "revoked",
  "created_at": "ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "updated_at": "ISO 8601 date (yyyy-MM-ddTHH:mm:ss.SSSZ)",
  "embodied_co2": 135,
  "revocation_reason": "UUID"
}
```

It is important to note that Reginald cannot see any of the private business-critical data loaded by `Heidi` or `Emma`.