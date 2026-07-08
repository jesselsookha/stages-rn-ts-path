# Stage 2 — Environment Setup

## ❖ Introduction

Before writing a single line of application code, we need a working environment.
React Native development traditionally involves a fair number of moving parts —
but thanks to **Expo CLI**, the process is considerably simpler than a fully native
setup.

This Stage walks you through setting up your environment on **Windows** and
**macOS**, explains the difference between running on an emulator versus a
physical device, and gets you to the point of creating and running your very
first project.

!!! abstract "Before You Begin"
    If you're working through this as part of the Mobile App Scripting course,
    please read this note before installing anything.

    In class, you'll do all of your early practice inside the lab's Virtual
    Machine — Node.js, VS Code, Android Studio, and the Android Emulator are
    already installed and configured there. Use that time to *watch* what's
    happening: how VS Code and the emulator talk to each other, how ADB
    recognises the Virtual Device, and how your app actually appears once you
    run it.

    Once that process feels familiar, come back to this Stage and treat it as
    your guide for installing everything on your **own machine**. You'll notice
    quickly that Visual Studio Code and Android Studio are only the visible tip
    of the setup — underneath, there's Node.js, the Expo tooling, environment
    variables, and more, all working together. Practise and get comfortable in
    the VM first. Install on your own machine once you're ready.

## ✦ Why Environment Setup Matters

- React Native apps are typically tested on **Android** (straightforward on
  Windows machines) or **iOS** (which requires a Mac).
- Expo CLI allows us to run apps quickly, without compiling native code
  ourselves.
- Understanding *what* is being installed, and *why*, means you can debug setup
  issues with confidence — whether that's in class or on your own machine, months
  from now.

Before the step-by-step instructions, it's worth actually watching what a full
setup and first run looks like. This video was shared with students previously,
and remains a good visual companion to the written steps below — pay particular
attention to how the presenter explains environment variables, since that's the
part students tend to gloss over until something breaks.

<iframe width="560" height="315" src="https://www.youtube.com/embed/y6DwGxe2E_k" title="How to setup React Native with Expo quickly" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## ✦ Expo CLI vs React Native CLI

React Native offers two main ways to start a project:

- **Expo CLI**
    - Recommended for beginners and classroom use.
    - Handles most native configuration automatically.
    - Provides the **Expo Go** app, for running projects instantly on a physical
      device.
    - Supports live reload and hot reloading out of the box.
- **React Native CLI**
    - Used for advanced or fully native development.
    - Requires Android Studio (Windows) or Xcode (Mac) to build.
    - Gives full control over native modules and configuration.
    - A more complex setup, and slower to get started with.

**For this course, Expo CLI is what we'll use throughout.** Even the official
reactnative.dev documentation points to Expo as the recommended starting
point.[^1]

!!! warning "Watch Out"
    You may come across older tutorials online instructing you to run
    `npm install -g expo-cli`. Don't. The standalone, globally installed Expo CLI
    was deprecated some time ago in favour of running everything through `npx
    expo` directly — you'll see this in the steps below. Installing the old
    global CLI won't break anything immediately, but it will nag you with outdated
    warnings and isn't part of how we work in this course.

---

# React Native Development Setup: Student Guide (Windows)

## Common Setup (For Both Versions)

These steps are common to both Version 1 and Version 2 below.

### 1. Install Node.js (Recommended: LTS Version)

