# Guard Rails

To ensure integrity of data within transactions (and therefore on chain), it's possible to define custom processes that validate transactions. When a user attempts to run a transaction to create or burn tokens, they also supply a validating process (a process ID and version number). If the validation fails, the transaction will not run. Processes are stored on chain and managed using [`dscp-process-management`](https://github.com/digicatapult/dscp-process-management/).

This method is called Guard Railing - it is difficult, and perhaps counter productive, to over restrict what can be in a transaction, however a few simple rules for each transaction type can greatly improve the quality of data on chain, and reduce typos or tokens with missing data from permanently ending up on chain.

## Writing processes

`dscp-node` has a set of [restrictions](https://github.com/digicatapult/dscp-node/#restrictions) that can be combined with [binary operators](https://github.com/digicatapult/dscp-node/#binary-operations) using postfix notation.

Example

```JSON
[
   {
      "restriction":{
         "SenderHasInputRole":{
            "index":"0",
            "roleKey":"Supplier"
         }
      }
   },
   {
      "restriction":{
         "FixedNumberOfInputs":{
            "numInputs":"1"
         }
      }
   },
   {
      "op":"And"
   }
]
```

### Building a process from a token model

One of the simplest transactions is the creation of the initial state of an entity. For example, a transaction for a gallery to add a new work of art, a sculpture, to a chain being used to track art in galleries around the world.

- The transaction isn't related to any existing tokens on chain so there are no `inputs`, and the only `output` is a single `SCULPTURE` token.
- Within a token type, `roles` are consistent. For a `SCULPTURE` token we might expect the roles: `Artist`, `Owner` and `Gallery`. Role keys should be chosen by someone with domain knowledge and be based upon which parties will be members of the chain.
- Every token has `literal` metadata for its `type`, `state` and `version` thus every process has guard rails for the value of those keys. As this is for tokens about sculptures, `type: 'SCULPTURE'`. The `state` value depends on the transaction, for this transaction it will be something to show it's newly added e.g. `new`. `version: 0` as this is the first iteration of the sculpture token model. If the structure of a sculpture token needed to change at a later date, after it's 'live' and some sculpture tokens have been stored on chain, `version` can be used to handle the change (see [versioning](./bestPractice.md#versioning)).
- Other metadata is customisable and domain-specific. For a sculpture there might be `file` metadata containing an image and the artwork's dimensions.

`metadata` you might expect as part of a `SCULPTURE` token - the keys would be defined in a token model by someone with domain knowledge:

| Metadata                        |
| :------------------------------ |
| `<Literal>` type: `SCULPTURE`   |
| `<Literal>` state: `new`        |
| `<Literal>` version: `0`        |
| `<File>` image: `image.png`     |
| `<File>` dimensions: `spec.pdf` |

Based on the information above it's possible to define a set of restrictions. First, as this is adding something new, there are 0 inputs and 1 output:

```json
[
  {
    "restriction": {
      "FixedNumberOfInputs": {
        "num_inputs": 0
      }
    }
  },
  {
    "restriction": {
      "FixedNumberOfOutputs": {
        "num_outputs": 1
      }
    }
  }
]
```

The transaction is intended to be run by galleries:

```json
[
  {
    "restriction": {
      "SenderHasOutputRole": {
        "index": 0,
        "role_key": "Gallery"
      }
    }
  }
]
```

If more than one party could run the transaction, multiple instances of the above restriction with different `role_key`s could be set combined with the `or` boolean.

Presence of the other roles is enforced with:

```json
[
  {
    "restriction": {
      "OutputHasRole": {
        "index": 0,
        "role_key": "Owner"
      }
    }
  },
  {
    "restriction": {
      "OutputHasRole": {
        "index": 0,
        "role_key": "Artist"
      }
    }
  },
  { "op": "or" }
]
```

This is a transaction to specifically make a `new` `SCULPTURE` token with `version: 0`:

```json
[
  {
    "FixedOutputMetadataValue": {
      "index": 0,
      "metadata_key": "state",
      "metadata_value": "new"
    }
  },
  {
    "FixedOutputMetadataValue": {
      "index": 0,
      "metadata_key": "type",
      "metadata_value": "SCULPTURE"
    }
  },
  {
    "FixedOutputMetadataValue": {
      "index": 0,
      "metadata_key": "version",
      "metadata_value": "0"
    }
  }
]
```

The token is expected to include image and dimensions files:

```json
[
  {
    "FixedOutputMetadataValueType": {
      "index": 0,
      "metadata_key": "image",
      "metadata_value_type": "File"
    }
  },
  {
    "FixedOutputMetadataValueType": {
      "index": 0,
      "metadata_key": "dimensions",
      "metadata_value_type": "File"
    }
  }
]
```

The restrictions could be chained with `and` booleans to enforce they all must pass to validate the token e.g. `[restriction0, restriction1, op: and, restriction2, op: and, restriction3, op:and ...]`.
