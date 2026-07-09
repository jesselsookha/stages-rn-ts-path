# Stage 14 — Data Persistence & Networking

## ❖ Introduction

`AsyncStorage` has already been doing real work since Stage 11 — saving game
results, loading them back on the Scoreboard screen. This Stage looks at it
properly: the version actually in use, and a newer version worth knowing
about, since the two work quite differently. It then turns to a different
kind of external data entirely — **networking**, fetching information from
outside the app altogether.

## ✦ AsyncStorage v2 — What We've Been Using

Everything built so far uses AsyncStorage's original API: a single, default
import, with methods called directly on it.

```typescript
import AsyncStorage from '@react-native-async-storage/async-storage';

await AsyncStorage.setItem('username', 'Alice');
const value = await AsyncStorage.getItem('username');
await AsyncStorage.removeItem('username');
```

This is still what Expo's own documentation currently installs and
recommends[^1] — one shared storage space, for the whole app, addressed by
string keys.

!!! note "Conceptual Note"
    Best practice worth restating from Stage 11: AsyncStorage only ever
    stores strings. Anything more structured — objects, arrays, the score
    entries from Stage 12 — needs `JSON.stringify()` going in, and
    `JSON.parse()` coming back out. Keep key names descriptive
    (`'scores'`, not `'s'`) — with one shared storage space, a vague key
    name is much more likely to collide with something else later.

## ✧ What's New in v3: Scoped Storage Instances

AsyncStorage's newer major version changes the API meaningfully — rather
than one shared storage for the whole app, v3 introduces
**`createAsyncStorage()`**, which creates a **named, isolated storage
instance**:

```typescript
import { createAsyncStorage } from '@react-native-async-storage/async-storage';

const scoresStorage = createAsyncStorage('xoGameScores');

await scoresStorage.setItem('lastWinner', 'Alice');
const value = await scoresStorage.getItem('lastWinner');
```

Every instance is **scoped** — completely isolated from any other instance,
even within the same app.[^2] Two different `createAsyncStorage()` calls
with different names genuinely can't see each other's data, the same way
two different apps can't read each other's storage.

!!! info "Deep Dive"
    Why would an app want more than one storage instance? Picture the XO
    Game supporting multiple players on a shared device, each with their own
    profile — `createAsyncStorage('user-alice')` and
    `createAsyncStorage('user-bob')` would keep each player's saved scores
    completely separate, with no risk of one player's data accidentally
    overwriting another's. Under the hood, each named instance gets its own
    file — on iOS and Android, its own `.sqlite` database file, isolated
    from every other instance's file.

!!! warning "Watch Out"
    Scoped storage instances aren't supported everywhere — on **Windows and
    visionOS**, `createAsyncStorage()` currently falls back to a single
    shared storage, the same as v2, regardless of what name you give it.
    Worth knowing if a project ever targets those platforms specifically.

## ✱ Batch Operations (v3)

Alongside scoped instances, v3 adds methods for working with several keys
at once — useful anywhere a single item at a time becomes tedious:

```typescript
// Store several values in one call
await scoresStorage.setMany({
  player1LastResult: 'win',
  player2LastResult: 'loss',
});

// Retrieve several values in one call
const results = await scoresStorage.getMany(['player1LastResult', 'player2LastResult']);
// { player1LastResult: 'win', player2LastResult: 'loss' }

// Remove several values in one call
await scoresStorage.removeMany(['player1LastResult', 'player2LastResult']);
```

`getAllKeys()` and `clear()` — already familiar from v2 — remain available
on a v3 instance too:

```typescript
const allKeys = await scoresStorage.getAllKeys();
await scoresStorage.clear(); // e.g. a "Clear Scoreboard" feature
```

!!! tip "Pro-Tip"
    `scoresStorage.clear()` is exactly the kind of feature a "Reset
    Scoreboard" button on the Scoreboard screen would call — it removes
    every key from that one scoped instance, without touching any other
    storage instance the app might have. This isn't wired into the XO Game
    built in these notes, since our project still uses v2 — but it's worth
    recognising as the shape a real reset feature would take, if the
    project ever migrated.

## ❖ A Note on Which Version This Course Uses

The XO Game built across these Stages uses **v2** throughout, matching what
Expo currently ships by default.[^1] The v3 material above is worth knowing
conceptually — scoped storage and batch operations solve real problems — but
isn't something you need to retrofit into the game itself. If you start a
new project later and find `createAsyncStorage()` already the default,
you'll recognise exactly what's changed and why.

## ✧ Networking Basics

Unlike storage, the XO Game has no genuine need to talk to the outside
world — it's a fully local, two-player game. Networking is still worth
covering here, since most real apps eventually need it. React Native offers
two common approaches:

- **Fetch API** — built directly into JavaScript, no installation required.
- **Axios** — a separate library, installed like any other package, with a
  slightly more convenient syntax and some extra features.

### Fetch API

```tsx
useEffect(() => {
  const fetchData = async () => {
    try {
      const response = await fetch('https://jsonplaceholder.typicode.com/posts');
      const data = await response.json();
      console.log(data);
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  };
  fetchData();
}, []);
```

`fetch()` returns a Promise almost immediately, but that Promise resolves to
a *response*, not the actual data — `.json()` is a second, separate step
that parses the response body.

### Axios

```bash
npx expo install axios
```

```tsx
import axios from 'axios';

useEffect(() => {
  const fetchData = async () => {
    try {
      const response = await axios.get('https://jsonplaceholder.typicode.com/posts');
      console.log(response.data);
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  };
  fetchData();
}, []);
```

Axios parses JSON automatically — `response.data` is already usable,
without a separate `.json()` step.

## ✦ Debugging Network Requests

!!! bug "Common Bug Spotlight"
    Networking issues tend to fall into a small set of recurring causes:

    - **No network connectivity** — the device itself is offline.
    - **Wrong URL** — a typo, or an endpoint that's moved.
    - **CORS errors** — a server-side configuration issue, not something
      fixable from the app's code.
    - **Timeouts** — the server is slow, unreachable, or blocking the
      request entirely.

    In every case, start the same way: log the actual error caught in
    `catch`, check the HTTP status code if a response came back at all
    (`200` succeeded, `404` means the URL was wrong, `500` means the server
    itself failed), and, if genuinely stuck, test the same URL outside the
    app entirely — a browser, or a tool like Postman — to rule out whether
    the problem is your code or the server.

## Reflection

AsyncStorage and networking solve two different problems that sound similar
at first: one keeps data on the device, between sessions; the other reaches
somewhere else entirely, that the device doesn't control. The XO Game only
ever needed the first — not every app needs both, and recognising which one
a feature actually calls for is worth pausing on before reaching for either.

[Part V](../part-5-enhancements/stage-15-animations-and-gestures.md) begins
next, polishing how the game looks and feels — animations, fonts, and
styling — with the game's core logic now complete.

---

[^1]: Expo — Official Documentation, "Store data." Available at: https://docs.expo.dev/develop/user-interface/store-data/#async-storage (Accessed: 9 July 2026).
[^2]: React Native Async Storage — Official Documentation, "Database naming" and "Usage." Available at: https://react-native-async-storage.github.io/latest/ (Accessed: 9 July 2026).