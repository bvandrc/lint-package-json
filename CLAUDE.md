## Project

`lint-package-json` — a composite GitHub Action that lints every `package.json` in a repo with [npm-package-json-lint](https://npmpackagejsonlint.org/), against the shared rule set in `.npmpackagejsonlintrc.json`.

- **Layout**: `action.yml` is the whole action — a bash step that merges the caller's optional `config-file` over the defaults, then runs the linter through `npx`. `.npmpackagejsonlintrc.json` is the rule set those defaults come from.
- **No build, no package**: there is no `package.json` here and nothing is published to npm. Consumers reference the action by ref (`bvandrc/lint-package-json@v2`).
- **Versioned by tag**: a rule change that can fail a repo which passed before is a major, since consumers pin `@v2` and get it on their next run without asking. The README's "Upgrading from v1" section is the shape that note takes.

## Code conventions

Conventions live outside this file, synced from https://github.com/bvandrc/bvandrc-conventions — follow all of them:

@conventions/all.md — practice for every repo: branches, formatting, comments, testing, markdown, PR reviews

Only `all.md` is synced. The language and framework files have nothing here to apply to: the action is shell and a JSON rule set.

## Repo conventions

- **Convention files**: `conventions/` is synced from https://github.com/bvandrc/bvandrc-conventions by `.github/workflows/sync-conventions.yml` and overwritten on every sync. Edit a rule upstream, never in that directory.
