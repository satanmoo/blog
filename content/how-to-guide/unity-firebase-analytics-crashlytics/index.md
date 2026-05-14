---
tags:
  - unity
  - firebase
  - google_analytics
  - firebase_crashlytics
references:
  - https://firebase.google.com/docs/unity/setup#prerequisites
  - https://firebase.google.com/docs/analytics/unity/start
  - https://firebase.google.com/docs/unity/setup-alternative
  - https://firebase.google.com/docs/android/android-play-services#google-play-services-required-recommended
---
## "Prerequisites" 확인

[Prerequisites](https://firebase.google.com/docs/unity/setup#prerequisites)를 확인하자.

---
## Create a Firebase project

### 1. Sign into the [Firebase console](https://console.firebase.google.com/?_gl=1*1c4pq8a*_ga*MTE1MTc4MTc2MS4xNzQ5NDYxOTUz*_ga_CW55HF8NVT*czE3NzAwMjk5MzMkbzEyJGcxJHQxNzcwMDMwNDI1JGo1MiRsMCRoMA..).

![[화면 캡처 2026-02-02 201344.png]]

### 2. Click the button to create a new Firebase project.

위 화면에서 "Create a new Firebase project"  선택

### 3. In the text field, enter a **project name**.

![[화면 캡처 2026-02-02 201953.png]]

> If you're part of a Google Cloud org, you can optionally select which folder you create your project in.
> 
> [Create a Firebase project](https://firebase.google.com/docs/unity/setup#create-firebase-project

새로운 프로젝트의 이름을 입력한다. "Firebase"는 "Google Cloud"에 속하는 프로젝트라서 이름이 연동된다.

### 4. If prompted, review and accept the [Firebase terms](https://firebase.google.com/terms), then click **Continue**.

프롬프트를 따라 갑시다.

### 5. _(Optional)_ Set up Google Analytics for your project, which enables an optimal experience using these Firebase products: [Firebase A/B Testing](https://firebase.google.com/docs/ab-testing), [Cloud Messaging](https://firebase.google.com/docs/cloud-messaging), [Crashlytics](https://firebase.google.com/docs/crashlytics), [In-App Messaging](https://firebase.google.com/docs/in-app-messaging), and [Remote Config](https://firebase.google.com/docs/remote-config) (including [Personalization](https://firebase.google.com/docs/remote-config/personalization)).

![[Pasted image 20260202202432.png]]

기존의 "Google Analytics" 서비스에 속했던 기능이 필요하면 추가하자

![[Pasted image 20260202202526.png]]

> Either select an existing [Google Analytics account](https://support.google.com/analytics/answer/1009618) or create a new account. If you create a new account, select your [Analytics reporting location](https://firebase.google.com/docs/projects/locations), then accept the data sharing settings and Google Analytics terms for your project.
> 
> [Create a Firebase project](https://firebase.google.com/docs/unity/setup#create-firebase-project)

"Google Analytics" 계정을 만드는 개념이다.
### 6. Create Project

프롬프트를 모두 완료했다면 프로젝트가 생성된다.

---
## Register your app with Firebase

> You can register one or more apps or games to connect with your Firebase project.
> 
> If you're releasing your game on both iOS and Android, register both build targets of your Unity project _with the same Firebase project_. If you have _multiple build variants_ with different iOS bundle IDs or Android app IDs defined, you must register each variant with the same Firebase project.
> 
> [Step 2: Register your app with Firebase](https://firebase.google.com/docs/unity/setup#register-app)

하나의 *Firebase project* 에 여러 게임/앱을 등록할 수 있다.

여러 빌드 변형(variant)이 있는 경우 서로 다른 빌드를 모두 같은 *Firebase project* 에 등록해야 한다.
### 1. Launch the setup workflow

![[Pasted image 20260203123250.png]]

> In the center of the project overview page, click the **Unity** icon (plat_unity) to launch the setup workflow.
> 
> If you've already added an app to your Firebase project, click **Add app** to display the platform options.
> 
> [Step 2: Register your app with Firebase](https://firebase.google.com/docs/unity/setup#register-app)

### 2. Select build target

> Select which build target of your Unity project that you’d like to register, or you can even select to register both targets now at the same time.
> 
> [Step 2: Register your app with Firebase](https://firebase.google.com/docs/unity/setup#register-app)

![[Pasted image 20260203125307.png]]

빌드 타겟을 선택한다.
- 복수 선택 가능

### 3. Enter Unity project’s platform-specific ID(s).

> **For iOS** — Enter your Unity project’s iOS ID in the [**iOS bundle ID**](https://cocoacasts.com/what-are-app-ids-and-bundle-identifiers/) field.
> 
> **For Android** — Enter your Unity project’s Android ID in the [**Android package name**](https://developer.android.com/studio/build/application-id) field. The terms _package name_ and _application ID_ are often used interchangeably.

빌드 타겟에 따라 *유니티 프로젝트 식별자* 를 찾아서 입력한다.
#### Where do you find your Unity project's ID?

![[화면 캡처 2026-02-03 134958.png]]

안드로이드의 경우 유니티 에디터의 **Android(Build Profile)  > Player Settings > Other Settings**. 에서 찾을 수 있다.

> [!NOTE] Make sure that you enter the ID that your project is actually using. The ID value is case-sensitive, and it cannot be changed for these Firebase apps after they're registered with your Firebase project.

주의사항
- 프로젝트 식별자는 "case-sensitive" 하다.
- *파이어 베이스 앱* 에 등록하고 바꿀 수 없다.

### 4. _(Optional)_ Enter your Unity project’s platform-specific nickname(s).  
    
> These nicknames are internal, convenience identifiers and are only visible to you in the Firebase console.

![[화면 캡처 2026-02-03 135731.png]]

내부적으로 사용할 닉네임도 입력할 수 있다.

프로젝트 식별자와 닉네임을 입력하고 "Register app" 을 누르면 앱이 등록된다.

---
## Add Firebase configuration files

![[Pasted image 20260203140620.png]]

프롬프트를 따라가면 된다.
1.  Download google-services.json
2. Open the **Project** window of your Unity project, then move your config file(s) into the `Assets` folder.

> [!NOTE] You can place the Firebase config file anywhere within the Assets folder.

**Assets** 폴더 내부이기만 하면 어디에 있어도 상관없다.

---
## Add Firebase Unity SDKs

> Rather than downloading a large `.zip` file containing all `.unitypackage` files for both .NET 3.X and .NET 4.X, you can download individual packages from the [Google APIs for Unity site](https://developers.google.com/unity/packages).
> 
> [Additional Unity installation options](https://firebase.google.com/docs/unity/setup-alternative#open-close-upm)

*individual pacakges* 를 선택해서 다운로드 받으면 된다.
- [Google Analytics](https://developers.google.com/unity/packages#google_analytics)
- [Firebase Crashlytics](https://developers.google.com/unity/packages#firebase_crashlytics)

`.unitypackage` 파일로 받았다면, 유니티 에디터의 **Assets** > **Import Package** > **Custom Package** 로 프로젝트에 추가할 수 있다.

### 제외할 폴더

- `Editor Default Resources`
- `ExternalDependencyManager` 관련 폴더
	- 보통 프로젝트에 *EDM4U* 를 기본적으로 설치하니까

> [!NOTE] *Google Analytics* , *Firebase Crashlytics* 모두에 해당한다.

---
## Confirm Google Play services version requirements

> Some products in the Firebase Unity SDK for Android require [Google Play services](https://developers.google.com/android/guides/overview). Learn [which products have this dependency](https://firebase.google.com/docs/android/android-play-services#google-play-services-required-recommended). Google Play services must be up-to-date before those products can be used.
> 
> [Confirm Google Play services version requirements](https://firebase.google.com/docs/unity/setup#confirm-google-play-version)

![[Pasted image 20260203151451.png]]

> _The Firebase SDK for Google Analytics can send events on any device, but some automatic insights such as demographics are only available on devices with Google Play services._
> 
> [Google Play services required or recommended](https://firebase.google.com/docs/android/android-play-services#google-play-services-required-recommended)

*Anlytics* 는 "Google Play services"가 추천 사항이다.

> Add the following `using` statement and initialization code at the start of your application. You can check for and optionally update Google Play services to the required version before calling any other methods in the SDK.
> 
> [Confirm Google Play services version requirements](https://firebase.google.com/docs/unity/setup#confirm-google-play-version)

```csharp
Firebase.FirebaseApp.CheckAndFixDependenciesAsync().ContinueWithOnMainThread(task => {
  var dependencyStatus = task.Result;
  if (dependencyStatus == Firebase.DependencyStatus.Available) {
    // Create and hold a reference to your FirebaseApp,
    // where app is a Firebase.FirebaseApp property of your application class.
       app = Firebase.FirebaseApp.DefaultInstance;

    // Set a flag here to indicate whether Firebase is ready to use by your app.
  } else {
    UnityEngine.Debug.LogError(System.String.Format(
      "Could not resolve all Firebase dependencies: {0}", dependencyStatus));
    // Firebase Unity SDK is not safe to use here.
  }
});
```

`CheckAndFixDependenciesAsync` 함수를 사용하면 필요한 의존성이 준비됐는지 점검하고, *Google Play services* 업데이트를 사용자에게 요구할 수 있다.

### Google Play Service 업데이트를 수동으로 사용자가 수행해야 되는 상황도 있다.

> Google Play services powers a broad set of SDKs on Android for building your app, enhancing privacy and security, engaging users, and growing your business. These SDKs only require a lightweight client library to be included in your app, as shown in figure 1. At runtime, the client library communicates with a full implementation running in the main Google Play services application.
> 
> [Overview of Google Play services](https://developers.google.com/android/guides/overview)

안드로이드 앱 빌드에는 경량의 *client library* 만 포함된다.

*Google Play Service* 는 사용자 디바이스에 설치되어 있다. 즉 *client library* 와 독립된 환경을 유지하며, 정기적으로 자동 업데이트 된다.
- 만약 사용자가 *client library* 가 포함된 앱을 실행했을 때 사용자 디바이스 *Google Play Service* 가 호환되지 않는다면, 사용자는 직접 "Play Store"에 이동해 *Google Play Service* 를 업데이트할 수 있다. (자동 업데이트 타이밍과 앱 실행 타이밍이 안 맞을 수 있음)
- `CheckAndFixDependenciesAsync` 함수는 *client library* 와 *Google Play Service* 의 호환을 확인하고 *Google Play Service* 의 업데이트를 유도한다.

---
## Get started with Google Analytics for Unity

[Log events](https://firebase.google.com/docs/analytics/unity/start#log_events) 를 참고해서 `LogeEvent` 함수를 작성해보자.

```C#
Firebase.Analytics.FirebaseAnalytics.LogEvent("[DEV] infra_initialized");
```

### 1. Firebase Analytics Event Debugging

> DebugView enables you to see the raw event data logged by your app on development devices in near real-time. This is very useful for validation purposes during the instrumentation phase of development and can help you discover errors and mistakes in your Analytics implementation and confirm that all events and user properties are logged correctly.
> 
> [Debug events](https://firebase.google.com/docs/analytics/debugview)

#### Enable debug mode

> Generally, events logged by your app are batched together over the period of approximately one hour and uploaded together. This approach conserves the battery on end users’ devices and reduces network data usage. However, for the purposes of validating your Analytics implementation (and, in order to view your Analytics in the DebugView report), you can enable debug mode on your development device to upload events with a minimal delay.
> 
> [Enable debug mode](https://firebase.google.com/docs/analytics/debugview#enable_debug_mode)

*Firebase Analytics Event* 의 기본 동작은 1시간 정도 배치로 모아서 일괄 업로드

디버그 모드를 활성화 하면 이벤트가 즉시 업로드 되어 *Debug View* 에서 확인할 수 있다.

![[Pasted image 20260204161742.png]]

> To enable Analytics debug mode on an Android device, execute the following commands:
> 
> `adb shell setprop debug.firebase.analytics.app PACKAGE_NAME`
> 
> This behavior persists until you explicitly disable debug mode by executing the following command:
> 
> `adb shell setprop debug.firebase.analytics.app .none.`

터미널을 실행하고 `adb shell setprop debug.firebase.analytics.app PACKAGE_NAME` 을 실행한다.
- 디버깅 모드를 중지하려면 `adb shell setprop debug.firebase.analytics.app .none.`

기기를 연결하고 실행하면 아래와 같이 *Firebaes console* 의 "DebugView" 에서 실시간으로 이벤트를 확인할 수 있다.

![[Pasted image 20260204165205.png]]

> [!INFO] 
> 
> [user_engagement](https://support.google.com/analytics/answer/11109416?sjid=9178904538690416515-NC#user-engagement) 이벤트는 실행하면 자동으로 업로드하는 이벤트다.
> 
> app_remove 이벤트도 패키지를 삭제하면 자동으로 업로드하는 이벤트다.

---

## Get started with Crashlytics for Unity

### 1. Initialize Crashlytics

```C#
**// Initialize Firebase
        Firebase.FirebaseApp.CheckAndFixDependenciesAsync().ContinueWith(task => {
            var dependencyStatus = task.Result;
            if (dependencyStatus == Firebase.DependencyStatus.Available)
            {
                // Create and hold a reference to your FirebaseApp,
                // where app is a Firebase.FirebaseApp property of your application class.
                // Crashlytics will use the DefaultInstance, as well;
                // this ensures that Crashlytics is initialized.
                Firebase.FirebaseApp app = Firebase.FirebaseApp.DefaultInstance;

                // When this property is set to true, Crashlytics will report all
                // uncaught exceptions as fatal events. This is the recommended behavior.
                Crashlytics.ReportUncaughtExceptionsAsFatal = true;

                // Set a flag here for indicating that your project is ready to use Firebase.
            }
            else
            {
                UnityEngine.Debug.LogError(System.String.Format(
                  "Could not resolve all Firebase dependencies: {0}",dependencyStatus));
                // Firebase Unity SDK is not safe to use here.
            }
        });**
```

위의 [[#Confirm Google Play services version requirements]] 에서 작성한 `CheckAndFixDependenciesAsync` 기반 코드에 `Crashlytics.ReportUncaughtExceptionsAsFatal = true;` 를 추가한다.
- [Initialize Crashlytics](https://firebase.google.com/docs/crashlytics/unity/get-started#add-sdk) 참고

### 2. _(Android only)_ Get set up for symbol uploading

> [!NOTE] **_This step is only required for Android apps that use IL2CPP._**

![[Pasted image 20260205163311.png]]

**Build Profile > Android > Player Settings > Other Settings > Configuration** 에서 *Scripting Backend* 가 *IL2CPP* 라면 이 과정이 필요하다.

> Crashlytics’s Unity SDK 8.6.1+ automatically includes NDK crash reporting, which allows Crashlytics to automatically report Unity [IL2CPP](https://docs.unity3d.com/Manual/IL2CPP.html) crashes on Android. However, to see symbolicated stack traces for native library crashes in the Crashlytics dashboard, you must upload symbol information at build time using the Firebase CLI.
> 
> [_(Android only)_ Get set up for symbol uploading](https://firebase.google.com/docs/crashlytics/unity/get-started#set-up-symbol-uploading)

*symbol information* 을 ==build time== 에 *Firebase CLI* 를 통해 업로드 해야 한다.

![[Pasted image 20260205164555.png]]

**Build Profile > Android > Platform Settings > Debug Symobls** 에서 *Public (Symbol_Table)* 또는 *Debugging (Full)* 을 체크하면 *symbol information* 이 생성된다.

> To get set up for symbol uploading, follow the instructions to [install the Firebase CLI](https://firebase.google.com/docs/cli).
> 
> If you've already installed the CLI, make sure to [update to its latest version](https://firebase.google.com/docs/cli#update-cli).
> 
> [_(Android only)_ Get set up for symbol uploading](https://firebase.google.com/docs/crashlytics/unity/get-started#set-up-symbol-uploading)


생성된 심볼을 *Firebae CLI* 로 업로드 하면 된다.

### 3. Build your project and upload symbols

> From the _Build Settings_ dialog, do one of the following: Build your APK directly from the Unity Editor. Before building, make sure the checkbox for **Create symbols.zip** is checked in the _Build Settings_ dialog.
> 
> [Build your project and upload symbols#Android](https://firebase.google.com/docs/crashlytics/unity/get-started#add-sdk)

[[#2. _(Android only)_ Get set up for symbol uploading]] 에서 본 것 처럼 유니티 에디터에서 심볼을 생성하도록 설정한다.

> Once your build has finished, generate a Crashlytics-compatible symbol file and upload it to Firebase servers by running the following Firebase CLI command:
> 
> `firebase crashlytics:symbols:upload --app=FIREBASE_APP_ID PATH/TO/SYMBOLS`

*Firebase CLI* 를 실행하고, 명령어를 입력한다.

> [!TIP] *Firebas App ID* 를 찾는 방법은 다음과 같다.
> 
> 1. In your `google-services.json` file, your App ID is the `mobilesdk_app_id` value
> 2. In the Firebase console, go to your [_Project settings_](https://console.firebase.google.com/project/_/settings/general/). Scroll down to the _Your apps_ card, then click on the desired Firebase App to find its App ID.

> [!TIP] PATH/TO/SYMBOLS 는 빌드 머신에서 생성된다. 
> 
> Built the APK directly from within Unity — PATH/TO/SYMBOLS is the path of the zipped symbol file generated in the project root directory when your build finished (for example: `myproject/myapp-1.0-v100.symbols.zip`).
> 
> APK, ABB 파일이 생성되는 폴더에 함께 생성된다.

![[Pasted image 20260205174228.png]]

*CLI 명령어* 에 추가적인 플래그는 위와 같다.

### 4. Force a test crash to finish setup

```C#
Firebase.Crashlytics.Crashlytics.Log("Test log from dev build.");  
Firebase.Crashlytics.Crashlytics.LogException(new Exception("Test exception from dev build."));
```

이런 함수를 작성해서 *CrashLytics* 가 제대로 동작하는지 확인해보자.






