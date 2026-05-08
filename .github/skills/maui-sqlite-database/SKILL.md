---
name: maui-sqlite-database
description: >
  Add SQLite local database storage to .NET MAUI apps using sqlite-net-pcl.
  Covers data models with ORM attributes, async database service with lazy init,
  DI registration, WAL mode, and file management. Works with any UI pattern
  (XAML/MVVM, C# Markup, MauiReactor).
  USE FOR: "SQLite database", "local database", "sqlite-net-pcl", "offline storage",
  "CRUD database", "database service", "WAL mode", "CreateTableAsync",
  "local data persistence", "ORM attributes".
  DO NOT USE FOR: remote API access, secure credential storage
  (use maui-secure-storage), or file picker workflows.
---

# SQLite Database — Gotchas & Best Practices

For full service implementation, constants, data model templates, and common patterns, see `references/sqlite-database-api.md`.

## Package selection

```xml
<!-- Avoid: mixing unrelated SQLite libraries -->
<PackageReference Include="Microsoft.Data.Sqlite" />
<PackageReference Include="sqlite-net" />
<PackageReference Include="SQLitePCL.raw" />

<!-- Prefer: sqlite-net-pcl and its bundle -->
<PackageReference Include="sqlite-net-pcl" Version="1.9.*" />
<PackageReference Include="SQLitePCLRaw.bundle_green" Version="2.1.*" />
```

## Common Mistakes

### Avoid `Environment.GetFolderPath` for the database path

```csharp
// Avoid: platform-specific path assumptions
var path = Path.Combine(Environment.GetFolderPath(
    Environment.SpecialFolder.LocalApplicationData), "app.db3");

// Prefer: FileSystem.AppDataDirectory
var path = Path.Combine(FileSystem.AppDataDirectory, "app.db3");
```

### Avoid multiple `SQLiteAsyncConnection` instances for the same file

`SQLiteAsyncConnection` is **not thread-safe** for multiple instances pointing at the same file. Use a single instance via DI singleton:

```csharp
// Avoid: create a new connection per request
public async Task<List<Item>> GetItems()
{
    var db = new SQLiteAsyncConnection(Constants.DatabasePath);
    return await db.Table<Item>().ToListAsync();
}

// Prefer: one lazily created shared connection
private SQLiteAsyncConnection? _database;
private async Task<SQLiteAsyncConnection> GetDatabaseAsync()
{
    if (_database is not null) return _database;
    _database = new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    await _database.ExecuteAsync("PRAGMA journal_mode=WAL;");
    await _database.CreateTableAsync<TodoItem>();
    return _database;
}
```

### Enable WAL mode

Without WAL, readers block writers. Always enable it at initialization:

```csharp
await _database.ExecuteAsync("PRAGMA journal_mode=WAL;");
```

### Close the database before file operations

```csharp
// Avoid: move or delete the database while the connection is open
File.Delete(Constants.DatabasePath);

// Prefer: close the connection first
await databaseService.CloseConnectionAsync();
if (File.Exists(Constants.DatabasePath))
    File.Delete(Constants.DatabasePath);
```

## Platform Pitfalls

| Platform | Pitfall |
|----------|---------|
| iOS | `FileSystem.AppDataDirectory` is iCloud-backed — use `FileSystem.CacheDirectory` to exclude DB from iCloud backup |
| All | Multiple `SQLiteAsyncConnection` instances to same file → data corruption |
| All | No WAL → readers block writers, poor concurrent performance |
| All | File operations on open DB → corruption |

## Decision Framework

| Question | Recommendation |
|----------|---------------|
| DI lifetime? | **Singleton** — one connection, WAL handles concurrent reads |
| WAL mode? | **Always enable** — no reason not to on mobile |
| Database path? | `FileSystem.AppDataDirectory` — never `Environment.GetFolderPath` |
| Save pattern? | Check `Id != 0` → Update, else Insert |
| Multiple tables? | Add all `CreateTableAsync<T>()` calls in lazy init |
| Need to export/backup? | Close connection first, then `File.Copy` |

## Performance Tips

1. **Use transactions for batch writes** — individual inserts are slow; wrap in `RunInTransactionAsync`
2. **Add `[Indexed]` to frequently queried columns** — especially foreign keys
3. **WAL mode** eliminates reader/writer contention
4. **Avoid `ToListAsync()` on large tables** — use `Where()` filtering and pagination
5. **Use raw SQL for complex queries** — `QueryAsync<T>` is faster than chained LINQ for joins

## Checklist

- [ ] Install `sqlite-net-pcl` + `SQLitePCLRaw.bundle_green` (not `Microsoft.Data.Sqlite`)
- [ ] Database path uses `FileSystem.AppDataDirectory`
- [ ] Models have `[PrimaryKey, AutoIncrement]`
- [ ] Single `DatabaseService` with lazy async init pattern
- [ ] WAL enabled via `PRAGMA journal_mode=WAL`
- [ ] `DatabaseService` registered as **singleton** in DI
- [ ] Connection closed before any file move/copy/delete
- [ ] iOS: DB excluded from iCloud backup if needed
