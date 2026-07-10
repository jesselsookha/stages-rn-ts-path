# Stage 23 — GitHub Workflow

## ❖ Introduction

Version control isn't just a place to store code — it's how you prove work
happened, and how it happened. This Stage isn't about learning new Git
commands; you've been committing since Stage 6. It's about establishing a
proper discipline around *when* to commit, *what* a commit message should
say, and what a repository needs to contain to genuinely document a
project — for your ICE Tasks, for your assignment, and well beyond this
course.

## ✦ Repository Setup

1. **Create a new repository** on GitHub, with a clear, specific name —
   `mast-xo-game`, not `project` or `test`.
2. **Initialise with a `README.md`**, and a `.gitignore` (an Expo/React
   Native template is provided automatically if you select one when
   creating the repo).
3. **Clone it locally**:
   ```bash
   git clone <repo-url>
   ```
4. Confirm the folder structure you actually work in — `App.tsx`,
   `screens/`, `components/`, `utils/`, `assets/` — matches what's tracked
   in the repository, and that `node_modules/` is excluded via
   `.gitignore`.

## ✧ Basic Commands

- `git status` — check what's changed.
- `git add .` — stage changes.
- `git commit -m "message"` — commit, with a clear message.
- `git push origin main` — send commits to GitHub.
- `git pull origin main` — sync with the latest version.

## ❖ Commit Discipline: When, and What to Say

*When* to commit matters as much as the commands themselves. A commit is
worth making at any point that represents genuine, working progress — a
feature that now runs, a bug that's now fixed, a refactor that's now
complete. Working for three hours and committing once at the end
communicates almost nothing about how the work actually happened.

Compare:

```
✗ "stuff"
✗ "fixes"
✗ "update"
```

```
✔ "Add TextInput fields for player names"
✔ "Fix winner check running before board state updates"
✔ "Refactor GameScreen into separate Cell component"
```

!!! note "Conceptual Note"
    A good commit message describes *what changed and why it mattered* —
    specific enough that reading your commit history from top to bottom
    should read almost like a diary of how the project actually came
    together, checkpoint by checkpoint.

## ✱ README Requirements

Your `README.md` is the single, complete document for a project — nothing
required by this course lives in a second file. It should contain:

- **Basic information** — project title, your name, course, and any
  relevant links.
- **Project overview** — what the app does, in a few sentences.
- **Purpose & features** — the problem it solves, and its key features.
- **Design overview** — screens, components, and how they relate.
- **Technologies & dependencies** — libraries used, and why.
- **File/folder structure** — a short explanation of how the project is
  organised.
- **Screenshots or a short video** — visual proof the app runs.
- **A CHANGELOG section** (see below).
- **Decisions & Difficulties** (see below).
- **References** — tutorials, docs, or guides consulted.

!!! tip "Pro-Tip"
    Writing clean, well-structured Markdown by hand can be fiddly,
    especially with tables and nested lists. [Andika](https://jesselsookha.github.io/andika/)
    is a simple browser-based Markdown editor built for exactly this —
    worth a look if formatting is slowing you down, purely as a writing
    aid, not a requirement.

## ❖ The CHANGELOG Section

Rather than a separate `CHANGELOG.md`, this course keeps it **inside**
`README.md`, as its own section — one document, everything in one place.
Each entry should include a date, a short summary, and, where relevant,
what problem it solved:

```markdown
## Changelog

### 2026-07-08
- Added Player Setup screen with name inputs
- Connected navigation to Game screen

### 2026-07-09
- Implemented winner detection across all 8 lines
- Fixed bug where game continued after a win
```

## ✧ Decisions & Difficulties

A required README section, separate from the changelog: name **one
specific decision** you made and why, and **one specific difficulty** you
ran into and how you actually diagnosed and resolved it.

```markdown
## Decisions & Difficulties

**Decision:** I stored the board as a single array instead of nine
separate variables, once I noticed how repetitive the individual-variable
version was becoming to maintain.

**Difficulty:** My winner check kept returning a winner one move too late.
I added console.log statements inside handlePress and realised I was
checking the old board state before setBoard had actually updated it —
I fixed it by checking the newly built array directly, before calling
setBoard.
```

!!! note "Conceptual Note"
    This section exists for a specific reason: it's very hard to write
    convincingly without having actually done the work. A copied solution
    doesn't come with a memory of *why* a particular choice was made, or
    what genuinely went wrong along the way — only the finished result.

## ❖ A Worked Example: Reading a Commit History

Below is what a short, honest commit history for a small feature might
look like — not a full project, just a few commits showing real
progression:

```
a1b2c3d  Set up blank Expo project with TypeScript template
d4e5f6g  Add TextInput fields for player names to PlayerScreen
h7i8j9k  Connect Start Game button to navigation
k1l2m3n  Add nine-tile board layout to GameScreen
n4o5p6q  Wire up tile press handlers with turn tracking
q7r8s9t  Add winner-checking logic
t1u2v3w  Fix winner check running against stale board state
w4x5y6z  Add Play Again button and draw detection
```

Notice each entry represents a distinct, meaningful step — not
"Part 1," "Part 2," "final version." A reader can follow *how* the app was
built, in order, purely from this list.

## ✦ Rules for Assignments and ICE Tasks

- **Proof of development** — a visible, incremental commit history is
  required, not optional.
- **Minimum commit count** — at least 10 meaningful commits per project,
  reflecting genuine steps, not artificially split trivial changes.
- **README completeness** — every section above must be present, and
  written in your own words.
- **AI usage** — you may use AI tools for guidance, but the work,
  understanding, and final code must be your own. If you use AI
  assistance meaningfully, say so honestly in your README, rather than
  leaving it unstated, as part of the Declaration of AI Usage section.
- **A single commit, or an AI-generated solution submitted without a
  genuine incremental history, will not be accepted.**

## Reflection

None of this is about performing busy-work for a grade. A commit history
that actually tells the story of how something was built, a README that
explains it in your own words, and a Decisions & Difficulties section that
only makes sense if you lived through it — together, these are simply what
honest evidence of learning looks like, whether you're a student today or
a working developer years from now, being asked in an interview to talk
through something you built.

[Stage 24](./stage-24-ice-tasks-and-homework.md) puts this workflow into
practice directly, across six ICE Tasks.

---
