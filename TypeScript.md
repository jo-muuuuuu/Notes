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
