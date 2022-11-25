# **Veritable**

Documentation for Veritable

`Veritable` is an open source demonstration of supply chain tracking using `blockchain` technology. `Veritable` enables the sharing of data across organisational boundaries whilst assuring data sensitivity, security, and resilience. The platform has an adaptable design architecture to effectively represent the business logic of real-world operations. It includes features like:

- Immutability of vital metadata for ensuring trust between interacting actors.
- Distributed file system for secure sharing of large files and data-blobs.
- [OpenAPI specification](https://swagger.io/docs/specification/about/) for cross-IT system data interactions with the platform and respective endpoints.
- Identity and permission management approach for managing organisational credentials and inherent access to respective systems and data.
- Governance framework and protocol (and accompanying derivation methodology) for management of decision-making within the decentralised ecosystem.

## READMEs

This repo includes READMEs that explain concepts within Veritable:

- [Architecture](./docs/architecture.md)
- [Governance](./docs/governance.md)
- [Data Visibility](./docs/dataVisibility.md)
- [Runtime Upgrade](./docs/runtimeUpgrade.md)

## Repositories

### Active Veritable repositories

These repositories contain code being actively maintained as part of the Veritable project.

| Repository                                                                              | Description                                                                     |
| --------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| [veritable-documentation](https://github.com/digicatapult/dscp-documentation)           | Documentation for Veritable                                                     |
| [veritable-flux-infra](https://github.com/digicatapult/dscp-flux-infra)                 | Flux repo to bring up Veritable with Kubernetes                                 |
| [veritable-node](https://github.com/digicatapult/dscp-node)                             | The blockchain node for Veritable, built with Substrate                         |
| [veritable-ipfs](https://github.com/digicatapult/dscp-ipfs)                             | IPFS node for Veritable                                                         |
| [veritable-api](https://github.com/digicatapult/dscp-api)                               | API to create/retrieve tokens on `veritable-node` and files on `veritable-ipfs` |
| [veritable-identity-service](https://github.com/digicatapult/dscp-identity-service)     | API for managing chain member identities in Veritable                           |
| [veritable-process-management](https://github.com/digicatapult/dscp-process-management) | Library for managing restricted process flows on `veritable-node`               |

### Project-specific Veritable repositories

These repositories contain code written for specific projects that use Veritable.

| Repository                                               | Description                                                                                     |
| -------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| [inteli-api](https://github.com/digicatapult/inteli-api) | API for interacting with `veritable-api` and `veritable-identity-service` as an Inteli end-user |

### Deprecated Veritable repositories

These repositories use Veritable but are now deprecated.

| Repository                                                                 | Description                                 |
| -------------------------------------------------------------------------- | ------------------------------------------- |
| [inteli-demo](https://github.com/digicatapult/inteli-demo)                 | React demonstrator for the Inteli project   |
| [vitalam-demo-client](https://github.com/digicatapult/vitalam-demo-client) | React demonstrators for the VITALam project |

## Lingo

The world of `Blockchain` and `Substrate` is full of lingo. Here's our glossary of what we mean by some of these terms in the context of `Veritable`:

| Term                 | Means                                                                                                                                                                                 |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Entity               | A real world thing that's tracked across multiple tokens on chain. e.g. an engine, a building, a cow, a pizza or anything else.                                                       |
| Token                | Data about entities are added to the chain as tokens. Each token is a record of the state of a single entity at a specific moment in time.                                            |
| Process/Process flow | A single transaction that burns/creates one to many tokens. A process can (and often does) involve tokens for many different entities.                                                |
| Restrictions         | Rules on a process to enforce who can run the transaction and what they can do in the transaction.                                                                                    |
| Roles                | Part of a token. A set of `RoleKey: AccountID` pairs used to define who interacts with a token.                                                                                       |
| Metadata             | Part of a token. A set of `MetadataKey: value` pairs which describe attributes of an entity on chain e.g. its entity `type`, useful files (as an `IPFS` hash), IDs of related tokens. |
| Parent/Child         | Within a single transaction, all tokens that are burned become `parents` of all tokens that are created. The new tokens are `children` of the burned tokens.                          |

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

If you want to contribute to `Veritable` that's brilliant! First of all have a look at our contributor guidelines [here](./CONTRIBUTING.md).
