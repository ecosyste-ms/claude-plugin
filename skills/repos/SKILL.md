---
name: repos
description: Query repos.ecosyste.ms for repository metadata across GitHub, GitLab, Bitbucket, Codeberg, SourceHut and 1900+ other forges. Use when looking up a repo by URL, listing an owner's repos, fetching tags/releases/manifests, finding which repositories depend on a package, or generating an SBOM for a repo.
allowed-tools: Bash(curl *)
---

# ecosyste.ms Repos API

Base URL: `https://repos.ecosyste.ms/api/v1`

All responses are JSON. No auth required. Set a `User-Agent` header identifying yourself. Unauthenticated clients share a rate limit and may see `429`.

Full OpenAPI spec: https://repos.ecosyste.ms/docs/api/v1/openapi.yaml

## Look up a repository

By URL or purl. Returns a single object (or 404).

```bash
curl -s 'https://repos.ecosyste.ms/api/v1/repositories/lookup?url=https://github.com/rails/rails'
curl -s 'https://repos.ecosyste.ms/api/v1/repositories/lookup?purl=pkg:github/ecosyste-ms/repos'
```

Useful fields: `full_name`, `description`, `stargazers_count`, `forks_count`, `archived`, `fork`, `license`, `language`, `topics`, `default_branch`, `pushed_at`, `homepage`, `metadata.files` (readme, license, changelog, funding paths), `commit_stats`.

## Hosts

Host names are case sensitive: `GitHub`, `gitlab.com`, `Bitbucket.org`, `codeberg.org`, `salsa.debian.org`. List all with `GET /hosts`.

Repository full names containing `/` must be URL-encoded (`%2F`).

```bash
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails'
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/gitlab.com/repositories/gitlab-org%2Fgitlab'
```

## Owners

```bash
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/owners/rails'
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/owners/rails/repositories?sort=stargazers_count&order=desc'
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/owners/lookup?email=andrewnez@gmail.com'
```

## Tags, releases, manifests, SBOM

```bash
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/tags'
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/releases'

# parsed dependency manifests for the default branch
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/repositories/numpy%2Fnumpy/manifests'

# manifests at a specific tag
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/repositories/numpy%2Fnumpy/tags/v2.0.0/manifests'

# CycloneDX SBOM
curl -s 'https://repos.ecosyste.ms/api/v1/hosts/GitHub/repositories/numpy%2Fnumpy/sbom'
```

## Package usage (dependent repositories)

Find repositories that depend on a package. Ecosystem here is the package manager name (`npm`, `pypi`, `cargo`, `bundler`, `go`, `maven`, `nuget`, `composer`), not the registry hostname.

```bash
# usage summary
curl -s 'https://repos.ecosyste.ms/api/v1/usage/npm/lodash'

# repos that depend on it
curl -s 'https://repos.ecosyste.ms/api/v1/usage/npm/lodash/dependent_repositories?per_page=20&sort=stargazers_count&order=desc'

# raw dependency edges (keyset pagination via ?after=ID)
curl -s 'https://repos.ecosyste.ms/api/v1/usage/npm/lodash/dependencies'
```

## Topics

```bash
curl -s 'https://repos.ecosyste.ms/api/v1/topics/cli?fork=false&archived=false'
```

## Pagination and filtering

List endpoints accept `?page=N&per_page=N`. Repository lists accept `fork=false`, `archived=false`, `created_after=ISO8601`, `updated_after=ISO8601`, `sort` and `order`.

## When to use

- The user asks about a repository on any forge (not just GitHub)
- Listing what dependencies a repo declares without cloning it
- Finding which projects use a given package in the wild
- Listing an organisation's repos sorted by stars or recency
- Getting a CycloneDX SBOM for an arbitrary public repo
