# SDLC repository contract

This repository uses the unified SDLC workflow. `.sdlc.yml` is the machine-
validated configuration; the shared `sdlc` skill family is the source of truth
for agent behavior.

## Provider and branches

- Provider: Forgejo (`operator/source-control`), operated through the
  `source-control` skill's `sc` CLI. Do not call `tea`, `gh`, or provider APIs
  directly for issue, label, or pull-request operations.
- Default branch: `main`.
- Work-item branches follow `sdlc/{issue-number}-{short-slug}`.
- Pull requests are created only after confirmation (`pull_request.creation:
  ask`). The provider integration cannot merge; merging is always a human step.

## Labels

Labels run in `mapped` mode: skills use canonical names and `.sdlc.yml`
translates them to this repository's Forgejo labels.

| Canonical | Forgejo label |
| --- | --- |
| `sdlc`, `sdlc-feature`, `sdlc-work-item`, `sdlc-requirements`, `sdlc-design` | same name (created by setup) |
| `ready` | `status/committed` |
| `in-progress` | `status/in-progress` |
| `review` | `status/needs-review` |
| `complete` | `status/closed` |
| `blocked` | `status/blocked` |
| `needs-discovery` | `needs/refinement` |

## Commands

```sh
pnpm run validate
pnpm run sdlc:validate-config -- .sdlc.yml
```

Use PNPM for all project commands. `validate` currently checks `.sdlc.yml`;
extend it with tests and linting as they are added to this repository.

Use `sdlc-wayfinder` for planning and requirements, `sdlc-design` for approved
system design and work-item generation, `sdlc-orchestrate <work-item-id>` for
one implementation, `sdlc-orchestrate --batch` for scheduled bounded queue
execution, and `sdlc-reconcile <feature-issue-id>` after merges.

Batch execution requires isolated worktrees and uses the configured
`max_concurrency`. It may process independent work items, including items from
different features, but never bypasses explicit dependencies or provenance
gates. It never merges pull requests automatically.
