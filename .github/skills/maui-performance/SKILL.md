---
name: maui-performance
description: >
  Performance optimization guidance for .NET MAUI apps covering profiling,
  compiled bindings, layout efficiency, image optimization, resource dictionaries,
  startup time, trimming, and Native AOT readiness.
  USE FOR: "performance optimization", "slow startup", "app performance", "compiled bindings",
  "layout optimization", "image optimization", "trimming", "NativeAOT", "profiling MAUI",
  "reduce app size", "startup time".
  DO NOT USE FOR: general binding syntax, deprecated API migration
  (use maui-current-apis), or unit testing setup (use maui-unit-testing).
---

# .NET MAUI performance

Use this skill when the request is about slow UI, slow startup, publish size, or MAUI-specific optimization tradeoffs. For the official Microsoft Learn references behind this skill, see `references/performance-api.md`.

## Profile first

- Use `dotnet-trace` on MAUI targets where it is supported.
- Use PerfView for Windows-specific CPU and allocation analysis.
- Profile on real devices when possible. Simulator-only numbers can be misleading.

```shell
dotnet-trace collect --process-id <PID> --providers Microsoft-DotNETCore-SampleProfiler
```

## Prefer compiled bindings

- Set `x:DataType` where the `BindingContext` is known.
- Compiled bindings resolve bindings at compile time and avoid reflection at runtime.
- They are required for Native AOT apps and apps with full trimming enabled.

```xml
<ContentPage xmlns:vm="clr-namespace:MyApp.ViewModels"
             x:DataType="vm:MainViewModel">
    <Label Text="{Binding Title}" />
</ContentPage>
```

- In templates, set `x:DataType` on the template root:
  ```xml
  <DataTemplate x:DataType="model:Item">
      <Label Text="{Binding Name}" />
  </DataTemplate>
  ```
- Do not bind text or values that can be set statically.

## Simplify the visual tree

- Remove layouts that only wrap a single child.
- Prefer `Grid` when the layout is tabular instead of recreating it with nested stack layouts.
- Reduce the number of elements on a page where possible.
- Set `IsVisible="False"` for elements you want to keep in the tree but skip during layout and rendering.

## Images and resources

- Size images close to their display size.
- Cache downloaded images for an appropriate time.
- Put app-wide styles in `App.xaml`.
- Keep page-specific resources at page scope so they are not parsed at startup.

## Startup and list performance

- Keep `App` and first-page constructors light.
- Remove unused handlers and effects from `MauiProgram.cs`.
- Prefer `CollectionView` over `ListView`.
- Use `ItemSizingStrategy="MeasureFirstItem"` when list items are uniform.

## Trimming and Native AOT

```xml
<PropertyGroup>
    <PublishTrimmed>true</PublishTrimmed>
    <TrimMode>full</TrimMode>
</PropertyGroup>
```

- Full trimming removes unused code but can break reflection-heavy code.
- Use source generators and explicit preservation attributes when reflection is required.
- The current Native AOT MAUI docs focus on iOS and Mac Catalyst publish flows.
- Native AOT validation must use `dotnet publish`, not just `dotnet build`.
- Fix all trimming and AOT warnings before treating the publish output as valid.

## Checklist

1. `x:DataType` is set where bindings are performance-sensitive.
2. The layout tree does not contain unnecessary wrappers.
3. Images are sized and cached appropriately.
4. Page-specific resources are not stored in `App.xaml`.
5. Startup constructors and service registration stay lean.
6. Trimming and Native AOT warnings are treated as real compatibility issues.
