# Stage 8 — Decisions & Conditional Rendering

## ❖ Introduction

You've already met `if..else`, ternary expressions, and `switch` back in
Stage 4. This Stage asks the same question in a new setting: what happens
when a decision doesn't just print something to a console, but decides
**what appears on screen**? That's what **conditional rendering** means —
and it's exactly what the XO Game needs next, to show whose turn it is, and
eventually, which player has claimed which tile.

## ✦ What Is Conditional Rendering?

In pseudocode, you'd write:

```text
if player1Turn then
    output "Player 1's Turn"
else
    output "Player 2's Turn"
endif
```

In React Native, the same decision can sit directly inside JSX, using a
ternary:

```tsx
<Text>
  {player1Turn ? "Player 1's Turn" : "Player 2's Turn"}
</Text>
```

The condition `player1Turn` decides which string is displayed — the ternary
operator you already know from Stage 4, just placed inside `{}` so JSX can
evaluate it.

## ✧ Using if..else Before Returning JSX

For more complex conditions, it's often clearer to decide *before* the
`return` statement, rather than inside the JSX itself:

```tsx
let turnMessage;
if (player1Turn) {
  turnMessage = <Text style={styles.red}>Player 1's Turn</Text>;
} else {
  turnMessage = <Text style={styles.blue}>Player 2's Turn</Text>;
}

return (
  <View>
    {turnMessage}
  </View>
);
```

`turnMessage` here holds an entire JSX element, decided once, before
rendering. This approach is useful once the styles or components involved
differ significantly between branches.

## ✱ Shorthand Conditional Rendering

For simpler cases, the ternary can sit directly inline:

```tsx
<Text style={player1Turn ? styles.red : styles.blue}>
  {player1Turn ? "Player 1's Turn" : "Player 2's Turn"}
</Text>
```

Both the style and the text change together, based on a single condition —
concise, though only worth reaching for once a condition is genuinely simple.

## ❖ XO Game Example: Building the Game Screen

With the theory in hand, let's build the actual game board — nine tiles,
laid out in three rows, using `<Text>` for each tile rather than an image.

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

function GameScreen({ route }: { route: any }) {
  const { player1, player2 } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{player1} vs. {player2}</Text>

      {/* Board layout: 3 rows of 3 cells, each an empty tile for now */}
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
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
  },
  input: {
    borderWidth: 1,
    borderColor: '#ccc',
    paddingHorizontal: 12,
    paddingVertical: 8,
    marginVertical: 8,
    width: '80%',
  },
  row: {
    flexDirection: 'row',
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
});
```

Nine tiles, laid out and styled, but not yet interactive. That's the next
step.

## ✧ XO Game Example: Showing Whose Turn It Is

Before worrying about what happens *inside* each tile, let's get the turn
indicator working — a direct application of the conditional rendering
covered above.

```tsx
function GameScreen({ route }: { route: any }) {
  const { player1, player2 } = route.params;
  const [player1Turn, setPlayer1Turn] = useState<boolean>(true);

  const handlePress = (index: number) => {
    console.log(`Cell ${index} pressed`);
    setPlayer1Turn(!player1Turn);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{player1} vs. {player2}</Text>

      <Text style={player1Turn ? styles.red : styles.blue}>
        {player1Turn ? `${player1}'s Turn` : `${player2}'s Turn`}
      </Text>

      <View style={styles.row}>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(0)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(1)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(2)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(3)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(4)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(5)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(6)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(7)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handlePress(8)}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
    </View>
  );
}
```

*(`PlayerScreen`, `App`, and `styles` are unchanged from the previous
checkpoint — only `GameScreen` is shown here onward, though every checkpoint
remains a complete, working file when combined with them.)*

Pressing any tile now toggles the turn indicator, and logs which cell was
pressed. Notice, though: nothing yet remembers *what* was pressed — that's
the next problem.

## ❖ XO Game Example: Which Block? Which Player? (if..else)

To remember what's inside each tile, we need one state variable per tile.
For now, let's wire up just the **first** tile, using an `if..else`, to see
the pattern clearly before repeating it eight more times.

```tsx
function GameScreen({ route }: { route: any }) {
  const { player1, player2 } = route.params;
  const [player1Turn, setPlayer1Turn] = useState<boolean>(true);

  // Each block holds one of three values:
  // 0 - not yet played | 1 - Player 1 played here | 2 - Player 2 played here
  const [block1, setBlock1] = useState<number>(0);
  const [block2, setBlock2] = useState<number>(0);
  const [block3, setBlock3] = useState<number>(0);
  const [block4, setBlock4] = useState<number>(0);
  const [block5, setBlock5] = useState<number>(0);
  const [block6, setBlock6] = useState<number>(0);
  const [block7, setBlock7] = useState<number>(0);
  const [block8, setBlock8] = useState<number>(0);
  const [block9, setBlock9] = useState<number>(0);

  // Decide what block1's tile should display, based on its current value
  let block1Content: string;
  if (block1 === 0) {
    block1Content = '';
  } else if (block1 === 1) {
    block1Content = 'X';
  } else {
    block1Content = 'O';
  }

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{player1} vs. {player2}</Text>
      <Text style={player1Turn ? styles.red : styles.blue}>
        {player1Turn ? `${player1}'s Turn` : `${player2}'s Turn`}
      </Text>

      <View style={styles.row}>
        <TouchableHighlight
          style={styles.cell}
          onPress={() => {
            if (player1Turn) {
              setBlock1(1);
            } else {
              setBlock1(2);
            }
            setPlayer1Turn(!player1Turn);
          }}
        >
          <Text style={styles.cellText}>{block1Content}</Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell}>
          <Text style={styles.cellText}></Text>
        </TouchableHighlight>
      </View>
    </View>
  );
}
```

!!! note "Conceptual Note"
    Only `block1` is wired up here, deliberately — the goal right now is to
    see *one* tile work correctly, before repeating the same pattern eight
    more times. Tapping the first tile now correctly shows `X` or `O`
    depending on whose turn it was.

## ✧ XO Game Example: Refactoring to a switch Statement

An `if..else if..else` chain works, but a `switch` reads more clearly once
you're comparing one value against a small, fixed set of possibilities —
exactly the case here, since `block1` can only ever be `0`, `1`, or `2`.

```tsx
let block1Content: string;
switch (block1) {
  case 0:
    block1Content = '';
    break;
  case 1:
    block1Content = 'X';
    break;
  case 2:
    block1Content = 'O';
    break;
  default:
    block1Content = '';
}
```

Everything else about this checkpoint is identical to the previous one — the
`if..else` block is simply replaced by this `switch`. The tile still behaves
exactly the same way; only the decision syntax has changed.

## ✱ XO Game Example: Blocking Repeated Clicks

Right now, tapping an already-played tile still fires `onPress` — it would
happily let a player overwrite an existing mark. A guard clause fixes this:

```tsx
<TouchableHighlight
  style={styles.cell}
  onPress={() => {
    if (block1 !== 0) {
      return; // tile already played — do nothing
    }
    if (player1Turn) {
      setBlock1(1);
    } else {
      setBlock1(2);
    }
    setPlayer1Turn(!player1Turn);
  }}
