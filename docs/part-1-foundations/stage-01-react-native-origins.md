# Stage 1 — React & React Native Origins

## ❖ Introduction

React Native does not exist in isolation. To understand it properly, we first need
to understand its parent: **React**. React began as a JavaScript library, created
at Facebook in 2013, to solve a problem that sounds simple but is genuinely
difficult in practice — how do you build a user interface that updates efficiently
and predictably as the underlying data changes?

React's answer is a **declarative** approach to UI. Rather than manually reaching
into the page and updating pieces of it by hand — the way you might in vanilla
JavaScript — you describe what the interface *should* look like for a given state,
and React takes on the responsibility of updating the view to match. You stop
thinking in terms of "now change this element," and start thinking in terms of
"this is what the screen looks like when the data looks like this."

That shift in thinking is the single most important idea to carry with you into
this course. Everything that follows — components, state, hooks, and eventually the
XO Game you'll be building stage by stage — rests on it.

## ✦ Why React Matters

React introduced a handful of ideas that reshaped how interfaces are built, and
that you'll meet repeatedly from here on:

- **Components** — small, self-contained, reusable building blocks of UI.
- **The Virtual DOM** — a lightweight, in-memory representation of the interface,
  which allows React to work out the *minimum* set of changes needed and apply
  them efficiently.
- **Unidirectional Data Flow** — data moves in one direction through an
  application, which makes it far easier to reason about *where* a value came from
  and *why* the screen looks the way it does.
- **Hooks** — functions such as `useState` and `useEffect` that give components a
  way to hold onto data and respond to change, without needing to write a class.

These aren't abstract theory kept separate from the practical work ahead. They are
the foundation React Native builds on directly.

## ✧ From React to React Native

React Native was released in 2015, also by Facebook, with a clear goal: bring
React's declarative model to mobile development. The key difference is *where* the
UI ends up. React renders HTML elements in a web browser. React Native renders
genuine **native components** — real iOS and Android interface elements — using
that same component-based, declarative approach.

- **React** → builds web applications (HTML, CSS, JavaScript/TypeScript).
- **React Native** → builds mobile applications (native UI components,
  JavaScript/TypeScript).

The underlying philosophy doesn't change between the two: **UI = f(state)** — the
interface is a function of your data. What changes is the environment the UI is
rendered into, and the specific building blocks available to you.

## ✱ A Glimpse Ahead: Hooks

You'll meet hooks properly, in depth, once the XO Game has state worth managing —
see [Stage 12 – State & Effects](../part-4-expanding-core-concepts/stage-12-state-and-effects.md).
For now, it's enough to recognise the names and have a rough sense of what each one
is for, so they don't feel unfamiliar when they first appear in code:

- `useState` — holds a piece of data that can change over time (for example, the
  current player's name, or whose turn it is).
- `useEffect` — runs some code in response to a change (for example, saving a
  finished game's result once the board is full).
- `useContext` — shares a value across several components without having to pass
  it down manually through every layer in between.

You don't need to understand these fully yet. Just let the names become familiar.

## ※ Why This Stage Matters

Starting with React's origins, rather than jumping straight into React Native
syntax, matters for a few reasons:

- It shows the **continuity** running through this course — from the structured
  thinking you've practised in earlier programming subjects, through TypeScript,
  into React, and finally into React Native.
- It makes clear that React Native is not a separate language to learn from
  scratch, but an extension of ideas you're about to become fluent in.
- It sets up *why* concepts like components, state, and hooks matter so much in
  the stages ahead — they aren't arbitrary features of a framework, they're the
  direct consequence of the declarative philosophy introduced here.

## Reflection

Think of React as the parent, and React Native as the child that inherits its
strengths and adapts them for a new environment. Before moving on, sit with two
questions:

- Where else — in a subject you've studied, or a tool you've used — have you seen
  the idea of *describing an outcome* rather than *manually performing every step*
  to reach it?
- React's declarative model reshaped UI development across the web, and React
  Native then carried that same model onto mobile. Why might a single underlying
  idea be powerful enough to work well in two very different environments?

There's no need to write formal answers to these — just carry them with you into
[Stage 2 – Environment Setup](./stage-02-environment-setup.md), where theory gives
way to practice.

!!! quote "Further Reading"
    - The official React documentation, for the declarative philosophy in the
      project's own words.[^1]
    - The official React Native documentation, for how that philosophy is applied
      to native mobile development.[^2]

---

[^1]: React — Official Documentation. Available at: https://react.dev (Accessed: 8 july 2026).
[^2]: React Native — Official Documentation. Available at: https://reactnative.dev (Accessed: 8 July 2026).