# Stage 3 — Programming Basics in TypeScript

## ❖ Introduction

In Stage 1, we looked at *why* React exists, and how React Native carries that
same thinking into mobile development. Before we can put that thinking into
practice, though, we need a solid grip on the language we'll be writing it in:
**TypeScript**.

If you've worked through Java or C# in your other programming subjects, much of
what follows will feel familiar — TypeScript is a **statically typed** language,
in the same spirit as those. What makes it a little different is *how* it gets
there: TypeScript isn't a language that runs directly. It's a layer written on
top of JavaScript, which compiles down into plain JavaScript before it ever
executes.

!!! info "Deep Dive"
    JavaScript itself has no concept of type-checking before your code runs — a
    variable can hold a number one moment and a string the next, and JavaScript
    won't complain until something breaks. TypeScript adds a checking step
    *before* that: it looks at your code, checks that the types you're using
    make sense, and only then compiles it down to ordinary JavaScript. The
    checking happens at **compile-time**; the actual running still happens as
    JavaScript, same as always.

## ✦ Variables, Constants & Data Types

TypeScript uses the same `let` and `const` you may already associate with
modern JavaScript — the difference is that TypeScript lets you (and encourages
you to) declare *what type of value* a variable is allowed to hold.

=== "JavaScript"
```javascript
    let age = 30;        // inferred as a number, but nothing stops this changing later
    let name = "Alice";  // inferred as a string
    age = "thirty";       // JavaScript allows this without complaint
```

=== "TypeScript"
```typescript
    let age: number = 30;
    let name: string = "Alice";
    age = "thirty";       // Error: Type 'string' is not assignable to type 'number'.
```

The core data types you'll use constantly are:

| Type | Example | Notes |
|---|---|---|
| `number` | `let count: number = 5;` | Covers integers and decimals — TypeScript doesn't separate `int` and `float` the way Java or C# do. |
| `string` | `let name: string = "Alice";` | Single, double, or backtick quotes — backticks allow template literals. |
| `boolean` | `let isReady: boolean = true;` | |
| `array` | `let scores: number[] = [10, 20, 30];` | An array of a single, declared type. |
| `any` | `let value: any = "hello";` | Opts *out* of type checking — useful occasionally, but avoid leaning on it; it defeats the purpose of using TypeScript at all. |
| `void` | `function log(): void { }` | Used for functions that don't return a value. |

`const` works exactly as you'd expect from other structured languages — a value
that, once assigned, cannot be reassigned:

```typescript
const PI: number = 3.14159;
```

## ✧ Type Inference

You won't always need to write the type explicitly. TypeScript is often able to
work it out on its own, based on the value you assign:

```typescript
let score = 100; // TypeScript infers: number
score = "high";  // Error — TypeScript already decided score is a number
```

This is worth understanding rather than memorising: TypeScript isn't asking you
to annotate *everything*. It's asking that, once a variable's type is
established — whether you wrote it explicitly or TypeScript inferred it — that
type is respected consistently from then on.

!!! tip "Pro-Tip"
    A common style question: should you write `let age: number = 30;` or just
    `let age = 30;`? Both are valid TypeScript. Many developers only write
    explicit annotations when the type *isn't* obvious from the value — for
    example, function parameters (which have no assigned value to infer from)
    almost always need one.

## ✱ Operators & Built-In Functions

TypeScript inherits JavaScript's operators directly — nothing new to learn here
syntactically, just the same structured-programming operators you've used
before:

- **Arithmetic:** `+`, `-`, `*`, `/`, `%`
- **Comparison:** `===`, `!==`, `<`, `>`, `<=`, `>=`
- **Logical:** `&&`, `||`, `!`
- **Assignment:** `=`, `+=`, `-=`, `*=`, `/=`

!!! warning "Watch Out"
    Always prefer `===` and `!==` over `==` and `!=`. The two-equals versions
    perform type *coercion* before comparing — `"5" == 5` is `true` in
    JavaScript, which is rarely what you actually want. TypeScript won't stop
    you from using `==`, so this is a habit worth building deliberately.

A few built-in functions you'll reach for constantly at this stage:

```typescript
console.log("Output to the console");
parseInt("42");        // converts a string to an integer: 42
parseFloat("3.14");    // converts a string to a decimal: 3.14
Math.round(4.7);       // 5
Math.max(3, 7, 2);     // 7
```

## ※ Expressions, Equations & Comments

An **expression** is anything that evaluates to a value — `2 + 2`, `age > 18`,
`name + " " + surname` are all expressions. An **equation**, in the sense we
mean here, is simply an expression assigned to a variable:

```typescript
let total: number = price * quantity; // an equation, built from an expression
```

Comments work exactly as in JavaScript:

```typescript
// A single-line comment

/*
 * A multi-line comment,
 * useful for explaining a block of logic
 */
```

!!! note "Conceptual Note"
    Get into the habit of commenting *why* something is done, not *what* is
    being done — the code itself already shows what's happening. A comment
    like `// increment counter` above `count++` adds nothing. A comment
    explaining *why* a particular value is hardcoded, or *why* a loop starts
    at 1 instead of 0, is genuinely useful to a reader six months from now —
    including yourself.

