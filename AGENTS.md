# AGENTS.md — plugin-example-step

Standalone plugin repo for the `examplestep` capability (`verb:examplestep`) — the
reference verb-as-step proving both the build-emit and deploy-execute legs of
plugin execution. The plugin is a Go module at `candy/plugin-example-step/`
(module path
`github.com/opencharly/plugin-example-step/candy/plugin-example-step`); the root
`charly.yml` only declares `discover: candy` so the repo is a project and its
candy is scanned.

Canonical files:

- `candy/plugin-example-step/charly.yml` — the `plugin-example-step:` candy entity
  (`plugin:` block, `plan:` check).
- `candy/plugin-example-step/plugin.go` — the provider (`NewProvider()` +
  `NewMeta()`) and the `OpEmit` / `OpExecute` dispatch.
- `candy/plugin-example-step/schema/examplestep.cue` — the self-contained
  `#ExamplestepInput`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-internals:plugin` — the plugin authoring reference: the `plugin:`
  block, the unified Provider model, the per-plugin CUE-schema contract,
  placement. Load before touching the provider or schema.
- `/charly-internals:install-plan` — the executor reverse channel (`OpExecute`),
  the build-emit seam (`OpEmit`), and the InstallPlan IR.
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `go build ./...` in `candy/plugin-example-step/` — compile the plugin module.
- `go test ./...` in `candy/plugin-example-step/` — the plugin's Go tests.
- `charly box validate` at the repo root — the structural check (the candy +
  `plugin:` block, CUE schema).
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate.

## Modify this repo

- Edit the `plugin-example-step:` candy entity, the Go source, and
  `schema/examplestep.cue` **together**.
- The plugin serves BOTH op selectors — `OpEmit` (build fragment) and `OpExecute`
  (deploy marker + teardown reverse op). Keep both legs working; the consumer
  candies `candy/examplestep-consumer` and `candy/examplestep-deploy-consumer`
  witness them.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
