# Stage 10 — Refactoring & Structure

## ❖ Introduction

Back in Stage 7, we noted that keeping everything inside `App.tsx` was
deliberate — a way to see a whole, working program in one place before
worrying about how it's organised. That reason has now run its course. The
file has grown a navigator, two screens, a nine-tile board, and winner
detection, all in one place. This Stage does two things, in order:

1. Refactors the code that's still awkward *within* `App.tsx` — specifically,
   a genuine mistake that's easy to make the first time you extract a
   function.
2. Properly plans, then carries out, splitting the project into separate
   files — the change we've been promising since Stage 7.

## ✦ Extracting a Helper Function — And Getting It Wrong First

Stage 9 left us with nine identical `TouchableHighlight` blocks, each calling
`handlePress(index)` with its own hardcoded index. A natural next step is to
extract the *cell itself* into a small helper function, so the board can be
built with less repetition still visible in the JSX. Here's a first attempt:

```tsx
const createCell = (index: number) => (
  <TouchableHighlight style={styles.cell} onPress={handlePress(index)}>
    <Text style={styles.cellText}>{board[index]}</Text>
  </TouchableHighlight>
);
```

This looks reasonable — but run it, and every tile fires immediately, before
you've even tapped anything, and the app likely crashes or misbehaves right
away.

!!! bug "Common Bug Spotlight"
    **Symptom:** tapping never seems to be what triggers the tile —
    something happens the moment the screen renders instead.

    **Cause:** `onPress={handlePress(index)}` **calls** `handlePress(index)`
    immediately, during render, and hands its return value — `undefined` —
    to `onPress`. `onPress` needs a *function to call later*, not the result
    of calling one *right now*.

    **Fix:** wrap it in an arrow function, so `onPress` receives a function
    reference instead of a call: `onPress={() => handlePress(index)}`.

```tsx
const createCell = (index: number) => (
  <TouchableHighlight
    key={index}
    style={styles.cell}
    onPress={() => handlePress(index)}
  >
    <Text style={styles.cellText}>{board[index]}</Text>
  </TouchableHighlight>
);
```

!!! note "Conceptual Note"
    This distinction — a function versus the result of calling it — comes up
    constantly in React. `onPress`, `onChangeText`, `useEffect`'s callback,
    and many others all expect to be handed a function they can call
    *themselves*, at the right time. Handing them the result of a call
    you've already made is one of the most common mistakes when refactoring
    inline logic into named functions, precisely because the working inline
    version (`onPress={() => { ... }}`) already looks similar enough to
    obscure the difference.

With `createCell` fixed, the board itself becomes a single line:

```tsx
<View style={styles.board}>
  {board.map((_, index) => createCell(index))}
</View>
```

## ✧ Planning Before Splitting Files

Before creating a single new file, it's worth asking a more basic question:
**what different kinds of things does this code actually contain?** Splitting
files without answering that first tends to produce folders that don't
actually make anything clearer — just the same code, spread out.

Looking at `App.tsx` as it stands, four distinct responsibilities are
tangled together:

- **Full screens** — `PlayerScreen` and `GameScreen`, each representing an
  entire view a user navigates between.
- **A reusable piece of UI** — the individual tile, which doesn't care which
  screen it's used on, only what value it displays and what happens when
  it's pressed.
- **Pure game logic** — the winner-checking logic, which needs no knowledge
  of React, navigation, or styling at all; given a board, it just returns an
  answer.
- **Static resources** — none in active use right now, since the board is
  text-based, but this is where images, fonts, or icons would live if the
  project needed them later.

Grouping files by *what kind of responsibility they hold* — rather than, say,
by feature, or leaving everything flat — is what makes each of the following
folders make sense on its own terms, before we've even written what goes
inside them.

## ✱ Suggested Directory Structure

