# TypeScript
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

## More on Functions
### Function Type Expressions
```
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}
 
function printToConsole(s: string) {
  console.log(s);
}
 
greeter(printToConsole);
```
The syntax `(a: string) => void` means “a function with one parameter, named a, of type string, that doesn’t have a return value”. Just like with function declarations, if a parameter type isn’t specified, it’s implicitly any.

Or we can use `type alias`
```
type GreetFunction = (a: string) => void;
function greeter(fn: GreetFunction) {
  // ...
}
```

### Call Signatures
In JavaScript, functions can have properties in addition to being callable. However, the function type expression syntax doesn’t allow for declaring properties. If we want to describe something callable with properties, we can write a <i>call signature</i> in an object type:

```
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}
 
function myFunc(someArg: number) {
  return someArg > 3;
}
myFunc.description = "default description";
 
doSomething(myFunc);
```
Note that the syntax is slightly different compared to a function type expression - use `:` between the parameter list and the return type rather than `=>`.

### Construct Signatures
JavaScript functions can also be invoked with the new operator. TypeScript refers to these as constructors because they usually create a new object. You can write a <i>construct</i> signature by adding the `new` keyword in front of a call signature:
```
type SomeConstructor = {
  new (s: string): SomeObject;
};
function fn(ctor: SomeConstructor) {
  return new ctor("hello");
}
```

Some objects, like JavaScript’s Date object, can be called with or without new. You can combine call and construct signatures in the same type arbitrarily:
```
interface CallOrConstruct {
  (n?: number): string;
  new (s: string): Date;
}
 
function fn(ctor: CallOrConstruct) {
  // Passing an argument of type `number` to `ctor` matches it against
  // the first definition in the `CallOrConstruct` interface.
  console.log(ctor(10));
               (parameter) ctor: CallOrConstruct
               (n?: number) => string
 
  // Similarly, passing an argument of type `string` to `ctor` matches it
  // against the second definition in the `CallOrConstruct` interface.
  console.log(new ctor("10"));
                   (parameter) ctor: CallOrConstruct
                    new (s: string) => Date
}
 
fn(Date);
```

### Generic Functions
In TypeScript, generics are used when we want to describe a correspondence between two values. We do this by declaring a <i>type parameter</i> in the function signature:
```
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```

#### Inference
Note that we didn’t have to specify Type in the sample above. The type was inferred - chosen automatically - by TypeScript.

We can use multiple type parameters as well. For example, a standalone version of map would look like this:
```
function map<Input, Output>(arr: Input[], func: (arg: Input) => Output): Output[] {
  return arr.map(func);
}
 
// Parameter 'n' is of type 'string'
// 'parsed' is of type 'number[]'
const parsed = map(["1", "2", "3"], (n) => parseInt(n));
```
Note that in this example, TypeScript could infer both the type of the Input type parameter (from the given string array), as well as the Output type parameter based on the return value of the function expression (number).

#### Constraints
We’ve written some generic functions that can work on any kind of value. Sometimes we want to relate two values, but can only operate on a certain subset of values. In this case, we can use a constraint to limit the kinds of types that a type parameter can accept.

Let’s write a function that returns the longer of two values. To do this, we need a length property that’s a number. We constrain the type parameter to that type by writing an `extends` clause:
```
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
 
// longerArray is of type 'number[]'
const longerArray = longest([1, 2], [1, 2, 3]);
// longerString is of type 'alice' | 'bob'
const longerString = longest("alice", "bob");
// Error! Numbers don't have a 'length' property
const notOK = longest(10, 100);
```

#### Working with Constrained Values
```
function minimumLength<Type extends { length: number }>(
  obj: Type,
  minimum: number
): Type {
  if (obj.length >= minimum) {
    return obj;
  } else {
    return { length: minimum };
    //Type '{ length: number; }' is not assignable to type 'Type'.
    //'{ length: number; }' is assignable to the constraint of type 'Type',
    //but 'Type' could be instantiated with a different subtype of constraint '{ length: number; }'.
  }
}
```
It might look like this function is OK - Type is constrained to { length: number }, and the function either returns Type or a value matching that constraint. The problem is that the function promises to return the same kind of object as was passed in, not just some object matching the constraint. 

