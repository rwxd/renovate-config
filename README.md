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

Automerges patch and minor updates silently, groups major updates into PRs:

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
- **`groups/major`**: Groups all major updates
- **`groups/minor`**: Groups all minor updates
- **`groups/patch`**: Groups all patch, pin, and digest updates

### Schedule

Located in `schedule/` directory. Controls when Renovate runs.

- **`schedule/weekly`**: Runs on Mondays

## Custom Configuration

Mix and match presets to fit your workflow:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/default.json",
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/automerge/patch.json",
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/groups/minor.json",
    "https://git.chaops.de/rwxd/renovate-config/raw/branch/main/groups/major.json"
  ]
}
```
