# Stage 12 — State & Effects

## ❖ Introduction

Stage 11 already put `useEffect` to work — loading the Scoreboard's saved
results the moment that screen appeared. This Stage doesn't reintroduce
that; instead, it explains *why* the pattern used there works the way it
does, what goes wrong when it's written slightly differently, and extends
the Scoreboard with a new component: `SectionList`.

## ✦ What Is a Side Effect?

A **side effect** is anything a component does that reaches *outside* its
own rendering — reading or writing storage, making a network request,
subscribing to an event, starting a timer. `useState` only ever manages a
value; `useEffect` is what lets a component reach beyond itself when
something changes.

## ✧ Dependency Arrays, Properly

```tsx
useEffect(() => {
  // side effect code
}, [dependencies]);
```

| Dependency Array | When the Effect Runs |
|---|---|
| `[]` | Once — right after the component first appears. |
| `[value]` | Once at first appearance, then again every time `value` changes. |
| *(omitted entirely)* | After **every** render, with no exceptions. |

## ❖ Revisiting Stage 11: Why the Empty Array Mattered

Stage 11's Common Bug Spotlight showed what happens without a dependency
array at all — an infinite loop, since loading scores updates state, which
triggers a re-render, which runs the effect again. Here's the actual
mechanism behind that:

```tsx
useEffect(() => {
  loadScores();
}, []); // <- this is what breaks the cycle
```

`[]` tells React "this effect depends on nothing that changes — run it once,
and never again on this screen." Without it, React has no way to know the
effect *shouldn't* run again after `setScores` causes a re-render, so it
runs it anyway, every time.

## ✱ A New Gotcha: Stale Closures

A dependency array that's *present but incomplete* causes a different,
sneakier problem. Consider:

```tsx
function TurnLogger() {
  const [player1Turn, setPlayer1Turn] = useState<boolean>(true);
  const [logCount, setLogCount] = useState<number>(0);

  useEffect(() => {
    console.log(`Log #${logCount}: ${player1Turn ? "Player 1" : "Player 2"}'s turn`);
  }, [player1Turn]); // logCount is used here, but missing from the array

  // ...
}
```

!!! bug "Common Bug Spotlight"
    **Symptom:** the logged `logCount` value seems permanently stuck at
    whatever it was the very first time this effect ran, even though the
    actual `logCount` state elsewhere in the app has since changed.

    **Cause:** this effect only re-runs when `player1Turn` changes, because
    that's the only dependency listed. The function itself, though, still
    reads `logCount` — but it's reading the value `logCount` held at the
    moment this particular version of the effect was created. This is
    called a **stale closure**.

    **Fix:** include every value the effect actually reads in its dependency
    array — here, that means `[player1Turn, logCount]`. As a habit: if your
    effect's code mentions a piece of state or a prop, it almost always
    belongs in the array.

## ❖ Cleanup Functions

Some side effects need to be explicitly stopped, not just left to run once.
`useEffect` can return a function that React calls automatically before the
effect runs again, or when the component disappears from screen entirely:

```tsx
useEffect(() => {
  const timer = setInterval(() => console.log('Tick'), 1000);

  return () => clearInterval(timer); // cleanup
}, []);
```

Without the `return () => clearInterval(timer)` line, the timer would keep
running even after the screen it belongs to is gone — a small, easy-to-miss
memory leak.

## ✦ Expanding the Scoreboard: Grouping Scores by Date

So far, the Scoreboard is a flat list of results — no sense of *when* each
game happened. **`SectionList`** renders grouped data, with a heading above
each group, which is a natural fit here: scores grouped by the date they
were played.

`SectionList` expects its data shaped differently to `FlatList` — as an
array of sections, each with a `title` and its own `data` array:

```tsx
const sections = [
  { title: '2026-07-08', data: ['Alice won', 'Bob won'] },
  { title: '2026-07-09', data: ['Alice vs Bob — Draw'] },
];
```

### Updating How Scores Are Saved

In `GameScreen.tsx`, `saveResult` needs to record *when* a result happened,
not just what it was. Everything else in that file stays exactly as it was
in Stage 11 — only `saveResult` changes:

```tsx
const saveResult = async (result: string) => {
  const existing = await AsyncStorage.getItem('scores');
  const scores = existing ? JSON.parse(existing) : [];
  const today = new Date().toISOString().split('T')[0]; // e.g. "2026-07-09"
  scores.push({ date: today, result });
  await AsyncStorage.setItem('scores', JSON.stringify(scores));
};
```

Each saved score is now an object — `{ date, result }` — rather than a
plain string.

### `screens/ScoreboardScreen.tsx`

```tsx
import { useState, useEffect } from 'react';
import { View, Text, SectionList, StyleSheet } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

