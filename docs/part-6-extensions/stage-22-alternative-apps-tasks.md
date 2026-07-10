# Stage 22 — Alternative Apps (Tasks)

## ❖ Introduction

Everything shown so far has come with code already written, ready to be
typed, run, and understood. This Stage works differently, deliberately.
What follows are three briefs — written the way a client or product owner
might actually hand requirements to a developer: a storyline, a list of
screens, a data model, and a set of user stories. No code. No wireframes.
The task is to read a brief the way you'll need to for your own assignment
project, and work out *how* to build it yourself.

## ✦ To-Do App

### Storyline

A student wants to stop losing track of assignments and daily tasks
scattered across notebooks and sticky notes. They want a simple app: add a
task, mark it done, remove it once it no longer matters.

### Screens

- **Task List** — every task, showing its title and completion status.
- **Add/Edit Task** — a form for a task's title and optional description,
  reused for both creating a new task and editing an existing one.

### Data Model

```typescript
type Task = {
  id: number;
  title: string;
  description?: string;
  completed: boolean;
  createdAt: string;
};
```

### User Stories

- As a user, I want to add a new task with a title, so that it appears in
  my list.
- As a user, I want to optionally add a short description to a task, so
  that I can remember extra detail later.
- As a user, I want to mark a task as complete, so that I can see what's
  still outstanding at a glance.
- As a user, I want to delete a task, so that my list doesn't fill up with
  things I no longer need.
- As a user, I want my tasks to still be there the next time I open the
  app, so that I don't lose my list by closing it.

## ✧ Notes App

### Storyline

Students often need somewhere to jot things down quickly — an idea during
a lecture, a reminder, a rough draft of something for later. A Notes app
is a simple, personal digital notebook.

### Screens

- **Notes List** — every saved note, showing a title and a short preview
  of its content.
- **Note Detail/Edit** — the full content of one note, editable in place.

### Data Model

```typescript
type Note = {
  id: number;
  title: string;
  content: string;
  lastEdited: string;
};
```

### User Stories

- As a user, I want to create a new note with a title and body text, so
  that I can capture an idea before I forget it.
- As a user, I want to see a preview of each note in my list, so that I can
  find the one I'm looking for without opening every one.
- As a user, I want to open a note and edit its content, so that I can
  update it later.
- As a user, I want to delete a note I no longer need.
- As a user, I want my notes saved between sessions, so closing the app
  doesn't lose my work.

## ❖ Weather App

### Storyline

A student wants to check the weather before leaving for class, without
switching to a separate app. A Weather app shows current conditions for a
city they choose.

### Screens

- **Search/Home** — a place to enter or select a city.
- **Current Weather** — temperature and conditions for the chosen city.
- **Forecast** *(optional extension)* — a short multi-day outlook.

### Data Model

```typescript
type WeatherResult = {
  city: string;
  temperature: number;
  conditions: string;
  fetchedAt: string;
};
```

### User Stories

- As a user, I want to search for a city by name, so that I can check
  weather somewhere other than my current location.
- As a user, I want to see the current temperature and conditions for that
  city, so that I know what to expect.
- As a user, I want to be told clearly if a city isn't found, rather than
  the app failing silently.
- As a user, I want to refresh the current city's weather, so that I'm not
  looking at stale data.
- As a user, I want the app to remember my last searched city, so that I
  don't have to type it again every time I open the app.

!!! note "Conceptual Note"
    Notice `WeatherResult` has no `id` — unlike `Task` and `Note`, this
    data isn't a record you own and manage; it's a snapshot of something
    fetched from elsewhere, each time you ask for it. Worth noticing which
    kind of data a given screen is actually working with — your own stored
    records, or someone else's data borrowed briefly.

## ✱ Connecting the Dots

Each of these briefs draws on ground already covered, without saying so
explicitly in the requirements themselves — that recognition is left for
you to make: a list of records with Create, Read, Update, and Delete; a
form with validation; data that needs to persist; a screen that fetches
something from outside the app entirely. None of these three apps
introduces a technique this course hasn't already put in your hands.

## Reflection

A user story doesn't tell you how to write the code. It tells you what
needs to be true once the code exists. Learning to read a brief like this
— pulling out the screens, the data, and the behaviour it implies — is
arguably a more durable skill than any single React Native pattern covered
in these notes, since it's the same translation work every real project
starts with, regardless of what you eventually build it in.

[Stage 23](./stage-23-github-workflow.md) covers the workflow expected
around building something like this — commits, documentation, and proof
that the work genuinely happened, step by step.

---