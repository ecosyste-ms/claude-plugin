---
name: commits
description: Query commits.ecosyste.ms for commit history and contributor statistics for any public repository. Use when checking how active a project is, who the top committers are, bus factor (DDS), bot commit ratio, or listing commits in a date range without cloning.
allowed-tools: Bash(curl *)
---

# ecosyste.ms Commits API

Base URL: `https://commits.ecosyste.ms/api/v1`

All responses are JSON. No auth required. Set a `User-Agent` header. Lookup endpoints return a 302 redirect, so use `curl -L`.

Full OpenAPI spec: https://commits.ecosyste.ms/docs/api/v1/openapi.yaml

## Repository commit stats

```bash
curl -sL 'https://commits.ecosyste.ms/api/v1/repositories/lookup?url=https://github.com/rails/rails'
```

Or directly by host (repo names containing `/` must be URL-encoded as `%2F`):

```bash
curl -s 'https://commits.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails'
```

Returns: `total_commits`, `total_committers`, `total_bot_commits`, `total_bot_committers`, `mean_commits`, `dds` (development distribution score, 0-1, higher means work is spread across more people), `committers[]` (name, email, login, count), plus `past_year_*` variants of each, and `last_synced_commit`.

## List commits

```bash
curl -s 'https://commits.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/commits?per_page=50'
curl -s 'https://commits.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/commits?since=2026-01-01T00:00:00Z&until=2026-04-01T00:00:00Z'
```

Filters: `since`, `until`, `sort`, `order`, `page`, `per_page`.

## Committers

Activity for a single login or email across all indexed repos on a host:

```bash
curl -s 'https://commits.ecosyste.ms/api/v1/hosts/GitHub/committers/dhh'
curl -s 'https://commits.ecosyste.ms/api/v1/hosts/GitHub/committers/andrewnez@gmail.com'
```

## Hosts

```bash
curl -s 'https://commits.ecosyste.ms/api/v1/hosts'
```

Common host names: `GitHub`, `gitlab.com`, `Bitbucket.org`, `codeberg.org`.

## When to use

- Checking whether a project is still actively developed
- Estimating bus factor via `dds` and `total_committers`
- Separating human from bot commits
- Finding which repos a given person commits to
