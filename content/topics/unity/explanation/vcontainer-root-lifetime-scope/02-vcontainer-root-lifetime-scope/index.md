---
tags:
  - explanation
  - unity
  - vcontainer
aliases:
  - VContainer Root LifetimeScope (2) Preloaded Assets에서 RootLifetimeScope instance 생성까지
  - Preloaded Assets에서 RootLifetimeScope instance 생성까지
references:
---
# VContainer Root LifetimeScope (2) Preloaded Assets에서 RootLifetimeScope instance 생성까지

이 글은 **Preloaded Assets**에 등록된 `VContainerSettings`가 runtime에서 **runtime root instance**를 만들고 **root container**를 build하는 흐름을 정리함

## VContainerSettings.OnEnable

`VContainerSettings`는 *Preloaded Assets*에 들어가는 `ScriptableObject`

`ScriptableObject.OnEnable`은 해당 asset instance가 메모리에 로드될 때 호출됨
- [ScriptableObject.OnEnable](https://docs.unity3d.com/ScriptReference/ScriptableObject.OnEnable.html)에서 확인 가능

*Preloaded Assets*는 player startup 때 로드되고, player terminate까지 유지됨
- [PlayerSettings.GetPreloadedAssets](https://docs.unity3d.com/ScriptReference/PlayerSettings.GetPreloadedAssets.html)에서 확인 가능

따라서 `VContainerSettings`는 player startup 시점에 메모리에 로드되어 게임 시작 초기에 `OnEnable()`이 호출됨

`OnEnable()`의 핵심 동작은 다음과 같음
- 첫 scene이 이미 loaded면 즉시 `OnFirstSceneLoaded()`를 호출함
- 첫 scene이 아직 loaded 상태가 아니면 `SceneManager.sceneLoaded`에 callback을 등록함

바로 root `LifetimeScope`를 생성하는 것이 아니라 ==첫 번째 scene 로드 후== `OnFirstSceneLoaded()`를 호출하는 것이 중요함
- 이미 scene이 loaded 상태면 `sceneLoaded` event를 기다리지 않고 즉시 `OnFirstSceneLoaded()` 실행
- 아직 loaded 상태가 아니면 `sceneLoaded` event에 등록해서 나중에 scene 로드 후 실행
- [`VContainerSettings.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/VContainerSettings.cs) line 90에서 확인 가능

> [!note] Play Mode 시점
>
> Play Mode에서는 첫 번째 scene 로드 후 `OnFirstSceneLoaded()`가 호출됨

> [!note] 에디터 모드의 Preloaded Assets
>
> 에디터 모드에서 *Preloaded Assets*를 수동으로 로드하는 코드는 [`VContainerSettings.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/VContainerSettings.cs) line 62에서 확인 가능

## VContainerSettings.OnFirstSceneLoaded

이 함수의 역할은 **Project root LifetimeScope**를 생성할지 결정하는 gate

동작은 아래와 같음
- 조건을 모두 만족하면 `GetOrCreateRootLifetimeScopeInstance()`를 호출해 *Project root LifetimeScope*를 생성함
- 조건은 [`VContainerSettings.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/VContainerSettings.cs) line 114에서 확인 가능

## VContainerSettings.GetOrCreateRootLifetimeScopeInstance

`RootLifetimeScope` prefab을 실제 *runtime root instance*로 만드는 함수

동작은 다음과 같음
- prefab 인스턴스화
  - 인스턴스는 `activeSelf` 값이 `false`가 되도록 설정했기에 `Awake()`가 실행되지 않음
  - 인스턴스 생성 직후 바로 `Awake()`가 실행되는 것을 막는 목적임
	  - [MonoBehaviour.Awake](https://docs.unity3d.com/ScriptReference/MonoBehaviour.Awake.html)에서 확인 가능
- `DontDestroyOnLoad`로 `RootLifetimeScope` instance가 scene 전환 때 파괴되지 않도록 등록

인스턴스 후 `DontDestroyOnLoad` 동작을 완료하고 `Awake()`가 실행되도록 ==가드==

## LifetimeScope.Awake

*runtime root instance*가 생성되고, 위의 `GetOrCreateRootLifetimeScopeInstance()`에서 `activeSelf`를 코드로 직접 `true`로 만들면 `Awake()`가 실행됨

동작은 다음과 같음
- `autoRun`이 `true`면 `Build()`를 호출함
	- `autoRun`의 기본값은 `true`임
- [`LifetimeScope.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/LifetimeScope.cs) line 135에서 확인 가능

![[02-vcontainer-root-lifetime-scope-001.png]]

prefab에서도 `autoRun`의 기본값이 `true`임을 확인 가능

## LifetimeScope.Build

현재 `LifetimeScope`에 대응되는 **DI container**를 만들기 위해 `ContainerBuilder`를 구성하는 함수
- 현재 `LifetimeScope`는 `RootLifetimeScope`

동작은 다음과 같음
- 아직 *root container*가 생성되지 않은 상태이므로 아래 컨테이너 생성 동작 실행
  - `ContainerBuilder` 생성
  - build 완료 후 실행할 callback 등록
    - `SetContainer` callback 등록
	    - Root `LifetimeScope` instance의 멤버 `Container`에 container를 지정하는 동작
	    - [`LifetimeScope.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/LifetimeScope.cs) line 226에서 확인 가능
  - root `LifetimeScope`에 등록할 **registration**을 `ContainerBuilder`에 주입
  - `ContainerBuilder.Build()`를 호출해 *root container* 생성
- [`LifetimeScope.cs`](https://github.com/hadashiA/VContainer/blob/master/VContainer/Assets/VContainer/Runtime/Unity/LifetimeScope.cs) line 189에서 확인 가능

> [!note] Registration 흐름
>
> VContainer *registration* 흐름은 별도 글에서 다룸

## ContainerBuilder.Build

위 과정에서 생성한 *Root ContainerBuilder*가 *root container*를 생성하는 단계

동작은 다음과 같음
- **registry** 생성
	- *registration*을 container에 실제로 등록하는 저장소를 초기화
- container instance 생성
- 등록된 callback 호출
	- 여기서 ==반드시 호출되는 callback==은 위에서 언급한 `SetContainer`

> [!note] Registration callback 흐름
>
> Registration callback 흐름은 별도 글에서 다룸
