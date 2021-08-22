---
title: Azure Active Directory 조건부 액세스에 대한 개발자 지침
titleSuffix: Microsoft identity platform
description: Azure AD 조건부 액세스 및 Microsoft ID 플랫폼을 위한 개발자 지침 및 시나리오입니다.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: 0492055262ccd627d2e3400f78e5c26db0b2acb2
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730049"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스에 대한 개발자 지침

Azure AD(Azure Active Directory)의 조건부 액세스 기능은 앱의 보안을 유지하고 서비스를 보호하는 데 사용할 수 있는 여러 가지 방법 중 하나를 제공합니다. 조건부 액세스를 통해 개발자 및 기업 고객은 다음과 같은 다양한 방법으로 서비스를 보호할 수 있습니다.

* [다단계 인증](../authentication/concept-mfa-howitworks.md)
* Intune 등록 디바이스만 특정 서비스에 액세스할 수 있도록 허용
* 사용자 위치 및 IP 범위 제한

조건부 액세스의 전체 기능에 대한 자세한 내용은 [조건부 액세스란?](../conditional-access/overview.md) 문서를 참조하세요.

이 문서에서는 Azure AD용 앱을 개발하는 개발자를 위해 조건부 액세스를 사용하는 방법을 보여 주고, 조건부 액세스 정책을 적용할 수 있는 제어 권한이 없는 리소스에 액세스하는 경우의 영향에 대해서도 알아봅니다. 또한 이 문서에서는 On-Behalf-Of 흐름, 웹앱, Microsoft Graph 액세스 및 API 호출에 수행되는 조건부 액세스의 영향도 알아봅니다.

[단일](quickstart-register-app.md) 및 [다중 테넌트](howto-convert-app-to-be-multi-tenant.md) 앱과 [일반 인증 패턴](./authentication-vs-authorization.md)에 대해 알고 있다고 가정합니다.

