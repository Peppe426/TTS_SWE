---
name: maui-current-apis
description: >
  Guardrail for current .NET MAUI APIs and version-aware guidance. Helps detect
  target frameworks and package versions before generating code, with verified
  .NET 10 deprecations and replacements for common outdated APIs.
  USE FOR: "deprecated API", "obsolete API", "API migration", "MAUI breaking changes",
  "check API currency", "review MAUI code", "generate MAUI code", "edit MAUI code".
  DO NOT USE FOR: learning a feature from scratch, performance optimization
  (use maui-performance), or testing guidance (use maui-unit-testing).
---

# .NET MAUI current APIs

Use this skill before generating or editing MAUI code. For the verified Microsoft Learn references behind this skill, see `references/current-apis-api.md`.

## Start with the project file

Read the project's `.csproj` and confirm the target framework and important package versions:

```xml
<TargetFramework>net10.0-android</TargetFramework>
<TargetFrameworks>net10.0-android;net10.0-ios;net10.0-maccatalyst;net10.0-windows10.0.19041.0</TargetFrameworks>
```

The target framework and package versions determine which APIs are valid. Never assume the project is already on .NET 10.

Check these packages when present:

- `CommunityToolkit.Maui`
- `CommunityToolkit.Mvvm`
- `Reactor.Maui`
- `Microsoft.AspNetCore.Components.WebView.Maui`

## Default rules

1. Read the `.csproj` before generating code.
2. Do not introduce `Xamarin.Forms` or `Xamarin.Essentials` namespaces in MAUI code.
3. Avoid `Microsoft.Maui.Controls.Compatibility.*` in new code.
4. Prefer the current async API names when .NET 10 deprecates the old forms.
5. If an example uses an API below, replace it before copying it into the project.

## Verified .NET 10 changes

| Deprecated or removed | Use instead | Notes |
| --- | --- | --- |
| `ListView` and `Cell`-based list item types | `CollectionView` with `DataTemplate` | `ListView`, `EntryCell`, `ImageCell`, `SwitchCell`, `TextCell`, and `ViewCell` are deprecated in .NET 10. |
| `TableView` | `CollectionView` or a custom layout | `TableView` is deprecated in .NET 10. |
| `ClickGestureRecognizer` | `TapGestureRecognizer` | `ClickGestureRecognizer` was removed. |
| `Accelerator` | `KeyboardAccelerator` | `Accelerator` was removed from `Microsoft.Maui.Controls`. |
| `MessagingCenter` | `WeakReferenceMessenger` | In .NET 10 `MessagingCenter` was made internal. |
| `Page.IsBusy` | Explicit busy UI such as `ActivityIndicator` | `IsBusy` is obsolete. |
| `FadeTo`, `RotateTo`, `ScaleTo`, `TranslateTo`, `LayoutTo`, and related APIs | `FadeToAsync`, `RotateToAsync`, `ScaleToAsync`, `TranslateToAsync`, `LayoutToAsync`, and related async APIs | Animation methods now use async names. |
| `DisplayAlert` and `DisplayActionSheet` | `DisplayAlertAsync` and `DisplayActionSheetAsync` | Pop-up APIs now use async names. |

## Prefer these current patterns

- Use `SafeAreaEdges` for current safe-area behavior control in .NET 10.
- Use MAUI layouts such as `Grid`, `VerticalStackLayout`, and `HorizontalStackLayout` instead of compatibility layouts.
- Use `BlazorWebView` or `HybridWebView` APIs that match the detected framework version.
- Check `Reactor.Maui` package version before using MauiReactor-specific APIs from old examples.

## Quick checks before you answer

1. Does the API exist in the project's target framework?
2. Is the example actually for MAUI, not Xamarin.Forms?
3. Does the project depend on a toolkit or third-party package with a version-specific API?
4. Is there a newer MAUI-native API that the docs now prefer?
