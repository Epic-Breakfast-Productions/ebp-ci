# EBP CI

Reusable CI workflows and composite actions for EBP GitHub projects.

## Usage

Reference workflows and actions from this repo using the full path:

```yaml
uses: Epic-Breakfast-Productions/ebp-ci/.github/workflows/quarkus-ci-pipeline.yml@main
```

---

## Reusable Workflows

### `quarkus-ci-pipeline.yml`

A full CI pipeline for Quarkus/Gradle services. Runs three parallel jobs after checkout: build, unit tests, and integration tests.

**Jobs:**
- **Build** — compiles the project, skipping all tests
- **Unit Tests** — runs `./gradlew test`, publishes JUnit results, checks JaCoCo coverage, and archives reports
- **Integration Tests** — runs `./gradlew quarkusIntTest`, publishes JUnit results, and archives reports

**Inputs:**

| Input | Description | Required | Default |
|---|---|---|---|
| `path` | Path to the service root relative to repo root (e.g. `software/core/my-service`) | Yes | — |
| `java-version` | Java version to build with | No | `21` |
| `run-int-tests` | Whether to run integration tests | No | `true` |
| `container-based-int-tests` | Whether integration tests require a container image build | No | `false` |
| `jacoco-min-coverage-overall` | Minimum overall JaCoCo line coverage (0–100) | No | `60` |
| `jacoco-min-coverage-changed` | Minimum JaCoCo line coverage for changed files (0–100) | No | `80` |

**Example caller workflow:**

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  ci:
    uses: Epic-Breakfast-Productions/ebp-ci/.github/workflows/quarkus-ci-pipeline.yml@main
    with:
      path: software/core/my-service
      java-version: '21'
      run-int-tests: true
      jacoco-min-coverage-overall: 60
      jacoco-min-coverage-changed: 80
```

---

## Composite Actions

### `setup-quarkus-runner`

Sets up Java (Temurin) and Gradle for Quarkus CI builds.

**Inputs:**

| Input | Description | Default |
|---|---|---|
| `java-version` | Java version to use | `21` |
| `cache-read-only` | Treat the Gradle cache as read-only. Use `false` for the build job, `true` for test jobs. | `false` |

**Example:**

```yaml
- name: Setup runner
  uses: Epic-Breakfast-Productions/ebp-ci/.github/actions/setup-quarkus-runner@main
  with:
    java-version: '21'
    cache-read-only: false
```
