# multiplayer-fabric-build

V-Sekai fork of the Godot engine. Produces the binaries used by baker and zone. Pinned at commit `b27142e94`.

## Source layout

```
godot/            # Godot engine source; SConstruct is at godot/SConstruct
justfile          # Build recipes (build-platform-target, build-platform-templates, …)
.github/workflows/
  build.yaml                           # Full platform matrix → GitHub Release
  nightly-prune.yaml                   # Prune old nightly releases
  test-engine-patches.yaml             # Smoke-test engine patch branches
  test-module-http3.yaml               # CI for feat/module-http3
  test-module-multiplayer-fabric.yaml  # CI for feat/module-multiplayer-fabric
  test-module-speech.yaml              # CI for feat/module-speech
  test-multiplayer-fabric.yaml         # CI for combined multiplayer-fabric branch
  test-open-telemetry.yaml             # CI for feat/open-telemetry
```

SConstruct is at `godot/SConstruct`. Dockerfiles and local builds must `cd godot` or set `WORKDIR /build/godot`.

## Build flags used by this stack

| Consumer | Target | Precision | Extra flags |
|----------|--------|-----------|-------------|
| `multiplayer-fabric-baker` | `editor` | `double` | `accesskit=no linuxbsd_speechd=no` |
| `multiplayer-fabric-zone` | `template_release` | `double` | `accesskit=no linuxbsd_speechd=no` |

## Platform build matrix

`build.yaml` runs on every push to `main`. Produces a GitHub Release tagged `latest.v-sekai-editor-<run>`.

| Platform | Architecture | Targets |
|----------|-------------|---------|
| macOS | arm64 (native) | editor, templates |
| iOS | arm64 (native) | templates |
| Linux | x86_64 | editor, template_release |
| Windows | x86_64 | editor, template_release |
| Android | arm64 | editor, template_release |
| Web (WASM) | wasm32 | template_release |

Web editor is excluded. Web templates are included in `v-sekai-godot-templates`.

## Feature-branch CI

Each `test-*.yaml` workflow shallow-clones a feature branch and builds `template_release` for linuxbsd and windows, validating the patch compiles before merging.

```bash
gh workflow run test-module-http3.yaml --repo V-Sekai-fire/multiplayer-fabric-build
```

## Triggering a full build

```bash
gh workflow run build.yaml --repo V-Sekai-fire/multiplayer-fabric-build
```

Takes ~35–45 minutes.

## Conventions

- Godot modifications go in the `godot/` path.
- Use `just` recipes for local builds.
- Commit style: sentence case, no `type(scope):` prefix.
