# Stage 16 — Fonts & Styling

## ❖ Introduction

Fonts and colours aren't decoration bolted on at the end — they're part of
how a user reads, trusts, and understands an app before they've consciously
thought about any of it. This Stage covers fonts (both local files and
Google Fonts), then styling in more depth than earlier Stages have needed,
including what actually separates **UI (user interface)** from **UX (user experience)** 
— a distinction worth being precise about before diving into Flexbox again.

## ✦ Font Choices in Apps

- **Serif** — traditional, formal; common in reading-heavy apps.
- **Sans-serif** — clean, modern; the default for most mobile UI text.
- **Monospace** — technical, evenly spaced; suited to code or data-heavy
  displays.
- **Display fonts** — bold, decorative; suited to headings and branding, not
  body text.

One or two fonts is usually enough for an entire app — one for body text,
prioritising legibility, and optionally one for headings, prioritising
character. More than that tends to read as inconsistency rather than
variety.

## ✧ Integrating Local Fonts

```bash
npx expo install expo-font expo-splash-screen
```

```tsx
import { useFonts } from 'expo-font';
import * as SplashScreen from 'expo-splash-screen';
import { useEffect } from 'react';
import { Text, View } from 'react-native';

SplashScreen.preventAutoHideAsync();

export default function App() {
  const [fontsLoaded] = useFonts({
    Roboto: require('./assets/fonts/Roboto-Regular.ttf'),
    RobotoBold: require('./assets/fonts/Roboto-Bold.ttf'),
  });

  useEffect(() => {
    if (fontsLoaded) {
      SplashScreen.hideAsync();
    }
  }, [fontsLoaded]);

  if (!fontsLoaded) {
    return null;
  }

  return (
    <View>
      <Text style={{ fontFamily: 'RobotoBold', fontSize: 20 }}>
        Custom Font Example
      </Text>
    </View>
  );
}
```

!!! warning "Watch Out"
    Older tutorials use `expo-app-loading` and an `<AppLoading />`
    component to wait for fonts. That package is deprecated — the current
    approach, shown above, keeps the splash screen visible
    (`preventAutoHideAsync()`) until fonts finish loading, then hides it
    (`hideAsync()`), rather than swapping in a separate loading component.

## ❖ Using Google Fonts

Rather than downloading font files manually, Expo provides a package for
almost every font on Google Fonts, pre-packaged and ready to import.[^1]

```bash
npx expo install expo-font @expo-google-fonts/poppins
```

```tsx
import { useFonts, Poppins_400Regular, Poppins_700Bold } from '@expo-google-fonts/poppins';

export default function App() {
  const [fontsLoaded] = useFonts({
    Poppins_400Regular,
    Poppins_700Bold,
  });

  if (!fontsLoaded) {
    return null;
  }

  return (
    <Text style={{ fontFamily: 'Poppins_700Bold', fontSize: 20 }}>
      Hello, Poppins!
    </Text>
  );
}
```

!!! tip "Pro-Tip"
    Every Google Font ships as its own package (`@expo-google-fonts/poppins`,
    `@expo-google-fonts/roboto`, and so on), and each weight or style
    (`_400Regular`, `_700Bold`, `_400Regular_Italic`) is its own named
    export. Only install the specific weights you'll actually use — a
    project rarely needs every weight a typeface offers.

