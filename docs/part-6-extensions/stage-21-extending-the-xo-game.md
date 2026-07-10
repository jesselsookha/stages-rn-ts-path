# Stage 21 — Extending the XO Game

## ❖ Introduction

The XO Game, as built across Parts III through V, is genuinely complete —
playable, styled, persisted, and ready to be built and shared. This Stage
isn't about adding more features to it. Most of the natural next steps —
richer animations, a smarter scoreboard, deeper persistence — have already
been shown, in Stage 15, Stage 12, and Stage 13 respectively. Repeating
that code here would only be repetition. Instead, this Stage looks
backward properly, tying every earlier Stage to where it actually lives in
the finished game, and looks forward to what all of it is actually *for*.

## ✦ A Few Directions Worth Remembering

Rather than re-showing code already covered, here's where to look back, if
you want to extend the game further on your own:

- **More animation** — the winner text fade from Stage 15 could extend to
  every tile, the moment it's filled, using the same `Animated.timing`
  pattern.
- **A richer scoreboard** — Stage 12's `SectionList`, grouped by date,
  could group by *winner* instead, or sort sections by most recent first.
- **More persistence** — Stage 13's theme setting could be joined by saved
  player names, using the exact AsyncStorage pattern from Stage 11.

None of these need new material — every one is the same tool from earlier
Stages, pointed at a slightly different part of the game.

## ✧ Connecting the Dots

Looking back across the whole build, here's where each concept actually
lives inside the finished XO Game:

| Stage | Concept | Where It Lives in the XO Game |
|---|---|---|
| 1 | React & React Native origins | The declarative thinking behind every component built since |
| 2 | Environment setup | The project itself — created, run, and tested |
| 3 | TypeScript basics | Every typed variable, from `player1: string` onward |
| 4 | Decisions & loops | The board's win-checking loop, `switch` statements in early tile logic |
| 5 | Interfaces, classes, modules | `User`, `CellProps`, and every `.ts`/`.tsx` file split by Stage 10 |
| 6 | Core components & layout | `View`, `Text`, `TextInput`, `Button` — the Player Setup screen |
| 7 | Navigation | Player → Game screen, carrying names via `RootStackParamList` |
| 8 | Conditional rendering | The turn indicator, and each tile's X/O/empty display |
| 9 | Arrays | `board`, replacing nine separate state variables |
| 10 | Refactoring & structure | `screens/`, `components/`, `utils/` — the whole project layout |
| 11 | Completion & scoreboard | Draw detection, Play Again, saving results with AsyncStorage |
| 12 | State & effects | The dependency array behind loading and grouping scores |
| 13 | Context & settings | The theme toggle, read from both Settings and Game screens |
| 14 | Data persistence & networking | AsyncStorage in depth; networking, covered conceptually |
| 15 | Animations & gestures | The winner text's fade-in |
| 16 | Fonts & styling | Every `StyleSheet`, the UI/UX framing, the colour palette |
| 17 | State management beyond useState | Recognised as unnecessary for this game — and why |
| 18 | File handling & CRUD | Explored in a separate app, not the game itself |
| 19 | Firebase (optional) | Explored conceptually, not wired into the game |
| 20 | Deployment & testing | How this exact project would actually reach a device |

Reading this table top to bottom is, in a real sense, reading the entire
course again — just from the opposite direction, starting from a finished
thing and tracing every piece back to where it was first learned.

## ❖ Application Storylines

None of what's listed above is actually about Tic-Tac-Toe. A board that
remembers state, a screen that navigates to another, a value saved so it
survives a restart, a decision that changes what's shown on screen — every
one of these is a general-purpose tool that happens to have been taught
using this particular game.

That's worth sitting with directly, because it's the actual point of
building one project across twenty Stages, rather than twenty unrelated
snippets: the *techniques* were never really about Tic-Tac-Toe. A todo
app's completed-task checkbox is the same conditional styling as the turn
indicator. A notes app's list is the same `FlatList` pattern as the
scoreboard. A login screen's validation is the same idea as the CRUD
form's error checking in Stage 18.

None of that means going back to reread a specific Stage every time a
similar problem shows up elsewhere — it means trusting that if you've
actually built something once, genuinely worked through how it works, the
shape of that solution stays available to you. When something in a future
project feels familiar, it very possibly is — and these notes will still be
here, to revisit, whenever that recognition needs a little help turning
into working code again.

## Reflection

Stage 6 opened with a single `Hello World` inside `App.tsx`. What sits at
the end of Stage 20 is a complete, tested, deployable application — built
one deliberate decision at a time, each one explained before it was used.
That arc is worth noticing on its own terms, separate from anything else
these notes still have to offer.

[Stage 22](./stage-22-alternative-apps-tasks.md) puts exactly this kind of
transfer into practice — three new app ideas, described only in words,
left for you to build.

---