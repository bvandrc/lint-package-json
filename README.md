# lint-package-json

GitHub Action that lints every `package.json` in a repo with [npm-package-json-lint](https://npmpackagejsonlint.org/), against a shared rule set defined in [.npmpackagejsonlintrc.json](.npmpackagejsonlintrc.json).

## Usage

```yaml
- uses: bvandrc/lint-package-json@v2
```

Lints every `package.json` under the target directory, excluding `node_modules`.

## Rules

All rules are `error` severity. Anything you disagree with is overridable — see below.

| Rule | Enforces |
| --- | --- |
| `prefer-property-order` | A canonical top-level key order. Keys *not* in the list are ignored rather than rejected, so tool config blocks won't fail the lint. |
| `require-name` | `name` is present |
| `require-version` | `version` is present |
| `require-license` | `license` is present |
| `name-format` | Lowercase only, URL-friendly characters, no leading period |
| `version-format` | An exact semver version, not a range: `1.0.0` passes, `1.0` and `^1.0.0` don't |
| `valid-values-private` | `private` is `true` or `false` |
| `no-duplicate-properties` | No repeated top-level keys |
| `prefer-alphabetical-dependencies` | `dependencies` sorted alphabetically |
| `prefer-alphabetical-devDependencies` | `devDependencies` sorted alphabetically |

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `directory` | `.` | Directory to lint, recursively. Must be inside the workspace. |
| `config-file` | — | Path to a config whose rules override the defaults. |

### Overriding rules

`config-file` **merges** with the defaults per-rule rather than replacing them. Any rule you don't name keeps its default; set a rule to `"off"` to switch it off.

The file uses npm-package-json-lint's own [configuration format](https://npmpackagejsonlint.org/docs/configuration/) — note that `extends` won't pull in this action's defaults, since they aren't published as a package; the `config-file` merge is what carries them over.

Point the input at your config:

```yaml
- uses: bvandrc/lint-package-json@v2
  with:
    config-file: .github/package-json-lint-overrides.json
```

Example override file:

```json
{
  "rules": {
    "prefer-property-order": ["error", ["name", "version", "private", "scripts", "dependencies"]],
    "require-license": "off"
  }
}
```

That example swaps in a custom key order and drops the license requirement — every other rule above still applies.

## Upgrading from v1

v1 enforced key order only. v2 adds required-field, format, value, and alphabetical-ordering rules, so repos that passed under v1 can fail under v2 without their `package.json` changing.

Either fix the new findings, or pin the rules back to v1 behaviour with a `config-file` that turns off everything except key order:

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
