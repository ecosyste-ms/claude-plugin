---
name: packages
description: Query packages.ecosyste.ms for package, version and dependency metadata across 75+ registries (npm, PyPI, RubyGems, crates.io, Maven, Go, NuGet and more). Use when looking up a package by name or purl, listing versions, finding dependent packages, checking maintainers, or comparing adoption across ecosystems.
allowed-tools: Bash(curl *)
---

# ecosyste.ms Packages API

Base URL: `https://packages.ecosyste.ms/api/v1`

All responses are JSON. No auth required. Set a `User-Agent` header identifying yourself. Unauthenticated clients share a rate limit and may see `429`.

Full OpenAPI spec: https://packages.ecosyste.ms/docs/api/v1/openapi.yaml

## Look up a package

By ecosystem + name, repository URL, or purl. Returns an array (the same name can exist in many ecosystems).

```bash
curl -s 'https://packages.ecosyste.ms/api/v1/packages/lookup?ecosystem=pypi&name=numpy'
curl -s 'https://packages.ecosyste.ms/api/v1/packages/lookup?purl=pkg:npm/lodash'
curl -s 'https://packages.ecosyste.ms/api/v1/packages/lookup?repository_url=https://github.com/numpy/numpy'
```

Bulk lookup (POST, up to 1000 at once):

```bash
curl -s -X POST 'https://packages.ecosyste.ms/api/v1/packages/bulk_lookup' \
  -H 'Content-Type: application/json' \
  -d '{"purls": ["pkg:npm/lodash", "pkg:pypi/requests"]}'
```

## Fetch from a specific registry

Registry names are hostnames: `npmjs.org`, `pypi.org`, `rubygems.org`, `crates.io`, `proxy.golang.org`, `repo1.maven.org`, `nuget.org`, `packagist.org`, `hub.docker.com`, `cocoapods.org`, `pub.dev`. List all with `GET /registries`.

Package names containing `/` must be URL-encoded (`%2F`).

```bash
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/lodash'
curl -s 'https://packages.ecosyste.ms/api/v1/registries/rubygems.org/packages/rails'
curl -s 'https://packages.ecosyste.ms/api/v1/registries/proxy.golang.org/packages/github.com%2Fstretchr%2Ftestify'
```

Useful fields in the response: `latest_release_number`, `latest_release_published_at`, `first_release_published_at`, `versions_count`, `dependent_packages_count`, `dependent_repos_count`, `docker_dependents_count`, `docker_downloads_count`, `downloads`, `rankings`, `maintainers`, `repo_metadata`, `advisories`, `funding_links`, `status`. The response also includes follow-up URLs: `dependent_packages_url`, `usage_url` (repos service), `docker_usage_url` (docker service).

## Versions

```bash
# all versions (paginated, newest first)
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/express/versions'

# just the version number strings
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/express/version_numbers'

# a single version with its declared dependencies
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/express/versions/4.19.2'

# latest only
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/express/latest_version'
```

## Dependent packages

Other packages on the same registry that declare this one as a dependency. Sortable by `downloads`, `dependent_repos_count`, `dependent_packages_count`, `latest_release_published_at` etc, so you can find the most significant downstream consumers first.

```bash
# top dependents by download count
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/lodash/dependent_packages?sort=downloads&order=desc&per_page=20'

# breakdown of dependent kinds (runtime, dev, peer)
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/lodash/dependent_package_kinds'

# packages often used alongside this one
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/packages/lodash/related_packages'
```

## Usage footprint across the ecosystem

The package response gives you counts (`dependent_packages_count`, `dependent_repos_count`, `docker_dependents_count`, `docker_downloads_count`). For the actual lists, follow up on the other services. The ecosystem name in these URLs is the package manager (`npm`, `pypi`, `cargo`, `bundler`, `go`, `maven`), not the registry hostname.

```bash
# repositories that depend on it (repos service)
curl -s 'https://repos.ecosyste.ms/api/v1/usage/npm/lodash/dependent_repositories?sort=stargazers_count&order=desc'

# docker images that ship it (docker service)
curl -s 'https://docker.ecosyste.ms/api/v1/usage/npm/lodash'
```

Together these three views (dependent packages, dependent repos, docker images) give a rounded picture of a package's blast radius.

## Maintainers and namespaces

```bash
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/maintainers/sindresorhus'
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/maintainers/sindresorhus/packages'
curl -s 'https://packages.ecosyste.ms/api/v1/registries/npmjs.org/namespaces/@babel/packages'
```

## Keywords and critical packages

```bash
curl -s 'https://packages.ecosyste.ms/api/v1/keywords/http'
curl -s 'https://packages.ecosyste.ms/api/v1/critical?registry=pypi.org'
curl -s 'https://packages.ecosyste.ms/api/v1/critical/sole_maintainers?registry=npmjs.org'
```

## Pagination and sorting

List endpoints accept `?page=N&per_page=N` (max 1000). Sortable endpoints accept `sort` (e.g. `downloads`, `dependent_repos_count`, `latest_release_published_at`) and `order` (`asc`/`desc`).

## When to use

- The user asks about a package on any registry
- Checking adoption, release cadence, or maintainer count for a library
- Finding what depends on a package and how widely it's deployed (registry, repos, docker)
- Estimating the blast radius of a vulnerability or breaking change
- Listing every package an org or maintainer publishes
- Resolving a purl to its registry metadata