- Go to: [https://nodejs.org/](https://nodejs.org/)
- Download and install the **LTS version**.
- After installation, verify:

```bash
node -v
npm -v
```

### 2. Install Chocolatey (Windows Only)

!!! note "Conceptual Note"
    Chocolatey is a package manager for Windows — it helps install and manage
    tools like Java and Android Studio from the command line, the same way
    Homebrew does on macOS.

Run Command Prompt **as Administrator**:

```powershell
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -ExecutionPolicy Bypass -Command "Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Then verify:

```bash
choco -v
```

### 3. Install Java Development Kit (JDK 17)

```bash
choco install openjdk17
```

Add JDK to your environment variables:

- `JAVA_HOME` → `C:\Program Files\OpenJDK\openjdk-17\`
- Add `%JAVA_HOME%\bin` to your `Path` system variable

!!! info "Deep Dive"
    An environment variable is a named value, stored by your operating system,
    that programs can read at any time. `JAVA_HOME` doesn't run anything by
    itself — it simply tells other tools (like Android Studio) *where to find*
    your Java installation, so they don't each need to be told separately. This
    is exactly the kind of "invisible" configuration the setup video above
    walks through visually.

### 4. Install Visual Studio Code

Download from: [https://code.visualstudio.com/](https://code.visualstudio.com/)

Suggested VS Code extensions:

- React Native Tools (by Microsoft)
- ESLint
- Prettier
- Path Intellisense
- GitLens
- Auto Import

---

## Version 1: Using Expo + Android Studio Emulator

### 5. Install Android Studio

- Download from: [https://developer.android.com/studio](https://developer.android.com/studio)
- During installation, select:
    - Android SDK
    - Android SDK Platform
    - Android Virtual Device (AVD)

### 6. Set Environment Variables (Windows)

- `ANDROID_HOME` → `C:\Users\<YourUserName>\AppData\Local\Android\Sdk`
- Add to `Path`:
    - `%ANDROID_HOME%\platform-tools`
    - `%ANDROID_HOME%\emulator`

### 7. Create an Android Virtual Device

- Open Android Studio → **Device Manager**
- Create an emulator using a Pixel or Nexus device
- Launch the emulator

### 8. Install Expo Go on a Physical Device (Optional Alongside the Emulator)

- Android: [Expo Go on Google Play](https://play.google.com/store/apps/details?id=host.exp.exponent)
- iOS: Expo Go on the App Store

### 9. Create Your First Project

```bash
npx create-expo-app my-app -t expo-template-blank-typescript
cd my-app
npx expo start
```

- Press ++a++ to open on the Android Emulator
- Or scan the QR code with **Expo Go** on your phone, if you'd rather use a
  physical device

!!! success "Verification"
    If everything is installed correctly, you should see the default Expo
    starter screen — either in the emulator window or on your phone through
    Expo Go — within a few seconds of the bundler finishing.

---

## Version 2: Using Expo + BlueStacks as Virtual Device

!!! tip "Pro-Tip"
    This version is helpful if the Android Studio emulator runs too slowly on
    your machine, or isn't available to you.

### 5. Install Expo Go in BlueStacks

- Install BlueStacks from: [https://www.bluestacks.com](https://www.bluestacks.com)
- Once installed, open the **Play Store** inside BlueStacks and install
  **Expo Go**.

### 6. Configure BlueStacks with ADB

**A. Enable Developer Mode**

- Inside BlueStacks → go to **Settings > Advanced**
- Note the **ADB IP address** and **port** (e.g. `192.168.0.101:5555`)

**B. Connect ADB to BlueStacks**

Make sure `adb` is installed — it's part of the Android SDK Platform Tools:

```bash
adb devices
```

Connect to BlueStacks:

```bash
adb connect 192.168.0.101:5555
```

Check the device is connected:

```bash
adb devices
```

You should see something like:

```
List of devices attached
192.168.0.101:5555    device
```

### 7. Create and Run a Project

```bash
npx create-expo-app my-app -t expo-template-blank-typescript
cd my-app
npx expo start
```

- BlueStacks should already be running with Expo Go open.
- In your Expo terminal, press ++a++ to run the app on Android.
- Alternatively, open Expo Go inside BlueStacks directly, and scan the QR code
  or enter the development URL manually.

!!! bug "Common Bug Spotlight"
    **Symptom:** `adb connect` succeeds, but the app never appears in
    BlueStacks.

    **Likely cause:** A firewall (often Windows Defender) is silently blocking
    the ADB connection.

    **Fix:** Temporarily allow `adb.exe` through your firewall, or connect
    while on the same network with the firewall's private-network rules
    enabled.

---

## Tips & Advice

- Keep Node.js, VS Code, and your extensions up to date.
- If using ADB with BlueStacks, make sure it isn't blocked by your firewall.
- You can reload an app at any time by pressing ++r++ in the Expo terminal.
- Hot Reloading is on by default — save a file, and see the change immediately.

---

# React Native with Expo — macOS Setup Guide

!!! info "Deep Dive"
    Expo CLI removes the need to compile native code yourself. We still install
    Android Studio and Java here, though, to give you the flexibility to run an
    Android emulator or move into native development later if you need to.

## Common Setup Steps

These steps apply to everyone, regardless of whether you end up using
BlueStacks, the Android Studio emulator, or a physical device.

### 1. Install Homebrew (Package Manager for macOS)

Homebrew helps install tools like Node, Watchman, and the JDK.

Open Terminal (via Spotlight, or Applications > Utilities) and paste:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then verify:

```bash
brew doctor
```

### 2. Install Node.js (Recommended: LTS Version) & Watchman

React Native needs **Node.js**, and **Watchman** to monitor your project files
for changes.

```bash
brew install node
brew install watchman
```

Verify:

```bash
node -v
npm -v
watchman --version
```

### 3. Install Java Development Kit (JDK 17)

Expo itself doesn't require Java, but it's worth installing if you plan to use
the Android Studio emulator.

```bash
brew install --cask zulu@17
```

Set the environment variable — add this to `~/.zshrc` or `~/.bash_profile`:

```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 17)
export PATH=$JAVA_HOME/bin:$PATH
```

Then reload:

```bash
source ~/.zshrc
```

Verify:

```bash
java -version
```

### 4. Install Visual Studio Code

Download from: [https://code.visualstudio.com/](https://code.visualstudio.com/)

Recommended extensions:

- React Native Tools
- ESLint
- Prettier
- GitLens
- Path Intellisense

### 5. Install Android Studio (Required for Emulator)

Download from: [https://developer.android.com/studio](https://developer.android.com/studio)

During installation, ensure these are selected:

- Android SDK
- Android SDK Platform
- Android Virtual Device (AVD)

### 6. Install Android SDK 35

In Android Studio:

1. Go to **More Actions > SDK Manager**
2. Under **SDK Platforms**, install:
    - Android 15 (VanillaIceCream) — Android SDK Platform 35, Google APIs
      ARM64 v8a System Image
3. Under **SDK Tools**, install:
    - Android SDK Build-Tools 35.0.0
    - Android Emulator

### 7. Set Up Environment Variables

Add this to your shell config:

```bash
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

