---
name: maui-unit-testing
description: >
  xUnit testing guidance for .NET MAUI apps — ViewModel testing, mocking MAUI
  services, test project setup, code coverage, and on-device test runners.
  USE FOR: "unit test MAUI", "xUnit test", "ViewModel testing", "mock MAUI services",
  "test project setup", "code coverage", "on-device test runner", "test MAUI app",
  "IServiceProvider mock", "Moq MAUI".
  DO NOT USE FOR: UI automation strategy,
  performance profiling (use maui-performance), or dependency injection setup (use maui-dependency-injection).
---

# .NET MAUI Unit Testing — Gotchas & Best Practices

For project templates, xUnit examples, ViewModel test patterns, and CLI commands, see `references/unit-testing-api.md`.

## Do not use platform-specific test TFMs

```xml
<!-- Avoid: platform-specific TFMs in the test project -->
<TargetFramework>net9.0-ios</TargetFramework>

<!-- Prefer: plain .NET TFMs for the test host -->
<TargetFrameworks>net9.0;net10.0</TargetFrameworks>
```

## App project `OutputType` when referenced by tests

If your test project references the app project, the app tries to build as `Exe` for the test TFM — this fails. Add a conditional `OutputType`:

```xml
<!-- Avoid: a single Exe output type for the test TFM -->
<OutputType>Exe</OutputType>

<!-- Prefer: Library for the test TFM and Exe for the platform TFMs -->
<PropertyGroup Condition="'$(TargetFramework)' == 'net9.0'">
  <OutputType>Library</OutputType>
</PropertyGroup>
<PropertyGroup Condition="'$(TargetFramework)' != 'net9.0'">
  <OutputType>Exe</OutputType>
</PropertyGroup>
```

## Common Mistakes

### Avoid static MAUI APIs in ViewModels

ViewModels that directly call static MAUI APIs are untestable:

```csharp
// Avoid: direct Shell.Current usage
public async Task GoToDetail(int id)
    => await Shell.Current.GoToAsync($"detail?id={id}");

// Prefer: inject an interface
public class MyViewModel(INavigationService nav)
{
    public async Task GoToDetail(int id)
        => await nav.GoToAsync($"detail?id={id}");
}
```

Static APIs to wrap behind interfaces:
- `Shell.Current` → `INavigationService`
- `Application.Current` → avoid entirely
- `SecureStorage.Default` → `ISecureStorage`
- `Connectivity.Current` → `IConnectivity`

### Assert on ViewModel state, not bindings

```csharp
// Avoid: assert against the UI binding target
Assert.Equal("Hello", label.Text);

// Prefer: assert against the ViewModel
Assert.Equal("Hello", viewModel.Title);
Assert.True(viewModel.SaveCommand.CanExecute(null));
```

## Mocking Strategy for MAUI Services

| MAUI Service | Mock Strategy |
|-------------|---------------|
| `ISecureStorage` | `Mock<ISecureStorage>` — stub `GetAsync`/`SetAsync` |
| `IPreferences` | `Mock<IPreferences>` — stub `Get`/`Set`/`Remove` |
| `IConnectivity` | `Mock<IConnectivity>` — return `NetworkAccess` |
| `IGeolocation` | `Mock<IGeolocation>` — return fixed `Location` |
| `IFilePicker` | `Mock<IFilePicker>` — return `FileResult` |
| `IMediaPicker` | `Mock<IMediaPicker>` — return `FileResult` |
| Shell navigation | Abstract behind `INavigationService` |
| `IDispatcher` | Stub `Dispatch` to invoke action synchronously |

## Architecture: Interface-First for Testability

Define service interfaces so ViewModels have zero MAUI platform dependencies:

```csharp
// These interfaces keep the ViewModel layer testable
public interface INavigationService
{
    Task GoToAsync(string route);
    Task GoBackAsync();
}

public interface IDialogService
{
    Task<bool> ConfirmAsync(string title, string message);
}
```

Register implementations in `MauiProgram.cs`; inject interfaces into ViewModels.

## Tips

- **No `Application.Current` or `Shell.Current` in ViewModels** — wrap in injectable services
- **Use `ObservableCollection<T>` and `[ObservableProperty]`** (MVVM Toolkit) for testable state
- **Assert on ViewModel properties and `CanExecute`**, not UI bindings
- **Use `TaskCompletionSource`** to test async waiting flows
- **Run `dotnet test` in CI** to catch regressions early
- **On-device tests**: Use `xunit.runner.devices` for real platform APIs (sensors, camera, Bluetooth)

## Checklist

- [ ] Test project targets plain `net9.0`/`net10.0` (not platform-specific TFMs)
- [ ] App project has conditional `OutputType` for test TFM
- [ ] All MAUI static APIs wrapped behind injectable interfaces
- [ ] ViewModels tested via properties and commands, not UI bindings
- [ ] `IDispatcher` mocked to invoke synchronously in tests
- [ ] `dotnet test` runs green in CI
