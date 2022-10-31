# Guard Rails

To ensure integrity of data within transactions (and therefore on chain), it's possible to define custom processes that validate transactions. When a user attempts to run a transaction to create or burn tokens, they also supply a validating process (a process ID and version number). If the validation fails, the transaction will not run. Processes are stored on chain and managed using [`dscp-process-management`](https://github.com/digicatapult/dscp-process-management/).

This method is called Guard Railing - it is difficult, and perhaps counter productive, to over restrict what can be in a transaction, however a few simple rules for each transaction type can greatly improve the quality of data on chain, and reduce typos or tokens with absent data from permanently ending up on chain.

## Writing processes

`dscp-node` has a set of [restrictions](https://github.com/digicatapult/dscp-node/#restrictions) that can be combined with [binary operators](https://github.com/digicatapult/dscp-node/#binary-operations) using postfix notation.

Example

```JSON
[
   {
      "Restriction":{
         "SenderHasInputRole":{
            "index":"0",
            "roleKey":"Supplier"
         }
      }
   },
   {
      "Restriction":{
         "FixedNumberOfInputs":{
            "numInputs":"1"
         }
      }
   },
   {
      "Op":"And"
   }
]
```

### Building a process from a token model

One of the simplest transactions is the creation of the initial state of an entity. For example, a transaction for a gallery to add a new work of art, a sculpture, to a chain being used to track art in galleries around the world.

- There are no `inputs` in the transaction, and the only `output` is a single `SCULPTURE` token.
- Within a token type, `roles` are consistent. For a `SCULPTURE` token we might expect the roles: `Artist`, `Owner` and `Gallery`.
- Every token has `literal` metadata for its `type` and `state`. For sculpture tokens, the `type` is always `SCULPTURE`. The `state` value depends on the transaction, for this transaction it will something to show it's newly added e.g. `new`.
- Other metadata is customisable and domain-specific. For a sculpture there might be `file` metadata containing an image and the artwork's dimensions.

`roles` and `metadata` you might expect as part of a `SCULPTURE` token - the keys would be defined in a token model by someone with domain knowledge:

| Metadata                        |
| :------------------------------ |
| `<Literal>` type: `SCULPTURE`   |
| `<Literal>` state: `new`        |
| `<File>` image: `image.png`     |
| `<File>` dimensions: `spec.pdf` |

Based on the information above it's possible to define a set of restrictions. First as this is adding something new, there are 0 inputs and 1 output:

```
  "FixedNumberOfInputs": [
    {
      "num_inputs": 0
    }
  ],
  "FixedNumberOfOutputs": [
    {
      "num_outputs": 1
    }
  ]
```

The transaction is intended to be run by galleries:

```
  "SenderHasOutputRole": [
    {
      "index": 0,
      "role_key": "Gallery"
    }
  ]
```

If more than one party could run the transaction, multiple instances of the above restriction with different `role_key`s could be set combined with the `or` boolean.

Presence of the other roles is enforced with:

```
  "OutputHasRole": [
    {
      "index": 0,
      "role_key": "Owner"
    }
  ],
  "OutputHasRole": [
    {
      "index": 0,
      "role_key": "Artist"
    }
  ]
```

This is a transaction to specifically make a `new` `SCULPTURE` token:

```
  "FixedOutputMetadataValue": [
    {
      "index": 0,
      "metadata_key": "state",
      "metadata_value": "new"
    }
  ],
  "FixedOutputMetadataValue": [
    {
      "index": 0,
      "metadata_key": "type",
      "metadata_value": "SCULPTURE"
    }
  ]
```

The token is expected to include image and dimensions files:

```
    {
      "index": 0,
      "metadata_key": "image",
      "metadata_value_type": "File"
    },
    {
      "index": 0,
      "metadata_key": "dimensions",
      "metadata_value_type": "File"
    }
```

The restrictions could be chained with `and` booleans to enforce they all must pass to validate the token.

### Handling arrays

Many restrictions refer to a specific index of an input or output to validate.
