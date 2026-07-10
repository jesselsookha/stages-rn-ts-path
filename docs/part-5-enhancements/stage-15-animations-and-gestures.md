# Stage 15 — Animations & Gestures

## ❖ Introduction

Not every idea in this Stage has a natural home in a turn-based board game.
A subtle fade can enhance the XO Game without complicating it — a drag
gesture, a swipeable list, or a pinch-to-zoom simply don't belong to
Tic-Tac-Toe. Rather than forcing gestures into the game where they don't
fit, this Stage gives the XO Game one small, restrained touch, and
introduces a second, separate mini-app — a simple task list — as a proper
home for the gesture-heavy material.

## ✦ Animated API Basics

React Native's built-in **Animated API** animates a value over time, and
binds that value to a style property.

```tsx
import { useRef, useEffect } from 'react';
import { Animated, Text, View } from 'react-native';

export default function FadeInExample() {
  const fadeAnim = useRef(new Animated.Value(0)).current;

  useEffect(() => {
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 2000,
      useNativeDriver: true,
    }).start();
  }, []);

  return (
    <View>
      <Animated.Text style={{ opacity: fadeAnim }}>
        Hello World
      </Animated.Text>
    </View>
  );
}
```

- `useRef(new Animated.Value(0))` creates a value React can animate directly,
  without triggering a full re-render on every frame.
- `Animated.timing(...)` describes how that value should change — here,
  from `0` to `1`, over 2000 milliseconds.
- `useNativeDriver: true` hands the animation off to the native platform to
  run smoothly, rather than JavaScript.
- `<Animated.Text>` — note the `Animated.` prefix — is a special version of
  `<Text>` that knows how to read an animated value directly in its style.

## ✧ XO Game Touch: Fading In the Winner Announcement

One small, natural fit: fading in the "X Wins!" text, rather than having it
appear instantly.

```tsx
import { useRef, useEffect } from 'react';
import { Animated } from 'react-native';

// Inside GameScreen, alongside the existing board/winner state:
const winnerFade = useRef(new Animated.Value(0)).current;

useEffect(() => {
  if (winner !== null) {
    Animated.timing(winnerFade, {
      toValue: 1,
      duration: 600,
      useNativeDriver: true,
    }).start();
  }
}, [winner]);
```

```tsx
{winner === 'X' && (
  <Animated.Text style={[styles.title, { opacity: winnerFade }]}>
    {player1} Wins!
  </Animated.Text>
)}
```

The `useEffect` here depends on `[winner]` — the fade only starts the moment
`winner` changes away from `null`, exactly the dependency-array reasoning
from Stage 12.

!!! note "Conceptual Note"
    That's the extent of animation this course adds to the XO Game itself.
    The point isn't that a board game can't be animated further — it's that
    every addition should earn its place. One considered fade communicates
    "something has happened" without turning the game into a demonstration
    of every animation technique available.

## ❖ Introducing a Mini-App: A Simple Task List

The remaining ideas — dragging, swiping, and gesture handling generally —
get their own small, standalone app instead: a short list of tasks, each of
which can be dragged and swiped away. Nothing here connects back to the XO
Game; it's a self-contained example purely for exploring what these
libraries offer.

### Setup

```bash
npx expo install react-native-gesture-handler react-native-reanimated
```

Reanimated also needs a small addition to `babel.config.js` — its plugin
must be listed **last** in the `plugins` array:

```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ['react-native-reanimated/plugin'], // must be listed last
  };
};
```

!!! warning "Watch Out"
    After editing `babel.config.js`, the Metro bundler's cache needs
    clearing, or the change may not take effect: stop the server and run
    `npx expo start -c` instead of the usual `npx expo start`.

### Reanimated: Dragging a Card

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import { GestureDetector, Gesture } from 'react-native-gesture-handler';
import { StyleSheet } from 'react-native';

function DraggableCard() {
  const translateX = useSharedValue(0);

  const panGesture = Gesture.Pan().onChange((event) => {
    translateX.value = event.translationX;
  }).onFinalize(() => {
    translateX.value = withSpring(0); // snaps back when released
  });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: translateX.value }],
  }));

  return (
    <GestureDetector gesture={panGesture}>
      <Animated.View style={[styles.card, animatedStyle]} />
    </GestureDetector>
  );
}

