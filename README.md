# Renovate Config

Shared Renovate configuration presets for consistent dependency management.

## Basic Configurations

### Default Configuration

Basic setup with best practices:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["https://git.chaops.de/rwxd/renovate-config/raw/branch/main/default.json"]
}
```

### Standard Configuration (Recommended)

Automerges patch and minor updates silently. Major updates are not grouped globally, so each major opens as its own MR, with no approval step. No schedule is applied. It also forces `fix(deps)` commits for major, minor, and patch updates of production dependencies so semantic-release produces patch releases; development dependencies, pin, digest, and lock file maintenance updates stay `chore` so they do not trigger releases:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["https://git.chaops.de/rwxd/renovate-config/raw/branch/main/standard.json"]
}
```

## Presets

### Automerge

Located in `automerge/` directory. All use `automergeType: branch` for silent merges without PR notifications.

- **`automerge/all`**: Automerges all updates (major, minor, patch, pin, digest)
- **`automerge/major`**: Automerges major version updates
- **`automerge/minor`**: Automerges minor version updates
- **`automerge/patch`**: Automerges patch updates
- **`automerge/pin`**: Automerges pin updates (range → exact version)
- **`automerge/digest`**: Automerges digest updates (SHA hash updates)

### Groups

Located in `groups/` directory. Groups multiple updates into a single PR.

- **`groups/all`**: Groups all updates (major, minor, patch, pin, digest)
- **`groups/major`**: Groups all major updates into one PR
- **`groups/minor`**: Groups all minor updates
- **`groups/patch`**: Groups all patch, pin, and digest updates

### Schedule

Located in `schedule/` directory. Controls when Renovate runs.

- **`schedule/weekly`**: Runs on Mondays

### Commits

Located in `commits/` directory. Forces `fix(...)` semantic commit messages instead of Renovate's default `chore(...)` for production dependencies, so that semantic-release produces patch releases. Development/non-production dependencies keep `chore(...)` and do not trigger releases; `pin`, `digest`, and `lockFileMaintenance` updates keep their `chore` type as well.

The dev exclusion covers the common non-production depTypes (`devDependencies`, `dev-dependencies`, `dev`, `dev-packages`, `dependency-groups`, uv/pdm dev groups, `build-dependencies`, `setup`, `test`, `extra`, `tool`) and dev requirement file names for managers without a depType (`pip_requirements`, `pip-compile`). If production and dev updates end up in the same group (e.g. combined with `groups/*`), Renovate promotes the group to the highest commit type (`fix`), so the group still triggers a release.

- **`commits/major`**: Uses `fix(...)` commits for major updates
- **`commits/minor`**: Uses `fix(...)` commits for minor updates
- **`commits/patch`**: Uses `fix(...)` commits for patch updates

These presets are standalone and can be combined directly with `default.json`. Each one repeats the non-production `chore` rules, so when you combine several of them, keep the order `major`, `minor`, `patch` (as `standard.json` does) so those `chore` rules are applied last.

### Languages

Located in `languages/` directory. Per-language presets that group non-major updates (plus pin and digest for Python and JavaScript) and add a language label. The Go preset also sets `postUpdateOptions: ["gomodUpdateImportPaths"]` so import paths are updated on major module updates. These groups mix production and dev dependencies, so a group containing production updates gets a `fix` commit and triggers a release; combine with `commits/*` only if that is acceptable.

- **`languages/python`**: Groups non-major Python updates (including pin/digest) and labels them `python`
- **`languages/go`**: Groups non-major Go module updates, labels them `go`, and updates import paths on major updates
- **`languages/javascript`**: Groups non-major JavaScript/TypeScript updates (including pin/digest) and labels them `javascript`
- **`languages/rust`**: Groups non-major Rust crate updates and labels them `rust`

Example combining the default config with a language preset and fix commits:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/default.json",
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/languages/rust.json",
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/commits/patch.json"
  ]
}
```

## Custom Configuration

Mix and match presets to fit your workflow:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/default.json",
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/automerge/patch.json",
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/groups/minor.json"
  ]
}
```