Reload, then check:

```bash
source ~/.zshrc
echo $ANDROID_HOME
```

---

## Expo Project Setup & Execution

You're now ready to create and run your first project.

### 8. Create a New Project Using Expo (with TypeScript)

```bash
npx create-expo-app my-app -t expo-template-blank-typescript
cd my-app
```

### 9. Start the Expo Development Server

```bash
npx expo start
```

This opens the Expo Dev Tools, and gives you several ways to run the app.

## How to Run Your Expo App

You have three options here — it's worth knowing all three, even if you settle
on one for most of your day-to-day work.

**Option 1 — Physical Device (Expo Go)**

1. Install Expo Go on your device (Google Play or the App Store).
2. Connect your device to the same Wi-Fi network as your Mac.
3. Open Expo Go, and scan the QR code shown in the terminal or browser.
4. Your app loads directly on your own phone.

**Option 2 — Android Studio Emulator**

1. Open Android Studio → **Device Manager**.
2. Create a Virtual Device (a Pixel or Nexus phone works well).
3. Select the Android 15 (API 35) system image, and launch it.
4. Press ++a++ in the terminal or browser to run the app on the emulator.

**Option 3 — BlueStacks Emulator (Advanced)**

Only needed if you'd rather use BlueStacks. You'll connect using ADB:

```bash
brew install --cask android-platform-tools
```

Enable ADB debugging in BlueStacks' settings, then:

```bash
adb connect 127.0.0.1:5555
npx expo start
```

Press ++a++ to launch on BlueStacks.

## Developer Tips

- Prefer `npx expo install` over `npm install` when adding Expo-compatible
  packages — it selects versions known to work with your project's Expo SDK.
- Live Reloading is on by default.
- The template you used already gives you a TypeScript-ready project.
- The Expo Dev Tools browser tab is useful for monitoring logs, QR codes, and
  connected devices at a glance.

---

## ✱ Your First Project, Up Close

Once your project is created and running, take a moment before writing any code
of your own — open the project folder in VS Code and look at three files in
particular:

- **`App.tsx`** — the single component that's currently rendering everything you
  see on screen. Everything you build across the rest of this course starts from
  this file.
- **`app.json`** — Expo's configuration file for your project: its name, icon,
  splash screen, and platform-specific settings.
- **`package.json`** — the standard Node.js project file, listing your
  dependencies and the scripts (like `npx expo start`) available to run.

You don't need to understand every line yet — `App.tsx` gets a proper, detailed
look in [Stage 6 – Basic Components & Layout](../part-3-core-rn-programming/stage-06-basic-components-and-layout.md).
For now, this first run is simply about confirming that your whole toolchain —
Node, Expo, and either an emulator, BlueStacks, or your own phone — is talking
to itself correctly, *before* there's any application logic to complicate things.

!!! tip "Pro-Tip"
    Whichever run option you used just now — the emulator, BlueStacks, or Expo
    Go on your phone — try at least one of the *other* two before moving on.
    Knowing all three matters more than it might seem right now: a lab machine,
    a personal laptop, and a phone in your pocket won't always agree on which
    option is fastest or even available.

!!! note "Conceptual Note"
    This course uses `expo-template-blank-typescript` as its starting point,
    and `react-navigation` once navigation is introduced. You'll also come
    across Expo's own template gallery and its file-based routing system
    (Expo Router) if you look around — both are worth knowing exist, but we
    deliberately start with the more explicit, manually-configured approach
    here, since it makes what's actually happening easier to see as a beginner.

---

## Reflection

You now have a working environment, and — more importantly — you've seen it
work in more than one way: emulator, BlueStacks, or your own device. Before
moving on to [Stage 3](../part-2-typescript-essentials/stage-03-programming-basics-in-typescript.md),
sit with this:

Everything you installed in this Stage was, at some point, a deliberate design
decision by someone else — Node.js, Watchman, the JDK, Expo itself. Pick one of
these tools, and try to answer: what specific problem does it solve, that the
others in this list don't?

---

[^1]: React Native — Official Documentation, "Environment Setup." Available at: https://reactnative.dev (Accessed: 2026).
[^2]: Cosden Solutions (2023) *How to setup React Native with Expo quickly*. YouTube. Available at: https://www.youtube.com/watch?v=y6DwGxe2E_k (Accessed: 2026).