#### Specifying Type Arguments
TypeScript can usually infer the intended type arguments in a generic call, but not always. For example, let’s say you wrote a function to combine two arrays:
```
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}
const arr = combine([1, 2, 3], ["hello"]);
// Type 'string' is not assignable to type 'number'.
```

If you intended to do this, however, you could manually specify Type:
```
const arr = combine<string | number>([1, 2, 3], ["hello"]);
```

#### Guidelines for Writing Good Generic Functions
- Push Type Parameters Down
- Use Fewer Type Parameters
- Type Parameters Should Appear Twice


### Optional Parameters
`x` is an optional parameter:
```
function f(x?: number) {
  // ...
}
f(); // OK
f(10); // OK
```

Parameter default value:
```
function f(x = 10) {
  // ...
}
```

#### Optional Parameters in Callbacks
When writing a function type for a callback, never write an optional parameter unless you intend to call the function without passing that argument
```
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i], i);
  }
}

myForEach([1, 2, 3], (a, i) => {
  console.log(i.toFixed());
              // 'i' is possibly 'undefined'.
});
```

### Function Overloads
In TypeScript, we can specify a function that can be called in different ways by writing `overload signatures`. To do this, write some number of function signatures (usually two or more), followed by the body of the function:
```
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3);
           // Error: No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
```
In this example, we wrote two overloads: one accepting one argument, and another accepting three arguments. These first two signatures are called the overload signatures.

Then, we wrote a function implementation with a compatible signature. Functions have an implementation signature, but this signature can’t be called directly. Even though we wrote a function with two optional parameters after the required one, it can’t be called with two parameters!

Key takeaway: Only function overloads are exported, so TypeScript only allows functions to be called in ways that match the declared overload signatures.

### Declaring `this` in a Function
TypeScript will infer what the this should be in a function via code flow analysis, for example in the following:
```
const user = {
  id: 123,
 
  admin: false,
  becomeAdmin: function () {
    this.admin = true;
  },
};
```
TypeScript understands that the function user.becomeAdmin has a corresponding this which is the outer object user. this, heh, can be enough for a lot of cases, but there are a lot of cases where you need more control over what object this represents. The JavaScript specification states that you cannot have a parameter called this, and so TypeScript uses that syntax space to let you declare the type for this in the function body.
```
interface DB {
  filterUsers(filter: (this: User) => boolean): User[];
}
 
const db = getDB();
const admins = db.filterUsers(function (this: User) {
  return this.admin;
});
```
This pattern is common with callback-style APIs, where another object typically controls when your function is called. Note that you need to use function and not arrow functions to get this behavior:
```
interface DB {
  filterUsers(filter: (this: User) => boolean): User[];
}
 
const db = getDB();
const admins = db.filterUsers(() => this.admin);
                The containing arrow function captures the global value of 'this'.
                Element implicitly has an 'any' type because type 'typeof globalThis' has no index signature.
```

### Other Types to Know About
#### void
void represents the return value of functions which don’t return a value. It’s the inferred type any time a function doesn’t have any return statements, or doesn’t return any explicit value from those return statements:
```
// The inferred return type is void
function noop() {
  return;
}
```
In JavaScript, a function that doesn’t return any value will implicitly return the value undefined. However, void and undefined are not the same thing in TypeScript. There are further details at the end of this chapter.

#### object
The special type object refers to any value that isn’t a primitive (string, number, bigint, boolean, symbol, null, or undefined). This is different from the empty object type { }, and also different from the global type Object. It’s very likely you will never use Object.

