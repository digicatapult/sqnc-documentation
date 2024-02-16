# Sequence (SQNC) Architecture

Sequence (SQNC) architecture has two variations depending on the use case:

- In most cases, an external API creates, reads and burns tokens via HTTP using `sqnc-api`, which then handles communication with `sqnc-node` and `sqnc-ipfs`.
- When it's necessary to monitor on-chain state directly within an external API service (e.g. `sqnc-matchmaker-api`), the service subscribes to `sqnc-node` via WebSocket. This means it must also communicate with `sqnc-ipfs` via HTTP to upload/download files in tokens.

See the diagram for more details
![Architecture Diagram](../assets/architecture-v1.svg)

[Edit diagram](https://docs.google.com/drawings/d/1eanItroFbYsq9VPdpPe-2vMjSPNHgFpFmLiL6K_K5mM/edit)
