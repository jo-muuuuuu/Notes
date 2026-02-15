## TypeScript
TypeScript is a superset of JavaScript. TypeScript offers all of JavaScript’s features, and an additional layer on top of these: <mark><b>TypeScript’s type system</b></mark>.

TypeScript can infer types for you, or you can <mark><b>define types</b></mark>.

## The Basics
### Static type-checking
Ideally, we could have a tool that <mark><b>helps us find these bugs (TypeError) before our code runs</mark></b>. 
That’s what a static type-checker like TypeScript does. 
<mark><b>Static type systems describe the shapes and behaviors of what our values will be when we run our programs</mark></b>.
A type-checker like TypeScript uses that information and tells us when things might be going off the rails.

### Catching non-exception failures
For JS, the following code snippet will return `undefined`
```
const user = {
  name: "Daniel",
  age: 26,
};
user.location; // returns undefined
```
In TS, it will be considered as an error: `Property 'location' does not exist on type '{ name: string; age: number; }'.`

Other than this, TS can also catch other legitimate bugs like `typos`, `uncalled functions`, or `basic logic errors`.

### TypeScript Compiler - `tsc`
Converts `.ts` file to `.js` file (after passing type-checking)

### Emitting with Errors
One of TypeScript’s core values is: much of the time, you will know better than TypeScript. 
Most of the time, your code is already working, so the `.js` file will be created even if there are type-checking errors. 

If you want to pass the type-checking strictly, use `noEmitOnError` compiler option.
```
tsc --noEmitOnError hello.ts
```

### Explicit Types
```
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
```
What we did was add `type annotations` on person and date to describe what types of values greet can be called with. 

Keep in mind, we don’t always have to write explicit type annotations. In many cases, TypeScript can even just infer (or “figure out”) the types for us even if we omit them.

### Erased Types
Type annotations aren’t part of JavaScript (or ECMAScript to be pedantic), so there really aren’t any browsers or other runtimes that can just run TypeScript unmodified. 
That’s why TypeScript needs a compiler in the first place - it needs some way to strip out or transform any TypeScript-specific code so that you can run it. 
Most TypeScript-specific code gets erased away, and likewise, here our type annotations were completely erased.

### Downleveling
The converted `.js` file is written with ECMAScript 5 (by default) for compatibility reasons.
You can specify other ES versions, for example: `--target es2015`

### Strictness
These strictness settings turn static type-checking from a switch (either your code is checked or not) into something closer to a dial. 
The further you turn this dial up, the more TypeScript will check for you. 

TypeScript has several type-checking strictness flags that can be turned on or off, and all of our examples will be written with all of them enabled unless otherwise stated. 
The `strict` flag in the CLI, or `"strict": true` in a tsconfig.json toggles them all on simultaneously, but we can opt out of them individually. 

The two biggest ones you should know about are `noImplicitAny` and `strictNullChecks`.

#### `noImplicitAny`
Recall that in some places, TypeScript doesn’t try to infer types for us and instead falls back to the most lenient type: `any`. 
This isn’t the worst thing that can happen - after all, falling back to `any` is just the plain JavaScript experience anyway.

However, using any often defeats the purpose of using TypeScript in the first place. 
The more typed your program is, the more validation and tooling you’ll get, meaning you’ll run into fewer bugs as you code. 

Turning on the <i>noImplicitAny</i> flag will issue an error on any variables whose type is implicitly inferred as `any`.

