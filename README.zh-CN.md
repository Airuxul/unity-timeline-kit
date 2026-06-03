# Timeline Kit（`com.air.unity-timeline-kit`）

[English](README.md)

Unity Timeline 扩展：通用 Clip / Track / Mixer 基类、引用清单导出、可插拔的运行时资源加载。

**分层：** 独立域包，仅依赖 `com.unity.timeline`；不依赖 `com.air.unity-game-core` 及其他 Air 栈包。

## 安装

```json
"com.air.unity-timeline-kit": "file:../CustomPackages/packages/com.air.unity-timeline-kit"
```

需要 Unity 2021.3+。本包 `package.json` 会自动拉取 `com.unity.timeline` 1.6.4+。

## 快速开始

### 自定义轨道与片段

1. 继承 `TrackEx<TClip, TMixer>`、`ClipEx<TBehaviour>`、`PlayableBehaviourEx`（参考 `Samples/Custom Clip/`，或在 Package Manager 中导入 **Custom Clip** 示例）。
2. 若片段需在运行时加载外部资源，实现 `ITimelineReferenceCollector` 并注册加载键。

### 预加载与播放

1. 在带 `PlayableDirector` 的 GameObject 上添加 `PlayableDirectorEx`，并指定 `TimelineAsset`。
2. 在 Inspector 中点击 **Export Reference Manifest**（修改 Timeline 后使用 **Update Reference Manifest**）。
3. 可选：在 `PlayableDirectorEx.Loader` 上注入自定义 `ITimelineAssetLoader`（默认 `ResourcesAssetLoader`）。
4. 运行时调用 `PlayWithPreload()`；结束后调用 `Unload()` 释放缓存。

## 主要 API

| 类型 | 作用 |
|------|------|
| `TrackEx<TClip, TMixer>` | 自动创建 Mixer 的轨道基类 |
| `ClipEx<TBehaviour>` | 密封 `CreatePlayable` 的片段基类 |
| `PlayableDirectorEx` | 基于清单的预加载与播放 |
| `TimelineReferenceExporter` | 编辑器扫描收集器片段并生成清单 |
| `ITimelineAssetLoader` | 加载/卸载后端（Resources、Addressables 等） |

## 示例

| 路径 | 说明 |
|------|------|
| `Samples/Custom Clip/` | 已注册 UPM 示例，可在 Package Manager 中导入 |
| `Samples/Spawn Prefab Clip/` | 生成 Prefab + 预加载示例（可从仓库复制参考） |

## 相关

- Unity 包：[Timeline](https://docs.unity3d.com/Packages/com.unity.timeline@latest)
- 元仓库：[registry.json](../../../config/registry.json)（`com.air.unity-timeline-kit`）
