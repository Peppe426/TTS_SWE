# .NET MAUI localization reference

This reference captures the Microsoft Learn page used to verify the guidance in `maui-localization`.

## Official docs

- Localize an app: https://learn.microsoft.com/en-us/dotnet/maui/fundamentals/localization?view=net-maui-10.0

## Verified guidance used by the skill

- MAUI localization is based on `.resx` resources and a configured neutral language.
- iOS and Mac Catalyst require `CFBundleLocalizations` and `CFBundleDevelopmentRegion` in `Info.plist`.
- Windows requires explicit `<Resource Language="..." />` entries in `Platforms\Windows\Package.appxmanifest`.
- VS Code needs extra `.csproj` metadata so strongly typed resource classes are generated outside Visual Studio design-time builds.
- `x:Static` is the standard way to consume generated resource properties in XAML.
- Right-to-left layout support is part of the MAUI localization guidance.
