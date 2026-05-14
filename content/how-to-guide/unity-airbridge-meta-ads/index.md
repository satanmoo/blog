---
tags:
  - unity
  - airbridge_sdk
  - meta_ads
---
> [!NOTE] [[Unity 프로젝트에 Airbridge SDK 연동하기]]를 완료해야 한다.

> To collect the Meta Install Referrer, enter the Meta App ID as the key value for `Meta Install Referrer (Facebook App ID)` in the [SDK settings](https://help.airbridge.io/en/developers/flutter-sdk-v4#configure-sdk-settings).
> 
> Note that the decryption key must be submitted to the Airbridge dashboard to read the decrypted Meta Install Referrer. Refer to this [user guide](https://help.airbridge.io/en/guides/meta-business-channel-integration#how-to-provide-the-app-id-and-the-decryption-key) to learn how to enter the decryption key.
> 
> [Set up Meta Install Referrer collection](https://help.airbridge.io/en/developers/unity-sdk-v4#set-up-meta-install-referrer-collection)

### 1. Enter the Meta App ID as the key value for `Meta Install Referrer (Facebook App ID)` in the [SDK settings](https://help.airbridge.io/en/developers/flutter-sdk-v4#configure-sdk-settings).

![[images/images/KakaoTalk_20260203_175424728.png]]

[Facebook App Id]([)](https://www.facebook.com/help/1397933243846983?cms_id=1397933243846983)를 찾아서 **Airbridge > Airbridge Settings > Meta Install Referreal(Facebook App ID)** 필드를 채운다.

### 2. Provide the Facebook App ID and the decryption key to Airbridge

> Navigate to [Basic]>[Settings] and scroll down to the Android section. Copy the Install Referrer Decryption Key. If you don't see the Android section, click **Add Platform** and select **Google Play**.
> 
> [How to find the Decryption Key](https://help.airbridge.io/en/guides/meta-business-google-play-install-referrer#how-to-find-the-decryption-key)

![[Pasted image 20260205195347.png]]

> Copy the Install Referrer Decryption Key from Meta for Developers. Go to Airbridge dashboard **[Integrations]>[Ad Channel Integration]>[Meta Ads]>[Channel Integration]**, and paste the Decryption Key into the **[Install Referrer Decryption Key]** field. Make sure the Decryption Key is correct, as data will not be decrypted with an incorrect key.
> 
> [Register Decryption Key in Airbrdige](https://help.airbridge.io/en/guides/meta-business-google-play-install-referrer#register-decryption-key-in-airbridge)

![[화면 캡처 2026-02-05 195927.png]]






