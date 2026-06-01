# Timeline Kit (`timeline-kit`)

Unity Timeline æ‰©å±•åº“ï¼Œæä¾›ï¼?
1. **æ‰©å±•æ¡†æ¶** â€?è‡ªå®šä¹?Clip / Track / Mixer çš„æ³›å‹åŸºç±?2. **å¼•ç”¨å¯¼å‡º** â€?æ‰«æ Timeline èµ„æºï¼Œç”Ÿæˆ?`TimelineReferenceManifest`
3. **åŠ¨æ€åŠ è½?* â€?`ITimelineAssetLoader` æŠ½è±¡ï¼ˆResourcesã€Addressables æˆ–è‡ªå®šä¹‰åç«¯ï¼?
## å®‰è£…

```json
"timeline-kit": "file:../CustomPackages/packages/timeline-kit"
```

- Unity **2021.3+**
- `com.unity.timeline` **1.6.4+**ï¼ˆè§ `package.json` dependenciesï¼?- Package Manager å¯å¯¼å…?**Custom Clip** ç¤ºä¾‹

---

## Directory Structure

```
Runtime/
  Interfaces/
    ITimelineReferenceCollector.cs â†?implement on clip assets to declare references
    ITimelineAssetLoader.cs        â†?abstraction for runtime asset loading
  Loader/
    ResourcesAssetLoader.cs        â†?Resources.Load implementation
  References/
    TimelineReferenceManifest.cs   â†?ScriptableObject storing collected references

Editor/
  Behaviour/
    PlayableBehaviourEx.cs         â†?base class for clip and mixer behaviours
    CustomPlayableBehaviour.cs     â†?example clip behaviour
    CustomMixPlayableBehaviour.cs  â†?example mixer behaviour
    Editor/
      CustomEditorBehaviour.cs     â†?example editor preview behaviour
  Clip/
    ClipEx.cs                      â†?generic base clip asset
    CustomClip.cs                  â†?example clip asset
  Track/
    TrackEx.cs                     â†?generic base track asset
    CustomTrack.cs                 â†?example track
  Interfaces/
    IEditorBehaviour.cs            â†?editor-only preview interface
  Export/
    TimelineReferenceExporter.cs   â†?exports TimelineReferenceManifest from a Timeline
```

---

## 1 â€?Extending Timeline

### Minimal example: 3 files, 1 with logic

**Step 1 â€?Define the clip asset (serialized data + optional reference declaration)**

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Timeline;
using TimelineKit;
using TimelineKit.Editor;

[Serializable]
public class SpawnClip : ClipEx<SpawnBehaviour>, ITimelineClipAsset, ITimelineReferenceCollector
{
    public ClipCaps clipCaps => ClipCaps.None;

    public GameObject prefab;   // referenced asset
    public float delay;

    protected override void SetupBehaviour(SpawnBehaviour behaviour)
    {
        behaviour.prefab = prefab;
        behaviour.delay  = delay;
    }

    // Declare which fields hold asset references (for TimelineReferenceManifest)
    public IEnumerable<(UnityEngine.Object asset, string fieldName)> CollectReferences()
    {
        if (prefab) yield return (prefab, nameof(prefab));
    }
}
```

**Step 2 â€?Define the behaviour (runtime logic)**

```csharp
using UnityEngine;
using UnityEngine.Playables;
using TimelineKit.Editor;

public class SpawnBehaviour : PlayableBehaviourEx
{
    public GameObject prefab;
    public float delay;

    public SpawnBehaviour()
    {
        // Optional: assign _editorBehaviour for editor scrubbing preview
        // _editorBehaviour = new SpawnEditorBehaviour();
    }

    public override void OnBehaviourPlay(Playable playable, FrameData info)
    {
        if (IsEditorPreview) { _editorBehaviour?.Editor_OnBehaviourPlay(playable, info); return; }
        if (prefab) Object.Instantiate(prefab);
    }
}
```

**Step 3 â€?Declare the track (zero code body)**

```csharp
using UnityEngine.Timeline;
using TimelineKit.Editor;

[TrackClipType(typeof(SpawnClip))]
public class SpawnTrack : TrackEx<SpawnClip, SpawnMixBehaviour> { }
```

---

## 2 â€?Editor/Runtime Separation

`PlayableBehaviourEx` exposes two hooks:

| Hook | When called |
|---|---|
| `_editorBehaviour.Editor_*` | Unity edit mode (Timeline scrubbing, not playing) |
| Standard `PlayableBehaviour` overrides | At runtime (play mode / build) |

Use `IsEditorPreview` to branch inside overridden methods:

```csharp
public override void ProcessFrame(Playable playable, FrameData info, object playerData)
{
    if (IsEditorPreview)
    {
        _editorBehaviour.Editor_ProcessFrame(playable, info, playerData);
        return;
    }
    // runtime per-frame logic
}
```

---

## 3 â€?Reference Export

### Export from the Editor

Right-click any `.playable` asset in the Project window and choose:

```
Timeline Kit â†?Export Reference Manifest
```

This creates a `TimelineReferenceManifest` ScriptableObject next to the Timeline asset.

### Implement `ITimelineReferenceCollector`

Only clips that implement `ITimelineReferenceCollector` are scanned. The fields you yield from `CollectReferences()` are recorded in the manifest.

```csharp
public IEnumerable<(UnityEngine.Object asset, string fieldName)> CollectReferences()
{
    if (prefab) yield return (prefab, nameof(prefab));
    if (audioClip) yield return (audioClip, nameof(audioClip));
}
```

### TimelineReferenceManifest fields

| Field | Description |
|---|---|
| `assetGuid` | Unity GUID |
| `assetPath` | Full project-relative path |
| `assetType` | Fully qualified C# type |
| `resourcePath` | Key for `Resources.Load` (empty if not under Resources) |
| `addressableKey` | Addressables key (fill in after export if using Addressables) |
| `clipType` | Clip asset class that holds the reference |
| `fieldName` | Field name on that clip asset |

---

## 4 â€?Dynamic Loading at Runtime

```csharp
ITimelineAssetLoader loader = new ResourcesAssetLoader();

// Load a prefab using the resourcePath from the manifest
var prefab = loader.Load<GameObject>(reference.resourcePath);

// Release when done
loader.Unload(reference.resourcePath);
```

For Addressables, implement `ITimelineAssetLoader` wrapping `Addressables.LoadAssetAsync<T>`.

---

## Changelog

### 0.1.0
- Renamed package from `com.air.timeline-exporter` to `timeline-kit`.
- Removed Timeline playback simulation (TimelinePlayer, ClipPlayableContext, etc.).
- Focused runtime on reference export and dynamic loading.
- Simplified `PlayableBehaviourEx`: removed IPlayableBehaviour delegation; users override standard PlayableBehaviour methods directly.
- Generic `ClipEx<TBehaviour>` seals `CreatePlayable` to guarantee `SetupBehaviour` is always called.
- `TrackEx<TClip, TMixer>` auto-creates the mixer; no user code needed.
- Added `ITimelineReferenceCollector`, `TimelineReferenceManifest`, `TimelineReferenceExporter`.
- Added `ITimelineAssetLoader` + `ResourcesAssetLoader`.
