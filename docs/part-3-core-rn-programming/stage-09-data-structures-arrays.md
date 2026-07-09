# Stage 9 — Data Structures (Arrays)

## ❖ Introduction

Stage 8 gave the XO Game nine working tiles — but at the cost of nine
separate state variables, and logic that had to be written out nine times to
match. This Stage picks that repetition up directly, and replaces it with a
single **array**.

Before refactoring anything, though, it's worth being honest about *why*
Stage 8 was built the repetitive way in the first place — since that reason
is exactly what tells us this is the right moment to change it, not the last.

## ✦ Why the Repetition in Stage 8 Was Intentional

When `block1` through `block9` were introduced, the goal wasn't efficiency —
it was **visibility**. Wiring up a single tile, watching it work, then
copying that same pattern eight more times, meant you could see *exactly*
what each tile needed: a value to hold, a way to display it, and a guard
against playing it twice. Nothing about that logic was hidden behind a loop
you'd have to trust without seeing it work first.

That visibility mattered *then*. It stops mattering now, for a simple
reason: you've already seen it work, nine times over. An array doesn't
change what each tile does — it changes how many times we have to *write*
what each tile does. Refactoring toward arrays now isn't about tidiness for
its own sake; it's that the repetition has already done its job of teaching
the pattern, and continuing to repeat it from here on would only be
copying, not learning.

!!! note "Conceptual Note"
    This is a useful habit to notice in your own work, not just this course:
    write the repetitive, obvious version first, when you're still figuring
    out *what* the pattern actually is. Refactor toward something more
    general only once that pattern is genuinely understood — refactoring
    too early risks generalising something you don't yet fully understand
    the shape of.

## ✧ From Nine Variables to One Array

The shift itself is simple to state: instead of

```typescript
const [block1, setBlock1] = useState<number>(0);
const [block2, setBlock2] = useState<number>(0);
// ...seven more of these
```

we declare one array, holding all nine values:

```typescript
const [board, setBoard] = useState<(string | null)[]>(Array(9).fill(null));
```

Here, each element is either `null` (not yet played), `'X'`, or `'O'` —
directly readable, rather than the `0` / `1` / `2` code we used before.
`board[0]` takes the place of `block1`, `board[1]` takes the place of
`block2`, and so on — the tile at position `i` in the array is exactly the
tile you'd have called `block(i + 1)` in Stage 8.

!!! warning "Watch Out"
    Notice `useState` here holds the *whole array* as one piece of state,
    not nine separate ones. That has a real consequence: to change one tile,
    you can't modify `board` directly — you need to create a **new** array
    with that one change, and hand the new array to `setBoard`. We'll see
    exactly why in the Debugging section below.

## ✱ Rendering the Board with a Loop

With one array instead of nine variables, the nine near-identical
`TouchableHighlight` tiles from Stage 8 can be generated with a single
`.map()` call, rather than written out by hand:

```tsx
<View style={styles.board}>
  {board.map((cell, index) => (
    <TouchableHighlight
      key={index}
      style={styles.cell}
      onPress={() => handlePress(index)}
    >
      <Text style={styles.cellText}>{cell}</Text>
    </TouchableHighlight>
  ))}
</View>
```

!!! bug "Common Bug Spotlight"
    **Symptom:** React logs a warning about "each child in a list should
    have a unique key prop."

    **Cause:** whenever you render a list of components from an array
    using `.map()`, React needs a way to tell each rendered item apart, so
    it can update the right one efficiently when the array changes.

    **Fix:** the `key={index}` prop above satisfies this. For a fixed board
    like this one, the tile's position genuinely never changes, so using
    its index as the key is safe — for lists where items can be reordered
    or removed, using the array index as a key can cause its own subtle
    bugs, which you'll want to be aware of if you use `.map()` elsewhere.

## ❖ XO Game Example: The Refactored Board

Here's the complete file — the array replacing all nine `block` variables,
the board rendered with `.map()`, and winner detection carried forward from
Stage 8, adapted to work against the array.

