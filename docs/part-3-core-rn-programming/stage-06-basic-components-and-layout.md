# Stage 6 — Basic Components & Layout

## ❖ Introduction

Every React Native app begins with **components** — the building blocks used
to construct everything you see on screen: text, buttons, images, and the
containers that hold them together. This Stage introduces the core
components, how they're arranged using **Flexbox**, and ends with the very
first screen of the XO Game itself: the Player Setup screen.

## ✦ Understanding `App.tsx`

`App.tsx` is the entry point of every Expo project — the first file that runs,
and the one you looked at briefly back in Stage 2.

```tsx
export default function App() {
  return (
    <View style={styles.container}>
      <Text>Hello World</Text>
    </View>
  );
}
```

- `App()` is a **functional component** — a function that returns what should
  appear on screen.
- What it returns is **JSX** — syntax that looks like HTML, but is actually
  TypeScript underneath, compiled into ordinary function calls.
- JSX must return a **single root element** — here, that's the outer `<View>`.

## ✧ Core Components

| Component | Purpose | Common Props |
|---|---|---|
| `<View>` | A container, similar to a `<div>` in HTML — used to group and arrange other components. | `style` |
| `<Text>` | Displays text. Text content must always be wrapped in a `<Text>` component — unlike HTML, you can't place raw text directly inside a `<View>`. | `style`, `numberOfLines` |
| `<TextInput>` | Allows user input. | `placeholder`, `value`, `onChangeText` |
| `<Button>` | A simple, pre-styled pressable button. | `title`, `onPress` |
| `<Image>` | Displays a local or remote image. | `source`, `style` |

We won't be using `<Image>` for the XO Game itself — once we reach the game
board, the X and O marks on each tile will be built with `<Text>` instead,
which keeps everything self-contained without depending on image assets.

## ✱ A First Example: State and Interaction

Before touching the XO Game, let's build a small, self-contained example to
see state and user interaction working together for the first time.

```tsx
import { useState } from 'react';
import { Button, StyleSheet, Text, View } from 'react-native';

export default function App() {
  const [count, setCount] = useState<number>(0);

  const handlePress = () => {
    setCount(count + 1);
  };

  return (
    <View style={styles.container}>
      <Text>COMPONENTS V1</Text>
      <Text>{count}</Text>
      <Button
        title="CLICK HERE"
        onPress={handlePress}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

A few pieces worth pulling apart:

- **`useState<number>(0)`** — declares a state variable, `count`, starting at
  `0`. `setCount` is the function used to change it. Whenever `count`
  changes, React re-renders the screen to reflect the new value — this is the
  "remembering" and "updating" behaviour that makes an app interactive rather
  than static.
- **`handlePress`** — runs whenever the button is pressed, and calls
  `setCount(count + 1)` to increase the count by one.
- **`{count}`** inside `<Text>` — the curly braces let you embed a
  TypeScript/JavaScript value directly inside JSX. Try changing this to
  `{count * 2}` and see what happens on screen.
- **`StyleSheet.create({...})`** — styling in React Native is written as
  TypeScript objects, not CSS. Property names use camelCase
  (`backgroundColor`, not `background-color`).

## ❖ Flexbox Basics

React Native uses **Flexbox** for layout — the same model used in modern CSS.
The properties you'll reach for constantly:

- `flexDirection` — `'row'` or `'column'` (defaults to `'column'`)
- `justifyContent` — alignment along the main axis
- `alignItems` — alignment along the cross axis

```tsx
<View style={{ flexDirection: 'row', justifyContent: 'space-around' }}>
  <Text>Box 1</Text>
  <Text>Box 2</Text>
  <Text>Box 3</Text>
</View>
```

!!! bug "Common Bug Spotlight"
    **Symptom:** you set `justifyContent: 'center'` on a `<View>`, but nothing
    appears to move.

    **Likely cause:** `flexDirection` defaults to `'column'`. `justifyContent`
    aligns along the *main axis* — which is vertical by default, not
    horizontal. If you're trying to center content horizontally,
    `alignItems: 'center'` is what you want instead; `justifyContent` centers
    it vertically until you explicitly set `flexDirection: 'row'`.

    **Fix:** be deliberate about which axis you're aligning along.
    `justifyContent` = main axis, `alignItems` = cross axis — and which axis
    is "main" depends entirely on `flexDirection`.

## ✦ The XO Game Begins: Player Setup Screen

With components, state, and layout in hand, we can build the very first
screen of the XO Game — where each player enters their name before the game
begins.

```tsx
// App.tsx
import { useState } from 'react';
import { View, Text, TextInput, Button, StyleSheet } from 'react-native';

export default function App() {
  const [player1, setPlayer1] = useState<string>('');
  const [player2, setPlayer2] = useState<string>('');

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Welcome to Tic-Tac-Toe!</Text>

      <TextInput
        placeholder="Player 1 Name"
        value={player1}
        onChangeText={setPlayer1}
        style={styles.input}
      />
      <TextInput
        placeholder="Player 2 Name"
        value={player2}
        onChangeText={setPlayer2}
        style={styles.input}
      />

      <Button
        title="Start Game"
        onPress={() => console.log(`Player 1: ${player1}, Player 2: ${player2}`)}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
    padding: 16,
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginVertical: 12,
  },
  input: {
    borderWidth: 1,
    borderColor: '#ccc',
    paddingHorizontal: 12,
    paddingVertical: 8,
    marginVertical: 8,
    width: '80%',
  },
});
```

- `useState<string>('')` tracks each player's name, starting empty.
- Each `<TextInput>` is **bound** to its own state variable — `value` shows
  the current state, and `onChangeText` updates it as the user types. This
  pairing is what makes an input "controlled" by React, rather than managing
  its own internal value.
- For now, pressing "Start Game" only logs the two names to the console. That
  isn't the finished behaviour — it's a placeholder, so we have something
  visible and testable before the next Stage introduces navigation and
  actually carries these names through to a second screen.

!!! note "Conceptual Note"
    Notice this screen doesn't do anything with the entered names yet beyond
    printing them. That's intentional. Building interfaces in small,
    verifiable steps — confirm the input works, *then* wire it up to
    something bigger — is a habit worth carrying through the rest of this
    course, not just this Stage.

## Exercise

If you haven't already, this is a good point to make your **first Git
commit** for this project — you now have a working Player Setup screen, which
is a meaningful, working checkpoint worth capturing. We'll cover Git and
GitHub workflow properly in [Stage 23](../part-6-extensions/stage-23-github-workflow.md),
but committing at natural milestones like this one, from the very start, is
worth building as a habit now rather than picking it up later.

Alongside that commit, take a moment to consider: what would happen right now
if a player left their name field empty and pressed "Start Game"? Nothing in
the code currently stops them. Keep that question in mind — we'll return to
it once there's an actual game screen worth protecting from an empty name.

## Reflection

You've now built a real, working piece of the XO Game — not a toy example,
but the actual first screen a player will see. Before moving to
[Stage 7](./stage-07-app-navigation.md), where these two names finally travel
somewhere: what do you think happens to `player1` and `player2` the moment
this component re-renders? Are they safe, or could they be reset?

---

[^1]: React Native — Official Documentation, "Core Components." Available at: https://reactnative.dev/docs/components-and-apis (Accessed: 7 July 2026).