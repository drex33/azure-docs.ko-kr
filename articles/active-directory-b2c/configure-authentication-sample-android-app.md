---
title: Azure Active Directory B2C를 사용하여 샘플 Android 애플리케이션에서 인증 구성
description: Azure Active Directory B2C를 사용하여 Android 애플리케이션에 사용자를 로그인하고 가입할 수 있습니다.
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
ms.openlocfilehash: b77ef28c5161c92bf8eb6c22cf62d5af41698441
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2021
ms.locfileid: "122566196"
---
# <a name="configure-authentication-in-a-sample-android-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 샘플 Android 애플리케이션에서 인증 구성

이 문서에서는 샘플 Android 애플리케이션(Kotlin 및 Java)을 사용하여 모바일 웹에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 설명합니다.

## <a name="overview"></a>개요

OIDC(OpenID Connect)는 애플리케이션에 사용자가 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0 기반의 인증 프로토콜입니다. 이 모바일 앱 샘플은 OpenId Connect 인증 코드 PKCE 흐름에서 [MSAL](../active-directory/develop/msal-overview.md) 라이브러리를 사용합니다. MSAL 라이브러리는 모바일 앱에 인증 및 권한 부여 지원을 간편하게 추가할 수 있도록 하는 Microsoft에서 제공하는 라이브러리입니다. 

로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자가 앱을 열고 **로그인** 을 선택합니다.
1. 앱에 모바일 디바이스의 시스템 브라우저가 열리고 Azure AD B2C에 대한 인증 요청을 시작합니다.
1. 사용자가 [가입하거나 로그인하거나](add-sign-up-and-sign-in-policy.md), [암호를 다시 설정하거나](add-password-reset-policy.md), [소셜 계정](add-identity-provider.md)으로 로그인합니다.
1. 로그인에 성공하면 Azure AD B2C는 앱에 인증 코드를 반환합니다.
1. 앱은 다음 작업을 수행합니다.
    1. 인증 코드를 ID 토큰, 액세스 토큰 및 새로 고침 토큰으로 교환합니다.
    1. ID 토큰 클레임을 읽습니다.
    1. 나중에 사용할 수 있도록 토큰을 메모리 내 캐시에 저장합니다.

### <a name="app-registration-overview"></a>앱 등록 개요

앱이 Azure AD B2C에 로그인하고 웹 API를 호출할 수 있도록 하려면 Azure AD B2C 디렉터리에 두 개의 애플리케이션을 등록합니다.  

- **모바일 애플리케이션** 등록을 사용하면 앱이 Azure AD B2C로 로그인할 수 있습니다. 앱을 등록하는 동안 *리디렉션 URI* 를 지정합니다. 리디렉션 URI는 사용자가 Azure AD B2C로 인증을 완료한 후 Azure AD B2C에 의해 리디렉션되는 엔드포인트입니다. 앱 등록 프로세스는 모바일 앱을 고유하게 식별하는 *애플리케이션 ID*(*클라이언트 ID* 라고도 함)를 생성합니다. 예를 들어 **앱 ID: 1** 을 생성합니다.

- **웹 API** 등록을 사용하면 앱에서 보호된 웹 API를 호출할 수 있습니다. 등록은 웹 API 권한(범위)을 노출합니다. 앱 등록 프로세스는 웹 API를 고유하게 식별하는 *애플리케이션 ID* 를 생성합니다.  예를 들어 **앱 ID: 2** 을 생성합니다. 웹 API 범위(앱 ID: 2)에 모바일 앱(앱 ID: 1) 권한을 부여합니다. 


다음 다이어그램에서는 앱 등록 및 애플리케이션 아키텍처를 설명합니다.