const styles = StyleSheet.create({
  card: { width: 100, height: 60, backgroundColor: '#416858', borderRadius: 8 },
});
```

- **`useSharedValue`** holds a value that Reanimated can update on the UI
  thread directly, without waiting on JavaScript — which is what makes
  Reanimated noticeably smoother than the Animated API for gesture-driven
  motion.
- **`Gesture.Pan()`** describes a drag gesture, with callbacks for while
  it's happening (`onChange`) and once released (`onFinalize`).
- **`withSpring(0)`** animates back to `0` with a natural, springy motion
  once the drag ends.

### Gesture Handling: Swipe to Delete a Task

```tsx
import { Swipeable } from 'react-native-gesture-handler';
import { View, Text, StyleSheet } from 'react-native';

function TaskItem({ text, onDelete }: { text: string; onDelete: () => void }) {
  return (
    <Swipeable
      renderRightActions={() => (
        <View style={styles.deleteAction}>
          <Text style={styles.deleteText}>Delete</Text>
        </View>
      )}
      onSwipeableOpen={onDelete}
    >
      <View style={styles.taskItem}>
        <Text>{text}</Text>
      </View>
    </Swipeable>
  );
}

const styles = StyleSheet.create({
  taskItem: { padding: 16, backgroundColor: '#e5eae6' },
  deleteAction: {
    backgroundColor: '#c0392b',
    justifyContent: 'center',
    alignItems: 'center',
    width: 80,
  },
  deleteText: { color: '#fff', fontWeight: 'bold' },
});
```

### Bringing the Mini-App Together

```tsx
import { useState } from 'react';
import { GestureHandlerRootView } from 'react-native-gesture-handler';
import { View, FlatList, StyleSheet } from 'react-native';

export default function TaskListApp() {
  const [tasks, setTasks] = useState<string[]>(['Buy milk', 'Walk the dog', 'Finish notes']);

  const removeTask = (index: number) => {
    setTasks(tasks.filter((_, i) => i !== index));
  };

  return (
    <GestureHandlerRootView style={styles.container}>
      <FlatList
        data={tasks}
        keyExtractor={(item, index) => item + index}
        renderItem={({ item, index }) => (
          <TaskItem text={item} onDelete={() => removeTask(index)} />
        )}
      />
    </GestureHandlerRootView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1 },
});
```

!!! bug "Common Bug Spotlight"
    **Symptom:** swipe and drag gestures simply don't respond at all —
    nothing happens, no errors either.

    **Cause:** `react-native-gesture-handler` requires the whole app (or, at
    minimum, the screen using gestures) to be wrapped in
    `<GestureHandlerRootView>`. Without it, gesture handlers are registered
    but never actually receive touch events.

    **Fix:** make sure `GestureHandlerRootView` sits at the top of the
    component tree — usually wrapping the entire app in `App.tsx` for a
    project that uses gestures throughout, or, as shown here, wrapping just
    this one self-contained mini-app.

## Exercise

Get the task list mini-app running — swiping a task away should remove it
from the list, and dragging the card should snap back when released. Commit
this checkpoint as its own small project (or a clearly separate file, if
you'd rather keep it inside the same repository as the XO Game for
reference). Then: try changing `withSpring(0)` to `withSpring(0, { damping: 5 })`
and see how the "snap back" motion changes. What does `damping` seem to
control?

## Reflection

Two different decisions ran through this Stage: where to *add* to the XO
Game, and where to *not*. The fade-in on the winner text took a few lines
and fit naturally into a game that already exists. Gestures needed an entire
separate example to explore properly — forcing them into the board game
would have meant adding drag-and-swipe behaviour to tiles that don't
actually need to be dragged or swiped anywhere.

[Stage 16](./stage-16-fonts-and-styling.md) returns to the XO Game directly,
looking at fonts and styling — territory that fits the game far more
naturally than gestures did here.

---

[^1]: React Native — Official Documentation, "Animations." Available at: https://reactnative.dev/docs/animations (Accessed: 9 July 2026).
[^2]: Software Mansion — React Native Reanimated, Official Documentation. Available at: https://docs.swmansion.com/react-native-reanimated/ (Accessed: 9 July 2026).
[^3]: Software Mansion — React Native Gesture Handler, Official Documentation. Available at: https://docs.swmansion.com/react-native-gesture-handler/ (Accessed: 9 July 2026).