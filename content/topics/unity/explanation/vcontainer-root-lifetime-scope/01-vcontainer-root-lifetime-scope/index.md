---
tags:
  - explanation
  - unity
  - vcontainer
aliases:
  - VContainer Root LifetimeScope (1) Project root LifetimeScope 등록
  - Project root LifetimeScope 등록
references:
  - https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/VContainerSettings.cs
  - https://docs.unity3d.com/ScriptReference/PlayerSettings.GetPreloadedAssets.html
---
# VContainer Root LifetimeScope (1) Project root LifetimeScope 등록

이 글에서 확인할 내용은 **Project root LifetimeScope**를 유니티 에디터에서 등록하는 흐름

> [!quote]
>
> You can specify a root LifetimeScope that will be the parent of all LifetimeScopes.

[공식 문서](https://vcontainer.hadashikick.jp/scoping/project-root-lifetimescope)가 제시하는 흐름은 다음과 같음
- root `LifetimeScope` prefab 생성
- `VContainerSettings` asset 생성
- `Root Lifetime Scope` 필드 지정

위 과정을 통해 모든 `LifetimeScope`의 parent가 될 *Project root LifetimeScope*를 명시할 수 있음

## RootLifetimeScope prefab 생성

씬에서 빈 `GameObject`를 만들고 root용 `LifetimeScope` 컴포넌트를 붙인 뒤 prefab으로 저장함

```csharp
using VContainer.Unity;

public sealed class RootLifetimeScope : LifetimeScope
{
}
```

## VContainerSettings 생성

`VContainer`가 프로젝트에 추가되어 있으면 `Assets -> Create -> VContainer -> VContainer Settings`로 `VContainerSettings`를 생성할 수 있음

![[01-vcontainer-root-lifetime-scope-001.png]]

![[01-vcontainer-root-lifetime-scope-002.png]]

`VContainerSettings`의 정체는 `ScriptableObject`
- [`VContainerSettings.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/VContainerSettings.cs)에서 확인 가능

`VContainerSettings`를 위 방식으로 생성하면 자동으로 **Preloaded Assets**에 등록됨
- `Assets/Create/VContainer/VContainer Settings`의 동작은 [`VContainerSettings.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/VContainerSettings.cs) line 31에서 확인 가능
- *Preloaded Assets*의 동작은 [PlayerSettings.GetPreloadedAssets](https://docs.unity3d.com/ScriptReference/PlayerSettings.GetPreloadedAssets.html)에서 확인 가능

![[01-vcontainer-root-lifetime-scope-003.png]]

`Edit/Project Settings`에서 `VContainer Settings`가 *Preloaded Assets*로 등록된 것을 확인할 수 있음

## Root Lifetime Scope 필드 지정

`VContainerSettings` 인스펙터의 `Root Lifetime Scope` 필드에 RootLifetimeScope prefab 을 지정함

![[01-vcontainer-root-lifetime-scope-004.png]]