```
project-root/
├── App.tsx                # Entry point — sets up navigation only
├── screens/
│   ├── PlayerScreen.tsx   # Full-page view: name entry
│   └── GameScreen.tsx     # Full-page view: the game board
├── components/
│   └── Cell.tsx           # One reusable tile
├── utils/
│   └── gameLogic.ts       # Pure winner-checking logic, no UI
└── assets/                # Reserved for images/fonts, currently unused
```

!!! note "Conceptual Note"
    `assets/` is included here even though nothing lives in it yet — Expo's
    default template already ships a small set of icons for the app itself
    (its home-screen icon, splash screen, and so on), which is why the
    folder exists in your project regardless. The XO Game itself doesn't add
    anything to it, since every tile is text-based by design.

## ❖ Breaking Down the Files

### `App.tsx`

Reduced to exactly one job — wiring up navigation:

```tsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import PlayerScreen from './screens/PlayerScreen';
import GameScreen from './screens/GameScreen';

type RootStackParamList = {
  Home: undefined;
  Game: { player1: string; player2: string };
};

const Stack = createNativeStackNavigator<RootStackParamList>();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={PlayerScreen} />
        <Stack.Screen name="Game" component={GameScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

### `screens/PlayerScreen.tsx`

```tsx
import { useState } from 'react';
import { View, Text, TextInput, Button, StyleSheet } from 'react-native';