```tsx
import { useState } from 'react';
import {
  View,
  Text,
  TextInput,
  Button,
  TouchableHighlight,
  StyleSheet,
} from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

type RootStackParamList = {
  Home: undefined;
  Game: { player1: string; player2: string };
};

const Stack = createNativeStackNavigator<RootStackParamList>();

function PlayerScreen({ navigation }: { navigation: any }) {
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

// Every possible winning line, as triples of board indices
const WINNING_LINES: number[][] = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8], // rows
  [0, 3, 6], [1, 4, 7], [2, 5, 8], // columns
  [0, 4, 8], [2, 4, 6],           // diagonals
];

function GameScreen({ route }: { route: any }) {
  const { player1, player2 } = route.params;
  const [board, setBoard] = useState<(string | null)[]>(Array(9).fill(null));
  const [player1Turn, setPlayer1Turn] = useState<boolean>(true);

  // Checks every winning line against the current board
  let winner: string | null = null;
  for (const line of WINNING_LINES) {
    const [a, b, c] = line;
    if (board[a] !== null && board[a] === board[b] && board[b] === board[c]) {
      winner = board[a];
    }
  }

  const handlePress = (index: number) => {
    if (winner !== null) {
      return; // game already has a winner — stop all further play
    }
    if (board[index] !== null) {
      return; // tile already played
    }

    const newBoard = [...board]; // copy, rather than modify board directly
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
          <TouchableHighlight
            key={index}
            style={styles.cell}
            onPress={() => handlePress(index)}
          >
            <Text style={styles.cellText}>{cell}</Text>
          </TouchableHighlight>
        ))}
      </View>

      {winner === 'X' && <Text style={styles.title}>{player1} Wins!</Text>}
      {winner === 'O' && <Text style={styles.title}>{player2} Wins!</Text>}
    </View>
  );
}

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
  input: {
    borderWidth: 1,
    borderColor: '#ccc',
    paddingHorizontal: 12,
    paddingVertical: 8,
    marginVertical: 8,
    width: '80%',
  },
  board: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    width: 246,
    marginTop: 20,
  },
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

## ✦ Code Breakdown

| Stage 8 (nine variables) | Stage 9 (one array) |
|---|---|
| `block1` … `block9`, nine `useState` calls | `board`, one `useState` call holding an array |
| Nine copies of a `switch` statement | `{cell}` — the array's own value, rendered directly |
| Nine copies of a `TouchableHighlight` in JSX | One `TouchableHighlight`, generated by `board.map()` |
| Eight separate `if` checks for winning lines | `WINNING_LINES`, one array, checked with a single loop |

Notice the winning-line check followed the exact same idea as the board
itself: eight near-identical `if` statements became one array of triples,
walked through with a loop — the same refactor, applied a second time, to a
different piece of repetition.

## ✧ Debugging Array State

!!! bug "Common Bug Spotlight"
    **Symptom:** you call `setBoard(...)`, but the screen doesn't update —
    or updates the wrong tile.

    **Likely cause:** modifying `board` directly, for example
    `board[index] = 'X'; setBoard(board);` — this changes the *same* array
    object that was already stored in state. React compares the old and new
    state to decide whether to re-render, and if it's literally the same
    array reference, React may not notice anything changed.

    **Fix:** always create a new array first — `const newBoard = [...board];`
    — then modify the copy, and pass the copy to `setBoard`. This is exactly
    what `handlePress` does above, and it's worth treating as a rule for any
    array or object held in state, not just this board.

## Exercise

Once the refactored board is working — same behaviour as Stage 8's version,
now driven by a single array — commit this checkpoint. Then consider: with
the board as an array, what would it actually take to support a 4×4 board
instead of 3×3? Compare your answer here to what you noted at the end of
Stage 8 — how much smaller is the list of things you'd need to change?

## Reflection

The array refactor didn't change what the XO Game does — a player still
taps a tile, still sees X or O appear, still eventually wins or draws. What
changed is how much of the program has to be rewritten every time something
about the board's *size* or *shape* changes. That's the real payoff of
choosing the right data structure: not less code today, necessarily, but far
less code tomorrow.

[Stage 10](./stage-10-refactoring-and-structure.md) picks up a different
kind of repetition — not in the data, but in the file itself, which has been
growing steadily since Stage 6.

---

[^1]: TypeScript — Official Handbook, "Arrays." Available at: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#arrays (Accessed: 2026).