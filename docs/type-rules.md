## Type Rules

### Hierarchy

This simplifies Scala type hierarchy which just removes "reference" type, and add
a null/nullable concept for refs.

```js
any -> value (number, string, object, array, union, null) -> bottom
```

See https://docs.scala-lang.org/tour/unified-types.html

#### Rules of any

- `any` type can take any value, including `null`;

```js
let x: any = 123;

let y: any = "456";

let z: any = true;

let value: any = null;
```

#### Rules of simple

- Simple types are not compatible

```js
let x: number = "123"; // error!

let y: string = 123; // error!

let z: null = true; // error!
```

#### Rules of null

Null is a special value type, with only one value is `null`.

Nullable type (`T?`) is just a synonym of `union<T, null>`.

-  Null type only have null value

```js
let t: null = null;

let t2: null = 123; // error!
```

- `T?`(nullable T) is same as `union<T, null>`

```js
let t: number? = 1;

let t2: union<number, null> = t;

let t3: number? = t2;
```

- Nullable T can take nonnull T, but not vice versa

```js
let x: number = 1;
let x1: number? = x;

let x2: number = x1; // error!
```

- A nonnull type cannot take null as its value

```js
let x: number = null; // error!
```

- Nulls in unions are mergeable

```js
let x: union<number?, string?, null> = null;

let y: union<number, string, null> = x;
```

#### Rules of union

Union stands for multiple possible types for a value, makes the data structure
more flexible. 

- Union should merge unions (including nullable)

```js
let x: union<union<number, string>, union<string, boolean>> = 1;

let y: union<number, string, boolean> = x;
```

- Union should merge compatible types

```js
let x: union<any, number> = 1;

let y: union<any> = y;
```

- `union<T>` is same as `T`

- which means `union<..., any, ...>` is same as `any`.

- Nullable union is same as `union<ts..., null>`, and vice versa.

```js
let x: union<number, string>? = null;

let y: union<number, string, null> = y;
``` 

#### Rules of object

Object are structural compatible, and constrained by following rules:

- Anonymous object type are compatible if all nonnull field are compatible.

```js
let x : { a: number, b: string } = { a: 1, b: "2" } ;

let y : { a: number } = x;
```

- Missing field can be treat as null value

```js
let x : { a: number?, b: string } = { b: "1" }; // { a: null } omitted

let y : { a: number?, b: string? } = x;
```

- Rules of object applied recursively

```js
let x : { a: { b: string? , c: number }, d: boolean } = 
  { a: { c: 456 }, d: false }

let y : { a: { c: number? }?, d: boolean } = x;
```

#### Rules of array

- Array with different types should merge into array of union

```js
let a: array<union<number, string, null>> = [123, '456', null];

let b: array<union<number, string>?> = a;
```

#### Rules of bottom

- You'll never use `bottom` as data type.

```js
let v: bottom; // BOOM!
```