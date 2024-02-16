# SQNC Token Model DSL

As an alternative approach to the [token model diagrams](./components.md) (see also the [pizza example](./example.md)) we have begun implementing a domain-specific-language (DSL) for designing token process flows. Note that this initially is being used as an illustration of a token model, but in the future we will be developing a compiler for automatically generating process [guard rails](./guardRails.md) using a purpose built compiler.

## sqnc-lang

`sqnc-lang`, the tool we use for parsing token flows, can be found in the [sqnc-node](https://github.com/digicatapult/sqnc-node) repository. At the moment we don't provide binary releases of `sqnc-lang` so to use the tool you will need a valid and up to date `rust` development environment. See [here](https://www.rust-lang.org/) for details.

To use the tool you will need to compile `sqnc-lang` by running from the root of that repository:

```
cargo build --profile=production -p sqnc-lang
```

That will build `sqnc-lang` under the `./target/production` directory. You can then parse a token definition file using the `parse` command, for example:

```
./target/production/sqnc-lang parse ./tools/lang/examples/l3.sqnc
```

## Writing a SQNC token model

A token model consists of two different things; token type definitions and token transition functions. These are written together in a single file (in these early days we don't yet have module support) and form the rules by which tokens can be created, modified and consumed.

### comments and whitespace

Single line comments can be specified using `//` and multiline comments by wrapping them in `/*` and `*/` similar to most C like languages. Additional whitespace between declaration elements is generally allowed.

### tokens

Tokens generally represent some persistent real world concept that will undergo various state transitions in their lifetime. Tokens will have various fields on them which describe different properties of the real world thing. The syntax for defining a token is as follows:

```
token [token_name] {
    [field1_name]: [field1_type],
    [field2_name]: [field2_type],
    ...etc
}
```

Names of tokens and fields are limited to a sequence of alphanumeric or '\_' characters but must start with an alpha character.

Types can be one of the following:

1. `Role` - which identifies a transacting account. This is useful for limiting transactions to certain individuals for example the `owner` of a thing
2. `File` - a reference to a file stored off-chain
3. `Literal` - an ASCII string value (max length 32 characters) that will be persisted on-chain
4. A specific literal value such as `"created"`. Literal values follow the same escaping principles as strings in JSON.
5. `None` - That the field must not be specified
6. A reference to a token type which will be persisted based on the original token id of that token
7. A union of any of the types above, for example the field `state: "created" | "allocated" | None` will allow the field `state` to contain either the literal `"created"` the literal `"allocated"` or to not be present at all

An example of a complete set of token definitions taken from the L3 project is then:

```rs
token Demand {
  owner: Role,
  subtype: "demand_a" | "demand_b",
  state: "created" | "allocated" | "cancelled",
}

token Match2 {
  optimiser: Role,
  member_a: Role,
  demand_a: Demand,
  member_b: Role,
  demand_b: Demand,
  state: "proposed" | "acceptedA" | "acceptedB" | "acceptedFinal" | "cancelled",
  replaces: Match2 | None,
}
```

### transition functions

Token transition functions specify the ways in which tokens can be created, manipulated and consumed within a token model. In reality this means they describe the conditions under which a transition should be allowed or not allowed to occur. They do this be specifying 3 things:

1. A set of input tokens of specific token types that must be provided when executing a transition
2. A set of output tokens of specific token types that must be provided when executing a transition
3. A set of additional constraints in the form of a set of boolean expressions that must also be satisfied

The syntax for defining a transition is then as follows:

```
[pub|priv] fn [fn_name] |
    [input_arg_1_name]: [input_arg_1_type],
    [input_arg_2_name]: [input_arg_2_type],
    ...
| => |
    [output_arg_1_name]: [output_arg_1_type],
    [output_arg_2_name]: [output_arg_2_type],
    ...
| where {
    [additional_condition_1],
    [additional_condition_2],
    ...
}
```

Additional conditions in the `where` clause must all pass for the provided set of inputs and outputs at execution time in order for a token transition to be valid. These additional conditions then take the form of a boolean expression comprised of a set of comparisons combined together with the logical operators `and` (`&`), `or` (`|`), and `xor` (`^`). The following comparisons are possible:

1. equality of an input token to an output token `[input_arg_1_name] == [output_arg_1_name]`. This condition implies the tokens represent the same logical entity not that their field values match.
2. equality of an input token field to an output token field `[input_arg_1_name].[field_1] == [output_arg_1_name].[field_2]`.
3. equality of an output token field to an input token `[output_arg_1_name].[field] == input_arg_1_name`
4. equality of an input or output token field to a literal value `[input_arg_1_name].[field_1] == "state"`
5. equality of an input or output token field to the transaction caller (`sender`) `[input_arg_1_name].[field_1] == sender`. Note the field must be of type `Role`.
6. inequality of any of the above replacing the `==` symbol with `!=`
7. check an input or output token field is of type `Role`, `Literal` or `File` with `[input_arg_1_name].[field_1]: File`. This check can be negated by replacing `:` with `!:`
8. evaluation of another transition function `[fn_name] | [input_1], [input_2], ... | => | [output_1], [output_2], ... |`

The precedence of logical operators is as follows:

1. `&`
2. `|`
3. `^`

Though this can be overridden by wrapping an expression in parenthesis `()`.

Finally function visibility can be used to specify that some transition functions should only be referenced as conditions and should not be published. Public functions should be declared using `pub` and functions declared either with `priv` or without a specified visibility are not published.

As an example the following function definitions are taken from L3:

```rs

fn clone_demand_partial | a: Demand | => | b: Demand | where {
  a == b,
  a.owner == b.owner,
  a.subtype == b.subtype
}

fn clone_demand | a: Demand | => | b: Demand | where {
  clone_demand_partial | a | => | b |,
  a.state == b.state,
}

fn clone_match_partial | a: Match2 | => | b: Match2 | where {
  a == b,
  a.optimiser == b.optimiser,
  a.member_a == b.member_a,
  a.demand_a == b.demand_a,
  a.member_b == b.member_b,
  a.demand_b == b.demand_b,
  a.replaces == b.replaces,
}

fn clone_match | a: Match2 | => | b: Match2 | where {
  clone_match_partial | a | => | b |,
  a.state == b.state,
}

pub fn demand_create || => | out: Demand | where {
  out.owner == sender,
  out.state == "created",
  out.parameters: File
}

pub fn match2_propose |
  demand_a_in: Demand,
  demand_b_in: Demand,
| => |
  demand_a_out: Demand,
  demand_b_out: Demand,
  match2_out: Match2
| where {
  clone_demand |demand_a_in | => |demand_a_out|,
  demand_a_in.subtype == "demand_a",
  clone_demand |demand_b_in | => |demand_b_out|,
  demand_b_in.subtype == "demand_b",
  match2_out.optimiser == sender,
  (match2_out.demand_a == demand_a_in & match2_out.member_a == demand_a_in.owner) &
  (match2_out.demand_b == demand_b_in & match2_out.member_b == demand_b_in.owner),
  match2_out.state == "proposed",
  match2_out.replaces: None
}

pub fn match2_accept | in: Match2 | => | out: Match2 | where {
  clone_match_partial | in | => | out |,
  in.state == "proposed",
  (out.state == "acceptedA" & out.member_a == sender) |
  (out.state == "acceptedB" & out.member_b == sender)
}

pub fn match2_acceptFinal | demand_a_in: Demand, demand_b_in: Demand, match_in: Match2 | => | demand_a_out: Demand, demand_b_out: Demand, match_out: Match2 | where {
  clone_demand_partial | demand_a_in | => | demand_a_out |,
  demand_a_in.subtype == "demand_a",
  demand_a_in.state == "created",
  demand_a_out.state == "allocated",

  clone_demand_partial | demand_b_in | => | demand_b_out |,
  demand_b_in.subtype == "demand_b",
  demand_b_in.state == "created",
  demand_b_out.state == "allocated",

  clone_match_partial | match_in | => | match_out |,
  match_in.demand_a == demand_a_in,
  match_in.demand_b == demand_b_in,
  match2_out.state == "acceptedFinal",
  (match_in.state == "acceptedA" & match_out.member_b == sender) |
  (match_in.state == "acceptedB" & match_out.member_a == sender)
}

pub fn demand_comment | in: Demand | => | out: Demand | where {
  clone_demand | in | => | out |,
  out.comment: File
}

pub fn match2_reject | match: Match2 | => || where {
  match.state == "proposed" | match.state == "acceptedA" | match.state == "acceptedB",
  match.optimiser == sender | match.member_a == sender | match.member_b == sender,
}

pub fn match2_cancel | demand_a_in: Demand, demand_b_in: Demand, match_in: Match2 | => | demand_a_out: Demand, demand_b_out: Demand, match_out: Match2 | where {
  clone_demand_partial | demand_a_in | => | demand_a_out |,
  demand_a_in.subtype == "demand_a",
  demand_a_in.state == "allocated",
  demand_a_out.state == "cancelled",

  clone_demand_partial | demand_b_in | => | demand_b_out |,
  demand_b_in.subtype == "demand_b",
  demand_b_in.state == "allocated",
  demand_b_out.state == "cancelled",

  clone_match_partial | match2_in | => | match2_out |,
  match_in.demand_a == demand_a_in,
  match_in.demand_b == demand_b_in,
  match2_in.state == "acceptedFinal",
  match2_out.state == "cancelled",
  match_out.member_a == sender | match_out.member_b == sender
}

pub fn rematch2_propose | demand_a_in: Demand, old_match_in: Match2, demand_b_in: Demand | => | demand_a_out: Demand, old_match_out: Match2, demand_b_out: Demand, new_match: Match2 | where {
  clone_demand | demand_a_in | => | demand_a_out |,
  demand_a_in.subtype == "demand_a",
  demand_a_in.state == "allocated",

  clone_demand | demand_b_in | => | demand_b_out |,
  demand_b_in.subtype == "demand_b",
  demand_b_in.state == "created",

  clone_match | old_match_in | => | old_match_out |,
  old_match_in.demand_a == demand_a,
  old_match_in.state == "acceptedFinal",

  new_match.optimiser == sender,
  (new_match.demand_a == demand_a_in & new_match.member_a == demand_a_in.owner) |
  (new_match.demand_b == demand_b_in & new_match.member_b == demand_b_in.owner),
  new_match.state == "proposed",
  new_match.replaces == old_match_in,
}

pub fn rematch2_acceptFinal |
  demand_a_in: Demand,
  old_demand_b_in: Demand,
  old_match_in: Match2,
  demand_b_in: Demand,
  new_match_in: Match2
| => |
  demand_a_out: Demand,
  old_demand_b_out: Demand,
  old_match_out: Match2,
  demand_b_out: Demand,
  new_match_out: Match2
| where {
  clone_demand | demand_a_in | => | demand_a_out |,
  demand_a_in.subtype == "demand_a",
  demand_a_in.state == "allocated",

  clone_demand_partial | old_demand_b_in | => | old_demand_b_out |,
  old_demand_b_in.subtype == "demand_b",
  old_demand_b_in.state == "allocated",
  old_demand_b_out.state == "cancelled",

  clone_match_partial | old_match_in | => | old_match_out |,
  old_match_in.state == "acceptedFinal",
  old_match_out.state == "cancelled",
  old_match_in.demand_a == demand_a_in,
  old_match_in.demand_b == old_demand_b_in,

  clone_demand_partial | demand_b_in | => | demand_b_out |,
  demand_b_in.subtype == "demand_b",
  demand_b_in.state == "created",
  demand_b_out.state == "allocated",

  clone_match_partial | new_match_in | => | new_match_out |,
  new_match_out.state == "acceptedFinal",
  new_match_in.demand_a == demand_a_in,
  new_match_in.demand_b == demand_b_in,
  (new_match_in.state == "acceptedA" & new_match_out.member_b == sender) |
  (new_match_in.state == "acceptedB" & new_match_out.member_a == sender),
}

```
