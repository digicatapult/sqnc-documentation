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

### HyProof Api Flow: Prerequisites: Setting Up Node Aliases

The values set for each persona are your choice, they should provide a recognisable value in response bodies.

1. On each node, set alias for all node addresses using **`PUT`** **`/members/{address}`**:

Example: TBD.

### HyProof Api Flow: Prerequisites: Retrieving nOde Aliases

Once there are some aliases in the system you can retrieve them with the appropriate endpoint.

Using **`GET`** **`/members`** on each node returns a list of all aliases as set above:

```json
[ { "address": "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty",
    "alias": "A"
  },
  { "address": "5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y",
    "alias": "B"
  },
  { "address": "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY",
    "alias": "C"
	} ]
```

---
