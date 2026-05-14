---
tags:
  - playconsole
references: https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401
---
# Google Play Console release guide
## Create your app

### Open Play Console

> 1. Open [Play Console](https://play.google.com/console)
>    
> [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

![[화면 캡처 2026-01-30 152512.png]]

### Click "Creat app"

> 2. Select **Home** > **Create app**.
> 
> [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

![[화면 캡처 2026-01-30 152512 1.png]]

"Home" 에서 보이는 대시보드에서 초록색으로 하이라이트한 "Creat app" 클릭

### Add "App name" & Select "Default language"

> 3. Select a default language and add the name of your app as you want it to appear on Google Play. You can change this later.
>    
> [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

![[화면 캡처 2026-01-30 153545.png]]

"App name", "Default language"를 작성한다.
- 나중에 바꿀 수 있다.

### Select "App or game"

> 4. Specify whether your application is an app or a game. You can change this later.
>    
> [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

![[화면 캡처 2026-01-30 154034.png]]

"App or Game"에서 선택한다.

### Select "Free or paid"

> 5. Specify whether your application is free or paid.
>    
> [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

![[화면 캡처 2026-01-30 154554.png]]

"Free or paid"를 선택한다.

### Select the checkbox for each item under "Declarations"

![[화면 캡처 2026-01-30 161114.png]]

> 6. Add an email address that Play Store users can use to contact you about this application.
>    
> [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

이메일 기입하는 곳은 안 보일 수 있다.

> 7. In the "Declarations" section:
    - Acknowledge the “Developer Program Policies” and “US export laws” declarations.
    - Accept the [Play App Signing Terms of Service](https://play.google.com/about/play-app-signing-updated-terms/).
   [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

"Declarations" 섹션에서 “Developer Program Policies” 그리고 “US export laws” 항목을 체크한다.

### Click "Create App"

> 8. Select **Create app**.
>    
> [Create your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

"Create App" 을 클릭하며 앱이 생성된다.

---
## Set up your app

> After you create your app, you can start setting it up. Your app’s dashboard will guide you through all the most important steps to get your app available on Google Play.
> 
> [Set up your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

앱을 생성하고 "Dashboard" 에 들어가면 "guide"가 나온다.

![[화면 캡처 2026-01-30 164105.png]]

> You’ll start by providing details about your app’s content, and entering information for your Google Play store listing. After that, you can move onto app release; this guides you through pre-release management, testing, and promotion to build pre-release excitement and awareness. The final step is launching your app on Google Play, making it available to billions of users.
> 
> [Set up your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

"You’ll start by providing details about your app’s content, and entering information for your Google Play store listing." >> 이 문장이 "Set up your app" 가이드의 과정을 요약한 것이다.

"Set up your app" 가이드 말고도 여러 가이드가 있다.

목적은 "us"(**Google Play**)에 우리 앱의 정보를 제공하는 것이다. 이 정보에 따라 스토어에서 어떻게 보여줄 지 결정한다.

### Set privacy policy

"개인정보처리방침" 을 호스팅하고 URL이 필요하다.

#### Generate Privacy Policy

[개인정보처리방침 생성기]((https://www.privacypolicygenerator.info/)를 사용해 생성한다.
- 생성형 AI를 사용해도 무방하다.

*github page* 등 호스팅 업체를 통해 호스팅하고 URL을 얻는다,

![[화면 캡처 2026-01-30 180145.png]]

URL을 입력하고 "Save"

### App access

![[Pasted image 20260130181447.png]]

리뷰어에게 게임의 기능에 제한이 걸려있다고 알려주는 개념이다.
- 게임의 기능에 제한이 없으면, 리뷰어가 게임의 모든 기능에 자유롭게 접근해 리뷰할 수 있다.
- 게임의 기능에 제한이 있는 경우, "instructions"를 명시해 리뷰할 방법을 리뷰어에게 알려주어야 한다.

### Ads

![[화면 캡처 2026-01-30 182009.png]]

광고 포함 여부에 따라 선택하면 된다.

### Content ratings

![[화면 캡처 2026-01-30 200834.png]]

"questionnaire" 을 작성하면 "Content ratings" 결과가 나온다.

### Target audience

![[화면 캡처 2026-01-30 201307.png]]

"Target age" 항목부터 순서대로 해결하면 된다

### Data safety

![[화면 캡처 2026-01-30 203009.png]]

문항에 읽고 대답하면 됩니다.

> [!TIP] 설정된 Data safety를 수정하는 방법은 "Monitor and improve" > "App content" > "Actioned"

### Government apps, Financial features, Health

똑같이 진행하면 된다.

### Select an app category and provide contact details

![[Pasted image 20260202164743.png]]

> [!TIP] 설정된 "Store settings"를 변경하려면 "Grow users" > "Store presence" > "Store settings"


### Set up your store listing

![[Pasted image 20260202165221.png]]

스토어에 보여지는 정보를 입력한다.

> [!TIP] "Grow users" > "Store presence" > "Store listings" 에서 변경할 수 있다.

### Create a merchant account, Set the price of your app

그대로 진행하면 된다.

![[Pasted image 20260202170032.png]]

무료 앱이면 간단하게..

### Testing requirements

> Developers with personal accounts created after November 13, 2023, must meet specific testing requirements before they can make their app available on Google Play. Read [this Help Center article](https://support.google.com/googleplay/android-developer/answer/14151465) to learn more.
> 
> [Set up your app](https://support.google.com/googleplay/android-developer/answer/9859152?hl=en&ref_topic=12233401)

2023/11/13 이후 생성한 개발자 계정은 특정 요구사항을 만족해야 테스트 및 출시 가능하다.

> If you have a newly created personal developer account, you must run a closed test for your app with a minimum of 12 testers who have been opted-in for at least the last 14 days continuously. When you meet these criteria, you can apply for production access on the [**Dashboard**](https://play.google.com/console/developers/app/app-dashboard) in Play Console so that you can ultimately distribute your app on Google Play. When you apply, you must answer some questions to help us understand your app, its testing process, and its production readiness.
> 
> You can read about the different types of testing tracks and requirements for each in more detail below, and find more details about applying for production access.
> 
> [Overview of testing requirements](https://support.google.com/googleplay/android-developer/answer/14151465)

조건은 다음과 같다.
- 최소 12명의 테스터를 구성해 최소 14일 연속으로 "closed test" 수행하기

---

## Starting testing now

> Release your app early for internal testing without review

"internal testing" 은 리뷰가 필요없다.

![[화면 캡처 2026-02-02 175254.png]]

"Play Console Dashboard"에서 "Starting testing now" 가이드를 따라가면 된다.

### Select testers

![[화면 캡처 2026-02-02 175813.png]]

"internal testing"의 테스터를 구성한다.

### Create a new release

![[화면 캡처 2026-02-02 180737.png]]

"AAB" 파일을 생성해 업로드 하면 된다.

![[화면 캡처 2026-02-02 183012.png]]

"Track" 의 상태가 "Active"가 되었다.

---

## Release your app

> Test your app with a larger group of testers that you control
> 
> With closed testing, you can test your app with larger groups of testers. You can control access using email addresses or Google Groups.

"closed testing"을 사용하면 더 많은 수의 사람과 테스트할 수 있다.

### SET UP YOUR CLOSED TEST TRACK

#### Select countries and regions, Select testers

![[화면 캡처 2026-02-02 183954.png]]

가이드에 따라서 "Countries/regions" 탭에서 출시할 나라와 지역을 선택하면 된다.

"Testers" 탭에서 테스터 선택하면 된다.

### CREATE AND ROLL OUT A RELEASE

"Create new release" 버튼을 누르면 "Create closed testing release" 창으로 이동한다.

![[화면 캡처 2026-02-02 184941.png]]

내부 테스트와 마찬가지로 "App bundels" 섹션에 AAB 파일을 업로드 하고 "Release details" 섹션을 작성하면 된다.

### "Publishing overview" 확인하기

![[화면 캡처 2026-02-02 184750.png]]

심사를 올리기 전에 미리 "Play Console"에서 점검을 해준다. 
- "quick checks"

여기서 이슈가 발생하면 고치고 심사를 올리면 된다.

### Open testing

![[화면 캡처 2026-02-02 194231.png]]

이미 "Closed Testing" 이 "Release" 상태라면 "Promote release" 기능이 활성화 된다.

상위 테스트로 승격하는 개념이다.

기존에 배포한 "Closed testing"을 "Open testing"으로 승격해보자.

![[화면 캡처 2026-02-02 194615.png]]

승격 콤보박스에서 "Open testing"을 선택하면 "Create open testing release" 탭으로 이동한다.

"App integrity", "App bundles", "Release details" 수정 사항이 생기면 수정하면 된다.





