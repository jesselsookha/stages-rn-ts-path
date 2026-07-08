# Stage 4 — Logical Structures & Data Handling

## ❖ Introduction

In Stage 3, the five-numbers program gave us a first working TypeScript
program — but it only showed us a single `for` loop and a single `if`
statement. Real programs need more tools than that. This Stage looks properly
at **decisions**, **loops**, **arrays**, and **functions** — the structures
that give a program its actual logic, rather than just its sequence.

We'll revisit the Stage 3 program directly for its loop and decision, and
introduce a fresh scenario to properly explore what arrays can do.

## ✦ Decision Logic

### if, if..else, and Nested if

You've already seen a simple `if` in Stage 3's search logic. Here it is again,
alongside its `else` and nested forms:

```typescript
let intFlag: number = 1;

// Simple if
if (intFlag === 1) {
  console.log("The value was found");
}

// if..else
if (intFlag === 1) {
  console.log("The value was found");
} else {
  console.log("The value was not found");
}

// Nested if
let intSearch: number = 42;
if (intFlag === 1) {
  if (intSearch > 0) {
    console.log("A positive value was found");
  } else {
    console.log("A negative value was found");
  }
}
```

### Shorthand Ternary

A ternary expression condenses a simple `if..else` into a single line — useful
when you're assigning a value based on a condition, rather than running a
whole block of code:

```typescript
let intFlag: number = 1;

const resultMessage: string = intFlag === 1 ? "Found" : "Not found";
console.log(resultMessage);
```

!!! note "Conceptual Note"
    A ternary is `condition ? valueIfTrue : valueIfFalse`. It's not a
    replacement for `if..else` in every situation — reach for it when you're
    choosing between two *values*, not when you need to run different blocks
    of logic.

### switch

`switch` is useful once you're comparing a single value against several
possibilities — a cleaner alternative to a long chain of `if..else if`:

```typescript
let grade: string = "B";

switch (grade) {
  case "A":
    console.log("Excellent");
    break;
  case "B":
    console.log("Good");
    break;
  case "C":
    console.log("Satisfactory");
    break;
  default:
    console.log("Grade not recognised");
}
```

!!! warning "Watch Out"
    Don't forget `break;` at the end of each case. Without it, execution
    "falls through" into the next case — sometimes intentional, but almost
    always a bug when it isn't.

## ✧ Loop Logic

### for — Revisited

Here's the loop from the Stage 3 program again:

```typescript
for (intCount = 0; intCount < 5; intCount++) {
  intSum += intNumbers[intCount];
}
```

The three parts inside the parentheses map directly onto what the pseudocode
described as `for intCount = 0 to 4 Step 1`:

- `intCount = 0` — where the loop starts
- `intCount < 5` — the condition checked *before* every iteration
- `intCount++` — what happens *after* each iteration

### while

A `while` loop checks its condition before each iteration, same as `for`, but
doesn't have a built-in counter — you manage that yourself:

```typescript
let counter: number = 0;

while (counter < 5) {
  console.log(`Counter is at ${counter}`);
  counter++;
}
```

### do..while

A `do..while` loop is similar, but checks its condition *after* running the
block — meaning it always runs at least once, even if the condition is false
from the start:

```typescript
let attempts: number = 0;

do {
  console.log(`Attempt number ${attempts + 1}`);
  attempts++;
} while (attempts < 3);
```

### for..in

`for..in` iterates over the **indices** (or keys) of a structure, rather than
its values directly:

```typescript
const fruits: string[] = ["Apple", "Banana", "Cherry"];

for (const index in fruits) {
  console.log(`Index ${index}: ${fruits[index]}`);
}
```

!!! info "Deep Dive"
    You'll also come across `for..of` in other people's code and online
    tutorials — it looks similar but iterates over **values** directly,
    rather than indices: `for (const fruit of fruits) { console.log(fruit); }`.
    Both exist in TypeScript; `for..in` is what's used consistently through
    these notes, but recognising `for..of` when you see it elsewhere is worth
    having in the back of your mind.

### forEach (Array "for-each")

Arrays also provide a `forEach` method, which runs a function once for every
element — a very common pattern in TypeScript and JavaScript code:

```typescript
const fruits: string[] = ["Apple", "Banana", "Cherry"];

fruits.forEach((fruit, index) => {
  console.log(`Index ${index}: ${fruit}`);
});
```

### Nested Loops

Loops can be placed inside one another — useful whenever you're working with
two dimensions of data, such as rows and columns:

```typescript
for (let row: number = 1; row <= 3; row++) {
  for (let col: number = 1; col <= 3; col++) {
    console.log(`Row ${row}, Column ${col}`);
  }
}
```

We'll put nested loops to proper use shortly, once we introduce 2D arrays
below.

## ✱ Arrays in Depth

For this section, we'll step away from the five-numbers program and work with
a fresh scenario: **a week of temperature readings**.

### Declaration and Input

```typescript
const temperatures: number[] = [22, 24, 19, 21, 23, 25, 20];
```

### Sum, Average, High, and Low

```typescript
let total: number = 0;
let highest: number = temperatures[0];
let lowest: number = temperatures[0];

for (let i: number = 0; i < temperatures.length; i++) {
  total += temperatures[i];

  if (temperatures[i] > highest) {
    highest = temperatures[i];
  }
  if (temperatures[i] < lowest) {
    lowest = temperatures[i];
  }
}

const average: number = total / temperatures.length;

console.log(`Total: ${total}`);
console.log(`Average: ${average.toFixed(1)}`);
console.log(`Highest: ${highest}`);
console.log(`Lowest: ${lowest}`);
```

