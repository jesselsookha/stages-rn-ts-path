# Stage 5 — Advanced TypeScript Features

## ❖ Introduction

This Stage closes out Part II. Where Stage 3 and Stage 4 covered the
fundamentals you'd recognise from any structured programming language, this
Stage introduces ideas that are more particular to TypeScript itself —
interfaces, classes, modules, enums, and a few ways of composing types
together. From Stage 6 onward, we put all five Stages of Part I & II to work
building the XO Game itself.

## ✦ Objects vs Interfaces

A plain JavaScript-style object works perfectly well in TypeScript:

```typescript
const person = {
  name: "Bob",
  age: 25,
};
```

TypeScript infers a type for `person` automatically here, but that inferred
type has no name — you can't reuse it anywhere else. An **interface** solves
this by giving a shape a name you can refer to again:

```typescript
interface Person {
  name: string;
  age: number;
}

const person: Person = {
  name: "Bob",
  age: 25,
};
```

Once declared, `Person` can be reused anywhere a value of that shape is
expected — as a function parameter, a return type, or another interface's
property.

!!! note "Conceptual Note"
    Think of an interface as a **contract**: it says nothing about *how* a
    value is created, only what it must *look like* once it exists. Anything
    that satisfies the shape — the right property names, the right types —
    satisfies the interface, regardless of where it came from.

Interfaces can also mark properties as **optional**, using `?`:

```typescript
interface Person {
  name: string;
  age: number;
  nickname?: string; // not required
}

const person: Person = { name: "Bob", age: 25 }; // valid, nickname omitted
```

## ✧ Classes & Access Modifiers

TypeScript classes extend JavaScript's own class syntax with **access
modifiers**, which control where a property or method can be used from:

```typescript
class Animal {
  private name: string;
  protected sound: string;
  public age: number;

  constructor(name: string, sound: string, age: number) {
    this.name = name;
    this.sound = sound;
    this.age = age;
  }

  public speak(): void {
    console.log(`${this.name} says ${this.sound}`);
  }
}

const dog = new Animal("Rex", "Woof", 3);
dog.speak();       // fine — speak() is public
console.log(dog.age); // fine — age is public
// console.log(dog.name); // Error — name is private
```

- **`public`** — accessible from anywhere (the default, if no modifier is
  given).
- **`private`** — accessible only from inside the class itself.
- **`protected`** — accessible from the class itself, and from classes that
  extend it.

TypeScript also offers a shorthand, declaring and assigning constructor
parameters in one step:

```typescript
class Animal {
  constructor(private name: string, protected sound: string, public age: number) {}

  public speak(): void {
    console.log(`${this.name} says ${this.sound}`);
  }
}
```

!!! info "Deep Dive"
    You won't see classes used to build the XO Game in this course. Modern
    React Native — and this course throughout — builds components as
    **functions**, using **hooks** (`useState`, `useEffect`, and others) to
    manage behaviour that classes were once needed for. You'll meet this
    properly in Part IV. Classes are still worth understanding here, both
    because you'll recognise them in your other programming subjects, and
    because you'll still come across class-based patterns in older React and
    React Native code you might encounter online.

## ✱ Modules & Imports

As programs grow, splitting code across multiple files becomes essential.
TypeScript uses the same `export` / `import` syntax as modern JavaScript:

```typescript
// math.ts
export function add(a: number, b: number): number {
  return a + b;
}

export const PI: number = 3.14159;
```

```typescript
// main.ts
import { add, PI } from "./math";

console.log(add(5, 3)); // 8
console.log(PI);        // 3.14159
```

A file can also have a single **default export** — useful when a file's whole
purpose is to provide one main thing:

```typescript
// greet.ts
export default function greet(name: string): string {
  return `Hello, ${name}!`;
}
```

```typescript
// main.ts
import greet from "./greet"; // no curly braces for a default import

console.log(greet("Alice"));
```

!!! tip "Pro-Tip"
    Named exports (`export function add`) are generally preferred once a file
    exports more than one thing — they make it clear, at the point of import,
    exactly what's being brought in. Default exports work well for files with
    a single, obvious purpose.

## ※ Enums

An enum defines a fixed set of named constant values — useful whenever a value
should only ever be one of a small, known set of options:

```typescript
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

const move: Direction = Direction.Up;
console.log(move); // 0 — enums default to numbering from 0
```

Enums can also be given explicit string values, which are often clearer to
read in logs or debugging output:

```typescript
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}

console.log(Direction.Up); // "UP"
```

## ✦ Null & Undefined Checks

TypeScript distinguishes carefully between a value that hasn't been assigned
yet (`undefined`) and a value that's deliberately empty (`null`):

```typescript
let username: string | undefined;
console.log(username); // undefined

let selectedItem: string | null = null;
console.log(selectedItem); // null
```

With **strict null checks** enabled (the default for new TypeScript
projects), the compiler forces you to handle the possibility of `null` or
`undefined` before using a value:

```typescript
function greetUser(name: string | null): string {
  if (name === null) {
    return "Hello, guest!";
  }
  return `Hello, ${name}!`;
}
```

!!! warning "Watch Out"
    Recall the `!` used in earlier Stages after `prompt(...)` — that's called
    the **non-null assertion operator**. It tells TypeScript "trust me, this
    will not be null," bypassing the check rather than handling it. Use it
    sparingly, and only when you're genuinely certain — it's a promise to the
    compiler, not a guarantee enforced at runtime.

## ✧ Composing Types (Unions & typeof)

A **union type** allows a value to be one of several specified types:

```typescript
function printId(id: number | string): void {
  console.log(`ID: ${id}`);
}

printId(101);      // valid
printId("A101");   // also valid
```

`typeof` can be used to check a value's type at runtime, often to safely
narrow a union down to one specific type before using it:

```typescript
function printId(id: number | string): void {
  if (typeof id === "number") {
    console.log(`Numeric ID: ${id.toFixed(0)}`);
  } else {
    console.log(`String ID: ${id.toUpperCase()}`);
  }
}
```

!!! note "Conceptual Note"
    This pattern — checking `typeof` before using a value — is called **type
    narrowing**. TypeScript is genuinely smart enough to understand, inside
    each branch of the `if`, exactly which type `id` must be, and will only
    let you call `.toFixed()` inside the branch where it knows `id` is a
    `number`.

## Reflection

Part II is now complete. Between Stages 3, 4, and 5, you've covered the same
ground a first structured-programming subject would — variables, decisions,
loops, arrays, functions — but every one of those ideas now comes with a
declared, checked type attached to it.

Before moving into [Stage 6](../part-3-core-rn-programming/stage-06-basic-components-and-layout.md),
where the XO Game itself begins, sit with this: of everything covered across
these three Stages, which idea do you feel least confident about right now?
Whatever it is, it's worth pausing on — Part III assumes all of it is already
comfortable, so that the focus there can shift fully onto React Native itself.

---

[^1]: TypeScript — Official Handbook, "Classes" and "Everyday Types." Available at: https://www.typescriptlang.org/docs/handbook/2/classes.html (Accessed: 2026).