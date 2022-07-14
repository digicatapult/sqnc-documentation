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

//TODO

| Term | Means |
| ---- | ----- |

## Contributing

If you want to contribute to `DSCP` that's brilliant! First of all have a look at our contributor guidelines [here](./CONTRIBUTING.md).
