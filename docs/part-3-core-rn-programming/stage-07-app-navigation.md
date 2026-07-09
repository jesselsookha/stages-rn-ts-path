# Stage 7 — App Navigation

## ❖ Introduction

Most apps aren't limited to a single screen. Users expect to move between
different views — and for the XO Game, that means moving from the **Player
Setup screen** you built in Stage 6, to an actual **Game screen**.

!!! note "Conceptual Note"
    Everything so far — and everything for the next few Stages — lives inside
    a single `App.tsx` file, on purpose. This is deliberate: it lets you see
    an entire, working program take shape in one place, without the added
    complexity of managing multiple files at the same time. It is **not**,
    however, how a real project should stay organised long-term. Once the
    game itself is complete, [Stage 10 — Refactoring & Structure](./stage-10-refactoring-and-structure.md)
    revisits this exact code and splits it sensibly across files. For now,
    focus on the logic — structure comes later.

In React Native, screen-to-screen navigation is handled by the **React
Navigation** library.[^1] This Stage focuses on the **Native Stack
Navigator** — the right tool for the XO Game, where one screen simply hands
off to the next.

## ✦ Navigators at a Glance

React Navigation offers several navigator types, each suited to a different
kind of app structure:

| Navigator | Typical Use | Used in the XO Game? |
|---|---|---|
| **Native Stack** | Screens stacked like cards — each new screen slides in over the last, with a back button returning to the previous one. | Yes — this is what we use. |
| **Bottom Tabs** | A row of tabs fixed to the bottom of the screen, for switching between a small number of top-level sections (common in social or shopping apps). | No |
| **Drawer** | A side menu that slides in, usually from the left, holding navigation links (common in larger apps with many sections). | No |
| **Material Top Tabs** | Swipeable tabs fixed to the top of the screen, styled after Material Design conventions. | No |

The XO Game only ever moves forward from one screen to the next — Player
Setup, then Game — which is exactly the shape a stack is built for. The other
three exist for apps with a different structure: several equally-important
sections a user jumps between, rather than one screen leading into another.
Worth knowing they exist, even though we won't use them here.

## ✧ Installing Navigation

Live reloading — which you've relied on since Stage 2 — only reflects changes
to your code. Installing a new package is different: it changes what's
available to your project at a more fundamental level, so the development
server needs to be stopped first.

In your terminal, press ++ctrl+c++ to stop `npx expo start`, then run:

```bash
npx expo install @react-navigation/native
npx expo install react-native-screens react-native-safe-area-context
npx expo install @react-navigation/native-stack
```

!!! tip "Pro-Tip"
    Notice all three use `npx expo install`, not `npm install` — as
    mentioned back in Stage 2, this matters because `expo install` checks
    which package version is actually compatible with your project's Expo
    SDK version, rather than always grabbing the newest release.

Once installation finishes, restart the server with `npx expo start`. You can
confirm the packages actually installed by opening `package.json` and looking
under `"dependencies"` — you should see `@react-navigation/native`,
`@react-navigation/native-stack`, `react-native-screens`, and
`react-native-safe-area-context` listed there.

## ✱ Understanding the Theory

- **`NavigationContainer`** — wraps the entire app, and manages the
  navigation state and linking behind the scenes.
- **`createNativeStackNavigator`** — a function that creates a stack-based
  navigation system, using native platform APIs for smooth screen
  transitions.
- **`Stack.Navigator`** — the component that holds and manages the available
  screens.
- **`Stack.Screen`** — registers a single screen, giving it a `name` and the
  `component` to render.
- **`navigation.navigate('ScreenName')`** — moves to another registered
  screen.
- **`navigation.goBack()`** — returns to the previous screen in the stack.

## ❖ XO Game Example: Player → Game Screen

### Part 1 — Getting Navigation Working

The first goal is simply to get from one screen to the other. We'll worry
about carrying the player names across in Part 2.

