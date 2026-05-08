# .NET MAUI current APIs reference

This reference captures the Microsoft Learn pages used to verify the current guidance in `maui-current-apis`.

## Official docs

- What's new in .NET MAUI for .NET 10: https://learn.microsoft.com/en-us/dotnet/maui/whats-new/dotnet-10?view=net-maui-10.0
- Basic animation in .NET MAUI: https://learn.microsoft.com/en-us/dotnet/maui/user-interface/animation/basic?view=net-maui-10.0
- Display pop-ups in .NET MAUI: https://learn.microsoft.com/en-us/dotnet/maui/user-interface/pop-ups?view=net-maui-10.0
- Safe area layout in .NET MAUI: https://learn.microsoft.com/en-us/dotnet/maui/user-interface/safe-area?view=net-maui-10.0

## Verified .NET 10 changes used by the skill

- `ListView`, `TableView`, and the `Cell`-based `ListView` item types are deprecated in favor of `CollectionView`.
- `ClickGestureRecognizer` was removed; use `TapGestureRecognizer`.
- `Accelerator` was removed from `Microsoft.Maui.Controls`; use `KeyboardAccelerator`.
- `MessagingCenter` was made internal; use `WeakReferenceMessenger` from `CommunityToolkit.Mvvm`.
- The old animation methods (`FadeTo`, `RotateTo`, `ScaleTo`, `TranslateTo`, and related APIs) were replaced by `*Async` methods.
- `DisplayAlert` and `DisplayActionSheet` were replaced by `DisplayAlertAsync` and `DisplayActionSheetAsync`.
- `Page.IsBusy` is obsolete; prefer explicit busy UI such as `ActivityIndicator`.
- `SafeAreaEdges` is the current safe-area control surface in .NET 10.
