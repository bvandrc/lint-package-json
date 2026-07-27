# lint-package-json

GitHub Action that checks package.json top-level key order using
[npm-package-json-lint](https://npmpackagejsonlint.org/)'s `prefer-property-order` rule.
The canonical order lives in this repo's [.npmpackagejsonlintrc.json](.npmpackagejsonlintrc.json) —
update it here and every consuming repo picks it up.

## Usage

```yaml
- uses: bvandrc/lint-package-json@v1
```

Lints every package.json in the repo (excluding node_modules). Requires Node on the runner
(present on all GitHub-hosted runners); no install step needed.
