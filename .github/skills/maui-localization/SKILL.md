---
name: maui-localization
description: >-
  Guidance for localizing .NET MAUI apps with `.resx` resource files,
  neutral language configuration, platform language declarations, RTL layout,
  and runtime culture considerations.
  USE FOR: "localization", "multi-language", "resx resource", "translate app", "RTL layout",
  "culture switching", "localize strings", "right-to-left", "language support MAUI",
  "Info.plist languages".
  DO NOT USE FOR: theming or visual styles (use maui-theming), accessibility-only
  adjustments, or remote content translation flows.
---

# .NET MAUI localization

Use this skill when the request is about app strings, supported languages, RTL behavior, or localized app metadata. For the official Microsoft Learn references behind this skill, see `references/localization-api.md`.

## Common issues

| Issue | Fix |
|---|---|
| `ResourceManager` returns `null` for default culture | Set `<NeutralLanguage>en-US</NeutralLanguage>` in `.csproj` |
| iOS ignores culture overrides | `CFBundleLocalizations` missing from `Info.plist` |
| Windows doesn't show correct language | `<Resource Language="..." />` missing from `Package.appxmanifest` |
| `x:Static` bindings don't update on language switch | `x:Static` is static; use a custom observable localization service if you support runtime switching |
| Strongly typed resource class isn't generated in VS Code | Add the official `.csproj` metadata for resource generation and run `dotnet build` |

## Always set the neutral language

```xml
<PropertyGroup>
  <NeutralLanguage>en-US</NeutralLanguage>
</PropertyGroup>

<!-- Missing this can cause ResourceManager to return null for unsupported languages -->
```

## Platform declarations

### iOS / Mac Catalyst

Add both `CFBundleLocalizations` and `CFBundleDevelopmentRegion`:

```xml
<key>CFBundleLocalizations</key>
<array>
  <string>en</string>
  <string>es</string>
  <string>fr</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

### Windows

```xml
<!-- Platforms/Windows/Package.appxmanifest -->
<Resources>
  <Resource Language="en-US" />
  <Resource Language="es" />
  <Resource Language="fr-FR" />
</Resources>
```

### Android

Android picks up `.resx`-based localization automatically. No extra manifest entries are required for the localization setup itself.

## XAML and runtime switching

```xml
<!-- Standard MAUI localization pattern -->
<Label Text="{x:Static resx:AppResources.WelcomeMessage}" />

<!-- For runtime switching, bind through a custom observable source -->
<Label Text="{Binding WelcomeMessage, Source={x:Static local:LocalizationManager.Instance}}" />
```

The Microsoft Learn guidance shows `x:Static` access for generated resource properties. If the app supports runtime language switching, use a custom localization service that raises change notifications instead of expecting `x:Static` to refresh.

When switching culture at runtime, update both UI resource culture and formatting culture:

```csharp
var culture = new CultureInfo("es");
CultureInfo.CurrentUICulture = culture;
CultureInfo.CurrentCulture = culture;
AppResources.Culture = culture;

// Only setting CurrentUICulture leaves dates and numbers in the old culture
CultureInfo.CurrentUICulture = new CultureInfo("es");
```

## Right-to-left layout

```xml
<!-- Prefer page-level flow direction so children inherit correctly -->
<ContentPage FlowDirection="RightToLeft">
  <StackLayout FlowDirection="MatchParent" />
</ContentPage>

<!-- Child-only flow direction often leaves the parent layout LTR -->
<ContentPage>
  <StackLayout FlowDirection="RightToLeft" />
</ContentPage>
```

## VS Code resource generation

Visual Studio runs a design-time build that generates the strongly typed resource class. VS Code does not, so add the official resource metadata to the project file:

```xml
<ItemGroup>
  <EmbeddedResource Update="Resources\Localization\AppResources.resx">
    <Generator>MSBuild:Compile</Generator>
    <StronglyTypedLanguage>CSharp</StronglyTypedLanguage>
    <StronglyTypedNamespace>MauiApp.Resources.Localization</StronglyTypedNamespace>
    <StronglyTypedFileName>$(IntermediateOutputPath)\Resource.Designer.cs</StronglyTypedFileName>
    <StronglyTypedClassName>AppResources</StronglyTypedClassName>
  </EmbeddedResource>

  <Compile Include="$(IntermediateOutputPath)\Resource.Designer.cs">
    <AutoGen>True</AutoGen>
    <DesignTime>false</DesignTime>
    <DependentUpon>Resources\Localization\AppResources.resx</DependentUpon>
  </Compile>
</ItemGroup>
```

## Decision framework

| Need | Approach |
|---|---|
| Localized strings | `.resx` files plus generated `AppResources` class |
| Runtime language switching | Custom observable localization service |
| Culture-specific images | Use platform image localization rather than storing device images in `.resx` |
| RTL support | Set `FlowDirection` at page level, detect with `TextInfo.IsRightToLeft` |
| Date/number formatting | Set `CultureInfo.CurrentCulture` alongside `CurrentUICulture` |

## Checklist

- [ ] `NeutralLanguage` set in `.csproj`
- [ ] Default `AppResources.resx` contains all keys
- [ ] Each target language has its own `AppResources.{culture}.resx`
- [ ] iOS and Mac Catalyst: `CFBundleLocalizations` and `CFBundleDevelopmentRegion` are set
- [ ] Windows: `Package.appxmanifest` declares `<Resource Language="..." />`
- [ ] RTL cultures set `FlowDirection` at page/app level
- [ ] Runtime switching sets all three: `CurrentUICulture`, `CurrentCulture`, `AppResources.Culture`
- [ ] VS Code projects include the resource-generation metadata when needed
