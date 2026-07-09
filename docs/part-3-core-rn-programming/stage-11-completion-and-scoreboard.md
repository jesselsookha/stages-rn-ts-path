# Stage 11 — Completion & Scoreboard

## ❖ Introduction

The official Manual's coverage of this kind of project ends around Stage 10 — but
the game itself isn't finished yet. Two loose ends were deliberately left
open: Stage 8's exercise asked what happens when all nine tiles fill and
nobody wins, and nothing yet remembers a result once a game ends. This Stage
closes both.

Two new tools are introduced here to make that possible — **AsyncStorage**,
for saving data that survives after the app closes, and **`useEffect`**, for
running code automatically when a screen appears. Both get a proper, deeper
look in Part IV — this Stage uses them practically first, the same way
`useState` was used from Stage 6 onward, long before Stage 12 explained
hooks in depth.

## ✦ Closing the Loop: Detecting a Draw

`gameLogic.ts` already answers "who won?" — it doesn't yet answer "is the
board full?" A draw is exactly that: no winner, and no empty tiles left.

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

export function isDraw(board: (string | null)[]): boolean {
  return checkWinner(board) === null && board.every((cell) => cell !== null);
}
```

`isDraw` deliberately checks `checkWinner(board) === null` first — a full
board with a winning line on it isn't a draw, it's a win that happened to
also fill the last tile.

## ✧ AsyncStorage: Saving Data Between Sessions

Everything built so far lives only in memory — close the app, and it's gone.
**AsyncStorage** is React Native's simple, built-in way to store small
amounts of data that survive between sessions, as key-value pairs.

```bash
npx expo install @react-native-async-storage/async-storage
```

```typescript
import AsyncStorage from '@react-native-async-storage/async-storage';

await AsyncStorage.setItem('scores', JSON.stringify(scores));
const saved = await AsyncStorage.getItem('scores');
const scores = saved ? JSON.parse(saved) : [];
```

!!! note "Conceptual Note"
    AsyncStorage only stores strings. Anything more structured — like an
    array of results — needs `JSON.stringify()` before saving, and
    `JSON.parse()` after reading it back.

## ✱ useEffect: Running Code When a Screen Appears

Reading saved scores back needs to happen automatically, the moment the
Scoreboard screen opens — not in response to a button press. That's what
`useEffect` is for: running code when a component first appears on screen.

```typescript
import { useEffect } from 'react';

useEffect(() => {
  loadScores();
}, []);
```

The empty array `[]` at the end matters — it tells React to run this code
**once**, when the screen first mounts, rather than after every re-render.
[Stage 12](../part-4-expanding-core-concepts/stage-12-state-and-effects.md)
explains exactly why that array is needed, and what happens without it.

## ❖ FlatList: Displaying the Saved Scores

`FlatList` renders a list of data efficiently — only the items currently
visible on screen are actually rendered, which matters once a list grows
long.

```tsx
<FlatList
  data={scores}
  renderItem={({ item }) => <Text>{item}</Text>}
  keyExtractor={(item, index) => index.toString()}
