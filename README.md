# **Sequence (SQNC)**

Documentation for Sequence (SQNC)

`SQNC` is an an open source demonstration of supply chain tracking using `blockchain` technology. `SQNC` enables the sharing of data across organisational boundaries whilst assuring data sensitivity, security, and resilience. The platform has an adaptable design architecture to effectively represent the business logic of real-world operations. It includes features like:

- Immutability of vital metadata for ensuring trust between interacting actors.
- Distributed file system for secure sharing of large files and data-blobs.
- [OpenAPI specification](https://swagger.io/docs/specification/about/) for cross-IT system data interactions with the platform and respective endpoints.
- Identity and permission management approach for managing organisational credentials and inherent access to respective systems and data.
- Governance framework and protocol (and accompanying derivation methodology) for management of decision-making within the decentralised ecosystem.

## READMEs

This repo includes READMEs that explain concepts within SQNC:

- [Architecture](./docs/architecture.md)
- [Governance](./docs/governance.md)
- [Data Visibility](./docs/dataVisibility.md)
- [Runtime Upgrade](./docs/runtimeUpgrade.md)
- [Token Models](./docs/tokenModels/index.md)

## Projects

READMEs that explain specific SQNC projects:

- [Logistics Living Lab](./docs/l3/index.md)
- [HyProof](./docs/hyproof/index.md)

## Repositories

### Active SQNC repositories

These repositories contain code being actively maintained as part of the SQNC project.

| Repository                                                                         | Description                                                                   |
| ---------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| [sqnc-documentation](https://github.com/digicatapult/sqnc-documentation)           | Documentation for SQNC                                                        |
| [sqnc-flux-infra](https://github.com/digicatapult/sqnc-flux-infra)                 | Flux repo to bring up SQNC with Kubernetes                                    |
| [sqnc-node](https://github.com/digicatapult/sqnc-node)                             | The blockchain node for SQNC, built with Substrate                            |
| [sqnc-ipfs](https://github.com/digicatapult/sqnc-ipfs)                             | IPFS node for SQNC                                                            |
| [openapi-merger](https://github.com/digicatapult/openapi-merger)                   | Amalgamated API specs for OpenAPI                                             |
| [sqnc-identity-service](https://github.com/digicatapult/sqnc-identity-service)     | API for managing chain member identities in SQNC                              |
| [sqnc-process-management](https://github.com/digicatapult/sqnc-process-management) | Library for managing restricted process flows on `sqnc-node`                  |
| [sqnc-matchmaker-api](https://github.com/digicatapult/sqnc-matchmaker-api)         | End-user API for matching 'demands' from different supply chain organisations |

### Project-specific SQNC repositories

These repositories contain code written for specific projects that use Sequence (SQNC).

| Repository                                                                 | Description                                                                     |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| [l3-flux-infra](https://github.com/digicatapult/l3-flux-infra)             | Flux repo to bring up SQNC with Kubernetes for the Logistics Living Lab project |
| [sqnc-hyproof-api](https://github.com/digicatapult/sqnc-hyproof-api)       | SQNC HyProof API layer                                                          |
| [sqnc-hyproof-client](https://github.com/digicatapult/sqnc-hyproof-client) | SQNC HyProof graphic user interface front-end client                            |

### Deprecated SQNC repositories

These repositories use SQNC but are now deprecated.

| Repository                                                                 | Description                                                                           |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| [inteli-demo](https://github.com/digicatapult/inteli-demo)                 | React demonstrator for the Inteli project                                             |
| [vitalam-demo-client](https://github.com/digicatapult/vitalam-demo-client) | React demonstrators for the VITALam project                                           |
| [dscp-api](https://github.com/digicatapult/dscp-api)                       | API to create/retrieve tokens on `sqnc-node` and files on `sqnc-ipfs`                 |
| [inteli-api](https://github.com/digicatapult/inteli-api)                   | API for interacting with `sqnc-api` and `sqnc-identity-service` as an Inteli end-user |

## Lingo

The world of `Blockchain` and `Substrate` is full of lingo. Here's our glossary of what we mean by some of these terms in the context of `SQNC`:

| Term                 | Means                                                                                                                                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Entity               | A real world thing tracked across multiple tokens on chain. e.g. an engine, a building. Can also be a process e.g. A purchase order for an engine, the energy performance certification of a building. |
| Token                | Data about entities are added to the chain as tokens. Each token is a record of the state of a single entity at a specific moment in time.                                                             |
| Token type           | Tokens about the same entity are grouped together by their token type e.g. every token about the order of engine would have `TYPE: ORDER`.                                                             |
| Process/Process flow | A single chain transaction that burns/creates one to many tokens. A process can (and often does) involve tokens for many different entities.                                                           |
| Restrictions         | Rules on a process to enforce who can run the transaction and what the contents of the transaction can be. Restrictions help with integrity of data on chain.                                          |
| Roles                | Part of a token. A set of `RoleKey: AccountID` pairs used to define who interacts with a token.                                                                                                        |
| Metadata             | Part of a token. A set of `MetadataKey: value` pairs which describe attributes of an entity on chain e.g. its entity `TYPE`, useful files (as an `IPFS` hash), IDs of related tokens.                  |
| Parent/Child         | Within a single transaction, all tokens that are burned become `parents` of all tokens that are created. The new tokens are `children` of the burned tokens.                                           |

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

If you want to contribute to `SQNC` that's brilliant! First of all have a look at our contributor guidelines [here](./CONTRIBUTING.md).