Note that in JavaScript, function values are objects: They have properties, have Object.prototype in their prototype chain, are instanceof Object, you can call Object.keys on them, and so on. For this reason, function types are considered to be objects in TypeScript.

#### unknown
The unknown type represents any value. This is similar to the any type, but is safer because it’s not legal to do anything with an unknown value:
```
function f1(a: any) {
  a.b(); // OK
}
function f2(a: unknown) {
  a.b();
'a' is of type 'unknown'.
}
```
This is useful when describing function types because you can describe functions that accept any value without having any values in your function body.

Conversely, you can describe a function that returns a value of unknown type:
```
function safeParse(s: string): unknown {
  return JSON.parse(s);
}
 
// Need to be careful with 'obj'!
const obj = safeParse(someRandomString);
```

#### never
Some functions never return a value:
```
function fail(msg: string): never {
  throw new Error(msg);
}
```
The never type represents values which are never observed. In a return type, this means that the function throws an exception or terminates execution of the program.

never also appears when TypeScript determines there’s nothing left in a union.
```
function fn(x: string | number) {
  if (typeof x === "string") {
    // do something
  } else if (typeof x === "number") {
    // do something else
  } else {
    x; // has type 'never'!
  }
}
```

#### Function
The global type Function describes properties like bind, call, apply, and others present on all function values in JavaScript. It also has the special property that values of type Function can always be called; these calls return any:
```
function doSomething(f: Function) {
  return f(1, 2, 3);
}
```
This is an untyped function call and is generally best avoided because of the unsafe any return type.

If you need to accept an arbitrary function but don’t intend to call it, the type () => void is generally safer.

### Rest Parameters and Arguments
#### Rest Parameters
A rest parameter appears after all other parameters, and uses the `...` syntax:
```
function multiply(n: number, ...m: number[]) {
  return m.map((x) => n * x);
}
// 'a' gets value [10, 20, 30, 40]
const a = multiply(10, 1, 2, 3, 4);
```
In TypeScript, the type annotation on these parameters is implicitly any[] instead of any, and any type annotation given must be of the form Array<T> or T[], or a tuple type (which we’ll learn about later).

#### Rest Arguments
Conversely, we can provide a variable number of arguments from an iterable object (for example, an array) using the spread syntax. For example, the push method of arrays takes any number of arguments:
```
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
arr1.push(...arr2);
```
Note that in general, TypeScript does not assume that arrays are immutable. This can lead to some surprising behavior:
```
// Inferred type is number[] -- "an array with zero or more numbers",
// not specifically two numbers
const args = [8, 5];
const angle = Math.atan2(...args);
A spread argument must either have a tuple type or be passed to a rest parameter.
```
The best fix for this situation depends a bit on your code, but in general a const context is the most straightforward solution:
```
// Inferred as 2-length tuple
const args = [8, 5] as const;
// OK
const angle = Math.atan2(...args);
```

### Parameter Destructuring
The type annotation for the object goes after the destructuring syntax:
```
function sum({ a, b, c }: { a: number; b: number; c: number }) {
  console.log(a + b + c);
}
```
This can look a bit verbose, but you can use a named type here as well:
```
// Same as prior example
type ABC = { a: number; b: number; c: number };
function sum({ a, b, c }: ABC) {
  console.log(a + b + c);
}
```

### Assignability of Functions
Functions with a `void` return type may return values, but those values are ignored by callers.
However, when `void` is specified on a literal function definition, the function body must not return a value.

```
// variable with function type
const fn: () => void = () => true; // ✅

// literal defintion
function fn2(): void { return true } // ❌
```

## Object Types
#### Anonymous Object Type
```
function greet(person: { name: string; age: number }) {
  return "Hello " + person.name;
}
```

#### Using Interface
```
interface Person {
  name: string;
  age: number;
}
 
function greet(person: Person) {
  return "Hello " + person.name;
}
```

