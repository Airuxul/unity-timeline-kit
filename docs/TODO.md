# TODO — `com.air.unity-timeline-kit`

**Last Updated:** 2026-06-03 · **Owner:** package maintainers · **Scope:** Timeline Kit follow-ups (English)

> **User doc (Chinese):** [../TODO.zh-CN.md](../TODO.zh-CN.md)

> **Boundary:** Custom track/clip bases, reference manifest export, `PlayableDirectorEx` preload/play.  
> **Out of scope:** `GameRuntime`, UI, connector, built-in Timeline clip types.  
> **Meta rollup:** [AirUnityPackage `docs/TODO_ROADMAP.md`](https://github.com/Airuxul/AirUnityPackage/blob/main/docs/TODO_ROADMAP.md)

## Capability baseline

- `TrackEx` / `ClipEx` / `PlayableBehaviourEx` authoring pattern
- `ITimelineReferenceCollector` → `TimelineReferenceManifest` (Resources paths)
- `PlayableDirectorEx` + `ITimelineAssetLoader` (default `ResourcesAssetLoader`)
- Samples: Custom Clip (UPM sample), Spawn Prefab Clip (repo only)

## TODO

| ID | Pri | Title | Description |
|----|-----|-------|-------------|
| TK-01 | P0 | Spawn Prefab preload key | `AssetKey = prefab.name` vs exporter `resourcePath` mismatch. |
| TK-02 | P0 | Manifest staleness guard | Verify `manifest.timelineGuid` vs current `playableAsset` before preload. |
| TK-03 | P1 | Deduplicate manifest entries | Exporter appends duplicate GUIDs per clip field. |
| TK-04 | P1 | `ResourcesAssetLoader.Unload` | Per-key unload vs global `UnloadUnusedAssets`. |
| TK-05 | P2 | Addressables key hook | Fill `addressableKey` without hard UPM dependency. |
| TK-06 | P2 | Register Spawn Prefab sample | Add to `package.json` `samples`. |
| TK-07 | P3 | Rename asmdef files | `TimelineExporter` filenames → `TimelineKit`. |
| TK-08 | P3 | Export UX warnings | Non-Resources assets when default loader is Resources. |
| TK-09 | P3 | Editor play-mode tests | Export → preload → `GetLoadedAsset` round-trip. |

## Do not assign here

| Topic | Owner package |
|-------|----------------|
| Cutscene orchestration, scene flow | Game / `com.air.unity-game-core` consumer |
| Addressables stack ownership | Game infra (inject loader only) |
| UI / entity systems | Other Air packages |
