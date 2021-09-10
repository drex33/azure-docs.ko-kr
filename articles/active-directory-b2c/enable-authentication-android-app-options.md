---
title: Azure Active Directory B2C를 사용하여 Android 모바일 애플리케이션 옵션을 사용하도록 설정
description: 이 문서에서는 Azure Active Directory B2C를 사용하여 Android 모바일 애플리케이션 옵션을 사용하도록 설정하는 여러 가지 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: c66d2faead9b14eb665622e1a710afd94b2ddd0f
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220692"
---
# <a name="configure-authentication-options-in-an-android-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 Android 앱에서 인증 옵션 구성 

이 문서에서는 Android 애플리케이션에 대한 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용하도록 설정하고 사용자 지정하고 개선할 수 있는 방법을 설명합니다. 

시작하기 전에 다음 문서를 숙지합니다. 
* [Azure AD B2C를 사용하여 샘플 Android 앱에서 인증 구성](configure-authentication-sample-android-app.md)
* [Azure AD B2C를 사용하여 사용자 자체 Android 앱에서 인증을 사용하도록 설정](enable-authentication-android-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

인증 URL에서 사용자 지정 도메인과 테넌트 ID를 사용하려면 [사용자 지정 도메인을 사용하는 것으로 설정](custom-domain.md)의 참고 자료를 따릅니다. MSAL(Microsoft 인증 라이브러리) 구성 개체를 찾고 사용자 지정 도메인 이름과 테넌트 ID로 ‘인증 기관’을 업데이트합니다.


#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

다음 Kotlin 코드는 변경 전의 MSAL 구성 개체를 보여 줍니다.

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```

다음 Kotlin 코드는 변경 후의 MSAL 구성 개체를 보여 줍니다.

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```


#### <a name="java"></a>[Java](#tab/java)

다음 Kotlin 코드는 변경 전의 MSAL 구성 개체를 보여 줍니다.

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```
다음 Kotlin 코드는 변경 후의 MSAL 구성 개체를 보여 줍니다.

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 사용자 지정 정책을 사용하는 경우 [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. MSAL 구성 개체를 찾고 로그인 힌트를 사용하여 `withLoginHint()` 메서드를 추가합니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withLoginHint("bob@contoso.com") 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withLoginHint("bob@contoso.com")  
    // More settings here
    .build();

b2cApp.acquireToken(parameters);

```

--- 

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. 해당 도메인 이름이 포함된 `domain_hint` 매개 변수를 목록에 추가합니다(예: `facebook.com`).
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `withAuthorizationQueryStringParameters` 메서드에 전달합니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("domain_hint", "facebook.com"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("domain_hint", "facebook.com"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [언어 사용자 지정을 구성합니다](language-customization.md).
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. 해당 언어 코드가 포함된 `ui_locales` 매개 변수를 목록에 추가합니다(예: `en-us`).
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `withAuthorizationQueryStringParameters` 메서드에 전달합니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("ui_locales", "en-us"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("ui_locales", "en-us"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 요소를 구성합니다.
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. `campaignId`와 같은 사용자 지정 쿼리 문자열 매개 변수를 추가합니다. 매개 변수 값을 설정합니다(예: `germany-promotion`).
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `withAuthorizationQueryStringParameters` 메서드에 전달합니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("campaignId", "germany-promotion"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("campaignId", "germany-promotion"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 사용자 지정 정책에서 [ID 토큰 힌트 기술 프로필](id-token-hint.md)을 정의합니다.
1. 코드에서 ID 토큰을 생성하거나 획득한 다음, 토큰을 변수로 설정합니다(예: `idToken`). 
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. ID 토큰을 저장하는 해당 변수를 사용하여 `id_token_hint` 매개 변수를 추가합니다.
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `withAuthorizationQueryStringParameters` 메서드에 전달합니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("id_token_hint", idToken))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("id_token_hint", idToken));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 



## <a name="next-steps"></a>다음 단계

- Android 구성에 대한 자세한 내용은 [Android용 MSAL 구성 옵션](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)을 참조하세요.