#### Type Alias
```
type Person = {
  name: string;
  age: number;
};
 
function greet(person: Person) {
  return "Hello " + person.name;
}
```

### Property Modifiers
Each property in an object type can specify a couple of things: the type, whether the property is optional, and whether the property can be written to.

#### Optional Properties
```
interface PaintOptions {
  shape: Shape;
  xPos?: number;
  yPos?: number;
}
```

#### `readonly` Properties
Properties can also be marked as readonly for TypeScript. While it won’t change any behavior at runtime, a property marked as readonly can’t be written to during type-checking.
```
interface SomeType {
  readonly prop: string;
}
 
function doSomething(obj: SomeType) {
  // We can read from 'obj.prop'.
  console.log(`prop has the value '${obj.prop}'.`);
 
  // But we can't re-assign it.
  obj.prop = "hello";
  // Cannot assign to 'prop' because it is a read-only property.
}
```

Using the readonly modifier doesn’t necessarily imply that a value is totally immutable - or in other words, that its internal contents can’t be changed. It just means the property itself can’t be re-written to.

In the following example, `resident` is a readonly property, which means you cannot assign a new value to `home.resident`.
However, object `{ name: string; age: number }` is still mutable; the `name` and `age` properties are not readonly.

```
interface Home {
  readonly resident: { name: string; age: number };
}
 
function visitForBirthday(home: Home) {
  // We can read and update properties from 'home.resident'.
  console.log(`Happy birthday ${home.resident.name}!`);
  home.resident.age++;
}
 
function evict(home: Home) {
  // But we can't write to the 'resident' property itself on a 'Home'.
  home.resident = {
  // Cannot assign to 'resident' because it is a read-only property.
    name: "Victor the Evictor",
    age: 42,
  };
}
```

#### Index Signatures
`[index: type]: T`
Sometimes you don’t know all the names of a type’s properties ahead of time, but you do know the shape of the values. In those cases you can use an index signature to describe the types of possible values, for example:
```
interface StringArray {
  [index: number]: string;
}
 
const myArray: StringArray = getStringArray();
const secondItem = myArray[1];
          // const secondItem: string
```

However, properties of different types are acceptable if the index signature is a union of the property types:
```
interface NumberOrStringDictionary {
  [index: string]: number | string;
  length: number; // ok, length is a number
  name: string; // ok, name is a string
}
```

### Excess Property Checks
Where and how an object is assigned a type can make a difference in the type system. One of the key examples of this is in excess property checking, which validates the object more thoroughly when it is created and assigned to an object type during creation.

```
interface SquareConfig {
  color?: string;
  width?: number;
}
 
function createSquare(config: SquareConfig): { color: string; area: number } {
  return {
    color: config.color || "red",
    area: config.width ? config.width * config.width : 20,
  };
}
 
let mySquare = createSquare({ colour: "red", width: 100 });
// Object literal may only specify known properties, but 'colour' does not exist in type 'SquareConfig'. Did you mean to write
// 'color'?
```

Getting around these checks is actually really simple. The easiest method is to just use a type assertion:
```
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
```

However, a better approach might be to add a string index signature if you’re sure that the object can have some extra properties that are used in some special way.
```
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: unknown;
}
```

<mark>I reckon the easiest way is just to make sure the input matches `SquareConfig` interface.</mark>


### Extending Types
The `extends` keyword on an interface allows us to effectively copy members from other named types, and add whatever new members we want. 

```
interface BasicAddress {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}
 
interface AddressWithUnit extends BasicAddress {
  unit: string;
}
```

### Intersection Types
`interfaces` allowed us to build up new types from other types by extending them. TypeScript provides another construct called intersection types that is mainly used to combine existing object types.

An intersection type is defined using the `&` operator.

```
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}
 
type ColorfulCircle = Colorful & Circle;
```

### Generic Object Types
```
interface Box<Type> {
  contents: Type;
}
```

