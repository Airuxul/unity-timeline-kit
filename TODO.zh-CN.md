# 待办 — `com.air.unity-timeline-kit`

**最后更新：** 2026-06-03 · **范围：** Timeline Kit 现有功能的后续优化（中文）

> **职责边界：** 自定义轨道/片段基类、引用清单导出、`PlayableDirectorEx` 预加载/播放。  
> **不负责：** `GameRuntime`、UI、连接器、内置 Timeline 片段类型。  
> Agent 英文条目：[`docs/TODO.md`](docs/TODO.md)

## 现有能力概要

- `TrackEx` / `ClipEx` / `PlayableBehaviourEx`  authoring 模式
- `ITimelineReferenceCollector` → `TimelineReferenceManifest`（Resources 路径）
- `PlayableDirectorEx` + `ITimelineAssetLoader`（默认 Resources 加载器）
- 示例：Custom Clip（UPM 示例）、Spawn Prefab Clip（仅仓库）

## 待办列表

| ID | 优先级 | 标题 | 说明 |
|----|--------|------|------|
| TK-01 | P0 | Spawn Prefab 预加载键 | `AssetKey` 与导出 `resourcePath` 不一致。 |
| TK-02 | P0 | 清单过期校验 | 预加载前核对 `timelineGuid` 与当前 `playableAsset`。 |
| TK-03 | P1 | 清单条目去重 | 导出时重复 GUID。 |
| TK-04 | P1 | `ResourcesAssetLoader.Unload` | 按 key 卸载 vs 全局 `UnloadUnusedAssets`。 |
| TK-05 | P2 | Addressables 键钩子 | 填充 `addressableKey` 且不硬依赖 Addressables 包。 |
| TK-06 | P2 | 注册 Spawn Prefab 示例 | 写入 `package.json` samples。 |
| TK-07 | P3 | asmdef 文件重命名 | `TimelineExporter` → `TimelineKit`。 |
| TK-08 | P3 | 导出 UX 警告 | 非 Resources 资源使用默认加载器时提示。 |
| TK-09 | P3 | 编辑器播放模式测试 | 导出 → 预加载 → `GetLoadedAsset` 往返。 |

## 请勿在本包实现

| 主题 | 归属 |
|------|------|
| 过场编排、场景流 | 游戏 / `com.air.unity-game-core` 消费方 |
| Addressables 栈所有权 | 游戏基础设施（仅注入 Loader） |
| UI / 实体 | 其他 Air 包 |
