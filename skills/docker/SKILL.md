---
name: docker
description: Query docker.ecosyste.ms for Docker Hub image metadata and the system/language packages installed inside images (via Syft SBOMs). Use when checking what's inside a Docker image, finding which images ship a given OS or language package, or browsing Linux distro variants.
allowed-tools: Bash(curl *)
---

# ecosyste.ms Docker API

Base URL: `https://docker.ecosyste.ms/api/v1`

All responses are JSON. No auth required. Set a `User-Agent` header.

Full OpenAPI spec: https://docker.ecosyste.ms/docs/api/v1/openapi.yaml

## Docker images

Image names containing `/` must be URL-encoded (`%2F`). Official images live under the `library/` namespace.

```bash
curl -s 'https://docker.ecosyste.ms/api/v1/packages/library%2Fnginx'
curl -s 'https://docker.ecosyste.ms/api/v1/packages/grafana%2Fgrafana'
```

Returns: `name`, `description`, `downloads`, `versions_count`, `latest_release_number`, `latest_release_published_at`, `dependencies_count`, `has_sbom`, `repository_url`.

## Image versions (tags) and their contents

```bash
curl -s 'https://docker.ecosyste.ms/api/v1/packages/library%2Fnginx/versions'
curl -s 'https://docker.ecosyste.ms/api/v1/packages/library%2Fnginx/versions/1.27.0'
```

The version response includes a `dependencies` array listing every OS and language package found in the image SBOM (ecosystem, name, version).

## Reverse lookup: which images contain a package

Ecosystem names here come from Syft: `deb`, `rpm`, `apk`, `npm`, `pypi`, `gem`, `golang`, `maven`, `cargo`, `nuget`, `composer`, `binary`. Package names with `/` must be URL-encoded.

```bash
# how many images ship this package, total downloads of those images
curl -s 'https://docker.ecosyste.ms/api/v1/usage/npm/lodash'
curl -s 'https://docker.ecosyste.ms/api/v1/usage/deb/openssl'
curl -s 'https://docker.ecosyste.ms/api/v1/usage/golang/github.com%2Fstretchr%2Ftestify'

# list all ecosystems with counts
curl -s 'https://docker.ecosyste.ms/api/v1/usage'

# packages in an ecosystem ranked by image count
curl -s 'https://docker.ecosyste.ms/api/v1/usage/apk?per_page=20'
```

## Linux distros

Distros and versions detected from `/etc/os-release` across indexed images:

```bash
curl -s 'https://docker.ecosyste.ms/api/v1/distros'
curl -s 'https://docker.ecosyste.ms/api/v1/distros/alpine-3-19'
curl -s 'https://docker.ecosyste.ms/api/v1/distros/alpine-3-19/versions'
```

## Pagination

List endpoints accept `?page=N&per_page=N`.

## When to use

- Inspecting what packages are bundled inside a Docker image without pulling it
- Finding which popular images include a vulnerable system library
- Estimating the Docker-side blast radius of a package
- Comparing base image distro choices
