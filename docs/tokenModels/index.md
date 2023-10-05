# Token Models

Reducing the time it takes to generate new DSCP projects is a long-term improvement goal of the DSCP platform. There can be a lot of manual setup when starting a new DSCP project, and one of the most time-consuming is defining a project's tokens. Tokens on chain are the source of truth in any implementation of DSCP. Defining a **token model** means:

- representing real-world entities as tokens with types, roles and metadata.
- representing real-world status of entities at a particular moment in time. (e.g. completing the build of a part, dispatching an order of three parts) as process flows - chain transactions that burn/create one to many tokens.
- deriving the necessary guard rails (restrictions) to ensure integrity of data within transactions (and therefore on chain).
- deriving the user-facing API routes required to run process flows via [dscp-api](https://github.com/digicatapult/dscp-api). This includes database schema for local representation of tokens.

In our roadmap, we plan on implementing:

    - re-usable token types, roles and metadata.
    - re-usable process flows.
    - a visual framework for defining new token types, roles, metadata and process flows. A 'skilled technical user' with domain knowledge could design flow diagrams to be interpreted by a DSCP expert into transactions (with guard rails), API routes and database schema. A visual tool would aim to reduce the knowledge required for a user to start a DSCP project - especially in terms of needing to understand blockchain idiosyncrasies.
    - a data schema for representing token type, roles and metadata and process flows. The schema could be programmatically interpreted to generate transactions (with guard rails) and API routes.
    - export of the visual diagram to that data schema.

## READMEs

Related concepts for token models:

- [Token Model Diagram Components](./components.md)
- [Token Model Example](./example.md)
- [Best Practice](./bestPractice.md)
- [Guard Rails](./guardRails.md)
- [dscp-lang - a DSL parser and compiler for writing token models](./language.md)