/>
```

## ❖ XO Game Example: Completing the Game

### `utils/gameLogic.ts`

Shown in full above — `checkWinner` unchanged from Stage 10, `isDraw` newly
added.

### `screens/GameScreen.tsx`

```tsx
import { useState } from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';
import Cell from '../components/Cell';
import { checkWinner, isDraw } from '../utils/gameLogic';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function GameScreen({ route, navigation }: { route: any; navigation: any }) {
  const { player1, player2 } = route.params;
  const [board, setBoard] = useState<(string | null)[]>(Array(9).fill(null));
  const [player1Turn, setPlayer1Turn] = useState<boolean>(true);

  const winner = checkWinner(board);
  const draw = isDraw(board);

  const saveResult = async (result: string) => {
    const existing = await AsyncStorage.getItem('scores');
    const scores = existing ? JSON.parse(existing) : [];
    scores.push(result);
    await AsyncStorage.setItem('scores', JSON.stringify(scores));
  };

  const handlePress = (index: number) => {
    if (winner !== null || draw) return;
    if (board[index] !== null) return;

    const newBoard = [...board];
    newBoard[index] = player1Turn ? 'X' : 'O';
    setBoard(newBoard);
    setPlayer1Turn(!player1Turn);

    // Check the freshly updated board, not the old one still in state
    const result = checkWinner(newBoard);
    if (result !== null) {
      const winnerName = result === 'X' ? player1 : player2;
      saveResult(`${winnerName} won`);
    } else if (isDraw(newBoard)) {
      saveResult(`${player1} vs ${player2} — Draw`);
    }
  };

  const handlePlayAgain = () => {
    setBoard(Array(9).fill(null));
    setPlayer1Turn(true);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{player1} vs. {player2}</Text>

      {winner === null && !draw && (
        <Text style={player1Turn ? styles.red : styles.blue}>
          {player1Turn ? `${player1}'s Turn` : `${player2}'s Turn`}
        </Text>
      )}
      {winner === 'X' && <Text style={styles.green}>{player1} Wins!</Text>}
      {winner === 'O' && <Text style={styles.green}>{player2} Wins!</Text>}
      {draw && <Text style={styles.green}>It's a Draw!</Text>}

      <View style={styles.board}>
        {board.map((cell, index) => (
          <Cell key={index} value={cell} onPress={() => handlePress(index)} />
        ))}
      </View>

      {(winner !== null || draw) && (
        <Button title="Play Again" onPress={handlePlayAgain} />
      )}

      <Button title="View Scoreboard" onPress={() => navigation.navigate('Scoreboard')} />
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
    marginBottom: 16,
  },
  red: { color: 'red', fontWeight: 'bold', fontSize: 26, textAlign: 'center' },
  blue: { color: 'blue', fontWeight: 'bold', fontSize: 26, textAlign: 'center' },
  green: { color: 'green', fontWeight: 'bold', fontSize: 26, textAlign: 'center' },
});
```

!!! note "Conceptual Note"
    `winner` and `draw` are still computed fresh from `board` on every
    render, exactly as in Stage 10 — no separate state variable tracks
    "has someone won yet." Inside `handlePress`, though, `checkWinner` is
    called a second time, directly against `newBoard`, purely to decide
    *whether to save a result*. That distinction matters: the display always
    reflects the current board; the save only needs to happen once, at the
    exact moment a game ends.

### `screens/ScoreboardScreen.tsx`

```tsx
import { useState, useEffect } from 'react';
import { View, Text, FlatList, StyleSheet } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function ScoreboardScreen() {
  const [scores, setScores] = useState<string[]>([]);

  useEffect(() => {
    const loadScores = async () => {
      const existing = await AsyncStorage.getItem('scores');
      if (existing) {
        setScores(JSON.parse(existing));
      }
    };
    loadScores();
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Scoreboard</Text>
      <FlatList
        data={scores}
        renderItem={({ item }) => <Text style={styles.item}>{item}</Text>}
        keyExtractor={(item, index) => index.toString()}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 16, backgroundColor: '#fff' },
  title: { fontSize: 24, fontWeight: 'bold', marginBottom: 12 },
  item: { fontSize: 18, marginVertical: 4 },
});
```

### `App.tsx`

```tsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import PlayerScreen from './screens/PlayerScreen';
import GameScreen from './screens/GameScreen';
import ScoreboardScreen from './screens/ScoreboardScreen';

type RootStackParamList = {
  Home: undefined;
  Game: { player1: string; player2: string };
  Scoreboard: undefined;
};

const Stack = createNativeStackNavigator<RootStackParamList>();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={PlayerScreen} />
        <Stack.Screen name="Game" component={GameScreen} />
        <Stack.Screen name="Scoreboard" component={ScoreboardScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

## ✧ Debugging Side Effects

!!! bug "Common Bug Spotlight"
    **Symptom:** the Scoreboard screen freezes, or the app becomes
    unresponsive shortly after opening it.

    **Cause:** if `useEffect`'s dependency array (`[]`) were left out
    entirely, `loadScores()` would run after **every** render — and since
    `setScores` inside it triggers a re-render, that re-render triggers
    `useEffect` again, which calls `setScores` again, forever.

    **Fix:** the `[]` at the end of `useEffect(() => { ... }, [])` tells
    React "run this once, and don't re-run it based on anything changing."
    Leaving it out entirely, or getting its contents wrong, is one of the
    most common `useEffect` mistakes — worth remembering here, ahead of
    Stage 12 covering it properly.

## Exercise

Once wins, draws, the scoreboard, and Play Again are all working, commit
this checkpoint — it's a meaningful one, since the game is now genuinely
complete. Then, open the Scoreboard screen a few times across a few games,
and check: does it correctly show every result in order, including draws?

## Reflection

This is the natural end of the storyline the official Manual sets out —
a complete, playable XO Game, with results that persist. It's not, however,
the end of these notes. Part IV picks apart *why* `useEffect` and
AsyncStorage behave the way they do, Part V polishes how the game looks and
feels, and Part VI extends the game further still.

Before [Stage 12](../part-4-expanding-core-concepts/stage-12-state-and-effects.md):
`saveResult` inside `handlePress` isn't wrapped in a `useEffect` — it runs
directly, immediately after `setBoard`. Given what `useEffect` is described
as doing above, why might that be the right call here, rather than something
`useEffect` should be handling instead?

---

[^1]: React Native — Official Documentation, "Async Storage." Available at: https://reactnative.dev/docs/asyncstorage (Accessed: 9 July 2026).
[^2]: React Native — Official Documentation, "Using a ScrollView" and "FlatList." Available at: https://reactnative.dev/docs/flatlist (Accessed: 9 July 2026).