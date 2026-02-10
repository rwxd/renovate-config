# Renovate Config

The shared configuration can be used with following content in the `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>rwxd/renovate-config"]
}
```

## Automerge Presets

- **`automerge-patch.json`**: Automerges patch updates, pins (range → exact version), and digest updates (SHA hash updates)
- **`automerge-minor.json`**: Automerges minor version updates
- **`automerge-major.json`**: Automerges major version updates

All use `automergeType: branch` for silent merges without PR notifications.

### Usage

Include the presets you want to enable:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>rwxd/renovate-config",
    "github>rwxd/renovate-config:automerge-patch",
    "github>rwxd/renovate-config:automerge-minor",
    "github>rwxd/renovate-config:automerge-major"
  ]
}
```
