---
title: 클라이언트 애플리케이션 구성(MSAL) | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)을 사용하는 공용 클라이언트 및 기밀 클라이언트 애플리케이션에 대한 구성 옵션을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/20/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 590c070617e20a3e8efda38619393d701dcfbfc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036241"
---
# <a name="application-configuration-options"></a>애플리케이션 구성 옵션

토큰을 인증하고 획득하려면 코드에서 새 공용 또는 기밀 클라이언트 애플리케이션을 초기화합니다. MSAL(Microsoft 인증 라이브러리)에서 클라이언트 앱을 초기화하는 경우 몇 가지 구성 옵션을 설정할 수 있습니다. 이러한 옵션은 다음 두 그룹으로 분류됩니다.

- 다음과 같은 등록 옵션:
  - [인증 기관](#authority)(ID 공급자 [인스턴스](#cloud-instance), 앱의 로그인 [대상 그룹](#application-audience), 테넌트 ID로 구성)
  - [클라이언트 ID](#client-id)
  - [URI 리디렉션](#redirect-uri)
  - [클라이언트 암호](#client-secret)(기밀 클라이언트 애플리케이션의 경우)
- 로그 수준, 개인 데이터 제어, 라이브러리를 사용하는 구성 요소 이름 등의 [로깅 옵션](#logging)

## <a name="authority"></a>Authority

인증 기관은 MSAL이 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다.

일반적인 인증 기관은 다음과 같습니다.

| 공동 인증 기관 URL | 사용 시기 |
|--|--|
| `https://login.microsoftonline.com/<tenant>/` | 특정 조직의 사용자만 로그인합니다. URL의 `<tenant>`는 Azure AD(Azure Active Directory) 테넌트(GUID)의 테넌트 ID 또는 테넌트 도메인입니다. |
| `https://login.microsoftonline.com/common/` | 회사 및 학교 계정 또는 개인 Microsoft 계정을 보유한 사용자를 로그인합니다. |
| `https://login.microsoftonline.com/organizations/` | 회사 및 학교 계정을 보유한 사용자를 로그인합니다. |
| `https://login.microsoftonline.com/consumers/` | 개인 Microsoft 계정(MSA)만 보유한 사용자를 로그인합니다. |

코드에서 지정하는 인증 기관은 Azure Portal의 **앱 등록** 에서 앱에 대해 지정한 **지원되는 계정 유형** 과 일치해야 합니다.

인증 기관은 다음과 같을 수 있습니다.

- Azure AD 클라우드 인증 기관.
- Azure AD B2C 인증 기관. [B2C 세부 정보](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)를 참조하세요.
- AD FS(Active Directory Federation Services) 인증 기관. [AD FS 지원](https://aka.ms/msal-net-adfs-support)을 참조하세요.

Azure AD 클라우드 인증 기관에는 두 부분이 있습니다.

- ID 공급자 '인스턴스'
- 앱의 로그인 '대상 그룹'

인스턴스와 대상 그룹을 연결하여 기관 URL로 제공할 수 있습니다. 다음 다이어그램에서는 인증 기관 URL을 구성하는 방법을 보여 줍니다.

![인증 기관 URL을 구성하는 방법](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>클라우드 인스턴스

'인스턴스'는 앱이 Azure 퍼블릭 클라우드 또는 국가별 클라우드의 사용자를 로그인하는지 여부를 지정하는 데 사용됩니다. 코드에서 MSAL을 사용하여, 열거형을 사용하거나 URL을 [국가별 클라우드 인스턴스](authentication-national-cloud.md#azure-ad-authentication-endpoints)에 `Instance` 멤버(알고 있는 경우)로 전달하여 Azure 클라우드 인스턴스를 설정할 수 있습니다.

`Instance`와 `AzureCloudInstance`가 모두 지정된 경우 MSAL.NET에서 명시적 예외를 throw합니다.

인스턴스를 지정하지 않으면 앱은 Azure 퍼블릭 클라우드 인스턴스(URL `https://login.onmicrosoftonline.com`의 인스턴스)를 대상으로 지정합니다.

## <a name="application-audience"></a>애플리케이션 대상 그룹

로그인 대상 그룹은 앱의 비즈니스 요구 사항에 따라 달라집니다.

- LOB(기간 업무) 애플리케이션 개발자인 경우 조직에서만 사용되는 단일 테넌트 애플리케이션을 빌드할 것입니다. 이 경우 테넌트 ID(Azure AD 인스턴스의 ID) 또는 Azure AD 인스턴스와 연결된 도메인 이름을 기준으로 조직을 지정합니다.
- ISV인 경우에는 모든 조직 또는 일부 조직(다중 테넌트 앱)에서 회사 및 학교 계정을 보유한 사용자를 로그인해야 합니다. 그러나 개인 Microsoft 계정을 보유한 사용자도 로그인해야 할 수 있습니다.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>코드/구성에서 대상 그룹을 지정하는 방법

코드에서 MSAL을 사용하여 다음 값 중 하나를 사용하여 대상 그룹을 지정합니다.

- Azure AD 인증 기관 대상 그룹 열거형
- 다음 형식의 테넌트 ID
  - 단일 테넌트 애플리케이션의 경우, GUID(Azure AD 인스턴스의 ID)
  - Azure AD 인스턴스와 연결된 도메인 이름(단일 테넌트 애플리케이션에도 해당)
- Azure AD 인증 기관 대상 그룹 열거형 대신 테넌트 ID로서 다음 자리 표시자 중 하나
  - 다중 테넌트 애플리케이션의 경우 `organizations`
  - 개인 계정만 보유한 사용자를 로그인하려면 `consumers`
  - 회사 및 학교 계정 또는 개인 Microsoft 계정을 보유한 사용자를 로그인하려면 `common`

Azure AD 인증 기관 대상 그룹과 테넌트 ID를 모두 지정하면 MSAL에서 의미 있는 예외를 throw합니다.

대상 그룹을 지정하지 않으면 앱은 Azure AD 및 개인 Microsoft 계정을 대상으로 지정합니다. 즉, `common`이 지정된 것처럼 동작합니다.

### <a name="effective-audience"></a>유효한 대상 그룹

애플리케이션의 유효한 대상 그룹은 앱에서 설정한 대상 그룹과 앱 등록에 지정된 대상 그룹의 최소(교집합이 있는 경우)가 됩니다. 실제로 [앱 등록](https://aka.ms/appregistrations) 환경에서 앱 대상 그룹(지원되는 계정 유형)을 지정할 수 있습니다. 자세한 내용은 [빠른 시작: Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](quickstart-register-app.md)을 참조하세요.

현재, 앱에서 개인 Microsoft 계정만 보유한 사용자를 로그인하는 유일한 방법은 다음 설정을 모두 구성하는 것입니다.

- 앱 등록 대상 그룹을 `Work and school accounts and personal accounts`로 설정합니다.
- 코드/구성의 대상 그룹을 `AadAuthorityAudience.PersonalMicrosoftAccount`(또는 `TenantID` ="consumers")로 설정합니다.

## <a name="client-id"></a>클라이언트 ID

클라이언트 ID는 앱이 등록될 때 Azure AD에서 앱에 할당한 고유 애플리케이션(클라이언트) ID입니다.

## <a name="redirect-uri"></a>리디렉션 URI

리디렉션 URI는 ID 공급자가 보안 토큰을 다시 보낼 URI입니다.

### <a name="redirect-uri-for-public-client-apps"></a>공용 클라이언트 앱의 리디렉션 URI

MSAL을 사용하는 공용 클라이언트 앱 개발자인 경우:

- 데스크톱 또는 UWP 애플리케이션(MSAL.NET 4.1 이상)에서 `.WithDefaultRedirectUri()`를 사용해야 합니다. 이 메서드는 공용 클라이언트 애플리케이션의 리디렉션 URI 속성을 공용 클라이언트 응용 프로그램의 기본 권장 리디렉션 URI로 설정합니다.

  | 플랫폼 | 리디렉션 URI |
  |--|--|
  | 데스크톱 앱(.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` |
  | UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`의 값입니다. 이렇게 하면 등록해야 하는 WebAuthenticationBroker.GetCurrentApplicationCallbackUri()의 결과로 값을 설정하여 브라우저에서 SSO를 사용할 수 있습니다. |
  | .NET Core | `https://localhost`. 이렇게 하면 대화형 인증에 시스템 브라우저를 사용할 수 있습니다(.NET Core는 현재 포함된 웹 보기에 대한 UI를 포함하지 않기 때문). |

- Broker 리디렉션 URI를 지원하지 않는 Xamarin Android 및 iOS 애플리케이션을 빌드하는 경우에는 리디렉션 URI를 추가할 필요가 없습니다. Xamarin Android 및 iOS의 경우 자동으로 `msal{ClientId}://auth`로 설정됩니다.

- [앱 등록](https://aka.ms/appregistrations)에서 리디렉션 URI를 구성합니다.

   ![앱 등록의 리디렉션 URI](media/msal-client-application-configuration/redirect-uri.png)

`RedirectUri` 속성을 사용하여 리디렉션 URI를 재정의할 수 있습니다(예: broker를 사용하는 경우). 다음은 해당 시나리오를 위한 리디렉션 URI의 몇 가지 예입니다.

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

iOS에 대한 자세한 내용은 [Microsoft Authenticator를 사용하는 iOS 애플리케이션을 ADAL.NET에서 MSAL.NET로 마이그레이션](msal-net-migration-ios-broker.md) 및 [iOS에서 broker 활용](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)을 참조하세요.
Android에 대한 추가 정보는 [Android의 조정된 인증](msal-android-single-sign-on.md)을 참조하세요.

### <a name="redirect-uri-for-confidential-client-apps"></a>기밀 클라이언트 앱의 리디렉션 URI

웹앱의 경우 리디렉션 URI(또는 회신 URL)는 Azure AD가 애플리케이션에 토큰을 다시 보내는 데 사용하는 URI입니다. 이 URI는 기밀 앱이 이 중 하나인 경우 웹앱/웹 API의 URL일 수 있습니다. 리디렉션 URI는 앱 등록에서 등록해야 합니다. 이 등록은 처음에 로컬로 테스트한 앱을 배포할 때 특히 중요합니다. 그런 다음 애플리케이션 등록 포털에서 배포된 앱의 회신 URL을 추가해야 합니다.

디먼 앱의 경우 리디렉션 URI를 지정할 필요가 없습니다.

## <a name="client-secret"></a>클라이언트 암호

이 옵션은 기밀 클라이언트 앱의 클라이언트 암호를 지정합니다. 이 비밀(앱 암호)은 애플리케이션 등록 포털이 제공하거나 PowerShell AzureAD, PowerShell AzureRM 또는 Azure CLI를 사용하여 앱을 등록하는 동안 Azure AD에 제공됩니다.

## <a name="logging"></a>로깅
디버깅 및 인증 실패 문제 해결 시나리오를 지원하기 위해 Microsoft 인증 라이브러리는 기본 제공 로깅 지원을 제공합니다. 로깅은 다음 문서에서 설명합니다.

:::row:::
    :::column:::
        - [MSAL.NET의 로깅](msal-logging-dotnet.md)
        - [Android용 MSAL에서 로깅](msal-logging-android.md)
        - [MSAL.js의 로깅](msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [iOS/macOS용 MSAL에서 로깅](msal-logging-ios.md)
        - [Java용 MSAL에서 로깅](msal-logging-java.md)
        - [Python용 MSAL에서 로깅](msal-logging-python.md)
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>다음 단계

[MSAL.NET을 사용하여 클라이언트 애플리케이션을 인스턴스화](msal-net-initializing-client-applications.md)하고 [MSAL.js를 사용하여 클라이언트 애플리케이션을 인스턴스화하](msal-js-initializing-client-applications.md)는 방법을 알아봅니다.
