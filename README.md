# lint-package-json

GitHub Action that lints every `package.json` in a repo with
[npm-package-json-lint](https://npmpackagejsonlint.org/), using a shared rule set that lives in
this repo's [.npmpackagejsonlintrc.json](.npmpackagejsonlintrc.json) — update it here and every
consuming repo picks it up.

## Usage

```yaml
- uses: bvandrc/lint-package-json@v1
```

Lints every `package.json` under the target directory (excluding `node_modules`). Requires Node on
the runner (present on all GitHub-hosted runners); no install step needed.

## Rules

All rules are `error` severity. Anything you disagree with is overridable — see below.

**Key order** — `prefer-property-order` enforces a canonical top-level key order covering both
classic and modern fields (`exports`, `types`, `sideEffects`, `workspaces`, `publishConfig`,
`browserslist`, and friends). Keys *not* in the list are ignored rather than rejected, so tool
config blocks won't fail the lint.

**Required fields** — `require-name`, `require-version`, `require-description`, `require-license`,
`require-repository`.

**Values and formats** — `name-format`, `version-format`, `description-format` (must start with a
capital letter), `valid-values-license` (a common SPDX allowlist plus `UNLICENSED`),
`valid-values-private`, `no-duplicate-properties`.

**Alphabetical** — `prefer-alphabetical-dependencies`, `prefer-alphabetical-devDependencies`.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `directory` | `.` | Directory to lint, recursively. Must be inside the workspace. |
| `config-file` | — | Path to a config whose rules override the defaults. |

### Overriding rules

`config-file` **merges** with the defaults per-rule rather than replacing them. Any rule you don't
name keeps its default; set a rule to `"off"` to switch it off.

```yaml
- uses: bvandrc/lint-package-json@v1
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

Two rules are the most likely to need overriding: `valid-values-license` if you ship under a
license outside the allowlist, and `require-license` / `require-repository` for private or
internal packages that legitimately omit them.