![웹 API 호출 등록 및 토큰이 있는 모바일 앱](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>웹 API 호출

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>로그아웃

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>사전 요구 사항

다음 중 하나를 실행하는 컴퓨터: 


- [JDK(Java 개발 키트)](https://openjdk.java.net/) 8 이상
- [Apache Maven](https://maven.apache.org/)
- [Android API 수준 16](https://developer.android.com/studio/releases/platforms) 이상
- [Android studio](https://developer.android.com/studio) 또는 다른 코드 편집기


## <a name="step-1-configure-your-user-flow"></a>1단계: 사용자 흐름 구성

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>2단계: 모바일 애플리케이션 등록

이 단계에서는 모바일 앱 및 웹 API 애플리케이션 등록을 만들고 웹 API의 범위를 지정합니다.

### <a name="21-register-the-web-api-app"></a>2.1 웹 API 앱 등록

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 웹 API 앱 범위 구성

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 모바일 앱 등록

다음 단계에 따라 모바일 앱 등록을 만듭니다.

1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *android-app1* 과 같이 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 
1. **리디렉션 URI** 에서 **공용 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택한 후 URL 텍스트 상자에 다음 URI 중 하나를 입력합니다.
    - Kotlin 샘플의 경우: `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Java 샘플의 경우: `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. **등록** 을 선택합니다.
1. 앱 등록이 완료되면 **개요** 를 선택합니다.
1. 모바일 애플리케이션을 구성하는 이후 단계에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.

    ![모바일 애플리케이션 ID 가져오기](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 웹 API에 대한 모바일 앱 권한 부여

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>3단계: Android 모바일 앱 샘플 가져오기

[Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip) 또는 [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip) 샘플 중 하나를 다운로드합니다. 샘플 ZIP 파일을 작업 폴더에 추출합니다.

또는 GitHub에서 샘플 Android 모바일 애플리케이션을 복제합니다. 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```bash
git clone https://github.com/Azure-Samples/ms-identity-android-kotlin
```

#### <a name="java"></a>[Java](#tab/java)

```bash
git clone https://github.com/Azure-Samples/ms-identity-android-java
```

--- 


## <a name="step-4-configure-the-sample-web-api"></a>4단계: 샘플 웹 API 구성

이 샘플은 모바일 앱이 웹 API에 사용할 수 있는 관련 범위를 사용하여 액세스 토큰을 획득합니다. 코드에서 웹 API를 호출하려면 다음 단계를 수행합니다.

1. 기존 웹 API를 사용하거나 새로 만듭니다. 자세한 내용은 [Azure AD B2C를 사용하여 사용자 고유의 웹 API에서 인증 사용](enable-authentication-web-api.md)을 참조하세요.
1. 샘플 코드를 변경하여 [웹 API를 호출](enable-authentication-android-app.md#call-a-web-api)합니다.

## <a name="step-5-configure-the-sample-mobile-app"></a>5단계: 샘플 모바일 앱 구성

Android Studio 또는 다른 코드 편집기를 통해 샘플 프로젝트를 엽니다.  그런 다음, `/app/src/main/res/raw/auth_config_b2c.json` 파일을 엽니다. 

*auth_config_b2c.json* 구성 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 모바일 앱은 이 정보를 사용하여 Azure AD B2C와 트러스트 관계를 설정하고, 사용자를 로그인 및 로그아웃시키고, 토큰을 획득하고, 유효성을 검사합니다. 

앱 설정의 다음 속성을 업데이트합니다.

|키  |값  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | [2.3단계](#23-register-the-mobile-app)의 모바일 애플리케이션 ID입니다. | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | [2.3단계](#23-register-the-mobile-app)의 모바일 애플리케이션 리디렉션 URI입니다. | 
| [인증 기관](../active-directory/develop/msal-client-application-configuration.md#authority)| 인증 기관은 MSAL이 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 다음 형식을 사용합니다. `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` `<your-tenant-name>`을 Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)으로 바꿉니다. 그런 다음, `<your-sign-in-sign-up-policy>`를 사용자 흐름 또는 [1단계](#step-1-configure-your-user-flow)에서 생성한 사용자 지정 정책으로 바꿉니다. | 


`B2CConfiguration` 클래스를 열고 다음 클래스 멤버를 업데이트합니다.

|키  |값  |
|---------|---------|
| 정책| [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 흐름 또는 사용자 지정 정책 목록입니다.|
| azureAdB2CHostName| Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다. 예들 들어 `https://contoso.b2clogin.com`입니다.|
| tenantName| Azure AD B2C 테넌트 전체 [테넌트 이름](tenant-management.md#get-your-tenant-name)입니다. 예들 들어 `contoso.onmicrosoft.com`입니다.|
| 범위| [2.4단계](#24-grant-the-mobile-app-permissions-for-the-web-api)에서 만든 웹 API 범위입니다.|


## <a name="step-6-run-and-test-the-mobile-app"></a>6단계: 모바일 앱 실행 및 테스트

1. 프로젝트를 빌드하고 실행합니다.
1. 햄버거 아이콘을 선택합니다.
    
    ![햄버거 아이콘을 선택하는 방법을 보여주는 스크린샷](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. **B2C 모드** 를 선택합니다.

    ![B2C 모드를 선택하는 방법을 보여주는 스크린샷](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. **사용자 흐름 실행** 을 선택합니다.

    ![로그인 흐름을 시작하는 방법을 보여주는 스크린샷](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Azure AD B2C 로컬 또는 소셜 계정으로 가입하거나 로그인합니다.

1. 성공적으로 인증되면 탐색 모음에 표시 이름이 표시됩니다.

    ![Azure AD B2C 액세스 토큰 및 사용자 ID가 표시됩니다.](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>다음 단계

* [고유 Android 애플리케이션에서 인증을 사용하도록 설정](enable-authentication-android-app.md)하는 방법 알아보기
* [Android 애플리케이션에서 인증 옵션 구성](enable-authentication-android-app-options.md)
* [고유 웹 API에서 인증을 사용하도록 설정](enable-authentication-web-api.md)
