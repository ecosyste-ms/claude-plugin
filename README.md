# ecosyste.ms skills

A [Claude Code plugin](https://code.claude.com/docs/en/plugins) providing skills for querying the [ecosyste.ms](https://ecosyste.ms) APIs.

## Install

Add the marketplace and install the plugin:

```
/plugin marketplace add ecosyste-ms/skills
/plugin install ecosystems@ecosystems
```

No local tools required, just `curl`.

## Skills

| Skill | Description |
|-------|-------------|
| [`packages`](skills/packages/SKILL.md) | Package, version and dependency metadata across 75+ registries |
| [`repos`](skills/repos/SKILL.md) | Repository metadata, manifests and dependents across 1900+ forges |
| [`advisories`](skills/advisories/SKILL.md) | Security advisories aggregated from GHSA, OSV, RustSec and others |
| [`issues`](skills/issues/SKILL.md) | Issue and PR statistics, time to close, contributor counts |
| [`commits`](skills/commits/SKILL.md) | Commit activity, committer stats, development distribution score |
| [`docker`](skills/docker/SKILL.md) | Docker image contents and which images ship a given package |

More services (summary, sponsors, timeline, parser and friends) to follow.

## Usage

Invoke skills directly:

```
/ecosystems:packages lodash
/ecosystems:repos https://github.com/rails/rails
```

Or just ask naturally:

```
What's the latest version of numpy on PyPI?
Which repos depend on left-pad?
Who maintains the express npm package?
```

## License

MIT
