---
name: maui-dependency-injection
description: >
  Guidance for dependency injection in .NET MAUI apps — service registration,
  lifetime selection (Singleton/Transient/Scoped), constructor injection,
  automatic resolution via Shell navigation, explicit resolution patterns,
  platform-specific registrations, and testability best practices.
  USE FOR: "dependency injection", "DI registration", "AddSingleton", "AddTransient",
  "AddScoped", "service registration", "constructor injection", "IServiceProvider",
  "MauiProgram DI", "register services".
  DO NOT USE FOR: general binding syntax, navigation design,
  or unit-test-specific mocking patterns (use maui-unit-testing).
---

# Dependency Injection in .NET MAUI

## Lifetime Decision Framework

| Question | Answer → Lifetime |
|---|---|
| Does it hold shared state or is expensive to create? | `AddSingleton` |
| Is it stateless, lightweight, or per-request? | `AddTransient` |
| Do you manage `IServiceScope` yourself? | `AddScoped` |

> Avoid `AddScoped` in MAUI unless you create and manage scopes yourself. There is no built-in scope per page.
> Using it without manually creating `IServiceScope` gives you singleton
> behaviour silently, which is confusing and error-prone.

### Singleton traps

```csharp
// Avoid: singleton ViewModel registration for navigated pages
builder.Services.AddSingleton<DetailViewModel>();

// Prefer: transient ViewModel registration
builder.Services.AddTransient<DetailViewModel>();
```

> Register **Pages and ViewModels as Transient**. Register **services that hold
> shared state as Singleton** (e.g., `IDataService`, `HttpClient` factory).

---

## Gotcha: XAML Resource Parsing vs. DI Timing

XAML resources (`App.xaml` styles, converters) are parsed **during
`InitializeComponent()`** — before the DI container is fully available. If a
resource or converter needs a service, resolve it in `CreateWindow()`, not
in the constructor.

```csharp
// Avoid: resolve services during XAML parsing
public App(IDataService data)
{
    InitializeComponent(); // XAML parses here
    _data = data;          // may fail for types not yet resolved
}

// Prefer: defer service resolution to CreateWindow
public partial class App : Application
{
    private readonly IServiceProvider _services;

    public App(IServiceProvider services)
    {
        _services = services;
        InitializeComponent();
    }

    protected override Window CreateWindow(IActivationState? activationState)
    {
        // Safe — container is fully built
        var mainPage = _services.GetRequiredService<MainPage>();
        return new Window(new AppShell());
    }
}
```

---

## Gotcha: Unregistered Page Silently Skips DI

If a Page is used in Shell XAML (`<ShellContent ContentTemplate="...">`) but
**not registered** in `builder.Services`, MAUI instantiates it with the
parameterless constructor. Dependencies are silently `null` — no exception.

```csharp
// Avoid: leave pages unregistered when they need constructor injection
// builder.Services.AddTransient<DetailPage>(); // missing!

// Prefer: register pages that need injection
builder.Services.AddTransient<DetailPage>();
builder.Services.AddTransient<DetailViewModel>();
```

---

## Anti-Pattern: Service Locator Overuse

```csharp
// Avoid: scattered service locator usage
public void DoWork()
{
    var service = this.Handler.MauiContext.Services.GetService<IDataService>();
    service.Load();
}

// Prefer: constructor injection
public class MyViewModel(IDataService dataService)
{
    public void DoWork() => dataService.Load();
}
```

> Use explicit resolution (`Handler.MauiContext.Services`) only when constructor
> injection is genuinely unavailable (e.g., inside a custom handler or
> platform callback).

---

## Platform-Specific Registration Pitfall

When using `#if` directives for platform services, ensure the **interface**
is always registered — otherwise consumers on untargeted platforms get a
runtime `null`.

```csharp
// Avoid: leave an interface unregistered on one target platform
#if ANDROID
builder.Services.AddSingleton<INotificationService, AndroidNotificationService>();
#elif IOS || MACCATALYST
builder.Services.AddSingleton<INotificationService, AppleNotificationService>();
#endif

// Prefer: cover every target or provide a fallback implementation
#if ANDROID
builder.Services.AddSingleton<INotificationService, AndroidNotificationService>();
#elif IOS || MACCATALYST
builder.Services.AddSingleton<INotificationService, AppleNotificationService>();
#elif WINDOWS
builder.Services.AddSingleton<INotificationService, WindowsNotificationService>();
#endif
```

---

## Checklist

- [ ] Every Page and ViewModel that needs injection is registered in `MauiProgram.cs`
- [ ] Pages/ViewModels are `AddTransient`; shared services are `AddSingleton`
- [ ] Constructor injection used everywhere possible; service locator only as last resort
- [ ] Interfaces defined for any service you need to mock in tests
- [ ] Platform-specific `#if` registrations cover all target platforms (or provide fallback)
- [ ] Late-bound services resolved in `CreateWindow()`, not during XAML parse
- [ ] `AddScoped` only used when you manually manage `IServiceScope`
