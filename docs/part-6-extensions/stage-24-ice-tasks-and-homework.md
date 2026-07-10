# Stage 24 — ICE Tasks & Homework

## ❖ Introduction

These six ICE (Integrated Curriculum Engagement) Tasks are marked
coursework, reinforcing what's been built across these notes — but unlike
Stage 22, this work is compulsory, and evaluated. Every rule from Stage 23
applies here directly: a genuine, incremental commit history, a complete
README with its Decisions & Difficulties section, and the changelog kept
inside that same README.

!!! warning "Watch Out"
    Work showing only one commit, or an AI-generated solution submitted
    without evidence of genuine, incremental development, will not be
    accepted. Your commit history is expected to tell the real story of how
    each task was built.

## ICE Task 1 — Blank App Setup & Reflection

**Deliverables:** a working Expo project displaying `Hello, [Your Name]!`;
a screenshot running on an emulator or physical device via Expo Go.

**Functionality Expected:** `App.tsx` modified to show a personal greeting,
including your real first name; the app runs successfully.

**Submission:** public repo `ICE1-BlankApp-[YourInitials]`; complete
project code; README (with Changelog and Decisions & Difficulties
sections) as specified in Stage 23.

## ICE Task 2 — Layout Exercise

**Deliverables:** a screen with at least three distinct sections (header,
content, footer); a screenshot of the layout.

**Functionality Expected:** Flexbox properties (`flexDirection`,
`justifyContent`, `alignItems`) used deliberately; consistent
`StyleSheet` usage; layout that adapts reasonably across screen sizes.

**Submission:** repo `ICE2-Layout-[YourInitials]`; README explaining your
layout choices, alongside the standard Stage 23 requirements.

## ICE Task 3 — Conditional Rendering Mini-App

**Deliverables:** a mini-app showing different content depending on a
condition; screenshots of both states.

**Functionality Expected:** `useState` used to toggle a condition;
different UI rendered based on it.

**Submission:** repo `ICE3-Conditional-[YourInitials]`; README explaining
the logic in your own words.

## ICE Task 4 — Array-based FlatList Exercise

**Deliverables:** a screen displaying an array of items via `FlatList`; a
screenshot of the rendered list.

**Functionality Expected:** an array of objects defined; rendered with
`FlatList`; a proper `keyExtractor`.

**Submission:** repo `ICE4-FlatList-[YourInitials]`; README describing
your array's structure.

## ICE Task 5 — AsyncStorage Practice

**Deliverables:** a screen where data can be saved and retrieved (notes,
preferences, or similar); a screenshot showing that data survives an app
restart.

**Functionality Expected:** AsyncStorage used to store and retrieve data;
persistence demonstrated across a restart, not just within one session.

**Submission:** repo `ICE5-AsyncStorage-[YourInitials]`; README explaining
how AsyncStorage was used.

## ICE Task 6 — Playlist Creator (Multi-Screen App)

### Introduction

This final task pulls several Stages together into one small, multi-screen
app: a Playlist Creator that stores *links* to songs on other platforms,
rather than playing audio itself.

### Storyline

A user wants a simple way to organise songs they like into playlists —
pulling together links they've found on YouTube or Spotify into one
personal collection, without needing either app open to browse what
they've saved.

### Screens

- **Splash Screen** — the app's logo, fading out after a short delay,
  before navigating automatically to the Home screen.
- **Home Screen** — a **Featured** playlist, and a list of the user's
  **other** playlists. Tapping a playlist's name reveals its full track
  list. Tapping a track opens its saved link in the user's browser or the
  relevant app (YouTube/Spotify), leaving your app entirely.
- **Manage Playlists Screen** — create a new playlist, edit an existing
  one's name, delete a playlist, and add or remove individual tracks
  (title, artist, and a pasted share link) within a playlist.

### Data Model

```typescript
type Track = {
  id: number;
  title: string;
  artist: string;
  link: string; // a YouTube or Spotify share link
};

type Playlist = {
  id: number;
  name: string;
  featured: boolean;
  tracks: Track[];
};
```

### User Stories

- As a user, I want to see a Splash screen with the app's branding
  briefly, so the app feels polished rather than opening abruptly.
- As a user, I want to see my Featured playlist and my other playlists on
  the Home screen, so I can find what I'm looking for quickly.
- As a user, I want to tap a playlist to see its tracks, so I don't need a
  separate screen for every single playlist.
- As a user, I want to tap a track and be taken directly to it on YouTube
  or Spotify, so I don't need to search for it myself.
- As a user, I want to create, rename, and delete playlists, so my
  collection stays organised over time.
- As a user, I want to add a track by pasting a link along with its title
  and artist, so I can save something I've found without leaving my
  workflow.
- As a user, I want my playlists to still be there the next time I open
  the app.

### A New Piece: Opening External Links

Opening a saved link in the user's browser or another app uses React
Native's built-in `Linking` API — worth a brief mention here, since it
hasn't come up elsewhere in these notes:

```typescript
import { Linking } from 'react-native';

const openTrack = async (url: string) => {
  const supported = await Linking.canOpenURL(url);
  if (supported) {
    await Linking.openURL(url);
  } else {
    console.warn(`Cannot open URL: ${url}`);
  }
};
```

`Linking.canOpenURL` checks whether the device is actually able to handle
a given link before attempting to open it — worth checking first, rather
than assuming every pasted link will be valid.

### Deliverables

- A working multi-screen app matching the three screens described above.
- Playlists and tracks that persist between sessions.
- A short video or set of screenshots demonstrating: the splash animation,
  expanding a playlist, opening a track's external link, and creating,
  editing, and deleting a playlist.

### Functionality Expected

- Navigation between all three screens (Stage 7).
- An animated Splash screen transition (Stage 15).
- Full CRUD on playlists and their tracks (Stage 18).
- Data persisted with AsyncStorage (Stage 11, Stage 14).
- `Linking.openURL` used to hand off to an external app.

### Submission Details

- Repo: `ICE6-Playlist-[YourInitials]`.
- README, with Changelog and Decisions & Difficulties, as specified in
  Stage 23.
- Minimum of 10 meaningful commits, genuinely reflecting how the app was
  built, screen by screen and feature by feature.

## Reflection

Six tasks, each isolating one idea at first, culminating in one task that
asks you to combine several at once — navigation, animation, CRUD,
persistence, and something entirely new, all in service of one small,
coherent app idea. That combination is deliberate: real projects are
rarely just one technique in isolation, and Task 6 is the first place in
this course that asks you to prove you can bring several together on your
own, with only a brief to work from.

This closes the ICE Tasks, and with them, the last of the 24 Stages. What
you've built — across the XO Game, the standalone mini-apps, and this
final Playlist Creator — is a genuinely complete foundation for whatever
comes next.

---