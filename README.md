# Timeline Kit (`com.air.unity-timeline-kit`)

[简体中文](README.zh-CN.md)

Unity Timeline extensions: generic Clip / Track / Mixer bases, reference manifest export, and pluggable runtime asset loading.

**Layer:** standalone domain package — depends on `com.unity.timeline` only; does not depend on `com.air.unity-game-core` or other Air stack packages.

## Install

```json
"com.air.unity-timeline-kit": "file:../CustomPackages/packages/com.air.unity-timeline-kit"
```

Requires Unity 2021.3+. Unity pulls `com.unity.timeline` 1.6.4+ from this package’s `package.json`.

## Quick start

### Custom tracks and clips

1. Subclass `TrackEx<TClip, TMixer>`, `ClipEx<TBehaviour>`, and `PlayableBehaviourEx` (see `Samples/Custom Clip/` or import the **Custom Clip** sample from Package Manager).
2. For clips that reference assets loaded at runtime, implement `ITimelineReferenceCollector` and register load keys.

### Preload and play

1. Add `PlayableDirectorEx` to a GameObject with a `PlayableDirector` and assign your `TimelineAsset`.
2. In the Inspector, click **Export Reference Manifest** (or **Update Reference Manifest** after edits).
3. Optionally assign a custom `ITimelineAssetLoader` on `PlayableDirectorEx.Loader` (default: `ResourcesAssetLoader`).
4. At runtime, call `PlayWithPreload()`; call `Unload()` when the sequence finishes.

## Key APIs

| Type | Role |
|------|------|
| `TrackEx<TClip, TMixer>` | Track with auto-created mixer playable |
| `ClipEx<TBehaviour>` | Clip with sealed `CreatePlayable` + `SetupBehaviour` |
| `PlayableDirectorEx` | Manifest-driven preload and play |
| `TimelineReferenceExporter` | Editor scan of collector clips → manifest |
| `ITimelineAssetLoader` | Load/unload backend (Resources, Addressables, etc.) |

## Samples

| Path | Notes |
|------|--------|
| `Samples/Custom Clip/` | Registered UPM sample — import via Package Manager |
| `Samples/Spawn Prefab Clip/` | Prefab spawn + preload example (copy or reference from repo) |

## Related

- Unity package: [Timeline](https://docs.unity3d.com/Packages/com.unity.timeline@latest)
- Meta repo: [registry.json](../../../config/registry.json) (`com.air.unity-timeline-kit`)