Think of Box as a template for a real type, where Type is a placeholder that will get replaced with some other type. When TypeScript sees Box<string>, it will replace every instance of Type in Box<Type> with string, and end up working with something like { contents: string }. In other words, Box<string> and our earlier StringBox work identically.

```
interface Box<Type> {
  contents: Type;
}
interface StringBox {
  contents: string;
}
 
let boxA: Box<string> = { contents: "hello" };
boxA.contents;
         //(property) Box<string>.contents: string
 
let boxB: StringBox = { contents: "world" };
boxB.contents;
        //(property) StringBox.contents: string
```

It is worth noting that type aliases can also be generic.
```
type Box<Type> = {
  contents: Type;
};
```

#### The Array Type
It turns out we’ve been working with a type just like that throughout this handbook: the Array type. Whenever we write out types like `number[]` or `string[]`, that’s really just a shorthand for `Array<number>` and `Array<string>`.
```
function doSomething(value: Array<string>) {
  // ...
}
 
let myArray: string[] = ["hello", "world"];
 
// either of these work!
doSomething(myArray);
doSomething(new Array("hello", "world"));
```

#### The `ReadonlyArray` Type
The `ReadonlyArray` is a special type that describes arrays that shouldn’t be changed.
```
function doStuff(values: ReadonlyArray<string>) {
  // We can read from 'values'...
  const copy = values.slice();
  console.log(`The first value is ${values[0]}`);
 
  // ...but we can't mutate 'values'.
  values.push("hello!");
// Property 'push' does not exist on type 'readonly string[]'.
}
```

Unlike Array, there isn’t a `ReadonlyArray` constructor that we can use. Instead, we can assign regular Arrays to `ReadonlyArray`s.
```
const roArray: ReadonlyArray<string> = ["red", "green", "blue"];
```

Can also use `readonly Type[]`
```
function doStuff(values: readonly string[]) {
  ...
}
```

One last thing to note is that unlike the readonly property modifier, assignability isn’t bidirectional between regular Arrays and ReadonlyArrays.
```
let x: readonly string[] = [];
let y: string[] = [];
 
x = y;
y = x;
// The type 'readonly string[]' is 'readonly' and cannot be assigned to the mutable type 'string[]'.
```

#### Tuple Types
A tuple type is another sort of `Array` type that knows exactly how many elements it contains, and exactly which types it contains at specific positions.
```
type StringNumberPair = [string, number];
```

Tuples can also have rest elements, which have to be an array/tuple type.
```
type StringNumberBooleans = [string, number, ...boolean[]];
type StringBooleansNumber = [string, ...boolean[], number];
type BooleansStringNumber = [...boolean[], string, number];
```

Why might optional and rest elements be useful? Well, it allows TypeScript to correspond tuples with parameter lists. Tuples types can be used in rest parameters and arguments, so that the following:
```
function readButtonInput(...args: [string, number, ...boolean[]]) {
  const [name, version, ...input] = args;
  // ...
}
```
is basically equivalent to:
```
function readButtonInput(name: string, version: number, ...input: boolean[]) {
  // ...
}
```

#### `readonly` Tuple Types
One final note about tuple types - tuple types have readonly variants, and can be specified by sticking a readonly modifier in front of them - just like with array shorthand syntax.
```
function doSomething(pair: readonly [string, number]) {
  // ...
}
```

Tuples tend to be created and left un-modified in most code, so annotating types as readonly tuples when possible is a good default. This is also important given that array literals with const assertions will be inferred with readonly tuple types.
```
let point = [3, 4] as const;
 
function distanceFromOrigin([x, y]: [number, number]) {
  return Math.sqrt(x ** 2 + y ** 2);
}
 
distanceFromOrigin(point);
// Argument of type 'readonly [3, 4]' is not assignable to parameter of type '[number, number]'.
// The type 'readonly [3, 4]' is 'readonly' and cannot be assigned to the mutable type '[number, number]'.
```
