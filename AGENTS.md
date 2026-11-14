# AGENTS.md

## What this repo is

Pure Renovate preset collection: standalone JSON partial configs consumed by other repos via raw URLs. No app code, no `package.json`, no tests, no build step.

- Presets are referenced as `https://git.chaops.de/rwxd/renovate-config/raw/branch/main/<path>.json`.
- Consumers resolve from **`main`**, and this repo's own `.renovaterc.json` extends `standard.json` via that URL, so local edits do not affect any Renovate run until pushed.
- A new preset that other presets reference (e.g. from `standard.json`) only resolves after the file exists on `main`.

## Layout

- `default.json` — base preset; extends `config:best-practices`, sets `minimumReleaseAge: "14 days"`, `internalChecksFilter: "strict"`.
- `standard.json` — recommended preset: `default` → `automerge/{digest,pin,patch,minor}` → `commits/{major,minor,patch}`.
- `automerge/`, `groups/`, `schedule/`, `commits/`, `languages/` — one standalone preset per file; the filename is the preset slug.
- `README.md` documents every preset; update it whenever you add or change one.
- `.forgejo/workflows/lint.yml` is the only CI job (validates all `*.json`).

## Verify

```sh
npx --yes --package renovate -- renovate-config-validator --no-global <files...>
```

- `--no-global` is required; without it files are validated as self-hosted global config.
- CI pins `renovate@44` and detects `--no-global` support before passing it; local `npx` may be newer.
- Do not `npm install` in this repo; use `npx` only (CI uses Node 25 + globally installed Renovate).
- The validator catches unknown keys/options and invalid `packageRules`, but does not prove URL presets resolve.

## Renovate precedence (drives the file layout)

- `extends` is ordered: later presets win. `packageRules` arrays are concatenated and the **last matching rule wins**.
- Override presets must come after `default.json` (that is why `commits/*` is last in `standard.json`).
- `config:best-practices` already applies `:semanticPrefixFixDepsChoreOthers`: production deps get `fix`, dev deps get `chore`. A broad `semanticCommitType: "fix"` without a later dev-dep `chore` rule makes dev updates trigger semantic-release patch releases.
- Grouped updates take the highest commit type by priority `chore < ci < build < fix < feat`; a mixed prod+dev group becomes `fix` and still triggers a release.
- `commits/{major,minor,patch}` each repeat the dev `chore` rules; when combining them keep the `major, minor, patch` order so the `chore` rules land last.
- Do not group updates in presets: with `minimumReleaseAge: "14 days"` + `internalChecksFilter: "strict"`, a group stays unmergeable until every dependency in it matures. `groups/*` is opt-in only and is not used by `standard.json` or `languages/*`.
- Global `minimumReleaseAge` + `internalChecksFilter: "strict"` stalls `lockFileMaintenance` (no release timestamps). `default.json` nulls `minimumReleaseAge` for it — keep that rule.

## Quirks

- These managers do not exist: `uv`, `conda`, `pnpm`, `yarn`, `volta`, `corepack`, `cargo-binstall`. Use `pep621`/`pip-compile` (uv), `npm` (pnpm/yarn), `cargo`, `rust-toolchain`.
- Categories: Go is `golang`, JavaScript is `js`.
- Scope language rules with `matchManagers`; custom filenames go in `managerFilePatterns` on the manager object, not the legacy `fileMatch`.
- `pip_requirements` and `pip_setup` expose no `depType`; dev files must be separated with `matchFileNames` (see `commits/*`).
- `rangeStrategy` cannot appear in a rule with `matchUpdateTypes` (validator error), and `commitMessagePrefix` disables semantic commits.
- Go import-path rewriting is opt-in via `languages/go-import-paths.json`; keep it out of `standard.json` (it downloads `mod@latest`, rewrites imports across the repo, forces `go mod tidy`, and forces a PR on artifact failure).

## Git

- Conventional Commits (semantic-release keys off commit types); commit only when explicitly asked, then push.
