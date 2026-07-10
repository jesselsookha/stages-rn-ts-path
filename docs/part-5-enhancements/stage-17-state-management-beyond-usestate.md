# Stage 17 — State Management Beyond useState

## ❖ Introduction

Every Stage so far has taught something this course expects you to actually
use, in the XO Game itself. This one is different, and worth being upfront
about: **Redux**, and the wider world of state management libraries, is
territory well beyond what an introductory subject needs to cover in
depth. What follows is deliberately an on-ramp — enough to recognise the
vocabulary, understand why these tools exist, and know where to look
further, rather than a complete treatment. Where that's true elsewhere in
this Stage too, it'll be said plainly, with a link pointing toward where
real depth lives.

## ✦ Context, Revisited Briefly

Context API was covered properly back in
[Stage 13](../part-4-expanding-core-concepts/stage-13-context-and-settings.md)
— nothing new to add here. Worth restating only its boundary: Context
solves *sharing* a value across screens. It doesn't, on its own, give you
Redux's stricter guarantees about *how* that value is allowed to change.
That distinction is exactly what the rest of this Stage is about.

## ✧ Redux: The Idea, Not Mastery

Redux centralises an app's state into a single **store**, and enforces a
strict rule about how that state is allowed to change: components never
modify it directly — they **dispatch** an action describing *what
happened*, and a **reducer** — a pure function — decides what the new state
should be.

- **Store** — holds the entire application's state.
- **Action** — a plain object describing an intent (`{ type: 'INCREMENT' }`).
- **Reducer** — a function that takes the current state and an action, and
  returns the new state.
- **Dispatch** — the function used to send an action to the store.

!!! note "Conceptual Note"
    Redux's own maintainers no longer recommend the original, hand-written
    version of this pattern for new projects — **Redux Toolkit**
    (`@reduxjs/toolkit`) is now the official, recommended way to write
    Redux, specifically because it removes a lot of repetitive setup code
    without changing the underlying idea.[^1] The example below uses Redux
    Toolkit for that reason.

```bash
npx expo install @reduxjs/toolkit react-redux
```

```typescript
// counterSlice.ts
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
  },
});

export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;
```

```typescript
// store.ts
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

export const store = configureStore({
  reducer: { counter: counterReducer },
});
```

```tsx
// App.tsx
import { Provider, useSelector, useDispatch } from 'react-redux';
import { View, Text, Button } from 'react-native';
import { store } from './store';
import { increment, decrement } from './counterSlice';

function Counter() {
  const count = useSelector((state: any) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <View>
      <Text>{count}</Text>
      <Button title="+" onPress={() => dispatch(increment())} />
      <Button title="-" onPress={() => dispatch(decrement())} />
    </View>
  );
}

export default function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

`createSlice` generates the action creators (`increment`, `decrement`) and
the reducer together, from one definition — noticeably less code than
writing action types, action creators, and a `switch`-based reducer
separately.

!!! tip "Pro-Tip"
    Redux Toolkit isn't the only modern option worth knowing exists.
    **Zustand** has become a genuinely popular, much lighter alternative
    for apps that don't need Redux's full structure — worth a look
    ([zustand docs](https://github.com/pmndrs/zustand)) if Redux here feels
    like a lot of ceremony for what it's doing. Recognising that a choice
    exists here is the actual goal of this section, not memorising Redux
    Toolkit specifically.

## ❖ AsyncStorage vs Redux

Both manage state, but solve different problems:

| | AsyncStorage | Redux |
|---|---|---|
| **Scope** | Local device storage | In-memory application state |
| **Use case** | Data surviving app restarts (scores, preferences) | Live state shared across many screens |
| **Persistence** | Survives app restarts by default | Reset on restart, unless deliberately combined with storage |
| **Complexity** | Simple, minimal setup | More setup, more structure |

The two are commonly used *together* in real apps — Redux for what's
happening right now, AsyncStorage (or a Redux-aware persistence layer) for
what should still be true after the app reopens.

## ✦ Debugging State Management

- **Prop drilling** — solved with Context (Stage 13) or, at larger scale,
  Redux.
- **State resetting on restart** — solved with AsyncStorage, or a
  persistence layer on top of Redux.
- **A reducer growing unmanageable** — split it into smaller slices, each
  responsible for one part of the state, as `createSlice` already
  encourages.
- **Unexpected re-renders** — check `useSelector`'s selector function isn't
  returning a new object on every call, since that alone can trigger a
  re-render even when the underlying data hasn't meaningfully changed.

## ❖ Where This Fits — And Where It Doesn't

The XO Game itself never needed Redux — a board, a turn flag, and a
scoreboard array were always well within what `useState`, `useEffect`, and
Context could handle comfortably. That's not a limitation of this course;
it's a realistic reflection of when these tools actually earn their place.
Redux (or Zustand, or any alternative) becomes worth reaching for once an
app's state genuinely needs to be read and changed from many unrelated
places at once — a shopping cart touched by a product page, a cart icon,
and a checkout screen simultaneously, for instance.

## Exercise

No commit expected for this one, since nothing here extends the XO Game
itself. Instead: read through the [official Redux "Getting Started"
guide](https://redux.js.org/introduction/getting-started) far enough to
answer one question in your own words — what problem is Redux actually
solving, that Context by itself doesn't?

## Reflection

This Stage was deliberately different in shape from the ones before it —
less "build this," more "here's a door, and here's where it leads." That's
worth noticing as its own kind of information: not everything worth knowing
about needs to be mastered inside a single introductory subject. Knowing a
tool exists, roughly what problem it solves, and where to read further when
the time comes is its own legitimate outcome.

[Stage 18](./stage-18-file-handling-and-crud.md) returns to firmer ground —
file handling and CRUD operations, built directly into the XO Game project.

---

[^1]: Redux — Official Documentation, "Why Redux Toolkit is How To Use Redux Today." Available at: https://redux.js.org/introduction/why-rtk-is-redux-today (Accessed: 9 July 2026).
[^2]: Redux Toolkit — Official Documentation. Available at: https://redux-toolkit.js.org/ (Accessed: 9 July 2026).