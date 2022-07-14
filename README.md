# **DSCP**

Documentation for DSCP

`DSCP` is the **D**istributed-**S**upply-**C**hain-**P**latform; an open source demonstration of supply chain tracking using `blockchain` technology. `DSCP` enables the sharing of data across organisational boundaries whilst assuring data sensitivity, security, and resilience. The platform has an adaptable design architecture to effectively represent the business logic of real-world operations. It includes features like:

- Immutability of vital metadata for ensuring trust between interacting actors.
- Distributed file system for secure sharing of large files and data-blobs.
- [OpenAPI specification](https://swagger.io/docs/specification/about/) for cross-IT system data interactions with the platform and respective endpoints.
- Identity and permission management approach for managing organisational credentials and inherent access to respective systems and data.
- Governance framework and protocol (and accompanying derivation methodology) for management of decision-making within the decentralised ecosystem.

## READMEs

This repo includes READMEs that explain concepts within DSCP:

- [Architecture](./docs/architecture.md)
- [Governance](./docs/governance.md)

## Repositories

### Active DSCP repositories

These repositories contain code being actively maintained as part of the DSCP project.

| Repository                                                                         | Description                                                           |
| ---------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| [dscp-documentation](https://github.com/digicatapult/dscp-documentation)           | Documentation for DSCP                                                |
| [dscp-flux-infra](https://github.com/digicatapult/dscp-flux-infra)                 | Flux repo to bring up DSCP with Kubernetes                            |
| [dscp-node](https://github.com/digicatapult/dscp-node)                             | The blockchain node for DSCP, built with Substrate                    |
| [dscp-ipfs](https://github.com/digicatapult/dscp-ipfs)                             | IPFS node for DSCP                                                    |
| [dscp-api](https://github.com/digicatapult/dscp-api)                               | API to create/retrieve tokens on `dscp-node` and files on `dscp-ipfs` |
| [dscp-identity-service](https://github.com/digicatapult/dscp-identity-service)     | API for managing chain member identities in DSCP                      |
| [dscp-process-management](https://github.com/digicatapult/dscp-process-management) | Library for managing restricted process flows on `dscp-node`          |

### Project-specific DSCP repositories

These repositories contain code written for specific projects that use DSCP.

| Repository                                               | Description                                                                           |
| -------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| [inteli-api](https://github.com/digicatapult/inteli-api) | API for interacting with `dscp-api` and `dscp-identity-service` as an Inteli end-user |

### Deprecated DSCP repositories

These repositories use DSCP but are now deprecated.

| Repository                                                                 | Description                                 |
| -------------------------------------------------------------------------- | ------------------------------------------- |
| [inteli-demo](https://github.com/digicatapult/inteli-demo)                 | React demonstrator for the Inteli project   |
| [vitalam-demo-client](https://github.com/digicatapult/vitalam-demo-client) | React demonstrators for the VITALam project |

## Lingo

The world of `Blockchain` and `Substrate` is full of lingo. Here's our glossary of what we mean by some of these terms:

| Term        | Means                                                                                                                                 |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Token       | The Internet of things is a system of interrelated computing devices, mechanical and digital machines provided with unique identifier |
| Process     | A device that communicates readings of the environment over LoRaWAN. Elsewhere this may be referred to as a device or node            |
| Restriction | A device that communicates readings of the environment over LoRaWAN. Elsewhere this may be referred to as a device or node            |
| Role        | The term used in WASP v1 for `things`                                                                                                 |
| Metadata    | The contents of a token.                                                                                                              |
| Entity      | A real world thing that's being tracked across multiple tokens on chain. This could be a building, a cow, a pizza or anything else.   |

The following terms are related to `Substrate`:

| Term         | Means                                                                                                                                                                        |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Node         | A running instance of a blockchain client. Each node is part of the peer-to-peer network that allows blockchain participants to interact with one another.                   |
| FRAME        | Framework for Runtime Aggregation of Modularized Entities that enables developers to create blockchain runtime environments from a modular set of components called pallets. |
| Pallet       | A module that can be used to extend the capabilities of a FRAME-based runtime. Pallets bundle domain-specific logic with runtime primitives like events, and storage items   |
| Account      | An account represents an identity — usually of a person or an organization — that is capable of making transactions or holding funds                                         |
| Node address | A public key address to identify a specific account on a specific chain. [Substrate SS58](https://docs.substrate.io/reference/glossary/#ss58-address-format) format          |
| Transaction  | Transactions provide a mechanism for making changes to state that can be included in a block.                                                                                |
| Block        | A block is a single element of a blockchain that contains an ordered set of instructions — often in the form of transactions — that might result in a state change.          |
| Weight       | A convention to measure and manage the time it takes to validate a block. One unit of weight is one picosecond of execution time on reference hardware.                      |
| Origin       | A `FRAME` primitive that identifies the source of a dispatched function call into the runtime.                                                                               |

For more `Substrate` terms see [their glossary](https://docs.substrate.io/reference/glossary/).

## Contributing

If you want to contribute to `DSCP` that's brilliant! First of all have a look at our contributor guidelines [here](./CONTRIBUTING.md).
