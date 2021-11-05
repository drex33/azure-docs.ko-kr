---
title: Azure Active Directory 새로운 변경 참조
description: 애플리케이션에 영향을 줄 수 있는 Azure AD 프로토콜의 변경 내용을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 10/22/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 04dffd4dcebee3cee9023cf445fda6e3fd05b008
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050682"
---
# <a name="whats-new-for-authentication"></a>인증의 새로운 기능?

> 이 URL을 RSS 피드 판독기에 복사하고 붙여넣어 이 페이지의 최신 정보에 대한 알림을 받습니다.<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

인증 시스템에서는 보안 및 표준 준수를 개선하기 위해 지속적으로 변경하고 기능을 추가합니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 세부 정보를 제공합니다.

- 최신 기능
- 알려진 문제
- 프로토콜 변경 내용
- 사용되지 않는 기능

> [!TIP]
> 이 페이지는 정기적으로 업데이트되므로 자주 방문해 주세요. 별도로 명시되지 않는 한, 이 변경 내용은 새로 등록된 애플리케이션에만 적용됩니다.

## <a name="upcoming-changes"></a>예정된 변경

인식해야 하는 예정된 변경이 없습니다. 

## <a name="october-2021"></a>2021년 10월

### <a name="error-50105-has-been-fixed-to-not-return-interaction_required-during-interactive-authentication"></a>대화형 인증 중에 `interaction_required`를 반환하지 않는 오류 50105가 수정되었습니다.

**적용 날짜**: 2021년 10월

**영향을 받는 엔드포인트**: v2.0 및 v1.0

