---
tags:
  - explanation
  - unity
  - vcontainer
aliases:
  - VContainer Root LifetimeScope (3) Bootstrap Scene에서 root container 직접 제어하기
  - Bootstrap Scene에서 root container 직접 제어하기
---
# VContainer Root LifetimeScope (3) Bootstrap Scene에서 root container 직접 제어하기

[[topics/unity/explanation/vcontainer-root-lifetime-scope/02-vcontainer-root-lifetime-scope/index|2편]]에서 첫 번째 scene 로드 후 **root container**가 생성되는 것이 기본 동작임을 확인했음
- 따라서 [VContainer 문서](https://vcontainer.hadashikick.jp/scoping/project-root-lifetimescope)를 그대로 따르면 첫 번째 scene 로드 후 *root container*가 생성됨

사용자가 실제로 보는 첫 scene보다 먼저 *root container*를 생성하고 application 초기화를 완료하고 싶다면? 
- ==첫 scene 앞에 얇은 **Bootstrap Scene**을 두고 startup 과정을 수행하면 됨==
- *Bootstrap Scene*은 ==화면을 꾸미는 scene이 아니==라 application startup runner 역할
	- *root container*가 build되기 전에 끝나야 하는 작업을 여기서 실행하고, 성공했을 때만 `SplashScene`으로 넘어감

## Bootstrap Scene이 필요한 이유

*VContainer*의 *Root LifetimeScope*는 모든 씬의 LifetimeScope의 parent가 됨
- 그래서 API client, auth session, remote config source, device identity 같은 의존성을 root scope에 두기 좋음

문제는 이 의존성 중 일부가 ==비동기 초기화 결과에 의존==할 수 있다는 점임

따라서 얇은 *Bootstrap Scene*에서 정상 초기화를 끝낸 뒤 사용자가 보는 첫 scene으로 넘어가면, 앱에 필요한 모든 초기화 과정이 끝나고 사용자가 실제로 보는 첫 화면이 시작됨

## Build Settings 순서

Build Settings의 scene 순서는 명시적으로 잡음

```text
0. BootstrapScene
1. SplashScene
2. 이후 게임 scene
```

`BootstrapScene`은 ==가능한 작게== 유지함
- `BootstrapRunner` 같은 `MonoBehaviour` 하나
- 실패 표시 또는 retry를 위한 최소 UI
- 필요하면 bootstrap 전용 camera/canvas

*Bootstrap Scene*은 *root container*를 build하기 전에 필요한 외부 상태를 준비하고, build 성공 후 다음 scene으로 전환하는 runner임

## RootLifetimeScope prefab 설정

*RootLifetimeScope prefab*의 `LifetimeScope.autoRun`을 끔

![[03-vcontainer-root-lifetime-scope-001.png]]

[[topics/unity/explanation/vcontainer-root-lifetime-scope/02-vcontainer-root-lifetime-scope/index#LifetimeScope.Awake|2편의 LifetimeScope.Awake]]에서 `autoRun`이 `true`면 `Build()`를 호출함을 확인했음

따라서 `autoRun` 값을 `false`로 만들어 자동 *root container* build를 막음

주의할 점은 `VContainerSettings` 자체는 ==여전히 **Preloaded Assets**에 있어야 한다==는 점
- `BootstrapRunner` 같은 `MonoBehaviour`에서 `VContainerSettings.Instance`를 통해 settings asset에 접근해야 하기 때문

## BootstrapRunner 역할

`BootstrapRunner`는 아래 동작을 담당함
1. startup 과정에 필요한 의존성 개체 생성 및 초기화
2. `RootLifetimeScope` instance 생성
3. 의존성을 등록해 container build
4. 유저가 가장 먼저 보는 scene으로 전환

```csharp
using System;
using UnityEngine;
using UnityEngine.SceneManagement;
using VContainer;
using VContainer.Unity;

public sealed class BootstrapRunner : MonoBehaviour
{
    [SerializeField] private string splashSceneName = "SplashScene";

    private void Start()
    {
        try
        {
            // Bootstrap에 필요한 의존성
            var bootstrapConfig = new BootstrapConfig();
            var authSession = new AuthSession();
            var remoteConfig = new RemoteConfig();

            // Preloaded Assets로 준비된 VContainerSettings 확인
            var settings = VContainerSettings.Instance;
            if (settings == null)
            {
                throw new InvalidOperationException("VContainerSettings is not loaded.");
            }

            // RootLifetimeScope instance 생성
            var root = settings.GetOrCreateRootLifetimeScopeInstance();
            if (root == null)
            {
                throw new InvalidOperationException("RootLifetimeScope prefab is not assigned.");
            }

            // RootLifetimeScope container를 직접 build해야 하므로 이미 build된 상태인지 확인
            if (root.Container != null)
            {
                throw new InvalidOperationException("RootLifetimeScope is already built.");
            }

            // 의존성 등록 및 container build
            using (LifetimeScope.Enqueue(builder =>
            {
                builder.RegisterInstance(bootstrapConfig);
                builder.RegisterInstance(authSession);
                builder.RegisterInstance(remoteConfig);
            }))
            {
                root.Build();
            }

            // SplashScene, 즉 유저가 처음으로 보는 scene 로드
            SceneManager.LoadScene(splashSceneName);
        }
        catch (Exception ex)
        {
            Debug.LogException(ex);
            // retry UI, maintenance scene, fallback scene 등
        }
    }

    private sealed class BootstrapConfig
    {
    }

    private sealed class AuthSession
    {
    }

    private sealed class RemoteConfig
    {
    }
}
```
