# plugin-example-dispatch

The reference plugin for the **reverse legs** (`verb:exampledispatch` plus the
peer verb `verb:exampledispatchpeer`) — a plugin that, during its own invoke,
calls back to the host.

The plugin demonstrates the nested-broker round-trip: while the host is running
its `Invoke` (with a reverse channel attached), the plugin calls back to

- invoke **another plugin's verb** (`sdk.Executor.InvokeProvider` —
  plugin ↔ plugin via the host broker), and
- request a **host-build** (`sdk.Executor.HostBuild` — the host runs a registered
  host-builder).

It also proves the venue-scoped-executor-session seam: the caller may supply a
venue descriptor alongside a peer command, and the out-of-process peer runs that
command over whatever executor the host threads onto it — its own, or a fresh one
materialized from the descriptor.

## What it provides

| Capability | Surface |
|---|---|
| `verb:exampledispatch` | the dispatch verb — exercises the plugin↔plugin and host-build reverse legs |
| `verb:exampledispatchpeer` | the out-of-process peer target invoked over the nested broker |

## How to use it

The verb is a reference/regression surface driven by the host's own dispatch
tests, not a general authoring step. Compose the plugin candy where the reverse
legs are being exercised:

```yaml
- '@github.com/opencharly/plugin-example-dispatch/candy/plugin-example-dispatch:<tag>'
```

## Layout

- `candy/plugin-example-dispatch/` — the plugin module: `plugin.go` (the provider
  + `NewProvider()`/`NewMeta()` + the dispatch/peer paths),
  `schema/exampledispatch.cue` (the self-contained `#ExampledispatchInput`),
  `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-internals:plugin` — the plugin/provider model and the
  reverse-channel legs. The candy-specific owning `skill:` entity is not yet
  authored; the thematic batch cutover
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291)
  owns authoring it for every candy family, and this repo's docs leg advances
  that batch by recording its gap there
  ([wave-4 gap record](https://github.com/opencharly/opencharly/issues/291#issuecomment-5881645261)).
- `/charly-internals:install-plan` — the executor reverse channel.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