> [!NOTE]
> 이 기능을 사용하려면 Azure AD Premium P1 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [Free, Basic 및 Premium 버전의 일반적으로 사용할 수 있는 기능 비교](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.
> [Microsoft 365 Business 라이선스](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)가 있는 고객은 조건부 액세스 기능에도 액세스할 수 있습니다.

## <a name="how-does-conditional-access-impact-an-app"></a>조건부 액세스가 앱에 어떤 영향을 주나요?

### <a name="app-types-impacted"></a>영향을 받는 앱 형식

대부분의 경우, 조건부 액세스는 앱의 동작을 변경하지 않으며 개발자의 변경이 필요하지 않습니다. 앱에서 서비스의 토큰을 간접적으로 또는 자동으로 요청하는 특정한 경우에만 앱에서 조건부 액세스 챌린지를 처리하도록 코드를 변경해야 합니다.  이것은 대화형 로그인 요청을 수행하는 것처럼 간단할 수도 있습니다.

특히 다음 시나리오에는 조건부 액세스 챌린지를 처리하는 코드가 필요합니다.

* On-Behalf-Of 흐름을 수행하는 앱
* 여러 서비스/리소스에 액세스하는 앱
* MSAL.js를 사용하는 단일 페이지 앱
* 리소스를 호출하는 Web Apps

조건부 액세스 정책은 앱뿐만 아니라 사용자 앱이 액세스하는 웹 API에도 적용할 수 있습니다. 조건부 액세스 정책을 구성하는 방법을 자세히 알아보려면 [빠른 시작: Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](../authentication/tutorial-enable-azure-mfa.md)를 참조하세요.

시나리오에 따라 기업 고객은 언제든지 조건부 액세스 정책을 적용하고 제거할 수 있습니다. 새 정책이 적용된 경우 앱이 계속 작동할 수 있도록 챌린지 처리를 구현합니다. 다음 예에서는 챌린지 처리를 보여 줍니다.

### <a name="conditional-access-examples"></a>조건부 액세스 예제

일부 시나리오는 조건부 액세스를 처리하기 위해 코드 변경이 필요한 반면 다른 시나리오는 그대로 작동합니다. 다음에서 다단계 인증을 수행하기 위해 조건부 액세스를 사용하는 몇 가지 시나리오를 살펴보고 차이를 파악할 수 있습니다.

* 단일 테넌트 iOS 앱을 빌드하고 조건부 액세스 정책을 적용합니다. 사용자는 앱에 로그인하고 앱은 API에 대한 액세스를 요청하지 않습니다. 사용자가 로그인하면 정책이 자동으로 호출되고 사용자는 MFA(Multi-Factor Authentication)를 수행해야 합니다.
* 중간 계층 서비스를 사용하여 다운스트림 API에 액세스하는 원시 앱을 빌드하고 있습니다. 이 앱을 사용하는 회사의 기업 고객은 다운스트림 API에 정책을 적용합니다. 최종 사용자가 로그인하면 원시 앱에서 중간 계층에 대한 액세스를 요청하고 토큰을 보냅니다. 중간 계층은 On-Behalf-Of 흐름을 수행하여 다운스트림 API에 대한 액세스를 요청합니다. 이때 클레임 “챌린지”가 중간 계층에 표시됩니다. 중간 계층은 조건부 액세스 정책을 준수해야 하는 네이티브 앱으로 챌린지를 다시 보냅니다.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph는 조건부 액세스 환경에서 앱을 빌드할 때 특별히 고려해야 할 사항이 있습니다. 일반적으로 조건부 액세스의 메커니즘은 동일하게 작동하지만 사용자에게 표시되는 정책은 앱이 그래프에서 요청하는 기본 데이터를 기반으로 합니다.

특히, 모든 Microsoft Graph 범위는 개별적으로 정책을 적용할 수 있는 일부 데이터 세트를 나타냅니다. 조건부 액세스 정책에는 특정 데이터 세트가 할당되기 때문에 Azure AD는 그래프 자체가 아니라 그래프 뒤에 있는 데이터를 기반으로 조건부 액세스 정책을 적용합니다.

예를 들어 앱이 다음 Microsoft Graph 범위를 요청하는 경우

```
scopes="ChannelMessages.Read.All Mail.Read"
```

앱은 사용자가 Teams 및 Exchange에 설정된 모든 정책을 충족하도록 할 수 있습니다. 일부 범위는 액세스 권한을 부여하는 경우 여러 데이터 세트에 매핑될 수 있습니다.

### <a name="complying-with-a-conditional-access-policy"></a>조건부 액세스 정책 준수

여러 가지 서로 다른 앱 토폴로지의 경우 세션이 설정될 때 조건부 액세스 정책이 평가됩니다. 조건부 액세스 정책은 앱 및 서비스 단위로 작동하므로 호출되는 시점은 수행하려는 시나리오에 따라 크게 달라집니다.

앱이 조건부 액세스 정책으로 서비스에 대한 액세스를 시도하면 조건부 액세스 챌린지가 발생할 수 있습니다. 이 챌린지는 Azure AD의 응답에서 제공되는 `claims` 매개 변수에 인코딩됩니다. 이 챌린지 매개 변수의 예는 다음과 같습니다.

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

개발자는 이 챌린지를 가져와 Azure AD에 대한 새 요청에 추가할 수 있습니다. 이 상태를 전달하면 최종 사용자에게 조건부 액세스 정책을 준수하는 데 필요한 모든 작업을 수행하라는 메시지가 나타납니다. 다음 시나리오에서 구체적인 오류 정보 및 매개 변수를 추출하는 방법을 설명합니다.

## <a name="scenarios"></a>시나리오

### <a name="prerequisites"></a>사전 요구 사항

Azure AD 조건부 액세스는 [Azure AD Premium](../fundamentals/active-directory-whatis.md)에 포함된 기능입니다. [Microsoft 365 Business 라이선스](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)가 있는 고객은 조건부 액세스 기능에도 액세스할 수 있습니다.

### <a name="considerations-for-specific-scenarios"></a>특정 시나리오에 대한 고려 사항

다음 정보는 이러한 조건부 액세스 시나리오에만 적용됩니다.

* On-Behalf-Of 흐름을 수행하는 앱
* 여러 서비스/리소스에 액세스하는 앱
* MSAL.js를 사용하는 단일 페이지 앱

다음 섹션에서는 더 복잡한 일반적인 시나리오에 대해 설명합니다. 핵심 작동 원리는 조건부 액세스 정책이 적용된 서비스에 대해 토큰을 요청할 때 조건부 액세스 정책이 평가된다는 것입니다.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>시나리오: On-Behalf-Of 흐름을 수행하는 앱

이 시나리오에서는 원시 앱이 웹 서비스/API를 호출하는 경우를 연습합니다. 이 서비스에서는 "On-Behalf-Of" 흐름을 차례로 수행하여 다운스트림 서비스를 호출합니다. 이 경우, 다운스트림 서비스(Web API 2)에 조건부 액세스 정책을 적용했고 서버/디먼 앱보다는 네이티브 앱을 사용합니다.

![On-Behalf-Of 흐름을 수행하는 앱 다이어그램](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1에서 항상 다운스트림 API를 호출하는 것은 아니므로 Web API 1에 대한 초기 토큰 요청 시 최종 사용자에게 다단계 인증을 요구하지 않습니다. Web API 1이 Web API 2에 대해 사용자를 대신하여 토큰 요청을 시도하면 사용자가 다단계 인증으로 로그인하지 않았으므로 요청에 실패합니다.

Azure AD는 몇 가지 흥미로운 데이터가 포함된 HTTP 응답을 반환합니다.

> [!NOTE]
> 이 예에서는 다단계 인증 오류에 대한 설명이지만 조건부 액세스와 관련하여 광범위한 `interaction_required`가 포함될 수 있습니다.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Web API 1에서는 `error=interaction_required` 오류를 catch하고 `claims` 챌린지를 데스크톱 앱에 다시 보냅니다. 이 시점에 데스크톱 앱은 `acquireToken()`을 새로 호출하고 `claims` 챌린지를 추가 쿼리 문자열 매개 변수로 추가합니다. 이 새로운 요청은 사용자가 다단계 인증을 수행하도록 요구한 후 이 새로운 토큰을 Web API 1로 다시 보내고 On-Behalf-Of 흐름을 완료합니다.

이 시나리오를 사용해 보려면 [.NET 샘플 코드](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-)를 참조하세요. 여기서는 Web API 1에서 네이티브 앱으로 클레임 챌린지를 전달하는 방법과 클라이언트 앱 내부에 새 요청을 생성하는 방법을 보여 줍니다.

## <a name="scenario-app-accessing-multiple-services"></a>시나리오: 여러 서비스에 액세스하는 앱

이 시나리오에서는 웹앱이 조건부 액세스 정책이 할당된 두 서비스에 액세스하는 경우를 살펴봅니다. 사용자 앱 논리에 따라, 앱에서 두 웹 서비스에 액세스하지 않아도 되는 경로가 존재할 수 있습니다. 이 시나리오에서 토큰을 요청하는 순서는 최종 사용자 환경에서 중요한 역할을 합니다.

웹 서비스 A와 B가 있고 웹 서비스 B에 조건부 액세스 정책이 적용되었다고 가정해 보겠습니다. 초기 대화형 인증 요청에는 두 서비스에 대한 동의가 필요하지만 모든 경우에 조건부 액세스 정책이 요구되지는 않습니다. 앱에서 웹 서비스 B에 대한 토큰을 요청하는 경우 정책이 호출되고 다음과 같이 웹 서비스 A에 대한 후속 요청도 성공합니다.

![여러 서비스 흐름에 액세스하는 앱 다이어그램](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

또는 앱이 웹 서비스 A에 대한 토큰을 처음으로 요청하는 경우 최종 사용자는 조건부 액세스 정책을 호출하지 않습니다. 이렇게 하면 앱 개발자가 최종 사용자 환경을 제어하고 모든 경우에 조건부 액세스 정책이 강제로 호출되는 것을 막을 수 있습니다. 까다로운 경우는 앱이 웹 서비스 B에 대한 토큰을 나중에 요청하는 경우입니다. 이때 최종 사용자는 조건부 액세스 정책을 준수해야 합니다. 앱에서 `acquireToken`을 시도하면 다음 오류를 생성할 수 있습니다(다음 다이어그램에 설명됨).

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![새 토큰을 요청하는 여러 서비스에 액세스하는 앱](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

앱에서 MSAL 라이브러리를 사용하는 경우, 토큰을 획득하지 못하면 항상 대화형으로 다시 시도합니다. 이 대화형 요청이 발생하면 최종 사용자는 조건부 액세스를 준수할 수 있는 기회를 얻게 됩니다. 요청이 `AcquireTokenSilentAsync` 또는 `PromptBehavior.Never`인 경우를 제외하고 앱은 최종 사용자가 정책을 준수할 수 있는 기회를 제공하기 위해 대화형 ```AcquireToken``` 요청을 수행해야 합니다.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>시나리오: MSAL.js를 사용하는 SPA(단일 페이지 앱)

이 시나리오에서는 MSAL.js를 사용하여 조건부 액세스 보호 웹 API를 호출하는 SPA(단일 페이지 앱)가 있는 경우를 살펴봅니다. 간단한 아키텍처이지만 조건부 액세스를 기반으로 개발할 때 고려해야 하는 미묘한 차이가 있습니다.

MSAL.js에는 토큰을 가져오는 몇 가지 함수가 있습니다(예: `acquireTokenSilent()`, `acquireTokenPopup()` 및 `acquireTokenRedirect()`).

* `acquireTokenSilent()`를 사용하여 액세스 토큰을 자동으로 가져올 수 있습니다. 즉, 어떠한 상황에서도 UI가 표시되지 않습니다.
* `acquireTokenPopup()` 및 `acquireTokenRedirect()`는 리소스에 대한 토큰을 대화형으로 요청하는 데 사용되며 이는 항상 로그인 UI가 표시됨을 의미합니다.

앱에서 웹 API를 호출하는 데 액세스 토큰이 필요한 경우 `acquireTokenSilent()`을 시도합니다. 토큰이 만료되거나 조건부 액세스 정책을 준수해야 하는 경우 *acquireToken* 함수가 실패하며 앱은 `acquireTokenPopup()` 또는 `acquireTokenRedirect()`를 사용합니다.

![MSAL 흐름 다이어그램을 사용하는 단일 페이지 앱](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

이제 조건부 액세스 시나리오에 대한 예를 살펴보겠습니다. 최종 사용자는 방금 사이트에 연결되었고 세션이 없습니다. `loginPopup()` 호출을 수행하면 다단계 인증 없이 ID 토큰을 가져옵니다. 그러면 사용자는 단추를 눌러 앱이 Web API로부터 데이터를 요청하도록 해야 합니다. 앱은 `acquireTokenSilent()` 호출을 수행하려고 하지만 사용자가 아직 다단계 인증을 수행하지 않았고 조건부 액세스 정책을 준수해야 하므로 실패합니다.

Azure AD는 다음 HTTP 응답을 다시 보냅니다.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

앱은 `error=interaction_required`를 catch해야 합니다. 그러면 애플리케이션은 동일한 리소스에서 `acquireTokenPopup()` 또는 `acquireTokenRedirect()`를 사용할 수 있습니다. 사용자는 다단계 인증을 수행해야 합니다. 사용자가 다단계 인증을 완료하면 앱에는 요청된 리소스에 대한 새로운 액세스 토큰이 발급됩니다.

이 시나리오를 수행하려면 [On-Behalf-Of 흐름을 사용하여 Node.js 웹 API를 호출하는 JavaScript SPA](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/2-call-api-api-ca) 코드 샘플을 참조하세요. 이 샘플 코드에서는 이 시나리오를 설명하기 위해 JavaScript SPA와 함께 이전에 등록한 조건부 액세스 정책과 웹 API를 사용합니다. 클레임 챌린지를 올바르게 처리하고 웹 API에 사용할 수 있는 액세스 토큰을 가져오는 방법을 보여 줍니다.

## <a name="see-also"></a>참고 항목

* 기능에 대해 자세히 알아보려면 [Azure Active Directory의 조건부 액세스](../conditional-access/overview.md)를 참조하세요.
* 더 많은 Azure AD 샘플 코드를 보려면 [샘플](sample-v2-code.md)을 참조하세요.
* MSAL SDK에 대한 자세한 내용과 참조 문서에 액세스하는 방법은 [Microsoft 인증 라이브러리 개요](msal-overview.md)를 참조하세요.
* 다중 테넌트 시나리오에 대한 자세한 내용은 [다중 테넌트 패턴을 사용하여 사용자를 로그인하는 방법](howto-convert-app-to-be-multi-tenant.md)을 참조하세요.
* [조건부 액세스 및 IoT 앱에 대한 액세스 보안](/azure/architecture/example-scenario/iot-aad/iot-aad)에 대해 자세히 알아보세요.