export default function PlayerScreen({ navigation }: { navigation: any }) {
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
        onPress={() =>
          navigation.navigate('Game', { player1: player1, player2: player2 })
        }
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

### `components/Cell.tsx`

The reusable tile, extracted properly this time — as its own component,
rather than a helper function living inside `GameScreen`:

```tsx
import { TouchableHighlight, Text, StyleSheet } from 'react-native';

type CellProps = {
  value: string | null;
  onPress: () => void;
};

export default function Cell({ value, onPress }: CellProps) {
  return (
    <TouchableHighlight style={styles.cell} onPress={onPress}>
      <Text style={styles.cellText}>{value}</Text>
    </TouchableHighlight>
  );
}

const styles = StyleSheet.create({
  cell: {
    width: 80,
    height: 80,
    borderWidth: 1,
    borderColor: '#333',
    justifyContent: 'center',
    alignItems: 'center',
    margin: 2,
  },
  cellText: {
    fontSize: 40,
    fontWeight: 'bold',
  },
});
```

!!! tip "Pro-Tip"
    Notice `Cell` takes `onPress` as a prop, rather than knowing anything
    about `handlePress` or the board itself. This is what makes it genuinely
    reusable — `Cell` doesn't need to know *why* it was pressed, only that
    something happens when it is. `GameScreen` still owns all the actual game
    logic; `Cell` just displays a value and reports a tap.

### `utils/gameLogic.ts`

The winner-checking logic from Stage 9, moved here exactly as it was — pure
logic, with no React or UI code mixed in:

```typescript
const WINNING_LINES: number[][] = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8], // rows
  [0, 3, 6], [1, 4, 7], [2, 5, 8], // columns
  [0, 4, 8], [2, 4, 6],           // diagonals
];

export function checkWinner(board: (string | null)[]): string | null {
  for (const line of WINNING_LINES) {
    const [a, b, c] = line;
    if (board[a] !== null && board[a] === board[b] && board[b] === board[c]) {
      return board[a];
    }
  }
  return null;
}
```

!!! note "Conceptual Note"
    Nothing about this function changed — only *where* it lives. That's
    worth noticing: refactoring file structure and refactoring logic are two
    separate concerns. We already refactored this logic properly, in
    Stage 9. Today's job is only ever about *where* code lives, not what it
    does.

### `screens/GameScreen.tsx`

```tsx
import { useState } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import Cell from '../components/Cell';
import { checkWinner } from '../utils/gameLogic';

export default function GameScreen({ route }: { route: any }) {
  const { player1, player2 } = route.params;
  const [board, setBoard] = useState<(string | null)[]>(Array(9).fill(null));
  const [player1Turn, setPlayer1Turn] = useState<boolean>(true);

  const winner = checkWinner(board);

  const handlePress = (index: number) => {
    if (winner !== null) return;
    if (board[index] !== null) return;

    const newBoard = [...board];
    newBoard[index] = player1Turn ? 'X' : 'O';
    setBoard(newBoard);
    setPlayer1Turn(!player1Turn);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{player1} vs. {player2}</Text>
      <Text style={player1Turn ? styles.red : styles.blue}>
        {player1Turn ? `${player1}'s Turn` : `${player2}'s Turn`}
      </Text>

      <View style={styles.board}>
        {board.map((cell, index) => (
          <Cell key={index} value={cell} onPress={() => handlePress(index)} />
        ))}
      </View>

      {winner === 'X' && <Text style={styles.title}>{player1} Wins!</Text>}
      {winner === 'O' && <Text style={styles.title}>{player2} Wins!</Text>}
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
    textAlign: 'center',
  },
  board: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    width: 246,
    marginTop: 20,
  },
  red: {
    color: 'red',
    fontWeight: 'bold',
    fontSize: 26,
    textAlign: 'center',
  },
  blue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 26,
    textAlign: 'center',
  },
});
```

Notice `GameScreen` is now considerably shorter than it was at the end of
Stage 9 — not because it does less, but because the tile's own styling and
markup now live in `Cell.tsx`, and the winner-checking logic now lives in
`gameLogic.ts`. `GameScreen`'s job has narrowed down to exactly one thing:
coordinating the board's state and reacting to taps.

## ❖ Debugging: Relative Import Paths

!!! bug "Common Bug Spotlight"
    **Symptom:** after moving files into folders, an import that used to
    work suddenly can't find its target — "Unable to resolve module" or
    similar.

    **Cause:** relative import paths (`./`, `../`) are relative to the file
    *doing* the importing, not to the project root. A file that used to sit
    directly in the project root, importing something also in the root, only
    needed `./`. Once that same file moves into `screens/`, anything it
    still needs from the root — or from a sibling folder like `components/`
    — now needs `../` to step back out first.

    **Fix:** in `GameScreen.tsx`, notice the imports are `../components/Cell`
    and `../utils/gameLogic` — one level up, then into the sibling folder —
    precisely because `GameScreen.tsx` itself now lives inside `screens/`.
    Whenever a file's location changes, its relative imports need to be
    checked, not assumed to still be correct.

## ✦ Why This Structure Matters

- **Clarity** — each file answers one question: is this a screen, a reusable
  piece of UI, or logic with no UI at all?
- **Testability** — `checkWinner` can now be tested completely on its own,
  with a plain array, no rendering involved.
- **Reusability** — `Cell` could be dropped into a different game entirely,
  without carrying any XO-specific logic along with it.
- **Growth** — adding a Scoreboard screen, in the next Stage, means adding a
  new file in `screens/`, not disturbing what already works.

## Exercise

Once the split project runs exactly as it did before — same board, same
turn logic, same winner detection — commit this checkpoint. Then: open
`Cell.tsx` on its own, without looking at `GameScreen.tsx`. Based only on
what `Cell.tsx` contains, could you tell it belongs to a Tic-Tac-Toe game
specifically? What does that tell you about how reusable it actually is?

## Reflection

Two different kinds of refactoring happened in this Stage — extracting a
helper function correctly (and seeing exactly how it goes wrong first), and
reorganising an entire project's file structure. Neither changed what the
XO Game does. Both changed how much confidence you can have that a future
change — a new feature, a bug fix, a second game reusing `Cell.tsx` — won't
require re-reading the entire program first to find where to make it.

[Stage 11](./stage-11-completion-and-scoreboard.md) builds on exactly this
structure, adding a Scoreboard screen that persists results between
sessions.

---

[^1]: React Native — Official Documentation, "Organizing Your Project." Available at: https://reactnative.dev/docs/typescript (Accessed: 9 July 2026).