
## Type System

Here is a type system follows a hierarchy model. `any` as a super type of all
types, sits on the top, and `bottom` as a subtype of all types lies on the bottom.
and there are all value types between them.

#### Primitives

Here are few primitive types: `number`, `string`, `boolean` and `null` which
stands for numbers, strings, boolean values and null value.

`null` type is special and only has single value `null`, it is helpful to
allow nullable values when combined with other types.

#### Compound types

`union`, `array`, and `object` are compound types which allow create new type with
existing types.

`union` is for values that may have multiple possible types. e.g. a value marked
with `union <number, string>`, can contains either a number or a string.

`array` is for a series values sharing same type. e.g. a number list, which can be
represented as `number[]` / (or maybe `array<number>`).

`object` is for a set of values connected with names (also called fields).

All compound types can be composited freely. e.g.:

```
type T = {
  a: number,
  b: boolean[],
  c: union<boolean?[], { d: string, e: null }>
};
```