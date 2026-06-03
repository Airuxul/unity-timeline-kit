# AGENTS — `com.air.unity-timeline-kit`

**Last Updated:** 2026-06-02 · **Scope:** canonical agent entry (this repository)

## User documentation

| File | Language |
|------|----------|
| [README.md](../README.md) | English |
| [README.zh-CN.md](../README.zh-CN.md) | Chinese |

## Agent documentation

| File | Purpose |
|------|---------|
| [AGENTS.md](AGENTS.md) | This file |
| [DOC_GOVERNANCE.md](DOC_GOVERNANCE.md) | Doc workflow for this repo |
| [CHANGELOG_AGENT.md](CHANGELOG_AGENT.md) | Agent change log |

## Package role

| Item | Value |
|------|--------|
| Package id | `com.air.unity-timeline-kit` |
| Layer | Standalone **domain** UPM package |
| Meta index | [AirUnityPackage](https://github.com/Airuxul/AirUnityPackage) (`config/registry.json`) |
| UPM dependency | `com.unity.timeline` only (`package.json`) |

**Must not** depend on `com.air.unity-game-core`, `com.air.game-core`, `com.air.unity-ui`, or other Air stack packages unless `package.json` / asmdef references change.

## Module map

| Path | Responsibility |
|------|----------------|
| `Runtime/Track/TrackEx.cs` | Generic track base; auto-creates track mixer `ScriptPlayable<TMixer>` |
| `Runtime/Clip/ClipEx.cs` | Clip bases (`ClipEx`, `ClipEx<TBehaviour>`) with sealed `CreatePlayable` |
| `Runtime/Behaviour/PlayableBehaviourEx.cs` | Playable behaviour base for clip/mixer types |
| `Runtime/Interfaces/ITimelineAssetLoader.cs` | Runtime asset load/unload abstraction |
| `Runtime/Interfaces/ITimelineReferenceCollector.cs` | Clips that contribute keys to reference manifest |
| `Runtime/Interfaces/IEditorBehaviour.cs` | Editor-only behaviour hooks for custom clips |
| `Runtime/Loader/ResourcesAssetLoader.cs` | Default `ITimelineAssetLoader` (Resources paths) |
| `Runtime/References/TimelineReferenceManifest.cs` | ScriptableObject listing Timeline external refs |
| `Runtime/PlayableDirectorEx.cs` | `PlayableDirector` wrapper: preload manifest, play, unload |
| `Editor/Export/TimelineReferenceExporter.cs` | Scan `TimelineAsset` → manifest (collector clips only) |
| `Editor/Inspector/PlayableDirectorExEditor.cs` | Inspector **Export / Update Reference Manifest** |
| `Samples/Custom Clip/` | UPM sample: custom track/clip/mixer pattern |
| `Samples/Spawn Prefab Clip/` | Additional example (prefab spawn + preload); not in `package.json` samples list |

Namespace: `TimelineKit` (runtime), `TimelineKit.Editor` (editor). Asmdefs: `com.air.TimelineKit.Runtime`, `com.air.TimelineExporter.Editor` (legacy names).

## Required reads before doc updates

1. `docs/AGENTS.md`
2. `docs/DOC_GOVERNANCE.md`
3. `README.md`, `README.zh-CN.md`

## Meta repository standards

When editing C# or package layout, also follow (meta repo):

- [C_SHARP_STANDARDS](https://github.com/Airuxul/AirUnityPackage/blob/main/.cursor/rules/C_SHARP_STANDARDS.md)
- [ARCHITECTURE](https://github.com/Airuxul/AirUnityPackage/blob/main/.cursor/rules/PACKAGE_ARCHITECTURE.md)
- [CONSTRAINTS](https://github.com/Airuxul/AirUnityPackage/blob/main/.cursor/rules/PACKAGE_CONSTRAINTS.md)

Doc skills (`doc-read-index`, `doc-generate-update`) live **only** in the meta repo `.cursor/skills/` — do not add skills under this package.

## Runtime preload contract

- Clips that need dynamic assets implement `ITimelineReferenceCollector` and register load keys.
- Editor: assign `PlayableDirectorEx` on a GameObject with a `TimelineAsset`, then **Export Reference Manifest** (creates/updates `TimelineReferenceManifest`).
- Runtime: set `PlayableDirectorEx.Loader` if not using `ResourcesAssetLoader`; call `PlayWithPreload()` then `Unload()` when finished.
- Built-in Unity Timeline clips are not collected; only kit collector clips participate in export.