!!! tip "Pro-Tip"
    `.toFixed(1)` rounds a number to a fixed number of decimal places, and
    returns it as a string — handy for display purposes, like showing a
    temperature to one decimal place instead of TypeScript's full floating
    point precision.

### Searching

```typescript
const searchValue: number = 24;
let found: boolean = false;

for (let i: number = 0; i < temperatures.length; i++) {
  if (temperatures[i] === searchValue) {
    found = true;
    console.log(`Found ${searchValue} at index ${i}`);
    break;
  }
}

if (!found) {
  console.log(`${searchValue} was not found`);
}
```

### Sorting

```typescript
const sortedAscending: number[] = [...temperatures].sort((a, b) => a - b);
console.log("Sorted ascending:", sortedAscending);
```

!!! bug "Common Bug Spotlight"
    **Symptom:** `[22, 24, 19].sort()` produces `[19, 22, 24]` — correct here,
    but try `[100, 25, 9].sort()` and you'll get `[100, 25, 9]` unchanged.

    **Cause:** by default, `.sort()` converts elements to strings and compares
    them alphabetically — so `"100"` sorts before `"25"` because `"1"` comes
    before `"2"`.

    **Fix:** always supply a comparison function for numeric sorting, as shown
    above — `(a, b) => a - b` for ascending, `(a, b) => b - a` for descending.

    Also notice `[...temperatures]` — the spread operator here creates a
    *copy* of the array before sorting, so the original `temperatures` array
    is left untouched. `.sort()` otherwise sorts the array **in place**.

### Parallel Arrays

Sometimes two arrays are used together, where matching indices represent
related data:

```typescript
const days: string[] = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"];
const temps: number[] = [22, 24, 19, 21, 23, 25, 20];

for (let i: number = 0; i < days.length; i++) {
  console.log(`${days[i]}: ${temps[i]}°C`);
}
```

### 2D Arrays

If we tracked temperatures across several weeks instead of just one, a 2D
array — an array of arrays — becomes useful:

```typescript
const weeklyTemps: number[][] = [
  [22, 24, 19, 21, 23, 25, 20], // Week 1
  [18, 20, 22, 21, 19, 23, 24], // Week 2
];

for (let week: number = 0; week < weeklyTemps.length; week++) {
  for (let day: number = 0; day < weeklyTemps[week].length; day++) {
    console.log(`Week ${week + 1}, Day ${day + 1}: ${weeklyTemps[week][day]}°C`);
  }
}
```

This is the nested loop from earlier, now put to genuine use — the outer loop
walks through each week, the inner loop walks through each day within it.

## ※ Functions & Procedures

Rather than a new example here, let's revisit the five-numbers program from
Stage 3 — this time, **refactored into functions**. Splitting a program into
functions doesn't change what it does; it changes how clearly its parts are
separated, and how reusable each part becomes.

```typescript
function inputNumbers(count: number): number[] {
  const numbers: number[] = new Array(count);
  for (let i: number = 0; i < count; i++) {
    numbers[i] = parseInt(prompt(`Enter number for cell [${i}]: `) as string);
  }
  return numbers;
}

function calculateSum(numbers: number[]): number {
  let sum: number = 0;
  for (let i: number = 0; i < numbers.length; i++) {
    sum += numbers[i];
  }
  return sum;
}

function calculateAverage(sum: number, count: number): number {
  return sum / count;
}

function searchArray(numbers: number[], target: number): boolean {
  for (let i: number = 0; i < numbers.length; i++) {
    if (numbers[i] === target) {
      return true;
    }
  }
  return false;
}

// Bringing it all together
const intNumbers: number[] = inputNumbers(5);
const intSum: number = calculateSum(intNumbers);
const fltAvg: number = calculateAverage(intSum, 5);

console.log(`The sum of the 5 numbers is ${intSum}`);
console.log(`The average of the 5 numbers is ${fltAvg}`);

const strSearchDecision: string = prompt(
  "Would you like to search for a value in the array? [YES / NO]: "
)!.toUpperCase();

if (strSearchDecision === "YES") {
  const intSearch: number = parseInt(prompt("Enter the value you wish to search: ") as string);
  const wasFound: boolean = searchArray(intNumbers, intSearch);
  console.log(wasFound ? "The value was found" : "The value was not found");
}
```

Notice what each function declares:

- **Parameters** — `inputNumbers(count: number)` takes in a `number`, and
  TypeScript checks that whatever's passed in matches.
- **Return type** — `: number[]`, `: number`, `: boolean` after each
  function's parameter list states exactly what kind of value comes back out.
- **Passing arrays** — `calculateSum(numbers: number[])` and
  `searchArray(numbers: number[], target: number)` both take an array as a
  parameter, the same way any other value can be passed in.

!!! note "Conceptual Note"
    Nothing about *what* this program does has changed from Stage 3 — same
    inputs, same logic, same output. What's changed is that each responsibility
    now has a name and a clear boundary. This is the same idea of refactoring
    you'll have encountered in your other programming subjects, and one we'll
    return to properly once we're refactoring actual React Native components,
    later in this course.

## Reflection

Between decisions, loops, arrays, and functions, you now have the full toolkit
that any program — regardless of language — is built from. TypeScript hasn't
asked you to think differently about any of these ideas; it's asked for
precision about the *types* moving through them.

Before moving to [Stage 5](./stage-05-advanced-typescript-features.md), consider:
looking back at the refactored version above, which function would you trust
to reuse in a completely different program without changes — and which one is
too tied to this specific problem to reuse as-is?

---

[^1]: TypeScript — Official Handbook, "Narrowing" and "Everyday Types." Available at: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html (Accessed: 2026).