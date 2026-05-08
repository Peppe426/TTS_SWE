---
name: maui-app-lifecycle
description: >
  .NET MAUI app lifecycle guidance covering the four execution states,
  cross-platform Window lifecycle events, platform event mapping, and
  state-preservation patterns.
  USE FOR: "app lifecycle", "window lifecycle", "Activated", "Deactivated",
  "Stopped", "Resumed", "Destroying", "backgrounding", "resume app",
  "save state on background".
  DO NOT USE FOR: navigation design, dependency injection setup
  (use maui-dependency-injection), or low-level platform interop.
---

# .NET MAUI app lifecycle

Use this skill when the request is about app state transitions, foreground and background behavior, or `Window` lifecycle hooks. For event mappings and official examples, see `references/lifecycle-api.md`.

## Core rule: `Resumed` is not first launch

`Resumed` only fires after the app has already gone through `Stopped`. On first launch the normal sequence is `Created` followed by `Activated`.

```csharp
// Avoid: put first-launch initialization in OnResumed
protected override void OnResumed()
{
    LoadUserProfile();
}

// Prefer: use OnActivated for work that should run on every foreground entry
protected override void OnActivated()
{
    LoadUserProfile();
}
```

## `Deactivated` does not mean the app is hidden

`Deactivated` means the window lost focus, but it might still be visible. Save expensive state when the app reaches `Stopped`, not every time focus changes.

```csharp
// Avoid: heavy save work on every loss of focus
protected override void OnDeactivated()
{
    SaveAllDataToDatabase();
}

// Prefer: save when the window is no longer visible
protected override void OnStopped()
{
    SaveAllDataToDatabase();
}
```

## `Stopped` means "not visible", not "guaranteed to return"

The official MAUI guidance is to disconnect from long-running work and cancel pending requests in `Stopped`. The app may be terminated after that point and never raise `Resumed`.

## Keep lifecycle handlers short

Lifecycle events should update state, cancel work, and schedule follow-up work. They should not block the UI thread.

```csharp
// Avoid: blocking work in a lifecycle handler
protected override void OnStopped()
{
    Thread.Sleep(3000);
    SaveData();
}

// Prefer: quick state persistence and fast exit
protected override void OnStopped()
{
    base.OnStopped();
    Preferences.Set("draft_text", _viewModel.DraftText);
}
```

## Additional guidance

- On iOS and Mac Catalyst, use the `Backgrounding` event when you need to persist a state payload that the OS can pass back through `CreateWindow`.
- If you need platform-native callbacks, use `ConfigureLifecycleEvents` in `MauiProgram.cs`.
- On iPad, Mac Catalyst, and Windows, each `Window` instance raises lifecycle events independently. Handle multi-window state per window.

## Checklist

- [ ] Transient UI state (scroll position, draft text) saved in `OnStopped`
- [ ] First-launch initialization handled in `Created` or `Activated`, not `Resumed`
- [ ] No heavy work in `Deactivated`
- [ ] Long-running work disconnected or cancelled in `Stopped`
- [ ] Multi-window apps handle state per window
- [ ] iOS and Mac Catalyst apps that need state payloads use `Backgrounding`
