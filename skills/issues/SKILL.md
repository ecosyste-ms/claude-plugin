---
name: issues
description: Query issues.ecosyste.ms for issue and pull request metadata and statistics across GitHub, GitLab and other forges. Use when checking how responsive a project is, average time to close, PR merge rate, contributor counts, or listing open issues without hitting the forge API directly.
allowed-tools: Bash(curl *)
---

# ecosyste.ms Issues API

Base URL: `https://issues.ecosyste.ms/api/v1`

All responses are JSON. No auth required. Set a `User-Agent` header. Lookup endpoints return a 302 redirect, so use `curl -L`.

Full OpenAPI spec: https://issues.ecosyste.ms/docs/api/v1/openapi.yaml

## Repository issue stats

```bash
curl -sL 'https://issues.ecosyste.ms/api/v1/repositories/lookup?url=https://github.com/rails/rails'
```

Or directly by host (repo names containing `/` must be URL-encoded as `%2F`):

```bash
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails'
```

Returns aggregate stats for all time and the past year: `issues_count`, `pull_requests_count`, `issues_closed_count`, `merged_pull_requests_count`, `avg_time_to_close_issue` (seconds), `avg_time_to_close_pull_request`, `issue_authors_count`, `pull_request_authors_count`, `avg_comments_per_issue`, `bot_issues_count`, `bot_pull_requests_count`, plus `past_year_*` variants of each.

## List issues and PRs

```bash
# open issues (not PRs)
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/issues?state=open&pull_request=false'

# PRs with a given label
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/issues?pull_request=true&label=activerecord'

# a single issue
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/issues/50000'

# label counts
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/repositories/rails%2Frails/labels'
```

Filters: `state` (open/closed), `pull_request` (true/false), `label`, `created_after`, `updated_after`, `sort`, `order`, `page`, `per_page`.

## Owners and authors

Aggregated issue stats across every repo owned by an org, and per-author activity:

```bash
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/owners/rails'
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/owners/rails/maintainers'
curl -s 'https://issues.ecosyste.ms/api/v1/hosts/GitHub/authors/dhh'
```

## Trigger a sync

If a repo isn't indexed yet, POST a job and poll it:

```bash
curl -s -X POST 'https://issues.ecosyste.ms/api/v1/jobs?url=https://github.com/owner/repo'
curl -s 'https://issues.ecosyste.ms/api/v1/jobs/<job-id>'
```

## When to use

- Assessing project health: are issues being closed, are PRs being merged
- Comparing maintainer responsiveness between projects
- Counting external vs bot contributions
- Listing issues/PRs without GitHub API rate limits
