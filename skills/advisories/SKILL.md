---
name: advisories
description: Query advisories.ecosyste.ms for security vulnerability metadata aggregated from GitHub Security Advisories, OSV, RustSec, ErlEf and others. Use when checking a package or repo for known CVEs, listing recent advisories for an ecosystem, or fetching CVSS/EPSS scores and patched version ranges.
allowed-tools: Bash(curl *)
---

# ecosyste.ms Advisories API

Base URL: `https://advisories.ecosyste.ms/api/v1`

All responses are JSON. No auth required. Set a `User-Agent` header. Results are deduplicated by CVE across sources.

Full OpenAPI spec: https://advisories.ecosyste.ms/docs/api/v1/openapi.yaml

## Look up advisories for a package or repo

By purl (include a version to get only advisories affecting that version) or by source repository URL:

```bash
curl -s 'https://advisories.ecosyste.ms/api/v1/advisories/lookup?purl=pkg:npm/lodash@4.17.20'
curl -s 'https://advisories.ecosyste.ms/api/v1/advisories/lookup?repository_url=https://github.com/rails/rails'
```

## List and filter advisories

```bash
# all advisories for a package
curl -s 'https://advisories.ecosyste.ms/api/v1/advisories?ecosystem=pypi&package_name=django'

# critical severity only, newest first
curl -s 'https://advisories.ecosyste.ms/api/v1/advisories?ecosystem=npm&severity=CRITICAL&sort=published_at&order=desc'

# everything published since a date
curl -s 'https://advisories.ecosyste.ms/api/v1/advisories?created_after=2026-01-01T00:00:00Z'
```

Filters: `ecosystem`, `package_name`, `severity` (LOW/MODERATE/HIGH/CRITICAL), `repository_url`, `source`, `created_after`, `updated_after`, `sort`, `order`, `page`, `per_page`.

## Single advisory

```bash
curl -s 'https://advisories.ecosyste.ms/api/v1/advisories/GHSA-jf85-cpcp-j695'
```

## Sources

```bash
curl -s 'https://advisories.ecosyste.ms/api/v1/sources'
```

## Response fields

`uuid`, `title`, `description`, `severity`, `cvss_score`, `cvss_vector`, `epss_percentage`, `epss_percentile`, `published_at`, `withdrawn_at`, `identifiers` (CVE/GHSA ids), `references`, `repository_url`, `blast_radius`, `source_kind`, `packages[]` (each with `ecosystem`, `package_name`, `purl`, `versions[].vulnerable_version_range`, `versions[].first_patched_version`, `affected_versions`, `unaffected_versions`).

## When to use

- Checking whether a specific package@version has known vulnerabilities
- Auditing a project's dependency list against known CVEs
- Finding the first patched version for an advisory
- Tracking new advisories published for an ecosystem since a given date
