# hearthctl/.github

Shared CI for hearthctl repositories.

## Verification contract

Every repository exposes the same make targets:

- `make verify` runs everything that needs no extra infrastructure: formatting,
  linters, unit tests, build. It must pass before a pull request is opened.
- `make e2e` is optional and holds what needs infrastructure. Only CI runs it.

A pull request reports one check named `verify`. Each repository keeps a caller
workflow whose `verify` job installs its toolchain between the two shared
actions:

```yaml
name: CI
on: pull_request
permissions:
  contents: read
jobs:
  verify:
    runs-on: ubuntu-24.04
    steps:
      - uses: hearthctl/.github/checkout@main
      # install the repository's toolchain here
      - uses: hearthctl/.github/verify@main
```

`checkout` checks out the current tip of the pull request's base branch and
merges the pull request on top. A merge conflict fails the check with a
"needs rebase" message. Its `head` and `base` inputs name the two commits
instead, so a manually triggered run can verify a pull request on a fixed base
commit. `verify` runs `make verify` and then `make e2e` when
the Makefile defines that target.

## Agent image

`agent/Dockerfile` extends the Kelos Codex worker image with the toolchains
`make verify` needs in hearthctl repositories: a C and C++ compiler, Python 3,
lz4, helm and uv. A `v*` tag publishes it as `ghcr.io/hearthctl/agent` for
Linux amd64 and arm64. Name the tag after the Kelos release it builds on, with
a counter: `v0.64.0-1`.