```tsx
import { useState } from 'react';
import {
  View,
  Text,
  TextInput,
  Button,
  StyleSheet,
} from 'react-native';

// NavigationContainer wraps the whole app and manages navigation state
import { NavigationContainer } from '@react-navigation/native';
// createNativeStackNavigator builds a stack-based navigator using native transitions
import { createNativeStackNavigator } from '@react-navigation/native-stack';

// navigation and route aren't typed yet here — Part 2 fixes that
function PlayerScreen({ navigation }: any) {
  const [player1Name, setPlayer1Name] = useState<string>('');
  const [player2Name, setPlayer2Name] = useState<string>('');

  return (
    <View style={styles.container}>
      <Text style={styles.welcomeText}>Welcome to Tic-Tac-Toe!</Text>

      <View style={styles.horizontalLayout}>
        <Text style={styles.nameQuestion}>Enter Player 1 Name:</Text>
        <TextInput
          style={styles.nameEntry}
          placeholder="Player 1 Name"
          onChangeText={(newText) => setPlayer1Name(newText)}
        />
      </View>
      <View style={styles.horizontalLayout}>
        <Text style={styles.nameQuestion}>Enter Player 2 Name:</Text>
        <TextInput
          style={styles.nameEntry}
          placeholder="Player 2 Name"
          onChangeText={(newText) => setPlayer2Name(newText)}
        />
      </View>
      <Button
        title="Start Game"
        onPress={() => {
          navigation.navigate('Game');
        }}
      />
    </View>
  );
}

function GameScreen({ navigation, route }: any) {
  return (
    <View style={styles.container}>
      <Text>Player 1 vs. Player 2</Text>
    </View>
  );
}

// Creates the stack navigator itself
const Stack = createNativeStackNavigator();

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
  welcomeText: {
    color: 'purple',
    fontWeight: 'bold',
    fontSize: 28,
    textAlign: 'center',
  },
  nameQuestion: {
    fontStyle: 'italic',
    fontSize: 16,
  },
  nameEntry: {
    padding: 5,
    backgroundColor: 'orange',
    fontSize: 16,
  },
  horizontalLayout: {
    flexDirection: 'row',
    justifyContent: 'space-evenly',
    marginTop: 30,
  },
});
```

!!! warning "Watch Out"
    `{ navigation }: any` above is a temporary shortcut, not good practice —
    `any` switches off TypeScript's checking entirely for that value. It's
    used here only to get navigation working first. Part 2 replaces it with
    a properly typed alternative.

At this point, pressing "Start Game" moves you to the Game screen — but the
names typed in aren't going anywhere yet. That's the next problem to solve.

### Part 2 — Passing Data Between Screens

```tsx
import { useState } from 'react';
import { View, Text, TextInput, Button, StyleSheet } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

// Declares every screen in the stack, and what parameters each expects
type RootStackParamList = {
  Home: undefined; // Home takes no parameters
  Game: { player1: string; player2: string }; // Game expects both player names
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
  // Reads the two parameters passed in from PlayerScreen
  const { player1, player2 } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{player1} vs. {player2}</Text>
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
});
```

## ✧ Code Breakdown

| Concept | Explanation |
|---|---|
| `RootStackParamList` | Declares every screen and the parameters each one expects, giving `navigate()` type safety. |
| `NavigationContainer` | Wraps the app and provides navigation context. |
| `createNativeStackNavigator<RootStackParamList>()` | Creates a stack navigator, now aware of `RootStackParamList`. |
| `Stack.Navigator` / `Stack.Screen` | Holds, and individually registers, each screen. |
| `navigation.navigate('Game', { player1, player2 })` | Moves to the Game screen, carrying both names along as parameters. |
| `route.params` | Reads the parameters a screen was navigated to with. |

!!! bug "Common Bug Spotlight"
    **Symptom:** TypeScript complains that `'Game'` doesn't match a known
    route, or that a required parameter is missing.

    **Likely cause:** the string passed to `navigate()`, or the shape of the
    object passed alongside it, doesn't match what `RootStackParamList`
    declares.

    **Fix:** treat `RootStackParamList` as the source of truth. If a screen
    needs a new parameter, add it there first — TypeScript will then guide
    you to every place that needs updating, rather than letting a mismatch
    slip through silently until the app is running.

## Exercise

Once Part 2 is working — names entered on the Player screen appear correctly
on the Game screen — commit this checkpoint. Then, consider: what happens
right now if `navigation.navigate('Game', {...})` is called, but the `Game`
route's declared type in `RootStackParamList` didn't include `player2`? Try
temporarily removing `player2` from the type definition and see what
TypeScript tells you before you've even run the app.

## Reflection

Navigation is the first place in this course where TypeScript's checking
extends beyond a single component — `RootStackParamList` is a contract that
every screen in the stack has to honour. Before moving to
[Stage 8](./stage-08-decisions-and-conditional-rendering.md), where the game
screen actually starts behaving like a game: what would break, right now, if
`GameScreen` tried to read a parameter that `PlayerScreen` never sent?

---

[^1]: React Navigation — Official Documentation. Available at: https://reactnavigation.org/ (Accessed: 8 July 2026).