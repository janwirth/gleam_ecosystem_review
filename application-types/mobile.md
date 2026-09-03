# Building Mobile Apps — Cross-Ecosystem Survey

> [!NOTE]
> **Status:** DRAFT · **Authoring:** AI-assisted, human-reviewed.

> The state of "ship one app to two app stores" in 2026, scored side-by-side.

**Snapshot 2026-04-29** — metrics from live GitHub web UI only (no API, no clones). Star counts, last-commit dates, and open-issue counts reflect what a logged-out visitor sees today.

## Table of Contents

1. [Goal](#goal)
2. [Legend](#legend)
   - [Facets — how to filter this page](#facets--how-to-filter-this-page)
3. [Categories — the five archetypes](#categories--the-five-archetypes)
   - [Native, one toolchain per OS](#native-one-toolchain-per-os) · [Custom canvas](#custom-canvas) · [Native widgets from one codebase](#native-widgets-from-one-codebase) · [Web view shell](#web-view-shell) · [Baseline: PWA](#baseline-pwa)
4. [What you actually pick when you pick a framework](#what-you-actually-pick-when-you-pick-a-framework)
5. [Per-framework reviews](#per-framework-reviews)
   - **Native, one toolchain per OS** — [Kotlin / Jetpack Compose (Android)](#native-kotlin--jetpack-compose-android) · [Swift / SwiftUI (iOS)](#native-swift--swiftui-ios)
   - **Custom canvas** — [Flutter](#flutter) · [Kotlin Multiplatform + Compose Multiplatform](#kotlin-multiplatform--compose-multiplatform)
   - **Native widgets from one codebase** — [React Native (bare CLI)](#react-native-bare-cli) · [Expo](#expo) · [.NET MAUI](#net-maui) · [NativeScript](#nativescript) · [Lynx](#lynx) · [Solito (Solid + Expo / RN + Next.js)](#solito-solid--expo--rn--nextjs)
   - **Web view shell** — [Capacitor (+ Ionic UI)](#capacitor--ionic-ui) · [Tauri Mobile](#tauri-mobile) · [Quasar (Vue + Capacitor/Cordova)](#quasar-vue--capacitorcordova)
   - **Baseline** — [PWA baseline](#pwa-baseline)
6. [Big comparison table](#big-comparison-table)
7. [Disregarded / EOL / niche](#disregarded--eol--niche)
8. [Leaderboards (per use-case)](#leaderboards-per-use-case)
9. [Cross-link](#cross-link)
10. [Discovery — search queries](#discovery--search-queries)

## Goal

Help a tech lead or engineer choose a mobile-app stack in 2026 by laying every meaningful option side-by-side, scored on the same rubric, with mobile-specific dimensions added (targets, code language, rendering model, dev loop, native-API access). The article answers: *given my team, my performance budget, and my code-reuse goals, which row of the comparison table is the right starting point?*

Out-of-scope: exhaustive UI-component-library comparison (each stack has many), backend-as-a-service choice, cross-platform game engines (Unity, Unreal, Godot — different category).

## Legend

Standard scoring scaffold from `workflows/REVIEW_METHODOLOGY.md`:

- **Stars (community)** — ≥10k = 🟩🟩 · ≥1k = 🟩 · <100 = 🟥
- **License** — 🟩 permissive (MIT/Apache-2.0/BSD/MPL) · 🟨 weak copyleft (LGPL) · 🟥 viral (GPL/AGPL)
- **Maintenance** — latest commit vs snapshot. 🟩🟩 within last month · 🟩 within last year · 🟥 > 1 year stale
- **README maturity** — 🟩🟩 guide-style + feature list · 🟩 clear tagline + basic usage · 🟥 minimal/scaffold
- **Idiomaticity** — does it feel native to its ecosystem? 🟩🟩 yes · 🟩 partial · 🟥 alien
- **Issues** — count + qualitative read (high count on active project ≠ bad)
- **Age** — first-release year (older ≠ better; just signal of stability vs novelty)

Mobile-specific extra rows (added per the brief):

- **Targets** — iOS · Android · desktop (macOS / Windows / Linux) · web · other (visionOS / TV / wearables)
- **Code language** — what the developer writes (Swift / Kotlin / Dart / TS / JS / C# / Rust / etc.)
- **Rendering model** — *native widgets* (uses platform UIKit / Android View) · *web view* (WKWebView / Android WebView) · *custom canvas* (Skia / Impeller / Metal / OpenGL / Vulkan) · *hybrid*
- **Dev loop** — hot reload (sub-second) · fast refresh (1–3s rebuild) · full rebuild (>10s) · OTA update story
- **Native API access** — *first-class* (raw platform SDK in same codebase) · *plugin-mediated* (TS/Dart bridge to a community plugin) · *limited* (sandboxed, web-API-only)

### Facets — how to filter this page

GitHub renders this file statically — no sorting, no filtering, no scripts. The workaround is a fixed vocabulary: every [per-framework review](#per-framework-reviews) ends with a **Facets** line of `key:value` tokens, and the [comparison table](#big-comparison-table) repeats them in its Facets row. Use the browser's find (⌘F / Ctrl+F) with one token — `render:webview`, `ota:built-in`, `target:desktop` — to hop through every framework that matches. Multi-valued facets carry several tokens. The [archetype sections](#categories--the-five-archetypes) are the same grouping done by hand for `archetype:*`.

| Facet | Values | Mirrors |
| --- | --- | --- |
| `archetype` | `native` · `canvas` · `widgets` · `webview` · `pwa` | the five [categories](#categories--the-five-archetypes) |
| `lang` | `swift` · `kotlin` · `dart` · `ts` · `csharp` · `rust` · `web` (any web stack) | *Code language* |
| `render` | `native` (real platform widgets) · `canvas` · `webview` · `browser` | *Rendering model* |
| `target` | `ios` · `android` · `desktop` · `web` · `other` (watch / TV / visionOS / auto) — first-class targets only, no out-of-tree forks | *Targets* |
| `api` | `first-class` · `plugin` · `limited` | *Native API access* |
| `ota` | `built-in` (the vendor ships an update service) · `third-party` · `diy` · `store-only` · `live` (plain web, nothing to ship) | *OTA updates* row |

## Categories — the five archetypes

Mobile frameworks are cut here along **one axis: what draws the pixels.** Four archetypes plus the PWA baseline. The 7-dim score is comparable *within* an archetype, less so *across* — a Capacitor-style web-view shop is not playing the same game as Flutter. Code language is deliberately *not* an axis; it is a facet (see [Legend](#legend)), which is why Tauri Mobile sits with Capacitor rather than in a category of its own.

The grouping is navigable in both directions: each archetype below lists its members, every [per-framework review](#per-framework-reviews) opens with an **Archetype** line that links back here, and the [comparison table](#big-comparison-table) carries an Archetype row. Section order and table columns follow the order below.

### Native, one toolchain per OS

*N codebases for N platforms, maximum fidelity.* Apple Swift + SwiftUI for iOS; Google Kotlin + Jetpack Compose for Android. Full SDK on day one of any new OS feature; you staff every platform separately.

- [Native: Kotlin / Jetpack Compose (Android)](#native-kotlin--jetpack-compose-android)
- [Native: Swift / SwiftUI (iOS)](#native-swift--swiftui-ios)

### Custom canvas

*One codebase, the framework draws every pixel itself.* Widgets are not real `UILabel` / `TextView` — they are pixels the framework paints. Upside: identical look across OSes. Downside: platform widgets feel slightly "off" to fluent users of either OS.

- [Flutter](#flutter) — Dart → Impeller on every target.
- [Kotlin Multiplatform + Compose Multiplatform](#kotlin-multiplatform--compose-multiplatform) — Skia/Metal canvas on iOS, desktop and web; on Android it *is* Jetpack Compose, i.e. native. Filed here because the cross-platform story is the canvas one.

### Native widgets from one codebase

*One codebase, real platform widgets driven through a bridge or an abstraction layer.* Every `<Text>` / `<Button>` becomes a real `UILabel` / `UIButton` / Android `View`. The price: new platform APIs surface through the bridge or binding layer later than in native.

- [React Native (bare CLI)](#react-native-bare-cli) — JS → Fabric/JSI → native views.
- [Expo](#expo) — the managed React Native toolchain; same rendering as RN.
- [.NET MAUI](#net-maui) — C# → per-platform handlers → native widgets; the compiled, non-JS member of this group.
- [NativeScript](#nativescript) — JS calls the platform API directly, no allow-list, no plugin needed.
- [Lynx](#lynx) — dual-thread JS engine → native views; a custom-canvas renderer is optional.
- [Solito (Solid + Expo / RN + Next.js)](#solito-solid--expo--rn--nextjs) — a navigation layer over Expo + Next.js; inherits Expo's rendering. Listed for completeness, not a standalone framework.

### Web view shell

*A web app in a native shell.* UI is HTML/CSS in `WKWebView` / Android `WebView`; native is reached through a JS-to-native bridge. Members differ in the host process, not in how pixels get drawn.

- [Capacitor (+ Ionic UI)](#capacitor--ionic-ui) — Swift/Kotlin host, TypeScript plugin API; Cordova's successor.
- [Tauri Mobile](#tauri-mobile) — Rust host process + system web view; the mobile target of Tauri 2.
- [Quasar (Vue + Capacitor/Cordova)](#quasar-vue--capacitorcordova) — Vue meta-framework that drives Capacitor or Cordova for you.
- Apache Cordova — the predecessor, in maintenance mode; see [Disregarded / EOL / niche](#disregarded--eol--niche).

### Baseline: PWA

*No shell, no store.* A regular web app installed via "Add to Home Screen". Not an archetype but the control case every row above is measured against: zero packaging, zero app-store review, capped capabilities (notably on iOS).

- [PWA baseline](#pwa-baseline)

## What you actually pick when you pick a framework

When you adopt a stack, you're committing to a tuple:

```
┌─────────────────────────┐
│  Code language          │  ← Swift / Kotlin / TS / Dart / C# / JS / Rust
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  UI framework           │  ← SwiftUI / Compose / RN / Flutter / Capacitor+web
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Rendering pipeline     │  ← Native widgets · Skia/Impeller · Metal · WebView
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Build / package        │  ← .ipa / .aab + signing pipeline
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Distribution           │  ← App Store · Play Store · TestFlight · OTA
└─────────────────────────┘
```

The **language** decision tends to be the most opinionated: if your team is web-shaped (TS/JS), going Swift+Kotlin doubles headcount cost. If you're a games shop already in C++ or a Rust shop, Tauri or native-with-FFI may be the only ergonomic option. Every framework listed below answers "which language" first, then the rest follows.

## Per-framework reviews

### Native: Kotlin / Jetpack Compose (Android)

**[github.com/JetBrains/kotlin](https://github.com/JetBrains/kotlin)** — language · 52.7k★ · Apache-2.0 · latest commit 2026-04-29 · 🟩🟩
**Jetpack Compose** ships under [androidx.compose](https://developer.android.com/jetpack/androidx/releases/compose) — Apache-2.0 · stable 1.11 (April 2026 release) · 🟩🟩

Google's first-party Android stack. Kotlin is the official Android language since 2019; Jetpack Compose is the declarative UI framework that replaced the imperative `View` system, stable since 2021 and the default for new projects since 2024. Compose 1.11 (April 2026) ships v2 testing APIs as default, host-default-providers for cross-module wiring, and Android Studio custom-preview annotations.

- **Archetype:** [Native, one toolchain per OS](#native-one-toolchain-per-os)
- **Targets:** Android (phone / tablet / TV / Wear OS / Auto). iOS *not* supported by stock Compose — Compose Multiplatform is the separate JetBrains project (see below).
- **Code language:** Kotlin (Java still works for legacy code).
- **Rendering model:** native Android widgets (Compose composes into the platform's `RenderNode` pipeline, GPU-accelerated via Skia under the hood).
- **Dev loop:** **hot reload** via Compose Multiplatform tooling and Android Studio `Live Edit` (sub-second for code-only changes). Full rebuild takes 10s–60s+ depending on Gradle config.
- **Native API access:** **first-class** — the entire Android SDK is one `import` away.
- **Facets:** `archetype:native` `lang:kotlin` `render:native` `target:android` `target:other` `api:first-class` `ota:store-only`

**Pick when:** you're Android-only or willing to staff two teams; you want every new platform API on day 0; your performance budget is tightest possible.
**Pass when:** you also need iOS and can't afford two codebases — pick Compose Multiplatform, KMP, or Flutter.

### Native: Swift / SwiftUI (iOS)

**[github.com/swiftlang/swift](https://github.com/swiftlang/swift)** — language · 69.9k★ · Apache-2.0 · latest commit 2026-04-29 · 🟩🟩 · latest release Swift 6.3.1 (2026-04-17)
**SwiftUI** ships inside the iOS / iPadOS / macOS / visionOS / watchOS SDK (closed source, distributed via Xcode).

Apple's first-party iOS stack. Swift 6's strict concurrency is the compiler default in 2026; SwiftUI is the declarative UI baseline for any new app. iOS 26 (the current SDK at snapshot) brought `WebView` / `WebPage`, Foundation Models on-device AI, and Live Activities natively in SwiftUI. Privacy Manifests are a mandatory App Store gate.

- **Archetype:** [Native, one toolchain per OS](#native-one-toolchain-per-os)
- **Targets:** iOS, iPadOS, macOS, visionOS, watchOS, tvOS. Android *not* supported (Apple has no incentive to open this).
- **Code language:** Swift (Objective-C still works for legacy code).
- **Rendering model:** native UIKit-backed render, with Metal under the hood for animations and effects.
- **Dev loop:** Xcode `#Preview` macro renders SwiftUI views in the canvas without launching the simulator (sub-second). Full simulator launch is 5s–30s.
- **Native API access:** **first-class** — every Apple framework (HealthKit, ARKit, Metal, RealityKit, etc.) is one `import` away.
- **Facets:** `archetype:native` `lang:swift` `render:native` `target:ios` `target:desktop` `target:other` `api:first-class` `ota:store-only`

**Pick when:** iOS-first or iOS-only; you need ARKit / Metal / HealthKit / Vision Pro / on-device Apple Intelligence; print-quality animation matters.
**Pass when:** Android matters equally; your team has zero Swift bandwidth.

> **Note:** SwiftUI itself is closed source (no GitHub). Score reflects the open-source Swift compiler + the practical reality that Apple's own framework drives the ecosystem.

### Flutter

**[github.com/flutter/flutter](https://github.com/flutter/flutter)** — 176k★ · BSD-3-Clause · latest commit 2026-04-29 · 🟩🟩 · ~5k+ open issues

Google's Dart-based, Skia-then-Impeller-rendered cross-platform UI toolkit. Owns the *render-the-pixels-ourselves* design intent. Impeller has been the default renderer on iOS since Flutter 3.27 (and on Android API 29+) — it precompiles shaders at build time, eliminating runtime jank, and benchmarks at ~50% lower frame-rasterisation cost on heavy scenes vs the legacy Skia path.

- **Archetype:** [Custom canvas](#custom-canvas)
- **Targets:** iOS, Android, Windows, macOS, Linux, web. *Six* targets from one `.dart` file — the broadest reach in this list.
- **Code language:** Dart (Dart 3+ has sound null-safety, records, patterns, sealed classes — the language has caught up to modern peers).
- **Rendering model:** **custom canvas** (Impeller via Metal on iOS, Vulkan on Android API 29+, OpenGL fallback). Widgets are not real `UILabel` — they are pixels Flutter draws. Pro: identical look across OSes. Con: platform widgets feel slightly "off" to fluent users of either OS.
- **Dev loop:** **stateful hot reload** — Flutter's marquee feature since 2017. Sub-second; preserves widget state on code save.
- **Native API access:** **plugin-mediated** — `flutter_blue_plus`, `geolocator`, `image_picker`, `firebase_*` etc. cover ~95% of common needs; for the long tail you write Dart-Swift / Dart-Kotlin platform channels yourself or use [`pigeon`](https://pub.dev/packages/pigeon) for type-safe codegen.
- **Facets:** `archetype:canvas` `lang:dart` `render:canvas` `target:ios` `target:android` `target:desktop` `target:web` `api:plugin` `ota:third-party`

**README maturity:** 🟩🟩 — full SDK, deep docs at flutter.dev, and the GitHub README itself functions as a guide.

**Pick when:** you want one codebase to ship to both stores *and* desktop *and* web; pixel-perfect brand consistency matters; you're greenfield (no existing native code to integrate); you can absorb Dart on the team.
**Pass when:** native look-and-feel is non-negotiable; you need bleeding-edge platform APIs the day they ship; your team is React-heavy.

### Kotlin Multiplatform + Compose Multiplatform

**[github.com/JetBrains/compose-multiplatform](https://github.com/JetBrains/compose-multiplatform)** — 19k★ · Apache-2.0 · latest commit 2026-04-29 · 🟩🟩 · 0 open issues *(positive signal)* · latest release **1.10.3** (2026-03-19)
Kotlin Multiplatform (KMP) is the underlying capability in the Kotlin compiler ([github.com/JetBrains/kotlin](https://github.com/JetBrains/kotlin) — 52.7k★, see *Native: Kotlin* above). Compose Multiplatform is the UI framework on top.

JetBrains' answer to *one Kotlin codebase, native everywhere*. **KMP** lets you share business logic, networking, persistence, and view-models across iOS, Android, desktop, web, and server (compiles Kotlin to JVM bytecode, native binaries via LLVM, JS, and Wasm). **Compose Multiplatform** then lets you share the UI itself.

**Compose Multiplatform 1.8.0 (May 2025) declared iOS support stable and production-ready.** Render path on iOS uses Skia via Metal; full UIKit interop for accessibility, keyboards, and navigation bars. Stable APIs include Hot Reload on iOS Simulator, Compose Previews, and Xcode integration. As of snapshot the latest stable line is 1.10.x (March 2026).

- **Archetype:** [Custom canvas](#custom-canvas) — on iOS / desktop / web; the Android side is native Jetpack Compose.
- **Targets:** Android, iOS, desktop (JVM-based — Windows / macOS / Linux), web (Wasm / JS via Compose for Web). KMP-only (without Compose UI) also targets server and embedded.
- **Code language:** Kotlin (100%).
- **Rendering model:** Android — native (same as Jetpack Compose). iOS — **custom canvas via Skia + Metal**. Desktop — Skia. Web — Wasm + Canvas/DOM.
- **Dev loop:** Hot Reload on Android Studio; Compose Hot Reload on iOS Simulator (1.8+); Live Edit for desktop.
- **Native API access:** **first-class on Android**; **expect/actual** mechanism for platform-specific code on iOS lets you call Swift/Objective-C APIs through bridge; **plugin-mediated** for cross-platform third-party libraries (the KMP ecosystem on Maven Central is large and growing).
- **Facets:** `archetype:canvas` `lang:kotlin` `render:canvas` `render:native` `target:ios` `target:android` `target:desktop` `target:web` `api:first-class` `ota:store-only`

**Idiomaticity:** 🟩🟩 for Kotlin teams already on Android. iOS developers find the rendering convincing but note it's not UIKit semantically.

**Pick when:** you have an Android team and want to add iOS without doubling headcount; you value JetBrains tooling (IntelliJ / Android Studio); you want to share *some* code (KMP-only) without committing the UI (use SwiftUI on iOS, Compose on Android).
**Pass when:** your team has no Kotlin; you need pixel-perfect platform-native iOS feel (SwiftUI is the only way).

### React Native (bare CLI)

**[github.com/facebook/react-native](https://github.com/facebook/react-native)** — 126k★ · MIT · latest commit 2026-04-29 · 🟩🟩 · 755 open issues · latest release **0.85.2** (2026-04-20)

Meta's JS-bridged-to-native-widgets toolkit, in production at Facebook, Instagram, Discord, Shopify, Microsoft Office, etc. The **New Architecture (Fabric + JSI + TurboModules)** has been the default since RN 0.76, and the legacy bridge was permanently removed in 0.82 — synchronous JS↔native calls, lazy native-module loading, and the React reconciler running natively in C++. Reported real-world gains: ~43% faster cold starts, ~39% faster rendering, ~26% lower memory.

- **Archetype:** [Native widgets from one codebase](#native-widgets-from-one-codebase)
- **Targets:** iOS, Android (first-class). Out-of-tree forks: `react-native-windows` (Microsoft), `react-native-macos` (Microsoft), `react-native-web` (Necolas, web), tvOS, visionOS — all maintained by their respective owners.
- **Code language:** TypeScript (JavaScript still works).
- **Rendering model:** **JS describes UI; native renderer executes** — every `<Text>` becomes a real `UILabel` / Android `TextView`. UI thread separated from JS thread.
- **Dev loop:** **fast refresh** — sub-second for component-tree edits. Native-code edits require rebuild.
- **Native API access:** **plugin-mediated** for the common case (camera, push, secure storage, etc., via npm packages); **first-class** if you reach for the `/ios/` and `/android/` folders that a bare CLI project ships with.
- **Facets:** `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `api:plugin` `ota:third-party`

**Bare vs Expo:** the bare CLI gives you raw `xcodeproj` and `build.gradle` to customise. Comes with no batteries — you wire your own EAS-equivalent CI, OTA story, native-module choices. Recommended only when (a) you need a custom native module you can't ship as an Expo dev-build, or (b) you're integrating RN into an existing native app. **Most new RN projects in 2026 should start with Expo** — see below.

**Pick when:** you have an existing React/web codebase and team; you want real native widgets (not a canvas); you need to integrate into an existing native app.
**Pass when:** you don't have a strong reason to skip Expo (Expo handles bare-workflow tradeoffs out of the box now).

### Expo

**[github.com/expo/expo](https://github.com/expo/expo)** — 49.1k★ · MIT · latest commit 2026-04-29 · 🟩🟩 · 326 open issues

The managed React Native toolchain. Built **on top of** RN — SDK 52+ uses Fabric/TurboModules by default. Adds: a curated set of native modules (Expo Modules), Expo Router (file-system routing inspired by Next.js, default since SDK 50), EAS Build & EAS Submit (cloud-builds + store-submission), EAS Update (OTA JS-bundle pushes without store review), and the Expo Go companion app (scan a QR code, the JS bundle runs on your device — no Xcode/Android Studio needed for the inner loop).

- **Archetype:** [Native widgets from one codebase](#native-widgets-from-one-codebase) — same rendering as React Native.
- **Targets:** iOS, Android, web (via `react-native-web`).
- **Code language:** TypeScript.
- **Rendering model:** same as RN (native widgets via JSI/Fabric).
- **Dev loop:** **fastest of any cross-platform option** — Expo Go shows code edits in <1s on a real device. **Dev clients** unlock custom-native-module use without giving up the loop.
- **Native API access:** **plugin-mediated** via Expo Modules (camera, notifications, file system, sensors, secure store, biometric, av, etc.) — covers ~90% of apps. For everything else, a **dev build** is one `eas build --profile development` away, then any RN community module works. The old "managed vs bare" wall came down with Continuous Native Generation (CNG) and prebuild.
- **Facets:** `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `target:web` `api:plugin` `ota:built-in`

**Idiomaticity:** 🟩🟩 — feels like Next.js + RN with the rough edges sanded.

**Pick when:** you're starting a new React Native app in 2026 (default recommendation); solo / small team; you value time-to-first-build over fine-grained native-tooling control.
**Pass when:** you're embedding RN into an existing native app (use the bare CLI); you have heavy custom-native-toolchain needs that fight CNG.

### .NET MAUI

**[github.com/dotnet/maui](https://github.com/dotnet/maui)** — 23.2k★ · MIT · latest commit 2026-04-29 · 🟩🟩 · ~3.7k open issues · latest release **.NET 10 SR6 (10.0.60)** (2026-04-29)

Microsoft's .NET 6+ **successor to Xamarin**. Xamarin reached end-of-support **2024-05-01** — no further updates, bug fixes, or security patches. MAUI carries the same single-C#-codebase idea forward, abstracts over the platforms via a unified API surface, and renders to **native widgets** on each OS (no canvas, unlike Flutter).

- **Archetype:** [Native widgets from one codebase](#native-widgets-from-one-codebase) — via a C# abstraction layer, not a JS bridge.
- **Targets:** Android, iOS, macOS (Catalyst), Windows (WinUI 3). Linux and tvOS are community-driven only.
- **Code language:** C# + XAML for declarative UI (or C# Markup for code-only).
- **Rendering model:** **native widgets via abstraction** — a `<Button>` becomes a `UIButton`, an `Android.Widget.Button`, a `Microsoft.UI.Xaml.Controls.Button`. Single-handler types in your code; per-platform handlers under the hood.
- **Dev loop:** **XAML Hot Reload** + **C# Hot Reload** in Visual Studio (and partially in Rider). Full rebuild is the norm for non-XAML code edits.
- **Native API access:** **first-class** — direct P/Invoke and platform-specific `#if ANDROID` / `#if IOS` guards let you reach raw Android/iOS APIs from the same C# project.
- **Facets:** `archetype:widgets` `lang:csharp` `render:native` `target:ios` `target:android` `target:desktop` `api:first-class` `ota:store-only`

**Idiomaticity:** 🟩🟩 for C#/.NET teams; 🟩 for newcomers (XAML has a steep curve).

**Pick when:** you're a .NET shop with existing C# expertise; you want native widgets (not a canvas); you also need a Windows desktop app from the same code.
**Pass when:** your team isn't on .NET (the ramp-up cost dominates); the high open-issue count concerns you (3.7k is the second-highest in this article — though commensurate with project age and surface area).

### NativeScript

**[github.com/NativeScript/NativeScript](https://github.com/NativeScript/NativeScript)** — 25.5k★ · MIT · latest commit 2026-04-29 · 🟩🟩 · 773 open issues · latest core **9.0.18** (2026-03-30)

The "JavaScript directly calls native APIs, no bridge" school. Distinguishes itself from React Native by exposing the **entire** iOS / Android API surface to JavaScript with no allow-list — you can call `UIView.alloc().init()` from TS at runtime. UI framework choice is plug-in: Angular, Vue, Svelte, React, SolidJS, or plain.

- **Archetype:** [Native widgets from one codebase](#native-widgets-from-one-codebase)
- **Targets:** iOS, Android, visionOS.
- **Code language:** TypeScript.
- **Rendering model:** **native widgets** (real `UIView` / Android `View` instantiated from JS via metadata-driven runtime).
- **Dev loop:** Hot Module Replacement; live sync to device.
- **Native API access:** **first-class** via reflective bridge — every public iOS/Android class is callable from TS without a community plugin, although typings come from a separate generation step.
- **Facets:** `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `target:other` `api:first-class` `ota:third-party`

**Idiomaticity:** 🟩 — neither feels "exactly like web React" nor "exactly like native iOS"; sits in its own corner. Strong loyalty in its existing user base; less mainstream pull than RN or Flutter.

**Pick when:** you need raw native API surface from JS without writing a plugin for every API; you're comfortable with the smaller ecosystem; you're building an Angular/Vue mobile app and want native widgets.
**Pass when:** you want the largest mobile community and plugin ecosystem (RN/Expo win); you don't need direct API access (Capacitor is simpler).

### Lynx

**[github.com/lynx-family/lynx](https://github.com/lynx-family/lynx)** — 14.8k★ · Apache-2.0 · latest commit 2026-04-29 · 🟩🟩 · 175 open issues · latest release **3.7.0** (2026-03-31)

ByteDance's cross-platform UI framework, **open-sourced March 2025** under the Lynx Family umbrella. In production at TikTok — **Search, Shop, Live, and the entire TikTok Studio app** ship on Lynx. Distinguishing technical bet: a **dual-thread** JS engine (PrimJS, ByteDance's V8 alternative) where one thread handles framework code and another handles user-script code, designed to keep the UI thread responsive during heavy work.

- **Archetype:** [Native widgets from one codebase](#native-widgets-from-one-codebase) — native views by default; a custom-canvas renderer is optional.
- **Targets:** Android (API 21+), iOS (10+), web. Harmony / Windows / macOS exist in the codebase but mobile + web are the marketed surface.
- **Code language:** TypeScript via **ReactLynx** (React-flavour) or **Lynx for Web** (web-component-flavour). Build tooling is **Rspeedy** — Rspack-based, Rust-backed.
- **Rendering model:** dual choice — **native rendering** on Android/iOS/Web (each platform's native view system), or **custom renderer** for pixel-perfect cross-platform consistency.
- **Dev loop:** Rspack-fast HMR.
- **Native API access:** plugin-mediated; younger than RN's ecosystem but documented and growing.
- **Facets:** `archetype:widgets` `lang:ts` `render:native` `render:canvas` `target:ios` `target:android` `target:web` `api:plugin` `ota:diy`

**Idiomaticity:** 🟩🟩 for React-shaped teams; 🟩 in absolute terms (the framework conventions and CSS layer are Lynx-specific).

**README maturity:** 🟩 — taglines + concept overview; the README points to lynxjs.org for the full guide.

**Pick when:** you want a fresh, performance-engineered alternative to RN; you're building a content/feed-heavy app where the dual-thread story matters; you can absorb being early-adopter on a tooling stack.
**Pass when:** you need a battle-tested ecosystem of third-party UI libraries today; ByteDance organisational risk is a concern for your industry.

### Solito (Solid + Expo / RN + Next.js)

**[github.com/nandorojo/solito](https://github.com/nandorojo/solito)** — 4.1k★ · MIT · latest release **v5: Next.js 16 + Expo 54** (2025-10-21) · 18 open issues

> **Naming clarification:** the brief asked about *"Solid Mobile / Solito (Solid+Expo)"*. **Solito** is **not** SolidJS-related — the name comes from "solo + tito"; it's specifically a **React Native + Next.js unifier** for shared code across iOS/Android/web. SolidJS *also* runs on Capacitor (template repo: [`ionic-team/capacitor-solidjs-templates`](https://github.com/ionic-team/capacitor-solidjs-templates)) and via community projects like [`tjjfvi/solid-native`](https://github.com/tjjfvi/solid-native) and [`NathanWalker/solid-x-platforms`](https://github.com/NathanWalker/solid-x-platforms), but there is no flagship "Solid Mobile" framework in 2026 with comparable traction. Reviewing the *Solito* row instead since that's the higher-traction tool.

Solito gives you one navigation API (under the hood: React Navigation on mobile, Next.js App Router on web) and one set of screens that work in both an Expo iOS/Android app and a Next.js website. As of v5 (Oct 2025) it's positioned as **web-first** — primary product is the web; mobile is a parallel target sharing the same screens.

- **Archetype:** [Native widgets from one codebase](#native-widgets-from-one-codebase) — inherited from Expo / React Native.
- **Targets:** iOS / Android (via Expo / RN) + web (via Next.js).
- **Code language:** TypeScript.
- **Rendering model:** native widgets on mobile (via RN); HTML/CSS on web.
- **Dev loop:** native to each side (Expo for mobile, Next.js for web).
- **Native API access:** mobile side inherits Expo's surface.
- **Facets:** `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `target:web` `api:plugin` `ota:built-in`

**Pick when:** you specifically need to share React screens between a Next.js website and an Expo app in a monorepo; team is already React-fluent.
**Pass when:** you don't have the web-and-mobile-shared-screens problem; you're on Vue/Solid/Svelte (Solito is React-only).

### Capacitor (+ Ionic UI)

**[github.com/ionic-team/capacitor](https://github.com/ionic-team/capacitor)** — 15.5k★ · MIT · latest commit 2026-04-29 · 🟩🟩 · 73 open issues · latest release **8.3.1** (2026-04-16)
**[github.com/ionic-team/ionic-framework](https://github.com/ionic-team/ionic-framework)** — 52.5k★ · MIT · latest commit 2026-04-29 · 🟩🟩 · 596 open issues · latest release **v8.8.5** (2026-04-29)

Ionic's pair: **Capacitor** is the native runtime (the WebView wrapper + plugin bridge); **Ionic Framework** is the optional UI-component library (Web Components: `ion-button`, `ion-tabs`, etc.) that gives you OS-styled mobile UI. They're decoupled — you can ship a Capacitor app with React + your own UI, or with vanilla web + Ionic.

- **Archetype:** [Web view shell](#web-view-shell)
- **Capacitor's positioning:** *successor to Cordova*. Same idea (web app inside `WKWebView`/`Android WebView`), much fresher implementation: TypeScript-first plugin API, Swift/Kotlin native shells, modern build tooling. The relationship is acknowledged but Capacitor is the recommended path for new projects.
- **Targets:** iOS, Android, web (the same web bundle just runs in a browser). Electron support exists but is community-maintained.
- **Code language:** TypeScript / JavaScript with any web framework — React, Vue, Angular, Svelte, SolidJS, plain. Capacitor doesn't care.
- **Rendering model:** **web view** — `WKWebView` on iOS, system WebView on Android. UI is HTML/CSS rendered by the OS browser engine.
- **Dev loop:** **sub-second** — your dev server's HMR works inside the WebView. Live-reload over LAN to a real device is built in.
- **Native API access:** **plugin-mediated** — official plugins (Camera, Filesystem, Geolocation, Push Notifications, Haptics, Preferences, Network, etc.) plus a healthy community ecosystem. Custom plugins are Swift/Kotlin classes with TS bindings; well-documented. For raw Swift/Kotlin you can drop into the platform projects.
- **Facets:** `archetype:webview` `lang:ts` `lang:web` `render:webview` `target:ios` `target:android` `target:web` `api:plugin` `ota:built-in`

**Idiomaticity:** 🟩🟩 if you're a web team — your tooling, your routing, your component model all carry over. 🟥 in the sense that the UX is "browser-feeling" unless you specifically style for native (Ionic's components do this for you).

**Pick when:** you have an existing web app or web team; you want one codebase across web + iOS + Android; performance budget allows a WebView (most line-of-business apps); you want to ship a PWA + an app-store binary from the same source.
**Pass when:** you need 60+fps custom animation; you need raw OpenGL/Metal access; users care about "feels native" at the haptic level.

### Tauri Mobile

**[github.com/tauri-apps/tauri](https://github.com/tauri-apps/tauri)** — 106k★ · Apache-2.0 + MIT (dual) · latest commit 2026-04-29 · 🟩🟩 · ~1.3k open issues · latest CLI 2.10.1 (2026-03-04)

Tauri 2 stabilised mobile (Android 7+, iOS 9+) as part of the 2.0 release. The architecture is **Rust core + system WebView** (WKWebView on iOS, Android System WebView on Android) — same idea as Capacitor's web view, but the host process is Rust, not Swift/Kotlin/Java.

- **Archetype:** [Web view shell](#web-view-shell) — Rust host process instead of Swift/Kotlin; the rendering is the same system web view as Capacitor.
- **Targets:** Windows, macOS, Linux (mature) + Android, iOS (stable but younger). The marquee story is *one Rust core + one web frontend → all five platforms*, but mobile feature-parity with desktop is **not yet complete** (the Tauri team has explicitly said mobile is improving in minor releases, not "first-class on day 1 of 2.0").
- **Code language:** Rust (core) + any web framework on the front-end (React, Vue, Svelte, SolidJS, Leptos, Yew if you go full-Rust).
- **Rendering model:** **web view** for UI; native window chrome via `tao` window manager on each platform.
- **Dev loop:** **HMR extended to mobile** in Tauri 2 — your web framework's hot reload works on emulators and devices.
- **Native API access:** **plugin-mediated** via Tauri's plugin system; mobile-plugin development is documented but the catalogue is younger than Capacitor's. You can drop into Swift / Kotlin for custom mobile plugins.
- **Facets:** `archetype:webview` `lang:rust` `lang:web` `render:webview` `target:ios` `target:android` `target:desktop` `api:plugin` `ota:diy`

**Idiomaticity:** 🟩 (mobile) / 🟩🟩 (desktop). Mobile is the newer surface; the team flags it as still maturing.

**Pick when:** you're already a Rust shop or want to be; you value tiny binary size and security-by-default; your desktop app needs a mobile companion and you'd rather one stack than two.
**Pass when:** you need polished, first-class mobile from day 0 — Capacitor / Expo / Flutter are more mature on mobile specifically. Wait one more minor cycle if you can.

### Quasar (Vue + Capacitor/Cordova)

**[github.com/quasarframework/quasar](https://github.com/quasarframework/quasar)** — 27.1k★ · MIT · latest commit 2026-04-29 · 🟩🟩 · 563 open issues · latest **quasar-v2.19.3** (2026-04-06)

Vue.js-centric meta-framework that builds **SPA, SSR, PWA, browser extension, Electron, and hybrid mobile** from one Vue codebase. The mobile path is *Quasar's CLI invokes Capacitor or Cordova for you*, then your Vue app runs inside the WebView. Extensive built-in component library tuned for both web and mobile.

- **Archetype:** [Web view shell](#web-view-shell) — via Capacitor or Cordova.
- **Targets:** Web (SPA / SSR / PWA / browser extension), desktop (Electron), iOS / Android (via Capacitor or Cordova).
- **Code language:** Vue 3 + TypeScript / JavaScript.
- **Rendering model:** WebView on mobile (Capacitor/Cordova-mediated).
- **Dev loop:** Vite-based HMR.
- **Native API access:** plugin-mediated via Capacitor's plugin ecosystem.
- **Facets:** `archetype:webview` `lang:ts` `lang:web` `render:webview` `target:ios` `target:android` `target:desktop` `target:web` `api:plugin` `ota:third-party`

**Idiomaticity:** 🟩🟩 for Vue teams; one of the deepest "build-target abstraction" layers in the JS world.

**Pick when:** you're a Vue shop; you want PWA + native + desktop + web from one codebase; you value batteries-included (component library + build pipelines for every target).
**Pass when:** you don't use Vue; performance budget rules out a WebView.

### PWA baseline

Not a framework but the control case. A **Progressive Web App** is a regular web app + a `manifest.json` + a service worker. Users install it via "Add to Home Screen". **Zero packaging, zero app-store review, zero native build.**

iOS reality in 2026 (per snapshot research):
- Installable from Safari/Chrome/Edge/Firefox/Orion via Share menu (iOS 16.4+, the install flow is multi-step and discoverability is poor).
- **Push notifications** work on iOS 16.4+ (only after Home-Screen install) outside the EU; in the EU since the DMA changes, PWAs open in Safari tabs and push is essentially gone.
- **Capability gaps that don't have a path to closure on iOS:** Web Bluetooth, Web NFC, WebUSB, Web Serial, Web MIDI, Battery Status, most non-basic sensors. Apple has refused to implement these citing fingerprinting/privacy.
- App Store visibility: none. Discovery is web-search-based.

Android reality in 2026:
- Trusted Web Activities (TWAs) let you ship a PWA *into the Play Store* as a near-native experience.
- Most Web APIs that iOS blocks (Bluetooth, NFC, USB) work on Android Chrome.

- **Archetype:** [Baseline: PWA](#baseline-pwa) — the control case, not an archetype.
- **Targets:** any modern browser (web). "Mobile" is a question of how it feels on a small screen + whether install prompts work.
- **Code language:** any web stack.
- **Rendering model:** browser engine.
- **Dev loop:** standard web dev — sub-second HMR with any modern bundler.
- **Native API access:** **limited** to the Web Platform's API surface. No WebKit-blocked APIs on iOS.
- **Facets:** `archetype:pwa` `lang:web` `render:browser` `target:web` `api:limited` `ota:live`

**Pick when:** the app is information-display + forms + light interactivity; iOS hardware-API gaps are tolerable; you want zero app-store friction; you want one URL to rule them all.
**Pass when:** the app needs Bluetooth peripherals, NFC, USB, or any blocked-on-iOS API; push reach in the EU matters; you need App Store presence for credibility.

## Big comparison table

| Dimension | [Native Android (Kotlin)](#native-kotlin--jetpack-compose-android) | [Native iOS (Swift)](#native-swift--swiftui-ios) | [Flutter](#flutter) | [Compose MP / KMP](#kotlin-multiplatform--compose-multiplatform) | [React Native (CLI)](#react-native-bare-cli) | [Expo](#expo) | [.NET MAUI](#net-maui) | [NativeScript](#nativescript) | [Lynx](#lynx) | [Solito](#solito-solid--expo--rn--nextjs) | [Capacitor](#capacitor--ionic-ui) | [Tauri 2 Mobile](#tauri-mobile) | [Quasar](#quasar-vue--capacitorcordova) | [PWA](#pwa-baseline) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Archetype** | [Native](#native-one-toolchain-per-os) | [Native](#native-one-toolchain-per-os) | [Canvas](#custom-canvas) | [Canvas](#custom-canvas) | [Widgets](#native-widgets-from-one-codebase) | [Widgets](#native-widgets-from-one-codebase) | [Widgets](#native-widgets-from-one-codebase) | [Widgets](#native-widgets-from-one-codebase) | [Widgets](#native-widgets-from-one-codebase) | [Widgets](#native-widgets-from-one-codebase) | [Web view](#web-view-shell) | [Web view](#web-view-shell) | [Web view](#web-view-shell) | [PWA](#baseline-pwa) |
| **Stars** | 52.7k (Kotlin) · 🟩🟩 | 69.9k (Swift) · 🟩🟩 | 176k · 🟩🟩 | 19k (CMP) · 🟩🟩 | 126k · 🟩🟩 | 49.1k · 🟩🟩 | 23.2k · 🟩🟩 | 25.5k · 🟩🟩 | 14.8k · 🟩🟩 | 4.1k · 🟩 | 15.5k · 🟩🟩 | 106k · 🟩🟩 | 27.1k · 🟩🟩 | n/a (web standard) |
| **License** | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 | BSD-3 · 🟩 | Apache-2.0 · 🟩 | MIT · 🟩 | MIT · 🟩 | MIT · 🟩 | MIT · 🟩 | Apache-2.0 · 🟩 | MIT · 🟩 | MIT · 🟩 | Apache-2.0 + MIT · 🟩 | MIT · 🟩 | n/a |
| **Latest commit** | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | ⬜ † | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | 2026-04-29 · 🟩🟩 | n/a |
| **Latest release** | Compose 1.11 (Apr 2026) | Swift 6.3.1 (2026-04-17) | (rolling) | CMP 1.10.3 (2026-03-19) | 0.85.2 (2026-04-20) | (SDK 53+) | .NET 10 SR6 (2026-04-29) | core 9.0.18 (2026-03-30) | 3.7.0 (2026-03-31) | v5 (2025-10-21) | 8.3.1 (2026-04-16) | CLI 2.10.1 (2026-03-04) | 2.19.3 (2026-04-06) | n/a |
| **Open issues** | n/a § | 5k+ ‡ | 5k+ | 0 (CMP) ¶ | 755 | 326 | 3.7k | 773 | 175 | 18 | 73 | 1.3k | 563 | n/a |
| **README maturity** | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | n/a |
| **Idiomaticity** | 🟩🟩 (for Android) | 🟩🟩 (for iOS) | 🟩 (Dart-shaped) | 🟩🟩 (for Kotlin) | 🟩🟩 (for React) | 🟩🟩 (for React) | 🟩🟩 (for .NET) | 🟩 (own corner) | 🟩🟩 (for React) | 🟩 (RN+Next niche) | 🟩🟩 (for web) | 🟩 (mobile young) | 🟩🟩 (for Vue) | 🟩🟩 (for web) |
| **Targets** | Android (phone / tablet / TV / Wear / Auto) | iOS / iPadOS / macOS / visionOS / watchOS / tvOS | iOS / Android / Windows / macOS / Linux / web | iOS / Android / desktop / web | iOS / Android (+forks: Win/Mac/web/visionOS/tvOS) | iOS / Android / web | Android / iOS / macOS / Windows | iOS / Android / visionOS | Android / iOS / web (+ Harmony/Win/Mac in tree) | iOS / Android (RN) + web (Next.js) | iOS / Android / web (+ Electron community) | Win / Mac / Linux / iOS / Android | Web / SPA / SSR / PWA / Electron / iOS / Android | any browser |
| **Code language** | Kotlin | Swift | Dart | Kotlin | TS | TS | C# + XAML | TS | TS (ReactLynx) | TS | TS / JS (any web framework) | Rust + web frontend | Vue 3 + TS | any web stack |
| **Rendering** | native (Compose + Skia) | native (UIKit + Metal) | custom canvas (Impeller) | Android: native; iOS: Skia/Metal; Web: Wasm | native via JS bridge | native via JS bridge | native (per-platform handlers) | native (reflective bridge) | dual: native or custom canvas | native (mobile) + HTML (web) | web view | web view | web view | browser engine |
| **Dev loop** | Live Edit (sub-s); rebuild 10–60s | Xcode `#Preview` (sub-s); rebuild 5–30s | hot reload (sub-s, stateful) | Compose Hot Reload | fast refresh (sub-s) | Expo Go (<1s on device) | XAML/C# Hot Reload | HMR + live-sync | Rspack HMR | Expo + Next.js HMR | HMR + LAN live-reload | HMR on emulator/device | Vite HMR | bundler HMR |
| **Native API access** | first-class | first-class | plugin-mediated | first-class (Android) + expect/actual (iOS) | plugin-mediated + drop to native | plugin-mediated (dev build for custom) | first-class (P/Invoke + `#if`) | first-class (reflective) | plugin-mediated | inherits Expo | plugin-mediated | plugin-mediated | plugin-mediated (Capacitor) | limited (Web APIs only) |
| **OTA updates** | Play Store + Play in-app updates | App Store only | OTA via [Shorebird](https://shorebird.dev/) | Android: Play Store; iOS: App Store | OTA via CodePush / EAS Update | **EAS Update** built-in | App Store only | OTA possible via plugins | OTA via Lynx infra | inherits Expo | **AppFlow** (Ionic SaaS) | not native (custom) | Capacitor → AppFlow | n/a (live web) |
| **First release** | 2011 (Kotlin) · 2021 (Compose stable) | 2014 (Swift) · 2019 (SwiftUI) | 2017 | 2023 (CMP iOS alpha) · 2025 (CMP iOS stable) | 2015 | 2017 | 2022 (.NET 6) | 2014 | 2025 (open source) | 2020 | 2019 (Cordova succ.) | 2020 (1.0); 2024 (2.0 mobile) | 2016 | 2015 (PWA term) |
| **Facets** | `archetype:native` `lang:kotlin` `render:native` `target:android` `target:other` `api:first-class` `ota:store-only` | `archetype:native` `lang:swift` `render:native` `target:ios` `target:desktop` `target:other` `api:first-class` `ota:store-only` | `archetype:canvas` `lang:dart` `render:canvas` `target:ios` `target:android` `target:desktop` `target:web` `api:plugin` `ota:third-party` | `archetype:canvas` `lang:kotlin` `render:canvas` `render:native` `target:ios` `target:android` `target:desktop` `target:web` `api:first-class` `ota:store-only` | `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `api:plugin` `ota:third-party` | `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `target:web` `api:plugin` `ota:built-in` | `archetype:widgets` `lang:csharp` `render:native` `target:ios` `target:android` `target:desktop` `api:first-class` `ota:store-only` | `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `target:other` `api:first-class` `ota:third-party` | `archetype:widgets` `lang:ts` `render:native` `render:canvas` `target:ios` `target:android` `target:web` `api:plugin` `ota:diy` | `archetype:widgets` `lang:ts` `render:native` `target:ios` `target:android` `target:web` `api:plugin` `ota:built-in` | `archetype:webview` `lang:ts` `lang:web` `render:webview` `target:ios` `target:android` `target:web` `api:plugin` `ota:built-in` | `archetype:webview` `lang:rust` `lang:web` `render:webview` `target:ios` `target:android` `target:desktop` `api:plugin` `ota:diy` | `archetype:webview` `lang:ts` `lang:web` `render:webview` `target:ios` `target:android` `target:desktop` `target:web` `api:plugin` `ota:third-party` | `archetype:pwa` `lang:web` `render:browser` `target:web` `api:limited` `ota:live` |

> † Solito's commit cadence is slower than mobile-framework norms; the v5 release in Oct 2025 is the most-recent visible activity peg. Repo is maintained but not daily-updated.
>
> ‡ Apple Swift's open-source repo issue tracker covers compiler/runtime/standard-library work, not SwiftUI itself (closed source). Volume is commensurate with project age and surface area; not a maintenance red flag.
>
> § Jetpack Compose ships through `androidx.compose` artifacts on Maven Central; bug tracker is Google's Issue Tracker (issuetracker.google.com), not GitHub Issues. Open count not directly comparable to a GitHub Issues number.
>
> ¶ Compose Multiplatform repo's GitHub Issues tab shows zero — issue tracking happens on JetBrains' YouTrack. Treat as ⬜ semantics-wise (zero-on-GitHub is a venue artifact, not a quality signal).

## Disregarded / EOL / niche

These are real options but explicitly **not recommended for new 2026 projects** — included so the corner is auditable.

| Tool | Reason |
| --- | --- |
| **Xamarin** ([dotnet/xamarin-forms](https://github.com/dotnet/xamarin-forms)) | **End-of-support 2024-05-01.** No further updates / security patches from Microsoft. Migrate to .NET MAUI. |
| **Apache Cordova** ([apache/cordova](https://github.com/apache/cordova)) | Predecessor to Capacitor. Apache organisation maintains the platforms (`cordova-android` 15.0.0 in Feb 2026 · 🟩, `cordova-ios` 8.0.1 in Mar 2026 · 🟩) but the **central `apache/cordova` umbrella has not been touched since 2026-02-22**, and several core plugins + the Windows / WebOS / Ubuntu / Tizen platforms are deprecated. New projects should choose **Capacitor** instead — same idea, modern implementation, active core team. Cordova remains in maintenance for the existing install base. |
| **Sencha Ext JS** | Commercial / proprietary licence (no OSS option); enterprise-scale UI framework with merged Sencha Touch (mobile) since v6.0 (2015). Still actively marketed in 2026 (114+ pre-built components, "trusted by 80% of Fortune 100"), but the licence model and lock-in put it outside the OSS-first scope of this article. **Pick if** you're already a Sencha shop with enterprise data-grids; **pass otherwise**. |
| **Appcelerator Titanium / TiDev** ([tidev/titanium-sdk](https://github.com/tidev/titanium-sdk)) | 2.8k★ · Apache-2.0 · latest release 13.2.0.GA (2026-04-08) · 🟩. **Axway officially discontinued the commercial Appcelerator offering in 2024.** The codebase was open-sourced and is now community-maintained by the TiDev non-profit. Active but small (~104 open issues, 36k commits over its history); ecosystem reach has shrunk dramatically. **Pick if** you're already on Titanium; **don't start a new project here** — RN/Expo/Capacitor have far larger ecosystems for the same JS-to-native value proposition. |
| **PhoneGap** | Adobe's commercial Cordova distribution. Sunset by Adobe in 2020. Use Cordova → preferably Capacitor. |
| **"Solid Mobile"** | No flagship framework with this name in 2026. SolidJS reaches mobile via [Capacitor's SolidJS templates](https://github.com/ionic-team/capacitor-solidjs-templates) (recommended path), via the community [solid-native](https://github.com/tjjfvi/solid-native) experiment, or via [solid-x-platforms](https://github.com/NathanWalker/solid-x-platforms). None of these have meaningful traction yet. SolidJS users wanting native should treat **Capacitor + SolidJS** as the production path. |
| **JetBrains compose-multiplatform-ios-android-template** ([JetBrains/compose-multiplatform-ios-android-template](https://github.com/JetBrains/compose-multiplatform-ios-android-template)) | **Archived 2023-12-20.** Replaced by the JetBrains Kotlin Multiplatform wizard. Listed only because it appears in search results for "Compose Multiplatform iOS template" and would otherwise mislead. |

## Leaderboards (per use-case)

The 7-dim score is comparable within an archetype, not across — a Capacitor app and a Flutter app are not playing the same game. These leaderboards collapse along **task** instead.

### Maximum native fidelity (every platform pixel + every new SDK on day 0)

1. 🥇 **Native (Swift+SwiftUI / Kotlin+Compose)** — by definition. New Apple/Google APIs land here first.
2. 🥈 **Compose Multiplatform** — Android side is Jetpack Compose verbatim; iOS side renders convincingly via Skia/Metal but is not literally UIKit. Closer to native than Flutter on Android, comparable on iOS.
3. 🥉 **.NET MAUI** — uses real platform widgets, but the abstraction layer means new platform APIs land in C# bindings later than native.
4. **Flutter** — a pixel-perfect canvas; widgets are *Flutter's*, not the platform's. "Native fidelity" is intentional brand consistency, not platform-mimicry.
5. **NativeScript / React Native / Lynx** — real native widgets but JS-bridged; ergonomic gap vs first-party.

### Maximum code reuse with an existing web team

1. 🥇 **Capacitor (+ Ionic UI optional)** — your TS/JS web app is the mobile app. The shortest distance from "we have a web app" to "we have an app-store binary". Works with React, Vue, Angular, Svelte, SolidJS, plain.
2. 🥈 **Expo** — react-flavoured, separate codebase from your website but the same team profile. EAS Update, Expo Router, OTA story all polished.
3. 🥉 **Quasar** — if you're a Vue shop, Quasar's "build SPA + PWA + Capacitor mobile + Electron from one codebase" story is unmatched.
4. **NativeScript / Lynx** — same JS skill carryover but smaller ecosystems.
5. **Tauri Mobile** — viable if your web frontend is already paired with a Rust backend you want to reuse on-device.

### Best dev experience for solo / small team / fastest time-to-first-app-store-build

1. 🥇 **Expo** — `npx create-expo-app && eas build && eas submit` and you're in TestFlight in an afternoon. Expo Go for instant device preview without setting up Xcode/Android Studio.
2. 🥈 **Flutter** — `flutter create && flutter run` and you're on emulator in a minute. Stateful hot reload is the gold standard.
3. 🥉 **Capacitor + a React/Vue/Svelte starter** — your web bundler's HMR + `npx cap sync ios` and you're in Xcode.
4. **Compose Multiplatform** — the JetBrains Wizard is one click; Android Studio + Xcode bridge is more setup than Expo.
5. **Native** — slowest to start (two codebases) but fastest *per-platform* once set up.

### Performance-sensitive / animation-heavy / games-adjacent

1. 🥇 **Native (Swift+SwiftUI / Kotlin+Compose)** — uncontested on raw frame budgets, GPU access (Metal / Vulkan), and on-device AI (Core ML / NNAPI / Foundation Models on iOS 26).
2. 🥈 **Flutter (Impeller)** — 90–120fps stable on Impeller-default builds; precompiled shaders mean no runtime jank. The strongest non-native option for animation-heavy UI.
3. 🥉 **Compose Multiplatform** — Android side inherits Compose's perf; iOS side via Skia/Metal is competitive but younger.
4. **Lynx** — dual-thread design is performance-engineered; in-production at TikTok at scale. Promising but newer.
5. **React Native (post-Fabric)** — ~43% faster cold starts and ~39% faster rendering vs legacy bridge per reported migrations. Closes a lot of the historical gap with native.
6. **Capacitor / Tauri / Quasar / PWA** — WebView ceiling. Fine for line-of-business, not for 60+fps custom canvas work.

### Maximum platform reach from one codebase (iOS + Android + desktop + web all in scope)

1. 🥇 **Flutter** — six targets (iOS, Android, Windows, macOS, Linux, web). The only stack hitting all five OSes + browser from one Dart file.
2. 🥈 **Compose Multiplatform** — Android, iOS, desktop (Windows/macOS/Linux via JVM), web (Wasm). Five surfaces.
3. 🥉 **Tauri 2** — Windows, macOS, Linux, iOS, Android. Mature on desktop, maturing on mobile.
4. **.NET MAUI** — Android, iOS, macOS, Windows. No web target; Linux community-only.
5. **Quasar** — adds web variants (SPA / SSR / PWA / browser extension / Electron) on top of iOS/Android. Different shape: one *web* codebase, many web-aware targets.

### Existing Rust / .NET / Kotlin shop wanting mobile

| Existing skill | Best fit | Reasoning |
| --- | --- | --- |
| **Rust** | Tauri Mobile | Only major option that puts Rust at the application core. |
| **.NET / C#** | .NET MAUI | First-party; closest to "your existing C# library just runs". |
| **Kotlin / Android team** | Compose Multiplatform | KMP shares logic, CMP shares UI, Android stays Compose-native. |
| **TypeScript / React** | Expo | Lowest friction; managed cloud-build path. |
| **TypeScript / Vue** | Quasar (or Capacitor + Vue) | Vue ecosystem stays Vue. |
| **TypeScript / Solid / Svelte** | Capacitor + your framework | Capacitor doesn't care which web framework wraps the WebView. |
| **Dart** *(rare as a starting point)* | Flutter | Only major mobile target for Dart. |
| **Swift / Kotlin separately** | Native, both stacks | If you have headcount, this is the highest-fidelity outcome. |

### "I want to get to push notifications, OTA, and TestFlight in one weekend"

1. 🥇 **Expo** — `eas build`, `eas submit`, and `eas update` cover all three out of the box.
2. 🥈 **Capacitor + Ionic AppFlow** — same idea, web-flavoured.
3. 🥉 **Flutter + Shorebird** — Shorebird is the OTA layer for Flutter; integrates cleanly.
4. **Native** — TestFlight + Play Internal Testing are first-party but you wire OTA yourself (or skip — store updates only).

## Cross-link

> See also: **[Building mobile apps with Gleam](../gleam/mobile-apps.md)** — how Gleam (a BEAM/JS-target language) reaches mobile via JS-compile + Capacitor / Tauri rather than via a native compile target. The Gleam → JS pipeline produces a TypeScript-shaped output that any of the **Capacitor**, **Tauri**, **Expo** (with caveats) or **Quasar** routes in this article can wrap. There is no native Gleam-on-iOS or Gleam-on-Android compile target; the route is always *Gleam → JS → web shell → native packaging*.

Other adjacent ecosystem reviews:

- [Web apps in Gleam](../gleam/web-and-http/web-apps.md) — full-stack frameworks, the input side of any Gleam-on-mobile pipeline.
- [Diagramming tools](../design/diagramming.md) — sister cross-ecosystem survey under a top-level dir, same style.
- [BDD with Gherkin](../practices/bdd-with-gherkin.md) — sister cross-ecosystem survey for testing.

## Discovery — search queries

Searches that surfaced the candidates above (so the next snapshot can re-run the sweep):

- `mobile app framework 2026 cross-platform`
- `Tauri 2 mobile iOS Android stability 2026`
- `Compose Multiplatform iOS stable 2025 release`
- `Lynx ByteDance open source mobile framework 2026`
- `.NET MAUI vs Xamarin retirement 2024 successor`
- `Solito Solid mobile React Native Expo 2026`
- `Solid Mobile SolidJS native iOS Android 2026`
- `Sencha Ext JS mobile framework 2026 status`
- `Appcelerator Titanium TiDev mobile framework 2026 status`
- `PWA Progressive Web Apps iOS install 2026 capabilities`
- `React Native New Architecture Fabric TurboModules 2026 status`
- `React Native CLI bare workflow 2026 vs Expo`
- `Flutter Impeller renderer Skia replaced 2026`
- `Jetpack Compose Android 2026 stable release`
- `SwiftUI 2026 latest version iOS development`

Frameworks intentionally excluded from this snapshot (out-of-scope, not "disregarded"):

- **Cross-platform game engines** (Unity, Unreal, Godot, Cocos, defold) — different design intent.
- **2D game frameworks** (LÖVE, Bevy, raylib mobile bindings) — different design intent.
- **Backend-as-a-service / mobile BaaS** (Firebase, Supabase, Appwrite, Convex) — orthogonal layer; can pair with any framework above.
- **In-app low-code builders** (FlutterFlow, Glide, Bubble) — code-generators, not frameworks.
- **Component-library-only** (NativeBase, Tamagui, gluestack) — UI libraries on top of RN/Expo, not standalone frameworks.
