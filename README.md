# Cactus modules: org configuration

This repository holds the GitHub configuration shared by every module in the
`cactus-foundation-modules` organisation. Today that means one thing: the build
gate.

## The build gate

`.github/workflows/module-build-gate.yml` is a reusable workflow. Every module
repo calls it in three lines:

```yaml
jobs:
  gate:
    uses: cactus-foundation-modules/.github/.github/workflows/module-build-gate.yml@main
```

It composes the module under test into a real Cactus core checkout, gives it a
real throwaway Postgres, runs the migrations and builds the lot exactly as a
deploy would. A module repo on its own cannot do this: `tsc` and `eslint` there
see a folder of files with no core around them, so a React hook imported into a
file with no `'use client'` - which makes it a server component and fails the
bundler - sails past both. Tag such a version and the first machine ever to
build it is a site owner's, mid-update.

That happened on 2026-08-30 and cost a live site its ability to update at all.

It lives here rather than in the core repo because core's tree is checked out
into every install, and a GitHub App may only write under `.github/workflows/`
with a permission installs do not grant. A workflow file in core fails the whole
update transaction.

Roll the caller out from a core checkout with `node scripts/install-module-ci.mjs`.
