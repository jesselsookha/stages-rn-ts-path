# Stage 13 — Context & Settings

## ❖ Introduction

Every screen built so far gets its data one of two ways: passed in directly
as a navigation parameter (like `player1` and `player2`), or held locally in
its own `useState`. Neither approach works well for something that needs to
be readable from *several* unrelated screens at once — a theme preference,
for instance, that both the Settings screen and the Game screen need to
know about. That's what **Context** solves.

## ✦ What Is Context?

Context lets you create a value once, near the top of your app, and read it
from any component nested inside — no matter how deep — without passing it
down manually through every screen in between. Three steps are always
involved:

1. **Create** a context.
2. **Provide** a value for it, wrapping the part of the app that needs
   access.
3. **Consume** it, in any component inside that wrapped area.

## ✧ Creating a Theme Context

```tsx
import { createContext, useContext, useState, ReactNode } from 'react';

type Theme = 'light' | 'dark';

type SettingsContextType = {
  theme: Theme;
  setTheme: (theme: Theme) => void;
};

const SettingsContext = createContext<SettingsContextType | undefined>(undefined);

export function SettingsProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>('light');

  return (
    <SettingsContext.Provider value={{ theme, setTheme }}>
      {children}
    </SettingsContext.Provider>
  );
}

export function useSettings(): SettingsContextType {
  const context = useContext(SettingsContext);
  if (context === undefined) {
    throw new Error('useSettings must be used within a SettingsProvider');
  }
  return context;
}
```

!!! note "Conceptual Note"
    `SettingsContextType` — an interface, from Stage 5 — describes exactly
    what the context provides: a `theme` value, and a way to change it.
    Typing this properly means every component consuming `useSettings()`
    gets full autocomplete and type-checking on `theme` and `setTheme`,
    rather than working with an untyped, unpredictable value.

!!! warning "Watch Out"
    Notice `useSettings()` explicitly throws an error if `context` is
    `undefined`, rather than quietly returning something unusable. This is a
    deliberate safety net — it turns "forgot to wrap the app in
    `SettingsProvider`" into an immediate, clear error message, rather than
    a confusing crash somewhere else entirely once a component tries to
    read `theme` from nothing.

## ✱ Providing Settings in App.tsx

`SettingsProvider` wraps the entire navigator, so every screen inside it —
present and future — can call `useSettings()`.

```tsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import PlayerScreen from './screens/PlayerScreen';
import GameScreen from './screens/GameScreen';
import ScoreboardScreen from './screens/ScoreboardScreen';
import SettingsScreen from './screens/SettingsScreen';
import { SettingsProvider } from './utils/SettingsContext';

type RootStackParamList = {
  Home: undefined;
  Game: { player1: string; player2: string };
  Scoreboard: undefined;
  Settings: undefined;
};

const Stack = createNativeStackNavigator<RootStackParamList>();

export default function App() {
  return (
    <SettingsProvider>
      <NavigationContainer>
        <Stack.Navigator initialRouteName="Home">
          <Stack.Screen name="Home" component={PlayerScreen} />
          <Stack.Screen name="Game" component={GameScreen} />
          <Stack.Screen name="Scoreboard" component={ScoreboardScreen} />
          <Stack.Screen name="Settings" component={SettingsScreen} />
        </Stack.Navigator>
      </NavigationContainer>
    </SettingsProvider>
  );
}
```

## ❖ `screens/SettingsScreen.tsx`

```tsx
import { View, Text, Button, StyleSheet } from 'react-native';
import { useSettings } from '../utils/SettingsContext';

export default function SettingsScreen() {
  const { theme, setTheme } = useSettings();

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Settings</Text>
      <Text>Current Theme: {theme}</Text>
      <Button
        title="Toggle Theme"
        onPress={() => setTheme(theme === 'light' ? 'dark' : 'light')}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 16, backgroundColor: '#fff' },
  title: { fontSize: 24, fontWeight: 'bold', marginBottom: 12 },
});
```

## ✧ Proving the Point: Using Theme in GameScreen Too

A setting that only the Settings screen can see isn't really solving prop
drilling — it's just state, with extra steps. The real value of Context
shows once a *different*, unrelated screen reads the same value, with no
prop passed between them at all:

```tsx
import { useSettings } from '../utils/SettingsContext';

export default function GameScreen({ route, navigation }: { route: any; navigation: any }) {
  const { theme } = useSettings();
  // ...existing GameScreen code, unchanged...

  return (
    <View style={[styles.container, theme === 'dark' && styles.containerDark]}>
      {/* ...existing board, turn indicator, and buttons... */}
    </View>
  );
}
```

```tsx
// Added to GameScreen's styles
containerDark: {
  backgroundColor: '#1a1a1a',
},
```

`GameScreen` never received `theme` as a prop, and `PlayerScreen` never
passed it along either — `GameScreen` simply asked `useSettings()` for it
directly, the same way `SettingsScreen` did.

## ✦ Debugging Context

!!! bug "Common Bug Spotlight"
    **Symptom:** "useSettings must be used within a SettingsProvider" — the
    error message built into `useSettings()` above.

    **Cause:** a component calling `useSettings()` sits *outside*
    `<SettingsProvider>` in the component tree — for example, if
    `SettingsProvider` only wrapped `<Stack.Navigator>` instead of the whole
    `NavigationContainer`, or if a screen were rendered through some other
    path that bypassed it.

    **Fix:** check exactly where `SettingsProvider` wraps the app in
    `App.tsx`, and make sure every screen that needs `useSettings()` is
    genuinely nested inside it.

## Exercise

Once toggling the theme in Settings visibly changes `GameScreen`'s
background too, commit this checkpoint. Then: `PlayerScreen` doesn't
currently read `theme` at all. Try adding the same conditional style there,
and confirm you didn't need to touch `App.tsx`, `GameScreen.tsx`, or
navigation at all to do it.

## Reflection

Context solves a specific problem: a value that many, unrelated parts of an
app need, without forcing every screen in between to know about it and pass
it along. It's not a replacement for `useState` everywhere — most of what
this game tracks (the board, whose turn it is) still belongs exactly where
it already lives, local to `GameScreen`. Context earns its place only for
things genuinely global to the app, like this one setting.

[Stage 14](./stage-14-data-persistence-and-networking.md) goes deeper into
AsyncStorage, and introduces networking — fetching data from outside the
app entirely.

---

[^1]: React — Official Documentation, "Passing Data Deeply with Context." Available at: https://react.dev/learn/passing-data-deeply-with-context (Accessed: 9 July 2026).