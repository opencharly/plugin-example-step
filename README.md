# plugin-example-step

The reference plugin proving **both legs of plugin execution for a verb-as-step**
— a standalone Go module that the host both build-emits and deploy-executes.

Composed as a candy `run:` step (`run: plugin: examplestep`), it exercises:

- **BUILD leg (`OpEmit`)** — at image build, charly's build-path connect seam
  host-builds and connects this plugin out-of-process and invokes its `OpEmit`;
  the returned Containerfile **fragment** (a `RUN` baking
  `/opt/examplestep-baked`) is spliced verbatim into the generated Containerfile.
- **DEPLOY leg (`OpExecute`)** — a `run: plugin: examplestep` step in a LOCAL/VM
  deploy is lowered to charly's `ExternalPluginStep`, which invokes `OpExecute`
  with the host's live executor on the go-plugin broker (the E3b reverse channel).
  The plugin writes a marker on the target venue and returns a plugin-script
  reverse op the host records and replays at teardown.

It is the verb-as-step analogue of the deploy-target `candy/plugin-example-deploy`.

## What it provides

| Capability | Surface |
|---|---|
| `verb:examplestep` | the `examplestep` verb-as-step — build-context `OpEmit` fragment + deploy-context `OpExecute` marker + teardown reverse op |

## How to use it

Compose the plugin candy in a box or check bed's `candy:` list, then author the
step:

```yaml
- '@github.com/opencharly/plugin-example-step/candy/plugin-example-step:<tag>'
```

```yaml
- run: plugin: examplestep
  plugin_input: {marker: my-step-marker}
```

The plugin's own ADE plan is a build-context check that the out-of-tree module is
present and buildable; the build-emit and deploy-execute legs are witnessed by the
consumer candies `candy/examplestep-consumer` and
`candy/examplestep-deploy-consumer`.

## Layout

- `candy/plugin-example-step/` — the plugin module: `plugin.go` (the provider +
  `NewProvider()`/`NewMeta()` + the `OpEmit`/`OpExecute` dispatch),
  `schema/examplestep.cue` (the self-contained `#ExamplestepInput`),
  `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-internals:plugin` — the plugin/provider model and the
  build/deploy execution legs. This candy carries no `skill:` entity of its own;
  the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- `/charly-internals:install-plan` — the executor reverse channel and the
  InstallPlan IR.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
