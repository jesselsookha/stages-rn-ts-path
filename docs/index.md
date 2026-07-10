# Stages: A React Native & TypeScript Path

## Welcome

Whether you're a first-year meeting React Native for the first time, a senior student
returning to sharpen your understanding, or a visitor who found this site while
learning React Native on your own — welcome. These notes are written to be read at
whatever depth you need right now, and returned to later when you need more.

## About This Companion

This site is a companion to the official Mobile App Scripting manual — not a
replacement for it. You are still expected to work through the manual and the
material shared on the LMS; what you'll find here is a different way into the same
subject, built around one continuous project rather than isolated examples.

Over the years, class notes here have taken a few shapes — slides, plain notepad
files during live coding, and more recently a series of markdown files shared
alongside each lesson. This site is the next step in that evolution: the same
notes, given room to breathe, cross-reference each other, and be searched.

It's built for Mobile App Scripting students, and shared openly for anyone learning
React Native.

## How These Notes Work

The spine of this site is a single application, built up piece by piece across
every Stage. Rather than isolated snippets, each Stage shows the *complete* program
at that point in its development — so you always know exactly what the code looked
like before a concept was introduced, and exactly what it looks like after.

All application code is written in TypeScript. The one exception is early on, where
JavaScript is shown briefly, side-by-side with TypeScript, purely to help you see
what changes and why.

!!! tip "Before You Begin"
    If you're working through this as part of the Mobile App Scripting course, read
    the setup note at the start of **Stage 2 — Environment Setup** before installing
    anything. It explains why we start in the classroom's Virtual Machine rather
    than on your own device.

## The Journey Ahead

The path is organised into six Parts, twenty-four Stages in total, each building on
the last:

**Part I — Foundations** 
> Where React Native comes from, and getting your environment ready.

1. React & React Native Origins
2. Environment Setup

**Part II — TypeScript Essentials**
> The language itself — syntax, structure, and how it differs from the JavaScript you may already know.

3. Programming Basics in TypeScript
4. Logical Structures & Data Handling
5. Advanced TypeScript Features

**Part III — Core RN Programming**
> Building the first working version of the application, one concept at a time.

6. Basic Components & Layout
7. App Navigation
8. Decisions & Conditional Rendering
9. Data Structures (Arrays)
10. Refactoring & Structure
11. Completion & Scoreboard

**Part IV — Expanding Core Concepts**
> State, context, and data that persists beyond a single screen.

12. State & Effects
13. Context & Settings
14. Data Persistence & Networking

**Part V — Enhancements**
> Styling, animation, structured state, and preparing an app for the real world — including CRUD operations, optional Firebase integration, and deployment.

15. Animations & Gestures
16. Fonts & Styling
17. State Management Beyond useState
18. File Handling & CRUD
19. Firebase Integration (Optional)
20. Deployment & Testing

**Part VI — Extensions**
> Taking what you've built further, alternative applications, and workflow practices for your own projects going forward.

21. Extending the XO Game
22. Alternative Apps (Tasks)
23. GitHub Workflow
24. ICE Tasks & Homework

**Start with Part I Stage 1 whenever you're ready.**

# References

Cosden Solutions (2023) *How to setup React Native with Expo quickly*. YouTube. Available at: https://www.youtube.com/watch?v=y6DwGxe2E_k (Accessed: 10 July 2026).

Expo (2026) *EAS Build*. Available at: https://docs.expo.dev/build/introduction/ (Accessed: 10 July 2026).

Expo (2026) *EAS Submit*. Available at: https://docs.expo.dev/submit/introduction/ (Accessed: 10 July 2026).

Expo (2026) *Fonts*. Available at: https://docs.expo.dev/develop/user-interface/fonts/ (Accessed: 10 July 2026).

Expo (2026) *Google Fonts*. GitHub repository. Available at: https://github.com/expo/google-fonts (Accessed: 10 July 2026).

Expo (2026) *Store data*. Available at: https://docs.expo.dev/develop/user-interface/store-data/ (Accessed: 10 July 2026).

Firebase (2026) *Cloud Firestore*. Available at: https://firebase.google.com/docs/firestore (Accessed: 10 July 2026).

Firebase (2026) *Structure data*. Available at: https://firebase.google.com/docs/firestore/manage-data/structure-data (Accessed: 10 July 2026).

React (2026) *React documentation*. Available at: https://react.dev (Accessed: 10 July 2026).

React (2026) *Passing data deeply with context*. Available at: https://react.dev/learn/passing-data-deeply-with-context (Accessed: 10 July 2026).

React (2026) *Synchronizing with effects*. Available at: https://react.dev/learn/synchronizing-with-effects (Accessed: 10 July 2026).

React Native (2026) *React Native documentation*. Available at: https://reactnative.dev (Accessed: 10 July 2026).

React Native (2026) *Animations*. Available at: https://reactnative.dev/docs/animations (Accessed: 10 July 2026).

React Native (2026) *Core components*. Available at: https://reactnative.dev/docs/components-and-apis (Accessed: 10 July 2026).

React Native (2026) *Environment setup*. Available at: https://reactnative.dev/docs/environment-setup (Accessed: 10 July 2026).

React Native (2026) *FlatList*. Available at: https://reactnative.dev/docs/flatlist (Accessed: 10 July 2026).

React Native (2026) *Handling text input*. Available at: https://reactnative.dev/docs/handling-text-input (Accessed: 10 July 2026).

React Native (2026) *Using TypeScript*. Available at: https://reactnative.dev/docs/typescript (Accessed: 10 July 2026).

React Native (2026) *SectionList*. Available at: https://reactnative.dev/docs/sectionlist (Accessed: 10 July 2026).

React Native Async Storage (2026) *Documentation*. Available at: https://react-native-async-storage.github.io/latest/ (Accessed: 10 July 2026).

React Navigation (2026) *React Navigation documentation*. Available at: https://reactnavigation.org/ (Accessed: 10 July 2026).

Redux (2026) *Why Redux Toolkit is how to use Redux today*. Available at: https://redux.js.org/introduction/why-rtk-is-redux-today (Accessed: 10 July 2026).

Redux Toolkit (2026) *Redux Toolkit documentation*. Available at: https://redux-toolkit.js.org/ (Accessed: 10 July 2026).

Software Mansion (2026) *React Native Gesture Handler*. Available at: https://docs.swmansion.com/react-native-gesture-handler/ (Accessed: 10 July 2026).

Software Mansion (2026) *React Native Reanimated*. Available at: https://docs.swmansion.com/react-native-reanimated/ (Accessed: 10 July 2026).

TypeScript (2026) *Classes*. Available at: https://www.typescriptlang.org/docs/handbook/2/classes.html (Accessed: 10 July 2026).

TypeScript (2026) *Everyday types*. Available at: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html (Accessed: 10 July 2026).

TypeScript (2026) *Object types*. Available at: https://www.typescriptlang.org/docs/handbook/2/objects.html (Accessed: 10 July 2026).