Choosing a font is really a small research task, not a purely aesthetic
one: a children's game calling for something rounded and playful, a finance
app calling for something neutral and precise, a reading app calling for
something quietly legible at length. Worth browsing [Google
Fonts](https://fonts.google.com) with the specific app in mind, rather than
picking whatever looks appealing in isolation.

## ✧ Styling in Depth: UI, UX, and Where Flexbox Fits

Before going further into styling techniques, it's worth being precise
about two terms that get used almost interchangeably, but mean different
things:

- **UI (User Interface)** — what the user actually sees and touches: the
  buttons, the colours, the text, the layout.
- **UX (User Experience)** — how it *feels* to use those things: whether
  the flow makes sense, whether feedback is clear, whether the app respects
  the user's time and attention.

A screen can have a polished UI and still have poor UX — buttons that look
great but are in a confusing order, for instance. Everything from here
onward is UI work in the strictest sense — code that controls what's drawn
on screen — but it's worth remembering *why* it's being done: every styling
decision either supports or undermines the experience sitting on top of it.

```text
┌────────────────────────────────────────────┐
│  UX — does this feel right to use?         │
│  ┌───────────────────────────────────────┐ │
│  │  UI — what does the user see?         │ │
│  │  ┌──────────────────────────────────┐ │ │
│  │  │  Layout — where does it sit?     │ │ │
│  │  │  (Flexbox, responsive sizing)    │ │ │
│  │  └──────────────────────────────────┘ │ │
│  └───────────────────────────────────────┘ │
└────────────────────────────────────────────┘
```

Each layer depends on the one inside it — layout decisions build the UI,
and the UI is what the UX is actually experienced through. A rough
wireframe of the Game screen makes this concrete:

```text
┌────────────────────────────────┐
│         Alice vs. Bob          │  <- title (Text, styled)
│         Alice's Turn           │  <- conditional styling (red/blue)
│                                │
│      ┌─────┬─────┬─────┐       │
│      │  X  │     │  O  │       │  <- board (Flexbox: row + wrap)
│      ├─────┼─────┼─────┤       │
│      │     │  X  │     │       │
│      ├─────┼─────┼─────┤       │
│      │  O  │     │     │       │
│      └─────┴─────┴─────┘       │
│                                │
│      [ Play Again ]            │  <- conditional render (winner/draw)
│      [ View Scoreboard ]       │
└────────────────────────────────┘
```

Every labelled piece above already exists in the XO Game — this Stage looks
at the styling techniques behind each one properly.

## ✱ Flexbox, Properly

Flexbox has been used since Stage 6, largely by example. Two properties do
almost all of the work:

- **`flexDirection`** — `'row'` or `'column'`, deciding which way children
  lay out.
- **`justifyContent`** and **`alignItems`** — positioning along the main
  axis and cross axis respectively, where "main axis" depends entirely on
  `flexDirection`.

The board itself is a good example of Flexbox solving a real layout
problem: nine fixed-size cells, wrapping into three rows automatically.

```tsx
board: {
  flexDirection: 'row',
  flexWrap: 'wrap',   // wraps to a new row once a row fills up
  width: 246,          // exactly 3 cells wide (80px + margins)
},
```

`flexWrap: 'wrap'` is what turns nine sequential cells into a 3×3 grid — the
`board` container never explicitly says "make three rows"; it just says
"wrap once you run out of horizontal space," and a fixed `width` of exactly
three cells' worth does the rest.

## ❖ Responsive Design

Fixed pixel values — `width: 246` above included — work, but only for one
screen size. **`Dimensions`** and percentage-based sizing adapt to whatever
device the app runs on.

```tsx
import { Dimensions, StyleSheet } from 'react-native';

const screenWidth = Dimensions.get('window').width;
const cellSize = (screenWidth * 0.8) / 3; // board takes up 80% of screen width

const styles = StyleSheet.create({
  cell: {
    width: cellSize,
    height: cellSize,
  },
});
```

Rather than a hardcoded `80`, `cellSize` is calculated from the actual
device's width — a tablet and a small phone each get a board sized
proportionally to their own screen, rather than a fixed size that might be
too small on one and too large on the other.

!!! note "Conceptual Note"
    `Dimensions.get('window').width` is read once, when the module loads.
    On a device that can rotate between portrait and landscape, this value
    won't update on its own — the `useWindowDimensions()` hook exists
    specifically for cases where a layout needs to react to that kind of
    change live, rather than just calculating it once.

## ✦ Conditional Styling

Conditional styling has already appeared in this course more than once —
worth naming properly now that it's had practice: applying a different
style based on some piece of state.

```tsx
<Text style={player1Turn ? styles.red : styles.blue}>
  {player1Turn ? `${player1}'s Turn` : `${player2}'s Turn`}
</Text>
```

```tsx
<View style={[styles.container, theme === 'dark' && styles.containerDark]}>
```

Both patterns from earlier Stages are genuinely the same idea: a style
value isn't fixed at write-time, it's computed at render-time, from
whatever the current state happens to be.

## ✧ Reusable Styles

Right now, `container`, `title`, `red`, and `blue` are each redefined
separately in `PlayerScreen.tsx`, `GameScreen.tsx`, and `ScoreboardScreen.tsx`
— the same styles, copy-pasted across files. A shared style file avoids
that:

```typescript
// styles/shared.ts
import { StyleSheet } from 'react-native';

export const sharedStyles = StyleSheet.create({
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
});
```

```tsx
// In any screen:
import { sharedStyles } from '../styles/shared';

<View style={sharedStyles.container}>
  <Text style={sharedStyles.title}>Welcome to Tic-Tac-Toe!</Text>
</View>
```

Styles genuinely specific to one screen — `board`, `cell`, `red`, `blue` —
stay local to that screen's own `StyleSheet.create({...})`. Only what's
truly shared moves into `sharedStyles`.

!!! tip "Pro-Tip"
    A useful test for whether a style belongs in a shared file: would
    changing it in one screen make sense to change everywhere else it's
    used, too? `container` and `title` — almost certainly yes. `board` — no,
    that's specific to `GameScreen` alone.

## ❖ Colour Choices in Apps

Colour, like fonts, isn't purely aesthetic — it's functional, and worth
approaching with a few principles in mind, not just a favourite hex code.

**Contrast** matters most for readability — dark text needs a light
background and vice versa, and this becomes especially important for
anyone with low vision or colour blindness, where a palette that "looks
fine" to one person may be genuinely hard to read for another.

**Consistency** means resisting the temptation to use every colour that
looks nice — a limited palette, generally three to five core colours, reads
as intentional; a palette that grows one colour at a time, screen by
screen, tends to read as accidental instead.

**Palette types** tend to fall into a few recognisable patterns: a
**neutral-plus-accent** approach (a calm base, one highlight colour) suits
productivity or utility apps; a **vibrant** approach (several bold colours
used deliberately) suits entertainment or children's apps; a **dark mode**
palette inverts the usual light-background assumption entirely, and needs
its own contrast checking rather than simply "the same colours, inverted."

**Assigning roles**, rather than using colours arbitrarily wherever they
look good, is what keeps a palette consistent as an app grows:

```typescript
const Colors = {
  primary: '#416858',   // main brand colour — buttons, highlights
  accent: '#f1ff58',    // sparingly — active states, emphasis
  background: '#f3f4f4',
  text: '#032425',
  error: '#dc3545',
  success: '#28a745',
};
```

Once a colour has a *role* — "this is the error colour" — every future
styling decision about errors becomes a lookup, not a new choice.

!!! note "Conceptual Note"
    Working out a palette from first principles, every time, is genuinely
    hard — especially for a beginner deciding what "primary" or "accent"
    should even be. If picking colours by eye and checking contrast
    manually feels like guesswork, the [Colour Scheme Generator](https://github.com/jesselsookha/colour-scheme-generator)
    is worth exploring — a browser-based tool built specifically to help
    generate a coherent palette, complete with contrast checking, rather
    than relying on trial and error alone.

## ✦ Application Storylines

- **Finance app** — sans-serif for clarity, a neutral palette with green
  and red reserved specifically for gains and losses.
- **Children's game** — a rounded display font, a vibrant palette with
  playful, high-contrast combinations.
- **Health app** — a calm sans-serif, soft blues and greens suggesting
  trust and relaxation.
- **News reader** — serif for body text (long-form reading), a restrained,
  neutral palette that stays out of the way of the content.

## ✧ Debugging Styling Issues

!!! bug "Common Bug Spotlight"
    **Symptom:** a custom font is specified in `fontFamily`, but the
    default system font still appears.

    **Likely causes:** the font name in `fontFamily` doesn't exactly match
    the key used in `useFonts({...})`; the component renders before
    `fontsLoaded` becomes `true`; or, for local fonts, the file path in
    `require()` is wrong.

    **Fix:** double-check the exact key name passed to `useFonts`, and
    confirm the component returns `null` (or a loading screen) until
    `fontsLoaded` is `true` — rendering text before fonts are ready is one
    of the most common causes of "my custom font isn't working."

## Exercise

Pick one Google Font and load it into the XO Game — the title text is a
reasonable place to try it. Once it renders correctly, commit this
checkpoint. Then: extract `container` and `title` into `sharedStyles.ts` as
shown above, and update all three screens to use it. Confirm nothing looks
different on screen — a successful refactor here should be invisible to
the user, only visible in the code.

## Reflection

Fonts and colours are the first things a user notices, and the last things
they consciously think about — which is exactly the point. Done well,
neither draws attention to itself; it simply makes the app feel considered.
The UI/UX distinction from earlier is worth carrying forward: everything in
this Stage is UI, in service of a UX decision made before any code was
written — what should this app feel like to use?

[Stage 17](./stage-17-state-management-beyond-usestate.md) moves past
styling and into state management approaches beyond `useState` itself.

---

[^1]: Expo — Official Documentation, "Fonts" and "expo-font." Available at: https://docs.expo.dev/develop/user-interface/fonts/ (Accessed: 9 July 2026).
[^2]: Expo — Google Fonts, GitHub Repository. Available at: https://github.com/expo/google-fonts (Accessed: 9 July 2026).