(That's why people call it AnyScript 😂)

#### `strictNullChecks`
By default, values like null and undefined are assignable to any other type. 
This can make writing some code easier, but forgetting to handle `null` and `undefined` is the cause of countless bugs in the world.

The <i>strictNullChecks</i> flag makes handling `null` and `undefined` more explicit.

## Everyday Types
### The primitives: `string`, `number`, and `boolean`
The type names String, Number, and Boolean (starting with capital letters) are legal, but refer to some special built-in types that will very rarely appear in your code.

Always use `string`, `number`, and `boolean`.

### Arrays
`number[]` or `Array<number>` (`T<U>` - generics)

### `any`
The `any` type is useful when you don’t want to write out a long type just to convince TypeScript that a particular line of code is okay.

### Type Annotations on Variables
```
let myName: string = "Alice";
```
In most cases, though, this isn’t needed. Wherever possible, TypeScript tries to automatically infer the types in your code. 

### Functions 
#### Parameter and Return Type Annotations
```
function greet(name: string): string {
  return "Hello, " + name.toUpperCase() + "!!";
}
```

#### Anonymous Functions
```
const names = ["Alice", "Bob", "Eve"];
 
// Contextual typing for function - parameter s inferred to have type string
names.forEach(function (s) {
  console.log(s.toUpperCase());
});
 
// Contextual typing also applies to arrow functions
names.forEach((s) => {
  console.log(s.toUpperCase());
});
```
Even though the parameter s didn’t have a type annotation, TypeScript used the types of the forEach function, along with the inferred type of the array, to determine the type s will have.

This process is called `contextual typing` because the context that the function occurred within informs what type it should have.

### Object Types
```
// The parameter's type annotation is an object type
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```

#### Optional Preperties
```
function printName(obj: { first: string; last?: string }) {
  // ...
}
// Both OK
printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" });
```

### Union Types
TypeScript’s type system allows you to build new types out of existing ones using a large variety of operators.

#### Defining a Union Type
```
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
// OK
printId(101);
// OK
printId("202");

printId({ myID: 22342 });
// Error: Argument of type '{ myID: number; }' is not assignable to parameter of type 'string | number'.
```

#### Working with Union Type
<i>Narrowing</i> occurs when TypeScript can deduce a more specific type for a value based on the structure of the code.
```
function printId(id: number | string) {
  if (typeof id === "string") {
    // In this branch, id is of type 'string'
    console.log(id.toUpperCase());
  } else {
    // Here, id is of type 'number'
    console.log(id);
  }
}
```

### Type Aliases
We’ve been using object types and union types by writing them directly in type annotations. This is convenient, but it’s common to want to use the same type more than once and refer to it by a single name.

A type <i>alias</i> is exactly that - a name for any type. The syntax for a type alias is:
```
type Point = {
  x: number;
  y: number;
};
 
// Exactly the same as the earlier example
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

### Interfaces
An <i>interface</i> declaration is another way to name an object type:
```
interface Point {
  x: number;
  y: number;
}
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

#### Differences Between Type Aliases and Interfaces
Type aliases and interfaces are very similar, and in many cases you can choose between them freely. Almost all features of an interface are available in type, the key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

### Type Assertions
Sometimes you will have information about the type of a value that TypeScript can’t know about.

For example, if you’re using document.getElementById, TypeScript only knows that this will return some kind of HTMLElement, but you might know that your page will always have an HTMLCanvasElement with a given ID.

In this situation, you can use a type assertion to specify a more specific type:
```
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
```
You can also use the angle-bracket syntax (except if the code is in a .tsx file), which is equivalent:
```
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```

Sometimes this rule can be too conservative and will disallow more complex coercions that might be valid. If this happens, you can use two assertions, first to any (or unknown, which we’ll introduce later), then to the desired type:
```
const a = expr as any as T;
```

### Literal Types
In addition to the general types string and number, we can refer to specific strings and numbers in type positions.

By themselves, literal types aren’t very valuable:
```
let x: "hello" = "hello";
// OK
x = "hello";
x = "howdy";
// Error: Type '"howdy"' is not assignable to type '"hello"'.
```

But by combining literals into unions, you can express a much more useful concept - for example, functions that only accept a certain set of known values:
```
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left");
printText("G'day, mate", "centre");
// Error: Argument of type '"centre"' is not assignable to parameter of type '"left" | "right" | "center"'.
```

#### Literal Inference
```
declare function handleRequest(url: string, method: "GET" | "POST"): void;
 
const req = { url: "https://example.com", method: "GET" };
handleRequest(req.url, req.method);
// Error: Argument of type 'string' is not assignable to parameter of type '"GET" | "POST"'.
```

This is not allowed because TS infers that you may assign other <i>string</i> values to `req.method`.

Solution:
1. You can change the inference by adding a type assertion in either location:
```
// Change 1:
const req = { url: "https://example.com", method: "GET" as "GET" };
// Change 2
handleRequest(req.url, req.method as "GET");
```
2. You can use as const to convert the entire object to be type literals:
```
const req = { url: "https://example.com", method: "GET" } as const;
handleRequest(req.url, req.method);
```

### `null` and `undefined`
#### Non-null Assertion Operator (Postfix !)
TypeScript also has a special syntax for removing null and undefined from a type without doing any explicit checking. Writing `!` after any expression is effectively a type assertion that the value isn’t null or undefined:
```
function liveDangerously(x?: number | null) {
  // No error
  console.log(x!.toFixed());
}
```

### Enums
This is a feature which you should know exists, but maybe hold off on using unless you are sure.

### Less Common Primitives
#### bigint
#### symbol


## Narrowing
```
function padLeft(padding: number | string, input: string): string {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```
Within our if check, TypeScript sees `typeof padding === "number"` and understands that as a special form of code called a type guard. TypeScript follows possible paths of execution that our programs can take to analyze the most specific possible type of a value at a given position. It looks at these special checks (called <i>type guards</i>) and assignments, and the process of refining types to more specific types than declared is called <i>narrowing</i>. In many editors we can observe these types as they change, and we’ll even do so in our examples.

### `typeof` type guards
TypeScript expects this to return a certain set of strings:
- "string"
- "number"
- "bigint"
- "boolean"
- "symbol"
- "undefined"
- "object"
- "function"

### Truthiness narrowing
In JS, arrays are `object` types.
However, in TS, `typeof null` also returns `"object"`
```
function printAll(strs: string | string[] | null) {
  if (typeof strs === "object") {
    for (const s of strs) {
// Error: 'strs' is possibly 'null'.
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  } else {
    // do nothing
  }
}
```

Values like `0`, `NaN`, `""`, `0n (the bigint version of zero)`, `null`, `undefined` all coerce to false, and other values get coerced to true.

So, we can use the following code:
```
function printAll(strs: string | string[] | null) {
  if (strs && typeof strs === "object") {
    for (const s of strs) {
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  }
}
```

However, in the following code snippet, we wrapped the entire body of the function in a truthy check. 
```
function printAll(strs: string | string[] | null) {
  // !!!!!!!!!!!!!!!!
  //  DON'T DO THIS!
  //   KEEP READING
  // !!!!!!!!!!!!!!!!
  if (strs) {
    if (typeof strs === "object") {
      for (const s of strs) {
        console.log(s);
      }
    } else if (typeof strs === "string") {
      console.log(strs);
    }
  }
}
```
This has a subtle downside: we may no longer be handling the empty string case correctly.

### Equality narrowing
TypeScript also uses switch statements and equality checks like `===`, `!==` `==`, and `!=` to narrow types. 

### The `in` operator narrowing
JavaScript has an operator for determining if an object or its prototype chain has a property with a name: the `in` operator. 
```
type Fish = { swim: () => void };
type Bird = { fly: () => void };
 
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
  }
 
  return animal.fly();
}
```

### `instanceof` narrowing
JavaScript has an operator for checking whether or not a value is an “instance” of another value. More specifically, in JavaScript x instanceof Foo checks whether the prototype chain of x contains Foo.prototype. 
```
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
               
(parameter) x: Date
  } else {
    console.log(x.toUpperCase());
               
(parameter) x: string
  }
}
```

### Assignments
```
let x = Math.random() < 0.5 ? 10 : "hello world!";
    // let x: string | number
x = 1;
 
console.log(x);
           //let x: number
x = true;
// ERROR: Type 'boolean' is not assignable to type 'string | number'.
 
console.log(x);
           // let x: string | number
```
Notice that each of these assignments is valid. Even though the observed type of x changed to number after our first assignment, we were still able to assign a string to x. This is because the declared type of x - the type that x started with - is string | number, and assignability is always checked against the declared type.

If we’d assigned a boolean to x, we’d have seen an error since that wasn’t part of the declared type.

### Control flow analysis
The analysis of code based on reachability is called `control flow analysis`, and TypeScript uses this flow analysis to narrow types as it encounters type guards and assignments. When a variable is analyzed, control flow can split off and re-merge over and over again, and that variable can be observed to have a different type at each point.
```
function example() {
  let x: string | number | boolean;
 
  x = Math.random() < 0.5;
  console.log(x);
             // let x: boolean
 
  if (Math.random() < 0.5) {
    x = "hello";
    console.log(x);
               // let x: string
  } else {
    x = 100;
    console.log(x);
               // let x: number
  }
 
  return x;
        // let x: string | number
}
```

### Using type predicates
To define a user-defined type guard, we simply need to define a function whose return type is a type predicate:
```
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

`pet is Fish` is our type predicate in this example. A predicate takes the form `parameterName is Type`, where `parameterName` must be the name of a parameter from the current function signature.

```
// Both calls to 'swim' and 'fly' are now okay.
let pet = getSmallPet();
 
if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```

### Discriminated Unions
When every type in a union contains a common property with literal types, TypeScript considers that to be a `discriminated union`, and can narrow out the members of the union.
```
interface Shape {
  kind: "circle" | "square";
  radius?: number;
  sideLength?: number;
}

function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius! ** 2;
    // have to use non-null assertions (!)
  }
}
```

To avoid using `!` 
```
interface Circle {
  kind: "circle";
  radius: number;
}
 
interface Square {
  kind: "square";
  sideLength: number;
}
 
type Shape = Circle | Square;

function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2;
                      
(parameter) shape: Circle
  }
}
```
The important thing here was the encoding of Shape. Communicating the right information to TypeScript - that Circle and Square were really two separate types with specific kind fields - was crucial. Doing that lets us write type-safe TypeScript code that looks no different than the JavaScript we would’ve written otherwise. From there, the type system was able to do the “right” thing and figure out the types in each branch of our switch statement.

### The `never` type
When narrowing, you can reduce the options of a union to a point where you have removed all possibilities and have nothing left. In those cases, TypeScript will use a `never` type to represent a state which shouldn’t exist.

### Exhaustiveness checking
The `never` type is assignable to every type; however, no type is assignable to never (except never itself). This means you can use narrowing and rely on never turning up to do exhaustive checking in a switch statement.

```
type Shape = Circle | Square;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```

Adding a new member to the Shape union, will cause a TypeScript error:
```
interface Triangle {
  kind: "triangle";
  sideLength: number;
}
 
type Shape = Circle | Square | Triangle;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
            //Error: Type 'Triangle' is not assignable to type 'never'.
      return _exhaustiveCheck;
  }
}
```