type ScoreEntry = {
  date: string;
  result: string;
};

type ScoreSection = {
  title: string;
  data: string[];
};

export default function ScoreboardScreen() {
  const [sections, setSections] = useState<ScoreSection[]>([]);

  useEffect(() => {
    const loadScores = async () => {
      const existing = await AsyncStorage.getItem('scores');
      const scores: ScoreEntry[] = existing ? JSON.parse(existing) : [];
      setSections(groupByDate(scores));
    };
    loadScores();
  }, []);

  const groupByDate = (scores: ScoreEntry[]): ScoreSection[] => {
    const grouped: { [date: string]: string[] } = {};

    for (const entry of scores) {
      if (!grouped[entry.date]) {
        grouped[entry.date] = [];
      }
      grouped[entry.date].push(entry.result);
    }

    return Object.keys(grouped).map((date) => ({
      title: date,
      data: grouped[date],
    }));
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Scoreboard</Text>
      <SectionList
        sections={sections}
        keyExtractor={(item, index) => item + index}
        renderItem={({ item }) => <Text style={styles.item}>{item}</Text>}
        renderSectionHeader={({ section }) => (
          <Text style={styles.sectionHeader}>{section.title}</Text>
        )}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 16, backgroundColor: '#fff' },
  title: { fontSize: 24, fontWeight: 'bold', marginBottom: 12 },
  sectionHeader: {
    fontSize: 16,
    fontWeight: 'bold',
    backgroundColor: '#eee',
    paddingVertical: 4,
    paddingHorizontal: 8,
    marginTop: 8,
  },
  item: { fontSize: 18, marginVertical: 4, paddingHorizontal: 8 },
});
```

!!! note "Conceptual Note"
    `groupByDate` is the interesting part here, not `SectionList` itself —
    `SectionList` just renders whatever shape of sections you give it.
    Reshaping flat data into grouped sections, before handing it to a
    component, is a pattern you'll meet again well beyond this course,
    any time raw data doesn't already match the shape a UI component
    expects.

## Exercise

Once scores are correctly grouped by date on the Scoreboard, commit this
checkpoint. Then: play a few games on the same day, and confirm they group
under one heading rather than several. What would need to change here if
you wanted the most recent date to appear at the top of the list?

## Reflection

`useEffect` isn't really about "running code on load" — that's just its
most common use. What it's actually for is keeping a component correctly in
sync with something outside of React's own rendering, whether that's
storage, a timer, or a subscription. The dependency array is how you tell
React exactly what that component depends on — get it right, and effects
stay quiet until something relevant actually changes.

[Stage 13](./stage-13-context-and-settings.md) introduces a different way of
sharing state — one that doesn't rely on passing props down through every
screen individually.

---

[^1]: React — Official Documentation, "Synchronizing with Effects." Available at: https://react.dev/learn/synchronizing-with-effects (Accessed: 9 July 2026).
[^2]: React Native — Official Documentation, "SectionList." Available at: https://reactnative.dev/docs/sectionlist (Accessed: 9 July 2026).