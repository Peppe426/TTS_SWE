---
name: maui-secure-storage
description: >
  Add secure storage to .NET MAUI apps using SecureStorage.Default.
  Covers SetAsync, GetAsync, Remove, RemoveAll, platform setup
  (Android backup rules, iOS Keychain entitlements, Windows limits),
  common pitfalls, and a DI wrapper service for testability.
  USE FOR: "secure storage", "SecureStorage", "store token securely", "Keychain",
  "Android Keystore", "save secret", "encrypted storage", "store credentials",
  "sensitive data storage".
  DO NOT USE FOR: general file storage, SQLite databases
  (use maui-sqlite-database), or end-to-end authentication flows.
---

# Secure storage in .NET MAUI

Use this skill when the request is about storing tokens, passwords, or other small sensitive values. For platform setup details and wrapper examples, see `references/secure-storage-api.md`.

## Platform notes

### Android backup behavior

Android Auto Backup can restore encrypted preferences to a new device, but the encryption keys do not move with the data. MAUI removes affected keys automatically, but cached unreadable values can still throw. Exclude secure storage from backup or disable Auto Backup when appropriate.

### Wrap reads and writes in `try/catch`

Secure storage calls can throw when the device does not support secure storage, when keys change, or when backed-up encrypted data becomes unreadable.

```csharp
// Avoid: unprotected read
var value = await SecureStorage.Default.GetAsync("key");

// Prefer: recover explicitly if the stored values are unreadable
try
{
    var value = await SecureStorage.Default.GetAsync("key");
}
catch (Exception)
{
    SecureStorage.Default.RemoveAll();
}
```

### iOS and Mac Catalyst

- Simulator builds require the Keychain entitlement.
- Device builds do not require the same entitlement configuration.
- Keychain values can remain after app uninstall, so a reinstall is not always a clean slate.

### iCloud Keychain

Values may sync across devices via iCloud Keychain if the user has it enabled. This is platform behavior, not controllable from MAUI. Don't store device-specific tokens that shouldn't roam.

### Windows Limits

- Key name: max **255 characters**
- Value: max **8 KB** per setting
- Composite storage: max **64 KB** total

## Common Mistakes

```csharp
// Avoid: large payloads
await SecureStorage.Default.SetAsync("profile_image", base64EncodedImage);

// Prefer: small secrets
await SecureStorage.Default.SetAsync("auth_token", jwtToken);

// Avoid: logging secret values
_logger.LogInformation("Token: {Token}", await SecureStorage.Default.GetAsync("auth_token"));

// Prefer: log presence, not the secret
_logger.LogInformation("Token exists: {Exists}", token is not null);

// Avoid: storing complex objects directly
await SecureStorage.Default.SetAsync("user", userObject);

// Prefer: serialize first
await SecureStorage.Default.SetAsync("user", JsonSerializer.Serialize(user));
```

## Decision Framework

| Question | Answer |
|----------|--------|
| Storing a token, password, or API key? | Use SecureStorage |
| Storing user preferences or settings? | Use `Preferences` instead |
| Storing large files or blobs? | Use file storage plus encryption |
| Need cross-device sync? | Account for iOS Keychain sync behavior |
| Need data cleared on uninstall? | Do not assume that on iOS |

## Testability: Always Use DI

Never call `SecureStorage.Default` directly from ViewModels — wrap it in an `ISecureStorageService` interface for testability. See `references/secure-storage-api.md` for the full DI wrapper pattern with mock examples.

```csharp
// Avoid: direct static access from a ViewModel
public class LoginViewModel
{
    public async Task SaveToken(string token)
        => await SecureStorage.Default.SetAsync("auth_token", token);
}

// Prefer: inject a wrapper service
public class LoginViewModel(ISecureStorageService secure)
{
    public async Task SaveToken(string token)
        => await secure.SetAsync("auth_token", token);
}
```

## Checklist

- [ ] Android: Auto Backup disabled or secure storage excluded from backup
- [ ] Android: All `GetAsync` calls wrapped in try/catch
- [ ] iOS: Keychain entitlements configured (Simulator only — remove for device builds)
- [ ] Values are strings only — complex data serialized to JSON
- [ ] No secret values logged anywhere
- [ ] `SecureStorage.Default` accessed via DI wrapper, not directly from ViewModels
