# .github

Shared workflows for the `mortennordbye` repositories.

## Dependency updates

Every repository uses Dependabot with a 7-day cooldown. Security updates go in a group named
`security`. Two reusable workflows handle the pull requests:

- `dependabot-automerge.yml` arms auto-merge. The merge happens only when the required checks
  in the repository ruleset pass.
- `dependabot-ci-failed.yml` labels a red Dependabot pull request `ci-failed` and requests a
  review from the owner. This is the only case that needs a person.

There are two tiers:

- Active repositories (headroom, logeverylift, verksted): patch, minor and security updates
  merge alone. A major gets the `needs-review` label.
- Quiet repositories: all updates merge alone, majors too (`allow-major: true`).

`skip-directories` keeps a manifest directory out of auto-merge, for example
`'["/runtime/cli"]'`.

When the repository has the `RELEASE_APP_CLIENT_ID` and `RELEASE_APP_PRIVATE_KEY` secrets in
its Dependabot secret store, the merge runs as that GitHub App. Then the push workflows on
`main` (releases, image builds) run for the merged update.

## Caller

```yaml
name: Dependabot auto-merge
on: pull_request
permissions:
  contents: write
  pull-requests: write
jobs:
  auto-merge:
    uses: mortennordbye/.github/.github/workflows/dependabot-automerge.yml@<sha> # v1
    with:
      allow-major: true
    secrets: inherit
```
