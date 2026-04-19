---
tags:
  - unity
  - facebook_sdk
references:
  - https://developers.facebook.com/docs/unity/getting-started/android
  - https://developers.facebook.com/docs/unity/gettingstarted
---

## Facebook Unity SDK 설치하기

### 1. Download Facebook Unity SDK

[여기](https://developers.facebook.com/docs/unity)에서 다운로드 하면 된다.

### 2. Improt Pacakges

**Assets > Import Package > Custom Package…** 로 다운로드 받은 패키지를 유니티 프로젝트에 불러온다.

> [!NOTE] 
> 1. *Examples* , StreamingAssets* 은 제외하자.
> 2. 프로젝트에 이미 *ExternalDependencyManager* 가 존재하면 제외하자.
> 3. Unity6 를 사용한다면 *Packages*도 제외하자.

#### *Packages* 를 제외하는 이유

> When importing the Facebook Unity package, make sure to uncheck the "Packages" folder in the import window.  
   Facebook modifies the com.unity.ugui package, which causes errors in your project.  
   Disabling that folder during import solved the issue for me — hope this helps others too!
   >
   >[issues 743](https://github.com/facebook/facebook-sdk-for-unity/issues/743)

### 3. Configure FacebookSettings in Unity

**Facebook > Edit Settings** 클릭하면 스크립터블 오브젝트가 열린다.

![[Pasted image 20260123165752 1.png]]

**Facebook App Dashboard** 에서 *App Name*, *App Id*, *Client Token* 을 복사해서 넣어주자.

"고급 설정" > "보안" 에서 *Client Token* 찾기

![[KakaoTalk_20260203_175403350.png]]

"일반 설정" 에서 *App Name*(표시 이름), *App Id* 찾기

![[images/images/KakaoTalk_20260203_175424728.png]]

*Android Keystore Path* 를 입력하자. 로컬에서 *Keystore* 경로를 넣어주면 *Debug Android Key Hash* 가 생성된다. 

![[Pasted image 20260123195216.png]]

### 4. Sync Unity Settings with Facebook App Settings

> Now, let's add the Bundle ID, Key Hash, and Class Name to your app's [Facebook settings](https://developers.facebook.com/apps/). Go to the 'Android' pane in the Basic tab of your app's 'Settings' page, and fill in the 'Bundle ID', 'Key Hashes' , and 'Class Name' fields. Now save your changes.
> 
> [Step 3: Configure Bundle ID and Key Hash](https://developers.facebook.com/docs/unity/getting-started/android)

"Pacakge Name", "Class Name", "Debug Android Hash" 를 "Facebook App Settings"에 입력하면 된다.

![[Pasted image 20260123195306.png]]

### 5. Initialize Facebook SDK in Unity

```C#
public void InitializeFacebookSdk()  
{  
    if (!FB.IsInitialized)  
    {        FB.Init(OnFacebookInitialized);  
    }    else  
    {  
        FB.ActivateApp();  
    }}  
  
private void OnFacebookInitialized()  
{  
    if (FB.IsInitialized)  
    {        FB.ActivateApp();  
        Debug.Log("[Infra] Facebook SDK 초기화 완료");  
    }    else  
    {  
        Debug.LogError("[Infra] Facebook SDK 초기화 실패");  
    }}
```

*Facebook SDK* 를 초기화 하는 코드를 작성하자

> [!cite] [Initialize the SDK](https://developers.facebook.com/docs/unity/examples#init)

### 6. Set Android Application Entry Point (Activity vs GameActivity)

> DeploymentOperationFailedException: No activity in the manifest with action MAIN and category LAUNCHER. Try launching the application manually on the device.

위와 같은 에러가 나오고 빌드가 실패할 수 있다.

> Well. Under Player Setting you can see the Application Entry Point, it allows you to choose between Activity or GameActivity. Not sure whether your project has default setting choosing Activity or not, but worth to check that.
> 
> [Failed to build 18.0.0 in Unity 6 with GameActivity option.](https://github.com/facebook/facebook-sdk-for-unity/issues/751)

![[화면 캡처 2026-01-26 155039.png]]

**Player Settings** > **Android** > **Application Entry Point**

*Activity* 만 체크한다.

### 7. Prevent Code Stripping with ProGuard

> For more control over the minification process, generate a custom `proguard.txt` file and configure it to specify what not to strip. To generate the file, select **Custom Proguard File** in the **Publishing Settings** section. This generates the `proguard.txt` file in your project’s `Assets/Plugins/Android` folder. For information on how to configure ProGuard minification, see the [ProGuard documentation](https://www.guardsquare.com/manual/configuration/usage).
> 
> [Minification](https://docs.unity3d.com/Manual/android-optimize-distribution-size.html)

![[화면 캡처 2026-02-03 200836.png]]

"Player Settings" > "Publishing Settings" > "Build" > "Custom Proguard File" 체크

체크 결과 `Assets\Plugins\Android\proguard-user.txt` 파일이 생성된다.

> Hey [@aaronhoogstraten](https://github.com/aaronhoogstraten) , can you try adding `-keep class com.facebook.internal.* {*;}` and `-keep class com.facebook.* {*;}` in your proguard file?
> 
> [issues 294](https://github.com/facebook/facebook-sdk-for-unity/issues/294)

생성된 `proguard-user.txt` 파일에 `-keep class com.facebook.** { *; }`를 추가하자.

![[화면 캡처 2026-01-26 164233.png]]

