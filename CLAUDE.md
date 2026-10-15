# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project

Padini e-Commerce, built on a fork of [nopCommerce](https://github.com/nopSolutions/nopCommerce) (ASP.NET Core, .NET 10).

- `origin` = our fork (`rezarazaleigh/nopCommerce`)
- `main` = our working branch for Padini customizations
- `develop` = mirror of upstream nopCommerce; keep it clean so upstream changes can be merged into `main`

## Build, run, test

All commands run from `src/` (SDK version pinned in `global.json`).

```sh
dotnet build NopCommerce.sln
dotnet run --project Presentation/Nop.Web          # first run opens the installer
dotnet test Tests/Nop.Tests/Nop.Tests.csproj
dotnet test Tests/Nop.Tests/Nop.Tests.csproj --filter "FullyQualifiedName~Catalog"
```

Docker alternatives live at the repo root: `docker-compose.yml` (SQL Server), `mysql-docker-compose.yml`, `postgresql-docker-compose.yml`.

## Layout

- `src/Libraries/Nop.Core` – domain entities, caching, infrastructure (engine, DI, type finder)
- `src/Libraries/Nop.Data` – data access (linq2db), FluentMigrator migrations under `Migrations/`
- `src/Libraries/Nop.Services` – business logic, one service per area (Catalog, Orders, Customers, ...)
- `src/Presentation/Nop.Web.Framework` – MVC infrastructure, base controllers, filters, plugin/widget plumbing
- `src/Presentation/Nop.Web` – storefront and admin (`Areas/Admin`), factories, models, views, themes
- `src/Plugins/Nop.Plugin.*` – plugins; built output is copied to `Nop.Web/Plugins`
- `src/Tests/Nop.Tests` – NUnit + Moq tests, run against an in-memory SQLite provider
- `upgradescripts/` – SQL upgrade scripts between nopCommerce versions

## Conventions

- Everything is async (`...Async` methods); follow that for new services and handlers.
- Controllers stay thin: models are built by `*ModelFactory` classes, logic lives in services.
- New settings, entities and locale resources need a migration in `Nop.Data`/`Nop.Services` migrations.
- Register services in the relevant `NopStartup` (`INopStartup`) class rather than ad hoc.
- Prefer implementing Padini-specific features as plugins under `src/Plugins` to keep merges from upstream easy; when core files must change, keep the diff small.
- Follow `.editorconfig` and match the style of surrounding code.

## Don't commit

`App_Data/appsettings.json`, `dataSettings.json`, `plugins.json`, `installedPlugins.json` and other generated/local files are git-ignored – keep connection strings and secrets out of source control.
