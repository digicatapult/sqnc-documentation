# DSCP Architecture

DSCP architecture has two variations depending on the use case:

- In most cases, an external API creates, reads and burns tokens via HTTP using `dscp-api`, which then handles communication with `dscp-node` and `dscp-ipfs`.
- When it's necessary to monitor on-chain state directly within an external API service (e.g. `dscp-matchmaker-api`), the service subscribes to `dscp-node` via WebSocket. This means it must also communicate with `dscp-ipfs` via HTTP to upload/download files in tokens.

See the diagram for more details
![Architecture Diagram](../assets/architecture-v1.svg)

[Edit diagram](https://docs.google.com/drawings/d/1eanItroFbYsq9VPdpPe-2vMjSPNHgFpFmLiL6K_K5mM/edit)
