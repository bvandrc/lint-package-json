# lint-package-json

GitHub Action that lints every `package.json` in a repo with
[npm-package-json-lint](https://npmpackagejsonlint.org/), using a shared rule set that lives in
this repo's [.npmpackagejsonlintrc.json](.npmpackagejsonlintrc.json) — update it here and every
consuming repo picks it up.

## Usage

```yaml
- uses: bvandrc/lint-package-json@v2
```

Lints every `package.json` under the target directory (excluding `node_modules`). Requires Node on
the runner (present on all GitHub-hosted runners); no install step needed.

## Rules

All rules are `error` severity. Anything you disagree with is overridable — see below.

**Key order** — `prefer-property-order` enforces a canonical top-level key order covering both
classic and modern fields (`exports`, `types`, `sideEffects`, `workspaces`, `publishConfig`,
`browserslist`, and friends). Keys *not* in the list are ignored rather than rejected, so tool
config blocks won't fail the lint.

**Required fields** — `require-name`, `require-version`, `require-license`.

**Values and formats** — `name-format`, `version-format`, `valid-values-private`,
`no-duplicate-properties`.

**Alphabetical** — `prefer-alphabetical-dependencies`, `prefer-alphabetical-devDependencies`.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `directory` | `.` | Directory to lint, recursively. Must be inside the workspace. |
| `config-file` | — | Path to a config whose rules override the defaults. |

### Overriding rules

`config-file` **merges** with the defaults per-rule rather than replacing them. Any rule you don't
name keeps its default; set a rule to `"off"` to switch it off.

The file uses npm-package-json-lint's own
[configuration format](https://npmpackagejsonlint.org/docs/configuration/) — note that `extends`
won't pull in this action's defaults, since they aren't published as a package; the `config-file`
merge is what carries them over.

```yaml
- uses: bvandrc/lint-package-json@v2
  with:
    config-file: .github/package-json-lint-overrides.json
```

```json
{
  "rules": {
    "prefer-property-order": ["error", ["name", "version", "private", "scripts", "dependencies"]],
    "require-license": "off"
  }
}
```

That example swaps in a custom key order and drops the license requirement — every other rule
above still applies.

The most likely rules to need overriding are `prefer-alphabetical-dependencies` /
`prefer-alphabetical-devDependencies` if you don't keep deps sorted, and `require-license` for
private or internal packages that legitimately omit it.

## Upgrading from v1

v1 enforced key order only. v2 adds required-field, format, value, and alphabetical-ordering
rules, so repos that passed under v1 can fail under v2 without their `package.json` changing.

Either fix the new findings, or pin the rules back to v1 behaviour with a `config-file` that turns
off everything except key order:

```json
{
  "rules": {
    "require-name": "off",
    "require-version": "off",
    "require-license": "off",
    "name-format": "off",
    "version-format": "off",
    "valid-values-private": "off",
    "no-duplicate-properties": "off",
    "prefer-alphabetical-dependencies": "off",
    "prefer-alphabetical-devDependencies": "off"
  }
}
```

The v2 key order list is also wider than v1's, so keys like `exports`, `types`, and `workspaces`
are now order-enforced where v1 ignored them.
