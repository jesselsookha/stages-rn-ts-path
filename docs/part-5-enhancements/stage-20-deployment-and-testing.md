# Stage 20 — Deployment & Testing

## ❖ Introduction

Building an app is only half the journey — the other half is getting it
reliably into someone else's hands. This Stage covers testing during
development, a real network issue worth knowing about ahead of time, and
an honest look at what "deploying" an Expo app actually involves — because
that word can mean several quite different things, with quite different
costs and effort attached.

## ✦ Testing with Expo CLI

```bash
npx expo start
```

Two ways to see the running app:

- **Scan the QR code** with Expo Go, on a physical device.
- **Press `a`** (Android) or **`i`** (iOS, Mac only) in the terminal, to
  launch on an already-running emulator/simulator.

Both are equally valid — scanning the QR code isn't a lesser option, and
skipping the terminal shortcuts entirely is not a mistake.

!!! bug "Common Bug Spotlight"
    **Symptom:** scanning the QR code sometimes works perfectly, and other
    times the app simply never loads on the phone — no clear error, it just
    never connects.

    **Cause:** this is almost always a **local network issue**, not a
    mistake in how the app was started. The QR code encodes a direct
    address (something like `exp://192.168.1.5:8081`) that the phone uses
    to find the computer on the same network — and that connection can
    fail for a few common reasons:

    - **Dual-band Wi-Fi** — many routers split into a 2.4GHz and a 5GHz
      network. If the phone and computer end up on different bands, the
      router may treat them as separate networks entirely, even though
      both show the same Wi-Fi name.
    - **A "Public" network profile** — Windows in particular sometimes
      classifies a Wi-Fi connection as "Public" rather than "Private,"
      which causes the firewall to silently block the phone's incoming
      connection to the Metro bundler.
    - **Networks that isolate devices from each other on purpose** —
      common on university or coffee-shop Wi-Fi, which often blocks
      device-to-device connections deliberately, regardless of band or
      firewall settings.

    **Fix:** check that both devices are genuinely on the same band first.
    If that's not the issue, check the computer's network profile is set
    to Private. If neither resolves it — or the network is a shared public
    one outside your control — run:

```bash
    npx expo start --tunnel
```

    This routes the connection through a public tunnel instead of the
    local network, so the phone can reach the computer from anywhere, at
    the cost of slightly slower reload times.

## ✧ From Expo Go to a Real App: Understanding EAS

Everything built across this course has run inside **Expo Go** — a
container app that runs your project's JavaScript, without your app
actually being its own standalone installable thing. That's ideal for
development, and entirely unsuitable for handing to someone else to
install permanently. Turning a project into an actual, installable app —
one with its own icon, its own listing, entirely independent of Expo Go —
requires **EAS (Expo Application Services)**, specifically **EAS Build**.

!!! warning "Watch Out"
    Older tutorials reference `expo build:android` and `expo build:ios`.
    Both are retired — Expo's classic build service no longer exists. **EAS
    Build**, via the separate `eas-cli` tool, is the current, actively
    maintained replacement.[^1]

```bash
npm install -g eas-cli
eas login
eas build:configure
```

`eas build:configure` generates an `eas.json` file, defining named **build
profiles** — commonly `development`, `preview`, and `production` — each
describing a different kind of build for a different purpose.

## ❖ Android: Building and Distributing

```bash
eas build --platform android --profile preview
```

A `preview` build produces an installable `.apk` file directly — shareable
with a link, installable on any Android device that allows it, with no
Google Play account required at all. This is genuinely enough for testing
with classmates, friends, or a lecturer, without needing to publish
anything publicly.

Publishing to the **Google Play Store** itself is a separate, further
step:

```bash
eas submit --platform android
```

This requires a **Google Play Developer account** — a **$25 one-time
fee** — plus meeting Play Store content and privacy policies, and choosing
a release track (internal testing, closed testing, or full public release).

## ✧ iOS: Building and Distributing

```bash
eas build --platform ios --profile preview
```

iOS is considerably less flexible here: Apple doesn't allow installing an
app outside its own ecosystem the way Android's `.apk` does. A preview
build still needs to go through Apple's own distribution mechanisms — at
minimum, **TestFlight**, Apple's official beta-testing platform — even
just to test with a handful of people.

```bash
eas submit --platform ios
```

Publishing to the **App Store** — or even TestFlight — requires an
**active Apple Developer Program membership**, at **$99 per year**, plus
meeting Apple's App Store Review Guidelines.

!!! note "Conceptual Note"
    This asymmetry is worth being honest about, and isn't a React Native
    or Expo limitation — it's a genuine difference in how Android and
    Apple approach installing apps outside their official stores. Android
    allows it; Apple, by design, essentially doesn't.

## ✱ Being Honest About "Publishing an App"

It's worth naming plainly what each stage of this process actually costs,
in time, money, or both — since "deploying an app" can mean genuinely
different things:

| Goal | What's Needed | Cost |
|---|---|---|
| Test on your own devices, via Expo Go | Nothing beyond what you already have | Free |
| Share an installable Android file with classmates | An EAS account, an `eas build` | Free tier available |
| Share an installable iOS build for testing | An EAS account, an Apple Developer account, TestFlight | $99/year (Apple) |
| Publish publicly on Google Play | A Google Play Developer account | $25 once |
| Publish publicly on the Apple App Store | An Apple Developer account, App Review approval | $99/year |

EAS itself also has its own free tier, with limits on build minutes and
queue priority[^1] — genuinely sufficient for a student project, but worth
knowing exists as a limit before assuming builds are unlimited.

None of this is meant to discourage actually publishing something — plenty
of student and hobby projects do reach real app stores. It's meant to set
realistic expectations: finishing the code is not the same milestone as
having the app in someone else's hands, and the gap between those two
points involves real accounts, real review processes, and in Apple's case,
a real recurring cost.

## ❖ Relevant Configuration Files

- **`app.json`** — app name, version, icon, splash screen, and platform
  identifiers (`package` for Android, `bundleIdentifier` for iOS).
- **`eas.json`** — build profiles, generated by `eas build:configure`.
- **`package.json`** — dependencies and scripts.
- **`assets/`** — icons and splash images referenced by `app.json`.

## ✧ Debugging Deployment Issues

- **Build failures** — check `npx expo-doctor`, which flags common
  configuration and dependency issues before they cause a failed build.
- **App works in Expo Go but not in an EAS build** — usually a sign of a
  package that isn't Expo Go–compatible, or a missing native configuration
  step; the error logs on the EAS build dashboard are the first place to
  look.
- **Rejected by a store's review process** — almost always privacy,
  permissions, or content guideline issues; both Apple's and Google's
  review guidelines are worth reading directly rather than guessing.

## Reflection

Testing and deployment sit right at the edge of what an introductory
subject can reasonably require — genuinely useful to understand, not
something every student needs to have actually published by the end of
this course. Knowing the real shape of the process — what's free, what
costs money, what's quick, and what involves waiting on someone else's
review — is worth having clearly in mind before deciding whether, and how
far, to take a project beyond the classroom.

[Part VI](../part-6-extensions/stage-21-extending-the-xo-game.md) begins
next — extending the XO Game further, and looking at workflow practices
for projects of your own.

---

[^1]: Expo — Official Documentation, "EAS Build" and "Create your first build." Available at: https://docs.expo.dev/build/introduction/ (Accessed: 10 July 2026).
[^2]: Expo — Official Documentation, "EAS Submit." Available at: https://docs.expo.dev/submit/introduction/ (Accessed: 10 July 2026).