>
  <Text style={styles.cellText}>{block1Content}</Text>
</TouchableHighlight>
```

The `if (block1 !== 0) { return; }` line exits the function immediately if
the tile is already taken, before any state changes happen.

## ❖ XO Game Example: All Nine Tiles, and Detecting a Winner

Now we repeat the same `switch` and guard-clause pattern for every tile, and
add the logic to detect a winner. This is the full file, all nine tiles
wired up:

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

function GameScreen({ route }: { route: any }) {
  const { player1, player2 } = route.params;
  const [player1Turn, setPlayer1Turn] = useState<boolean>(true);

  // 0 - not yet played | 1 - Player 1 played here | 2 - Player 2 played here
  const [block1, setBlock1] = useState<number>(0);
  const [block2, setBlock2] = useState<number>(0);
  const [block3, setBlock3] = useState<number>(0);
  const [block4, setBlock4] = useState<number>(0);
  const [block5, setBlock5] = useState<number>(0);
  const [block6, setBlock6] = useState<number>(0);
  const [block7, setBlock7] = useState<number>(0);
  const [block8, setBlock8] = useState<number>(0);
  const [block9, setBlock9] = useState<number>(0);

  const contentFor = (block: number): string => {
    switch (block) {
      case 1:
        return 'X';
      case 2:
        return 'O';
      default:
        return '';
    }
  };

  // Checks all 8 possible winning lines: 3 rows, 3 columns, 2 diagonals
  let winner: number = 0;
  if (block1 > 0 && block1 === block2 && block2 === block3) winner = block1;
  if (block4 > 0 && block4 === block5 && block5 === block6) winner = block4;
  if (block7 > 0 && block7 === block8 && block8 === block9) winner = block7;
  if (block1 > 0 && block1 === block4 && block4 === block7) winner = block1;
  if (block2 > 0 && block2 === block5 && block5 === block8) winner = block2;
  if (block3 > 0 && block3 === block6 && block6 === block9) winner = block3;
  if (block1 > 0 && block1 === block5 && block5 === block9) winner = block1;
  if (block3 > 0 && block3 === block5 && block5 === block7) winner = block3;

  // A reusable press handler, parameterised per tile
  const handleTilePress = (
    block: number,
    setBlock: (value: number) => void
  ) => {
    if (winner !== 0) {
      return; // game already has a winner — stop all further play
    }
    if (block !== 0) {
      return; // tile already played
    }
    if (player1Turn) {
      setBlock(1);
    } else {
      setBlock(2);
    }
    setPlayer1Turn(!player1Turn);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{player1} vs. {player2}</Text>
      <Text style={player1Turn ? styles.red : styles.blue}>
        {player1Turn ? `${player1}'s Turn` : `${player2}'s Turn`}
      </Text>

      <View style={styles.row}>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block1, setBlock1)}>
          <Text style={styles.cellText}>{contentFor(block1)}</Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block2, setBlock2)}>
          <Text style={styles.cellText}>{contentFor(block2)}</Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block3, setBlock3)}>
          <Text style={styles.cellText}>{contentFor(block3)}</Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block4, setBlock4)}>
          <Text style={styles.cellText}>{contentFor(block4)}</Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block5, setBlock5)}>
          <Text style={styles.cellText}>{contentFor(block5)}</Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block6, setBlock6)}>
          <Text style={styles.cellText}>{contentFor(block6)}</Text>
        </TouchableHighlight>
      </View>
      <View style={styles.row}>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block7, setBlock7)}>
          <Text style={styles.cellText}>{contentFor(block7)}</Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block8, setBlock8)}>
          <Text style={styles.cellText}>{contentFor(block8)}</Text>
        </TouchableHighlight>
        <TouchableHighlight style={styles.cell} onPress={() => handleTilePress(block9, setBlock9)}>
          <Text style={styles.cellText}>{contentFor(block9)}</Text>
        </TouchableHighlight>
      </View>

      {winner === 1 && <Text style={styles.title}>{player1} Wins!</Text>}
      {winner === 2 && <Text style={styles.title}>{player2} Wins!</Text>}
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
  row: {
    flexDirection: 'row',
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

A few things worth pausing on here, since this checkpoint answers the
question this Stage set out to ask:

- **`contentFor(block)`** replaces nine separate `switch` blocks (one per
  tile) with a single small function, called nine times — the `switch`
  logic itself hasn't changed, only how many times it's written out.
- **`handleTilePress(block, setBlock)`** does the same for the `onPress`
  logic — one function, parameterised, instead of nine near-identical copies.
- **The winner check comes *before* the tile-blocking check**, inside
  `handleTilePress`. That ordering is what actually stops the game once
  someone has won: `if (winner !== 0) { return; }` runs before anything else
  in the handler, so once `winner` is no longer `0`, every tile's `onPress`
  exits immediately, regardless of whether that specific tile is empty.

!!! note "Conceptual Note"
    Nine state variables, and nine near-identical tiles, is exactly the kind
    of repetition that becomes hard to manage as a program grows — even with
    `contentFor` and `handleTilePress` reducing *some* of the duplication.
    [Stage 9](./stage-09-data-structures-arrays.md) picks this exact
    problem up, replacing all nine `block` variables with a single array.

## ✦ Debugging Conditional Rendering

!!! bug "Common Bug Spotlight"
    **Symptom:** the winner text appears, but players can still tap tiles
    and the board keeps changing afterward.

    **Likely cause:** the `winner !== 0` check was added somewhere in the
    render logic (for example, only around the winner `<Text>`), but not
    inside `handleTilePress` itself.

    **Fix:** the check has to live at the very start of whatever function
    actually changes state — in this case, `handleTilePress` — not just in
    what gets displayed. Conditional *rendering* controls what's shown;
    it does nothing to stop logic from still running underneath it.

!!! warning "Watch Out"
    Notice every comparison above uses `===`, never `==` — consistent with
    the habit introduced back in Stage 3. `block1 == block2` and
    `block1 === block2` would behave the same here, since both sides are
    always numbers, but the habit is worth keeping regardless.

## Exercise

Once your board correctly stops after a win, commit this checkpoint. Then,
before moving on: what happens right now if all nine tiles fill up and
**nobody** wins? Try it — play out a draw by hand. Nothing currently tells
the players the game has ended. Keep that gap in mind; we won't fix it in
this Stage, but it's worth noticing before Stage 9 restructures this code.

## Reflection

Every tile in this board behaves identically — same three possible values,
same switch logic, same guard clauses — yet we've written that same logic
nine separate times. That repetition wasn't a mistake; it let us see the
*decision* clearly, once, before it was worth generalising.

Before [Stage 9](./stage-09-data-structures-arrays.md): looking at
`block1` through `block9` above, what would you need to change if the board
grew from 3×3 to 4×4? Count how many places in this file that change would
touch.

---

[^1]: React Native — Official Documentation, "Handling Text Input" and conditional rendering patterns. Available at: https://reactnative.dev/docs/handling-text-input (Accessed: 2026).