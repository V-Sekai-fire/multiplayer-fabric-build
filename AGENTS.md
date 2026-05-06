# multiplayer-fabric-build — Developer Reference

V-Sekai fork of the Godot engine. Source of truth for all Godot binaries used
by the multiplayer fabric stack. Pinned at commit `b27142e94`.

## Source layout

```
godot/            # Godot engine source (SConstruct is at godot/SConstruct)
justfile          # Build recipes (build-platform-target, build-platform-templates, …)
.github/workflows/
  build.yaml                        # Full platform matrix → GitHub Release
  nightly-prune.yaml                # Prune old nightly releases
  test-engine-patches.yaml          # Smoke-test engine patch branches
  test-module-http3.yaml            # CI for feat/module-http3
  test-module-multiplayer-fabric.yaml  # CI for feat/module-multiplayer-fabric
  test-module-speech.yaml           # CI for feat/module-speech
  test-multiplayer-fabric.yaml      # CI for combined multiplayer-fabric branch
  test-open-telemetry.yaml          # CI for feat/open-telemetry
```

**Important:** SConstruct is at `godot/SConstruct`, not at the repo root.
Dockerfiles and local builds must `cd godot` or set `WORKDIR /build/godot`.

## Build flags used by this stack

| Consumer | Target | Precision | Extra flags |
|----------|--------|-----------|-------------|
| `multiplayer-fabric-baker` | `editor` | `double` | `accesskit=no linuxbsd_speechd=no` |
| `multiplayer-fabric-zone` | `template_release` | `double` | `accesskit=no linuxbsd_speechd=no` |

## Platform build matrix (`build.yaml`)

Runs on every push to `main`. Produces a GitHub Release tagged `latest.v-sekai-editor-<run>`.

| Platform | Architecture | Targets |
|----------|-------------|---------|
| macOS | native (arm64) | editor, templates |
| iOS | native (arm64) | templates |
| Linux | x86_64 | editor, template_release |
| Windows | x86_64 | editor, template_release |
| Android | arm64 | editor, template_release |
| Web (WASM) | wasm32 | template_release only |

Web editor is excluded — web templates are included in `v-sekai-godot-templates`.

## Feature-branch CI

Each `test-*.yaml` workflow shallow-clones a feature branch (linuxbsd + windows,
`template_release` only) to validate patches compile before merging.

Trigger manually:
```bash
gh workflow run test-module-http3.yaml --repo V-Sekai-fire/multiplayer-fabric-build
```

## Triggering a full build

```bash
gh workflow run build.yaml --repo V-Sekai-fire/multiplayer-fabric-build
```

Build takes ~35–45 minutes. Artifacts are uploaded to a GitHub Release and also
available as workflow artifacts for downstream consumers.

## Conventions

- All Godot modifications are in the `godot/` submodule path.
- Use `just` recipes from the justfile for local builds.
- Commit style: sentence case, no `type(scope):` prefix.
