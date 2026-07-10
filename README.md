# Stages: A React Native & TypeScript Path

A companion to the Mobile App Scripting manual — a 24-Stage, project-based path through React Native and TypeScript, built around one continuous application rather than isolated examples.

**[Read the live site →](https://jesselsookha.github.io/stages-rn-ts-path/)**

## About

This repository contains the source for *Stages*, a set of MkDocs Material-based learning notes built to accompany a Mobile App Scripting subject. Rather than teaching concepts in isolation, the notes are structured around the progressive development of a single application — a Tic-Tac-Toe game — built up one deliberate concept at a time, from environment setup through to deployment.

This site is a **companion**, not a replacement, to the official course manual. Students working through the course are still expected to engage with the manual and LMS material directly; these notes offer a different, more narrative path through the same subject.

## Structure

The site is organised into six Parts, twenty-four Stages in total:

| Part | Focus |
|---|---|
| I — Foundations | React/React Native origins, environment setup |
| II — TypeScript Essentials | Core language fundamentals |
| III — Core RN Programming | Building the XO Game's first working version |
| IV — Expanding Core Concepts | State, context, and persistence |
| V — Enhancements | Styling, animation, CRUD, deployment |
| VI — Extensions | Reflection, independent tasks, and workflow discipline |

Each Stage follows a consistent structure — an introduction, conceptual explanation, complete (not snippeted) working code, common debugging scenarios, an exercise, and a closing reflection — so the pattern of a Stage is predictable, even as the content deepens.

## Local Development

To run this site locally:

```bash
git clone https://github.com/jesselsookha/stages-rn-ts-path.git
cd stages-rn-ts-path
pip install -r requirements.txt
mkdocs serve
```

The site will be available at `http://127.0.0.1:8000`.

To build a static version:

```bash
mkdocs build
```

## Deployment

This site deploys automatically to GitHub Pages via GitHub Actions on every push to `main` — see `.github/workflows/deploy.yml`. No manual build or push to a separate branch is required.

## Contributing

This is primarily a teaching resource maintained for a specific course, but corrections, typo fixes, and clarifications are welcome via issues or pull requests.

## License

This work is licensed under a [Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License](https://creativecommons.org/licenses/by-nc-nd/4.0/).

## Author

**Jessel Sookha** — Lecturer, Information Technology
GitHub: [@jesselsookha](https://github.com/jesselsookha)