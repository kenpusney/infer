# JSON syntax of Infer

## Synopsis

`infer` type syntax requires a parser, which adds complexity for applications.
Since infer's structure is an structured object, it would be simple to translate to JSON.
Also could be more convenient to be processed and extended.

## Example

```json
{
  "kind": "object",
  "fields": {
    "a": "string",
    "b": "number",
    "c": ["boolean"],
    "d": {
      "kind": "union",
      "types": [
        "string", "number", "boolean"
      ]
    }
  }
}
```


Alternative JavaScript version:
```js
var type = {
  kind: "object",
  fields: {
    a: String,
    b: Number,
    c: [Boolean],
    d: {
      kind: "union",
      types: [ String, Number, Boolean ]
    } 
  }
}
```

## Details

### Simple types

Simple Types can be represented by their name, or corresponded JavaScript Constructor.

| `infer` type |  JSON Reps   | JSON Reps Expanded                          | JS Alternative |
|--------------|--------------|---------------------------------------------|----------------|
| `number`     | `"number"`   | `{ "kind": "simple", "type": "number" }`    | `Number`       | 
| `string`     | `"string"`   | `{ "kind": "simple", "type": "string" }`    | `String`       | 
| `boolean`    | `"boolean"`  | `{ "kind": "simple", "type": "boolean" }`   | `Boolean`      | 
| `null`       | `"null"`     | `{ "kind": "simple", "type": "null" }`      | `"null"`       | 
| `bottom`     | `"bottom"`   | `{ "kind": "simple", "type": "bottom" }`    | `"bottom"`     | 

### Array

 - Infer: `T[]`
   - e.g.: `number[]` / `boolean[][]`
 - JSON Syntax: `[T]`
   - e.g.: `["number"]` / `[["boolean"]]`
 - Expanded JSON Syntax: `{ "kind": "array", "type": T}`
   - e.g.: `{ "kind": "array", "type": "number" }`
   - `{ "kind": "array", "type": { "kind": "array", "type": "boolean" } }`
 - Alternative JS:  `[T]`
   - e.g.: `[Number]` / `[[Boolean]]`

### Union

 - Infer: `union<T, U, V>`
   - e.g.: `union<number, boolean>`
 - Expanded JSON Syntax: `{ "kind": "union", "types": ["number", "boolean"]}`
 - Alternative JS:  `{ kind: "union", types: [T, U]}`
   - e.g.: `{ kind: "union", types: [Number, Boolean]}`

### Object

 - Infer: `{ t: T, u: U }`
 - Expanded JSON Syntax: `{ "kind": "object", "fields": { "t": "T", "u": "U" } }`
 - Alternative JS: `{ kind: "object", fields: { "t": T, "u": U } }`

### Nullable

Add an extra field `"nullable": true` on expanded json syntax or `nullable: true` on alternative JS syntax, denotes that type was nullable.

## Simplified JSON Syntax

Objects and Unions have their specific fields in expanded json syntax, so it would be simple
to just use these fields to determine which kind of type that this type are.

This means when you see a expanded json type includes field `fields`, then this type must be
an `object`, when `types`, then must be `union`.

So the example above can be simplified to:
```json
{
  "fields": {
    "a": "string",
    "b": "number",
    "c": ["boolean"],
    "d": {
      "types": [
        "string", "number", "boolean"
      ]
    }
  }
}
```

Also you can change `fields` to `object` or change `types` to `union`, which makes more
sense.

```json
{
  "object": {
    "a": "string",
    "b": "number",
    "c": ["boolean"],
    "d": {
      "union": [
        "string", "number", "boolean"
      ]
    }
  }
}
```


### Type Definition for JSON Syntax

Following document include 2 infer syntax extensions(not implemented yet): **recursion** and **mappable type**.

 - `"$"` stands for current type definition
 - `T[U]` stands for object with field type of T with key type of U.

```json
{
  "kind": "union",
  "types": [
    "string",
    {
      "kind": "object",
      "fields": {
        "kind":{ "type":  "string", "nullable": true },
        "type": "string"
      }
    },
    {
      "kind": "object",
      "fields": {
        "kind": { 
          "type": "string", "nullable": true
        },
        "types": ["$"]
      }
    },
    {
      "kind": "object",
      "fields": {
        "kind": { 
          "type": "string", "nullable": true
        },
        "fields": "$[string]"
      }
    },
    {
      "kind": "object",
      "fields": {
        "object": "$[string]"
      }
    },
    {
      "kind": "object",
      "fields": {
        "union": ["$"]
      }
    }
  ]
}
```