# .NET MAUI performance reference

This reference captures the Microsoft Learn pages used to verify the guidance in `maui-performance`.

## Official docs

- Improve app performance: https://learn.microsoft.com/en-us/dotnet/maui/deployment/performance?view=net-maui-10.0
- Compiled bindings: https://learn.microsoft.com/en-us/dotnet/maui/fundamentals/data-binding/compiled-bindings?view=net-maui-10.0
- Trim a .NET MAUI app: https://learn.microsoft.com/en-us/dotnet/maui/deployment/trimming?view=net-maui-10.0
- Native AOT deployment: https://learn.microsoft.com/en-us/dotnet/maui/deployment/nativeaot?view=net-maui-10.0

## Verified guidance used by the skill

- Profile first, and prefer device profiling over simulator-only measurements.
- Compiled bindings improve performance and are required for Native AOT and fully trimmed MAUI apps.
- Layout depth matters; extra wrappers and the wrong layout choices add measure and arrange work.
- App-wide resources should stay in `App.xaml`, while page-specific resources should stay at page scope to reduce startup parsing.
- Native AOT documentation currently focuses on iOS and Mac Catalyst. The documented publish flow requires `dotnet publish` and zero trimming/AOT warnings.
