# Common Workflows

Reusable GitHub Actions workflows shared by StudiUM Moodle plugin repositories.

## Moodle plugin CI

The [`moodle-plugin-ci-template.yml`](.github/workflows/moodle-plugin-ci-template.yml) workflow runs the standard Moodle plugin checks, including:

- PHP linting and Mess Detector
- Moodle coding standards and PHPDoc checks
- Plugin validation and upgrade savepoint checks
- Mustache and Grunt linting
- PHPUnit tests
- Optional Behat features

It tests a configurable matrix of Moodle branches, PHP versions, and databases. Behat fail dumps are uploaded as workflow artifacts when the Behat job fails.

### Usage

Add a workflow to the Moodle plugin repository that consumes this reusable workflow:

```yaml
name: Moodle plugin CI

on:
  push:
  pull_request:

permissions:
  contents: read

jobs:
  test:
    uses: StudiUM/common-workflows/.github/workflows/moodle-plugin-ci-template.yml@main
    with:
      moodle-branches: '["MOODLE_501_STABLE"]'
      php-versions: '["8.4"]'
      databases: '["mariadb"]'
      dependent-plugins: |
        --branch main StudiUM/moodle-qtype_tcs
```

The `moodle-branches`, `php-versions`, and `databases` values are JSON arrays. Multiple values create a test matrix. The `dependent-plugins` input is a newline-separated list of arguments passed to `moodle-plugin-ci add-plugin`.

### Inputs

| Input | Default | Description |
| --- | --- | --- |
| `moodle-branches` | `["MOODLE_501_STABLE"]` | JSON array of Moodle branches to test. |
| `php-versions` | `["8.4"]` | JSON array of PHP versions to test. |
| `databases` | `["mariadb"]` | JSON array containing `mariadb` and/or `pgsql`. |
| `exclude` | `[]` | JSON array of matrix combinations to exclude. |
| `dependent-plugins` | empty | Newline-separated plugin arguments. |
| `phpcs-exclude` | `moodle.Files.BoilerplateComment` | Comma-separated PHPCS rules to exclude. |
| `max-phpcs-warnings` | `0` | Maximum allowed PHPCS warnings. |
| `max-phpdoc-warnings` | `0` | Maximum allowed PHPDoc warnings. |
| `max-grunt-lint-warnings` | `0` | Maximum allowed Grunt lint warnings. |
| `run-behat` | `true` | Whether to run Behat features. |

## Requirements

- The caller repository must contain a Moodle plugin compatible with `moodle-plugin-ci`.
- The referenced workflow must be available on the selected branch or tag.
- The caller must grant `contents: read` permission.