## ✦ Sequential Logic in Practice

The best way to bring all of this together is to revisit a program you've
likely already written in pseudocode form in an earlier programming subject —
translated here into full, working TypeScript.

**The problem:** read five numbers into an array, calculate their sum and
average, then optionally search the array for a value the user provides.

**The pseudocode:**

```text
0. Start
1. Declarations
    num intNumbers[5]
    num intCount
    num intSum
    num fltAvg
    string strSearchDecision
    num intSearch, intFlag

2.  intSum = 0

3.  for intCount = 0 to 4 Step 1
        output "Enter number for cell [" + intCount + "]: "
        input intNumbers[intCount]
        intSum = intSum + intNumbers[intCount]
    endfor

4.  fltAvg = intSum / 5

5.  output "The sum of the 5 numbers is " + intSum
6.  output "The average of the 5 numbers is " + fltAvg

7.  output "Would you like to search for a value in the array? [YES / NO]: "
8.  input strSearchDecision

9.  if strSearchDecision = "YES" then
        intFlag = 0
        output "Enter the value you wish to search: "
        input intSearch

        for intCount = 0 to 4 Step 1
            if intSearch = intNumbers[intCount] then
                intFlag = 1
            endif
        endfor

        if intFlag = 0 then
            output "The value was not found "
        else
            output "The value was found "
        endif
    endif

10. Stop
```

**The TypeScript translation:**

```typescript
// 1. Declarations
let intNumbers: number[] = new Array(5);
let intCount: number = 0;
let intSum: number = 0;
let fltAvg: number = 0;
let strSearchDecision: string = "";
let intSearch: number = 0;
let intFlag: number = 0;

// 2. Initialise intSum
intSum = 0;

// 3. Loop to input numbers
for (intCount = 0; intCount < 5; intCount++) {
  intNumbers[intCount] = parseInt(
    prompt(`Enter number for cell [${intCount}]: `) as string
  );
  intSum += intNumbers[intCount];
}

// 4. Calculate average
fltAvg = intSum / 5;

// 5 & 6. Output sum and average
console.log(`The sum of the 5 numbers is ${intSum}`);
console.log(`The average of the 5 numbers is ${fltAvg}`);

// 7 & 8. Ask if the user wants to search for a value
strSearchDecision = prompt(
  "Would you like to search for a value in the array? [YES / NO]: "
)!.toUpperCase();

// 9. Search for the value, if requested
if (strSearchDecision === "YES") {
  intFlag = 0;
  intSearch = parseInt(prompt("Enter the value you wish to search: ") as string);

  for (intCount = 0; intCount < 5; intCount++) {
    if (intSearch === intNumbers[intCount]) {
      intFlag = 1;
    }
  }

  if (intFlag === 0) {
    console.log("The value was not found");
  } else {
    console.log("The value was found");
  }
}
```

!!! tip "Pro-Tip"
    Try this one out for yourself in the [TypeScript Playground](https://www.typescriptlang.org/play).
    It compiles and runs your TypeScript directly in the browser, which means
    `prompt()` works exactly as shown above. This won't be true everywhere —
    `prompt()` doesn't exist in Node.js, and it doesn't exist inside a React
    Native app either. For now, that's fine: this Stage is about the language
    itself, not the app. Real user input, through components like
    `TextInput`, starts once we're properly inside React Native.

A short walkthrough of what changed, and why, compared to the pseudocode:

- Every declaration now carries an explicit **type annotation** — `number[]`,
  `number`, `string` — matching what you declared in the pseudocode's
  `Declarations` step almost line for line.
- The `for` loop's structure — `for (intCount = 0; intCount < 5; intCount++)`
  — mirrors `for intCount = 0 to 4 Step 1` directly; we'll look at this
  translation properly in Stage 4.
- `intSum += intNumbers[intCount];` is shorthand for
  `intSum = intSum + intNumbers[intCount];` — both are valid, the shorthand is
  simply more common in practice.
- The `!` after the second `prompt(...)` call tells TypeScript "trust me, this
  won't be `null`" — without it, TypeScript would (correctly) point out that
  `prompt()` can return `null` if a user cancels the dialog, and refuse to let
  you call `.toUpperCase()` on a possibly-`null` value.

## Reflection

Every variable in the program above already existed, conceptually, in your
original pseudocode — TypeScript hasn't asked you to think any differently
about the *logic*. What it has asked for is precision: a declared type for
every value, up front, before the program runs.

We'll return to this exact program in [Stage 4](./stage-04-logical-structures-and-data-handling.md),
this time paying close attention to the `for` loop and the `if` statements —
the parts of this program concerned with *decisions* rather than *sequence*.

---

[^1]: TypeScript — Official Handbook, "Everyday Types." Available at: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html (Accessed: 2026).
[^2]: React Native — Official Documentation, "Using TypeScript." Available at: https://reactnative.dev/docs/typescript (Accessed: 2026).