**영향을 받은 프로토콜**: [사용자 할당이 필요한](../manage-apps/what-is-access-management.md#requiring-user-assignment-for-an-app) 앱의 모든 사용자 흐름

**변경**

오류 50105(현재 할당)는 할당되지 않은 사용자가 관리자가 사용자 할당이 필요한 것으로 표시한 앱에 로그인을 시도할 때 발생합니다.  이것은 일반적인 액세스 제어 패턴이며 사용자는 종종 액세스 차단을 해제하기 위해 할당을 요청하는 관리자를 찾아야 합니다.  오류에는 `interaction_required` 오류 응답을 올바르게 처리하는 잘 코딩된 애플리케이션에서 무한 루프를 일으키는 버그가 있었습니다. `interaction_required`는 앱에 대화형 인증을 수행하도록 지시하지만 그렇게 한 후에도 Azure AD는 여전히 `interaction_required` 오류 응답을 반환합니다.  

오류 시나리오가 업데이트되어 비대화형 인증(여기서 `prompt=none`은 UX를 숨기는 데 사용됨) 동안 `interaction_required` 오류 응답을 사용하여 대화형 인증을 수행하도록 앱에 지시합니다. 후속 대화형 인증에서 Azure AD는 이제 사용자를 유지하고 오류 메시지를 직접 표시하여 루프가 발생하지 않도록 합니다. 

참고로 Azure AD는 `AADSTS50105`에 대한 문자열 확인과 같은 개별 오류 코드를 감지하는 애플리케이션을 지원하지 않습니다. 대신 [Azure AD 지침](reference-aadsts-error-codes.md#handling-error-codes-in-your-application)은 표준을 따르고 `interaction_required` 및 `login_required`와 같은 [표준화된 인증 응답](https://openid.net/specs/openid-connect-core-1_0.html#AuthError)을 사용합니다. 이는 응답의 표준 `error` 필드에서 찾을 수 있습니다. 다른 필드는 문제 해결 중에 사람이 사용할 수 있는 필드입니다. 

오류 조회 서비스에서 50105 오류의 현재 텍스트와 자세한 내용을 검토할 수 있습니다. https://login.microsoftonline.com/error?code=50105 

### <a name="appid-uri-in-single-tenant-applications-will-require-use-of-default-scheme-or-verified-domains"></a>단일 테넌트 애플리케이션의 AppId URI는 기본 체계 또는 확인된 도메인을 사용해야 합니다.

**적용 날짜**: 2021년 10월

**영향을 받는 엔드포인트**: v2.0 및 v1.0

**영향을 받는 프로토콜**: 모든 흐름

**변경**

단일 테넌트 애플리케이션의 경우 AppId URI(identifierUris) 추가/업데이트 요청은 URI 값의 도메인이 고객 테넌트의 확인된 도메인 목록의 일부인지 또는 값이 Azure Active Directory에서 제공한 기본 체계(`api://{appId}`)를 사용하는지 확인합니다.
이렇게 하면 도메인이 확인된 도메인 목록에 없거나 값이 기본 체계를 사용하지 않는 경우 애플리케이션에서 AppId URI를 추가하지 못할 수 있습니다.
확인된 도메인에 대한 자세한 내용은 [사용자 지정 도메인 문서](../../active-directory/fundamentals/add-custom-domain.md)를 참조하세요.

변경 사항은 AppID URI에서 확인되지 않은 도메인을 사용하는 기존 애플리케이션에 영향을 미치지 않습니다. 새 애플리케이션만 유효성을 검사하거나 기존 애플리케이션이 식별자 URI를 업데이트하거나 identifierUri 컬렉션에 새 URI를 추가하는 경우 유효성을 검사합니다. 새 제한 사항은 2021년 10월 15일 이후에 앱의 identifierUris 컬렉션에 추가된 URI에만 적용됩니다. 2021년 10월 15일에 제한 사항이 적용되는 경우 애플리케이션의 identifierUris 컬렉션에 이미 있는 AppId URI는 해당 컬렉션에 새 URI를 추가하더라도 계속 작동합니다.

요청이 유효성 검사에 실패하면 만들기/업데이트에 대한 애플리케이션 API는 HostNameNotOnVerifiedDomain을 나타내는 `400 badrequest`를 클라이언트에 반환합니다.

[!INCLUDE [active-directory-identifierUri](../../../includes/active-directory-identifier-uri-patterns.md)]

## <a name="august-2021"></a>2021년 8월

### <a name="conditional-access-will-only-trigger-for-explicitly-requested-scopes"></a>조건부 액세스는 명시적으로 요청된 범위에 대해서만 트리거됩니다.

**적용 날짜**: 2021년 8월, 4월부터 점진적으로 출시됩니다. 

**영향을 받는 엔드포인트**: v2.0

**영향을 받는 프로토콜**: [동적 동의](v2-permissions-and-consent.md#requesting-individual-user-consent)를 사용하는 모든 흐름

현재 동적 동의를 사용하는 애플리케이션은 이름으로 `scope` 매개 변수에서 요청되지 않은 경우에도 동의한 모든 사용 권한을 부여합니다.  이를 통해 앱이 요청하는 경우에만(예: `user.read`) `files.read`에 동의하여 `files.read`사용 권한에 할당된 조건부 액세스를 강제로 전달할 수 있습니다. 

불필요한 조건부 액세스 프롬프트 수를 줄이기 위해 Azure AD는 명시적으로 요청된 범위만 조건부 액세스를 트리거하도록 요청되지 않은 범위가 애플리케이션에 제공되는 방식을 변경합니다. 이 변경으로 인해 Azure AD의 이전 동작(즉, 요청되지 않은 경우에도 모든 권한 제공)을 사용하는 앱이 중단될 수 있습니다. 요청하는 토큰에 권한이 누락되기 때문입니다.

이제 앱은 요청된 권한 및 조건부 액세스 프롬프트가 필요하지 않은 동의가 있는 권한이 혼합된 액세스 토큰을 받습니다.  액세스 토큰의 범위는 토큰 응답의 `scope` 매개 변수에 반영됩니다. 

이 동작에 대한 종속성이 관찰된 앱을 제외한 모든 앱에 대해 변경 내용이 적용됩니다.  개발자는 추가 조건부 액세스 프롬프트에 대한 종속성이 있을 수 있으므로 이러한 변경에서 제외되는 경우 지원을 받게 됩니다. 

**예제**

앱이 `user.read`, `files.readwrite` 및 `tasks.read`에 동의했습니다. `files.readwrite`에는 조건부 액세스 정책이 적용되며 다른 두 경우에는 적용되지 않습니다. 앱이 `scope=user.read`에 대한 토큰 요청을 하고 현재 로그인한 사용자가 조건부 액세스 정책을 통과하지 않은 경우 결과 토큰은 `user.read` 및 `tasks.read` 권한에 대한 것입니다. 앱에 동의가 있기 때문에 `tasks.read`가 포함되며 조건부 액세스 정책을 적용할 필요가 없습니다. 

그런 다음 앱이 `scope=files.readwrite`를 요청하면 테넌트에 필요한 조건부 액세스가 트리거되어 앱이 조건부 액세스 정책을 충족할 수 있는 대화형 인증 프롬프트를 표시하도록 합니다.  반환된 토큰에는 세 개의 범위가 모두 포함됩니다. 

앱이 세 가지 범위(예: `scope=tasks.read`) 중 하나에 대해 마지막 요청을 한 경우 Azure AD는 사용자가 `files.readwrite`에 필요한 조건부 액세스 정책을 이미 완료했음을 확인하고 세 가지 권한 모두에 대해 토큰을 다시 발급합니다. 


## <a name="june-2021"></a>2021년 6월

### <a name="the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt"></a>이제 디바이스 코드 흐름 UX에 앱 확인 프롬프트가 포함됩니다.

**적용 날짜**: 2021년 6월.

**영향을 받는 엔드포인트**: v2.0 및 v1.0

**영향을 받는 프로토콜**: [디바이스 코드 흐름](v2-oauth2-device-code.md)

보안을 개선하기 위해 디바이스 코드 흐름은 사용자가 예상하는 앱에 로그인하고 있는지 확인하는 프롬프트를 추가하도록 업데이트되었습니다. 이는 피싱 공격을 방지하기 위해 추가되었습니다.

표시되는 프롬프트는 다음과 같습니다.

:::image type="content" source="media/breaking-changes/device-code-flow-prompt.png" alt-text="'Azure CLI에 로그인하려고 하나요?'라는 새 프롬프트":::

## <a name="may-2020"></a>2020년 5월

### <a name="bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice"></a>버그 수정: Azure AD는 더 이상 상태 매개 변수를 두 번 인코딩하지 않습니다.

**적용 날짜**: 2021년 5월

**영향을 받는 엔드포인트**: v1.0 및 v2.0

**영향을 받는 프로토콜**: `/authorize` 엔드포인트를 방문하는 모든 흐름(암시적 흐름 및 인증 코드 흐름)

Azure AD 권한 부여 응답에서 버그가 발견되어 수정되었습니다. `/authorize`인증 기간 동안 요청의 `state` 매개 변수가 응답에 포함되어 앱 상태를 보존하고 CSRF 공격을 방지합니다. Azure AD가 `state` 매개 변수를 응답에 삽입하기 전에 URL을 잘못 인코딩하여 다시 한 번 인코딩했습니다.  이로 인해 애플리케이션이 Azure AD의 응답을 잘못 거부하게 됩니다.

Azure AD는 더 이상 이 매개 변수를 두 번 인코딩하지 않으므로 앱이 결과를 올바르게 구문 분석할 수 있습니다. 이 변경은 모든 애플리케이션에 적용됩니다. 

### <a name="azure-government-endpoints-are-changing"></a>Azure Government 엔드포인트 변경

**적용 날짜**: 5월 5일(2020년 6월 종료) 

**영향을 받는 엔드포인트**: 모두

**영향을 받는 프로토콜**: 모든 흐름

2018년 6월 1일부터 Azure Government의 공식 AAD(Azure Active Directory) 기관이 `https://login-us.microsoftonline.com` 에서 `https://login.microsoftonline.us`로 변경되었습니다. 이 변경 사항은 Azure Government AAD도 제공하는 Microsoft 365 GCC High 및 DoD에도 적용되었습니다. 미국 정부 테넌트 내에서 애플리케이션을 소유하는 경우 `.us` 엔드포인트에서 사용자를 로그인하도록 애플리케이션을 업데이트해야 합니다.  

5월 5일부터 Azure AD에는 엔드포인트 변경이 적용되어 정부 사용자가 퍼블릭 엔드포인트(`microsoftonline.com`)를 사용하여 미국 정부 테넌트에서 호스팅되는 앱에 로그인하지 못하도록 차단합니다.  영향을 받는 앱에는 `AADSTS900439` - `USGClientNotSupportedOnPublicEndpoint` 오류가 표시됩니다. 이 오류는 앱이 퍼블릭 클라우드 엔드포인트에서 미국 정부 사용자 로그인을 시도하고 있음을 나타냅니다. 앱이 퍼블릭 클라우드 테넌트에 있고 미국 정부 사용자를 지원하려는 경우 [앱을 업데이트하여 명시적으로 지원](./authentication-national-cloud.md)해야 합니다. 이를 위해서는 미국 정부 클라우드에서 새 앱 등록을 만들어야 할 수 있습니다. 

이 변경의 적용은 미국 정부 클라우드의 사용자가 애플리케이션에 로그인하는 빈도에 따른 점진적 롤아웃을 사용하여 수행됩니다. 미국 정부 사용자로 로그인하는 앱이 가장 먼저 적용되고 미국 정부 사용자가 자주 사용하는 앱은 마지막에 적용됩니다. 2020년 6월에 모든 앱에서 적용이 완료될 것으로 예상됩니다. 

자세한 내용은 [이 마이그레이션의 Azure Government 블로그 게시물](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)을 참조하세요. 

## <a name="march-2020"></a>2020년 3월

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>사용자 암호는 256자로 제한됩니다.

**적용 날짜**: 2020년 3월 13일

**영향을 받는 엔드포인트**: 모두

**영향을 받는 프로토콜**: 모든 사용자 흐름

Azure AD에 직접 로그인하는 암호(256자 초과)를 가진 사용자(ADFS 등의 페더레이션 IDP와 반대)는 2020년 3월 13일부터 로그인할 수 없으며 대신 암호를 다시 설정하라는 메시지가 표시됩니다.  관리자는 사용자 암호를 다시 설정하는 데 도움이 되도록 요청을 받을 수 있습니다.

로그인 로그의 오류는 AADSTS 50052: InvalidPasswordExceedsMaxLength가 됩니다.

메시지: `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

수정:

암호가 최대 허용 길이를 초과하기 때문에 사용자가 로그인할 수 없습니다. 암호를 다시 설정하려면 관리자에게 문의해야 합니다. SSPR이 테넌트에 사용하도록 설정된 경우 "암호 잊음" 링크에서 암호를 다시 설정할 수 있습니다.



## <a name="february-2020"></a>2020년 2월

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>로그인 엔드포인트의 모든 HTTP 리디렉션에 빈 조각이 추가됩니다.

**적용 날짜**: 2020년 2월 8일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: response_type=query를 사용하는 OAuth 및 OIDC흐름 - 경우에 따라 [인증 코드 흐름](v2-oauth2-auth-code-flow.md) 및 [암시적 흐름](v2-oauth2-implicit-grant-flow.md)이 포함됩니다.

HTTP 리디렉션을 통해 login.microsoftonline.com에서 애플리케이션으로 인증 응답을 보내면 서비스는 응답 URL에 빈 조각을 추가합니다.  이 작업을 수행하면 브라우저가 인증 요청에서 기존 조각을 모두 삭제하도록 하여 리디렉션 공격의 클래스를 방지합니다.  이 동작에 대한 종속성이 있는 앱이 없습니다.


## <a name="august-2019"></a>2019년 8월

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST 양식 의미 체계가 더 엄격하게 적용됩니다. 공백과 따옴표는 무시됩니다.

**적용 날짜**: 2019년 9월 2일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: POST가 사용되는 모든 곳([클라이언트 자격 증명](./v2-oauth2-client-creds-grant-flow.md), [인증 코드 상환](./v2-oauth2-auth-code-flow.md), [ROPC](./v2-oauth-ropc.md), [OBO](./v2-oauth2-on-behalf-of-flow.md) 및 [새로 고침 토큰 상환](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

9월 2일부터 POST 방법을 사용하는 인증 요청은 더 엄격한 HTTP 표준을 사용하여 검증됩니다.  특히 공백과 큰따옴표(")는 더 이상 요청 양식 값에서 제거되지 않습니다. 이번 변경으로 인해 기존 클라이언트가 중단되는 일은 없으며, Azure AD로 전송되는 요청을 매번 안정적으로 처리되도록 할 것입니다. Microsoft는 앞으로(위 참조) 중복 매개 변수를 추가로 거부하고 요청 내에서 BOM을 무시할 예정입니다.

예:

현재 `?e=    "f"&g=h`는 `?e=f&g=h`(즉, `e` == `f`)와 동일하게 구문 분석됩니다.  이 변경을 통해 이를 구문 분석하므로 `e` == `    "f"`는 유효한 인수가 될 가능성이 낮고 요청이 실패합니다.


## <a name="july-2019"></a>2019년 7월

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>단일 테넌트 애플리케이션에 대한 앱 전용 토큰은 클라이언트 앱이 리소스 테넌트에 있는 경우에만 발급됩니다.

**적용 날짜**: 2019년 7월 26일

**영향을 받는 엔드포인트**: [v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) 및 [v2.0](./v2-oauth2-client-creds-grant-flow.md) 모두

**영향을 받는 프로토콜**: [클라이언트 자격 증명(앱 전용 토큰)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

클라이언트 자격 증명 부여를 통해 앱 전용 토큰이 발급되는 방식을 변경하는 보안 변경 사항이 7월 26일에 적용되었습니다. 이전에는 애플리케이션이 테넌트의 존재 여부 또는 해당 애플리케이션에 대해 동의한 역할에 관계없이 다른 앱을 호출하기 위한 토큰을 받을 수 있었습니다.  이 동작은 리소스(웹 API라고도 함)가 단일 테넌트(기본값)로 설정되도록 업데이트되었으므로 클라이언트 애플리케이션이 리소스 테넌트 내에 있어야 합니다.  클라이언트와 API 간의 기존 동의는 여전히 필요하지 않으며 앱은 `roles` 클레임이 있고 API에 대한 예상 값을 포함하는지 확인하기 위해 계속해서 자체 권한 부여 검사를 수행해야 합니다.

이 시나리오에 대한 오류 메시지는 현재 다음과 같습니다.

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

이 문제를 해결하려면 관리자 동의 환경을 사용하여 테넌트에서 클라이언트 애플리케이션 서비스 주체를 만들거나 수동으로 만듭니다.  이 요구 사항은 테넌트가 테넌트 내에서 작동할 수 있는 애플리케이션 권한을 부여했는지 확인합니다.

#### <a name="example-request"></a>요청 예

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` 이 예에서 리소스 테넌트(권한)는 contoso.com이고 리소스 앱은 Contoso 테넌트에 대한 `gateway.contoso.com/api`라는 단일 테넌트 앱이며 클라이언트 앱은 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`입니다.  클라이언트 앱이 Contoso.com 내에 서비스 주체를 포함하는 경우 이 요청을 계속할 수 있습니다.  그러나 포함하지 않는 경우에는 위의 오류가 발생하여 요청이 실패합니다.

단, Contoso 게이트웨이 앱이 다중 테넌트 애플리케이션인 경우 Contoso.com 내에 서비스 주체가 있는 클라이언트 앱에 관계없이 요청이 계속됩니다.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>이제 리디렉션 URI는 쿼리 문자열 매개 변수를 포함할 수 있습니다.

**적용 날짜**: 2019년 7월 22일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 모든 흐름

[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)에 따라 이제 Azure AD 애플리케이션은 OAuth 2.0 요청에 대한 정적 쿼리 매개 변수(예: `https://contoso.com/oauth2?idp=microsoft`)를 사용하여 리디렉션(회신) URI를 등록 및 사용할 수 있습니다.  동적 리디렉션 URI는 보안 위험을 나타내므로 사용할 수 없으며 인증 요청에 대한 상태 정보를 유지하는 데 사용할 수 없습니다. 즉, `state` 매개 변수를 사용합니다.

정적 쿼리 매개 변수는 리디렉션 URI의 다른 부분과 마찬가지로 리디렉션 URI에 대한 일치 문자열을 따릅니다. URI가 디코딩된 redirect_uri와 일치하는 문자열이 등록되지 않은 경우에는 요청이 거부됩니다.  앱 등록에서 URI가 발견되면 정적 쿼리 매개 변수를 포함하여 전체 문자열이 사용자를 리디렉션하는 데 사용됩니다.

현재(2019년 7월 말) Azure Portal의 앱 등록 UX는 여전히 쿼리 매개 변수를 차단합니다.  그러나 애플리케이션 매니페스트를 수동으로 편집하여 쿼리 매개 변수를 추가하고 앱에서 이를 테스트할 수 있습니다.


## <a name="march-2019"></a>2019년 3월

### <a name="looping-clients-will-be-interrupted"></a>반복 클라이언트가 중단됩니다.

**적용 날짜**: 2019년 3월 25일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 모든 흐름

클라이언트 애플리케이션은 경우에 따라 잘못 작동하여 짧은 시간 동안 수백 개의 동일한 로그인 요청을 발행할 수 있습니다.  이러한 요청은 성공할 수도 있고 성공하지 못할 수도 있지만 모두 사용자 환경의 성능을 저하시키고 IDP의 워크로드를 늘리므로 사용자의 대기 시간이 증가하고 IDP의 가용성이 떨어집니다.  이러한 애플리케이션은 정상적인 사용 범위를 벗어나 작동되며, 올바르게 동작하도록 업데이트되어야 합니다.

중복 요청을 여러 번 발행하는 클라이언트에는 `invalid_grant` 오류: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`가 전송됩니다.

대부분의 클라이언트는 이 오류를 방지하기 위해 동작을 변경할 필요가 없습니다.  잘못 구성된 클라이언트(토큰 캐싱이 없거나 이미 프롬프트 루프가 있는 클라이언트)만 이 오류의 영향을 받습니다.  클라이언트는 다음 요소에 대해 로컬(쿠키를 통해) 인스턴스별로 추적됩니다.

* 사용자 힌트(있는 경우)

* 요청되는 범위 또는 리소스

* 클라이언트 ID

* 리디렉션 URI

* 응답 유형 및 모드

짧은 시간(5분)에 여러 요청(15개 이상)을 하는 앱에는 반복되고 있음을 설명하는 `invalid_grant` 오류가 수신됩니다.  요청되는 토큰의 수명은 충분히 길기 때문에(최소 10분, 기본적으로 60분) 이 기간 동안 반복되는 요청은 필요하지 않습니다.

모든 앱은 토큰을 자동으로 요청하는 대신 대화형 프롬프트를 표시하여 `invalid_grant`를 처리해야 합니다.  클라이언트는 이 오류를 방지하기 위해 수신하는 토큰을 올바르게 캐싱하는지 확인해야 합니다.


## <a name="october-2018"></a>2018년 10월

### <a name="authorization-codes-can-no-longer-be-reused"></a>권한 부여 코드는 더 이상 재사용할 수 없습니다.

**개시 날짜**: 2018년 11월 15일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: [코드 흐름](v2-oauth2-auth-code-flow.md)

2018년 11월 15일부터 Azure AD는 앱에서 이전에 사용된 인증 코드를 더 이상 수락하지 않습니다. 이 보안 변경은 Azure AD를 OAuth 사양에 맞추는 데 도움이 되며 v1 및 v2 엔드포인트 모두에 적용됩니다.

앱에서 여러 리소스에 대한 토큰을 얻기 위해 인증 코드를 재사용하는 경우에는 코드를 사용하여 새로 고침 토큰을 얻은 다음, 이 새로 고침 토큰을 사용하여 다른 리소스에 대한 추가 토큰을 얻는 것이 좋습니다. 인증 코드는 한 번만 사용할 수 있지만 새로 고침 토큰은 여러 리소스에서 여러 번 사용할 수 있습니다. OAuth 코드 흐름 중에 인증 코드를 다시 사용하려고 하는 새 앱에서는 invalid_grant 오류가 발생합니다.

새로 고침 토큰에 대한 자세한 내용은 [액세스 토큰 새로 고침](v2-oauth2-auth-code-flow.md#refresh-the-access-token)을 참조하세요.  ADAL 또는 MSAL을 사용하는 경우 이 작업은 라이브러리를 통해 처리됩니다. 'AcquireTokenByAuthorizationCodeAsync'의 두 번째 인스턴스를 'AcquireTokenSilentAsync'로 바꿉니다.

## <a name="may-2018"></a>2018년 5월

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 토큰은 OBO 흐름에 사용할 수 없습니다.

**날짜**: 2018년 5월 1일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 암시적 흐름 및 [On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md)

2018년 5월 1일 이 OBO 흐름에서 새 애플리케이션의 어설션으로 id_token을 사용할 수 없습니다. API를 보호하려면 클라이언트와 동일한 애플리케이션의 중간 계층 사이에서도 액세스 토큰을 대신 사용해야 합니다. 2018년 5월 1일 전에 등록된 앱은 계속 작동하고 id_token을 액세스 토큰으로 교환할 수 있지만 이 패턴은 모범 사례로 간주되지 않습니다.

이 변경 문제를 해결하려면 다음을 수행할 수 있습니다.

1. 하나 이상의 범위가 있는 애플리케이션의 웹 API를 만듭니다. 이 명시적 진입점으로 인해 더 세분화된 제어 및 보안이 가능합니다.
1. 앱 매니페스트, [Azure Portal](https://portal.azure.com) 또는 [앱 등록 포털](https://apps.dev.microsoft.com)에서 앱이 암시적 흐름을 통해 액세스 토큰을 발급할 수 있는지 확인합니다. 이 작업은 `oauth2AllowImplicitFlow` 키를 통해 제어됩니다.
1. 클라이언트 애플리케이션에서 `response_type=id_token`을 통해 id_token을 요청할 때 위에서 만든 웹 API의 액세스 토큰(`response_type=token`)도 요청합니다. 따라서 v2.0 엔드포인트를 사용하는 경우 `scope` 매개 변수는 `api://GUID/SCOPE`와 비슷하게 표시되어야 합니다. V1.0 엔드포인트에서 `resource` 매개 변수는 Web API의 앱 URI여야 합니다.
1. Id_token 대신 중간 계층으로 이 액세스 토큰을 전달합니다.
