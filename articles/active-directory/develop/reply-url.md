---
title: 리디렉션 URI(회신 URL) 제한 사항 | Azure AD
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼에서 적용하는 리디렉션 URI(회신 URL) 형식의 제한 사항에 대한 설명입니다.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/23/2021
ms.topic: conceptual
ms.subservice: develop
ms.custom: contperf-fy21q4-portal, aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: b9484973e724246db76ccc927437fccf2c4c7be1
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966467"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>리디렉션 URI(회신 URL) 제한 사항

리디렉션 URI 또는 회신 URL은 앱이 성공적으로 인증되고 인증 코드 또는 액세스 토큰이 부여된 후 권한 부여 서버가 사용자를 보내는 위치입니다. 권한 부여 서버는 코드 또는 토큰을 리디렉션 URI로 보내므로 앱 등록 프로세스의 일부로 올바른 위치를 등록하는 것이 중요합니다.

 리디렉션 URI에는 다음 제한 사항이 적용됩니다.

* 리디렉션 URI는 스키마 `https`로 시작해야 합니다. [Localhost 리디렉션 URI에 대한 몇 가지 예외](#localhost-exceptions)가 있습니다.

* 리디렉션 URI는 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 애플리케이션의 경로 `.../abc/response-oidc`의 일부로 포함하는 경우 리디렉션 URI에 `.../ABC/response-oidc`를 지정하지 마세요. 웹 브라우저는 경로를 대/소문자 구분으로 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.

* 패스 세그먼트가 없는 리디렉션 URI는 응답의 URI에 후행 슬래시를 추가합니다. 예를 들어 https://contoso.com 및 http://localhost:7071 과 같은 URI는 각각 https://contoso.com/ 및 http://localhost:7071/ 로 반환됩니다. 이는 응답 모드가 쿼리 또는 조각일 때만 적용됩니다.

* 패스 세그먼트를 포함하는 리디렉션 URI는 후행 슬래시를 추가하지 않습니다. (예: https://contoso.com/abc , https://contoso.com/abc/response-oidc 는 응답에 그대로 사용됨)

## <a name="maximum-number-of-redirect-uris"></a>최대 리디렉션 URI 수

다음 표에서는 Microsoft ID 플랫폼에서 앱 등록에 추가할 수 있는 리디렉션 URI의 최대 개수를 보여 줍니다.

| 로그인한 계정 | 최대 리디렉션 URI 수 | Description |
|--------------------------|---------------------------------|-------------|
| 모든 조직의 Azure AD(Azure Active Directory) 테넌트에 있는 Microsoft 회사 또는 학교 계정 | 256 | 애플리케이션 매니페스트의 `signInAudience` 필드가 *AzureADMyOrg* 또는 *AzureADMultipleOrgs* 로 설정됨 |
| 개인 Microsoft 계정 및 회사/학교 계정 | 100 | 애플리케이션 매니페스트의 `signInAudience` 필드가 *AzureADandPersonalMicrosoftAccount* 로 설정됨 |

## <a name="maximum-uri-length"></a>최대 URI 길이

앱 등록에 추가하는 각 리디렉션 URI에 대해 최대 256문자를 사용할 수 있습니다.

## <a name="supported-schemes"></a>지원되는 스키마

현재 Azure AD(Azure Active Directory) 애플리케이션 모델은 모든 조직의 Azure AD 테넌트에서 회사 또는 학교 계정에 로그인하는 앱에 HTTP 및 HTTPS 스키마를 모두 지원합니다. 이러한 계정 유형은 애플리케이션 매니페스트의 `signInAudience` 필드에서 `AzureADMyOrg` 및 `AzureADMultipleOrgs` 값으로 지정됩니다. 개인 Microsoft 계정(MSA) *및* 학교 계정(`signInAudience`이 `AzureADandPersonalMicrosoftAccount`로 설정됨)에 로그인하는 앱의 경우 HTTPS 스키마만 허용됩니다.

HTTP 스키마를 사용하는 리디렉션 URI를 회사 또는 학교 계정에 로그인하는 앱 등록에 추가하려면 Azure Portal에서 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908)의 애플리케이션 매니페스트 편집기를 사용합니다. 그러나 매니페스트 편집기를 사용하여 HTTP 기반 리디렉션 URI를 설정할 수 있지만, 리디렉션 URI에는 HTTPS 체계를 사용하는 것이 *좋습니다*.

## <a name="localhost-exceptions"></a>Localhost 예외

[RFC 8252 섹션 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) 및 [7.3](https://tools.ietf.org/html/rfc8252#section-7.3)에 대하여, "루프백" 또는 "localhost" 리디렉션 URI는 다음과 같은 두 가지 특별 고려 사항과 함께 제공됩니다.

1. `http` 리디렉션이 디바이스를 벗어날 수 없기 때문에 URI 스키마가 허용됩니다. 따라서 다음 두 URI를 모두 사용할 수 있습니다.
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. 종종 네이티브 애플리케이션에서 요구하는 임시 포트 범위 때문에 포트 구성 요소(예: `:5001` 또는 `:443`)는 리디렉션 URI 일치를 위해 무시됩니다. 따라서 이러한 모든 URI는 동일하게 간주됩니다.
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

개발 관점에서 이는 다음과 같은 몇 가지를 의미합니다.

* 포트가 서로 다른 경우에는 여러 리디렉션 URI를 등록해서는 안 됩니다. 로그인 서버는 항목을 임의로 선택하고 해당 리디렉션 URI와 연결된 동작을 사용합니다(예: `web`-, `native`- 또는 `spa`-형식 리디렉션 중 하나).

    이는 인증 코드 부여 및 암시적 흐름과 같이 동일한 애플리케이션 등록에서 다른 인증 흐름을 사용하려는 경우에 특히 중요합니다. 올바른 응답 동작을 각 리디렉션 URI와 연결하려면 로그인 서버에서 리디렉션 URI를 구분할 수 있어야 하며, 포트가 서로 다른 경우에는 이 작업을 수행할 수 없습니다.
* Localhost에 여러 리디렉션 URI를 등록하여 개발 중에 다른 흐름을 테스트하려면 URI의 *경로* 구성 요소를 사용하여 이를 구분합니다. 예를 들어 `http://localhost/MyWebApp`는 `http://localhost/MyNativeApp`과 같지 않습니다.
* IPv6 루프백 주소(`[::1]`)는 현재 지원되지 않습니다.

#### <a name="prefer-127001-over-localhost"></a>Localhost를 통한 127.0.0.1 선호

잘못 구성된 방화벽 또는 이름이 바뀐 네트워크 인터페이스에 의해 앱이 중단되지 않도록 하려면, `localhost` 대신 리디렉션 URI에서 IP 리터럴 루프백 주소 `127.0.0.1`을 사용합니다. 예들 들어 `https://127.0.0.1`입니다.

그러나 Azure Portal에서 **리디렉션 URI** 텍스트 상자를 사용하여 `http` 스키마를 사용하는 루프백 기반 리디렉션 URI를 추가할 수는 없습니다.

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="허용되지 않는 http 기반 루프백 리디렉션 URI를 표시하는 Azure Portal의 오류 대화 상자":::

`127.0.0.1` 루프백 주소를 사용하여 `http` 스키마를 사용하는 리디렉션 URI를 추가하려면 현재 애플리케이션 매니페스트에서 [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) 특성을 수정해야 합니다.

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>리디렉션 URI의 와일드카드에 대한 제한 사항

`https://*.contoso.com`과 같은 와일드카드 URI는 편리하게 보일 수 있지만, 보안의 영향을 받을 수 있으므로 피해야 합니다. OAuth 2.0 사양([RFC 6749의섹션 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2))에 따라 리디렉션 엔드포인트 URI는 절대 URI여야 합니다.

와일드카드 URI는 현재 개인 Microsoft 계정과 회사 또는 학교 계정에 로그인하도록 구성된 앱 등록에서 지원되지 않습니다. 그러나 조직의 Azure AD 테넌트에서 회사 또는 학교 계정에만 로그인하도록 구성된 앱에는 와일드카드 URI를 사용할 수 있습니다.

와일드카드를 사용하는 리디렉션 URI를 회사 또는 학교 계정에 로그인하는 앱 등록에 추가하려면 Azure Portal에서 **앱 등록** 의 애플리케이션 매니페스트 편집기를 사용합니다. 매니페스트 편집기를 사용하여 와일드카드로 리디렉션 URI를 설정할 수 있지만, RFC 6749의 3.1.2 섹션을 *준수하고* 절대 URI만 사용하는 것이 좋습니다.

시나리오에 허용되는 최대 제한보다 많은 리디렉션 URI가 필요한 경우 와일드카드 리디렉션 URI를 추가하는 대신 다음, 상태 매개 변수 방법을 고려합니다.

#### <a name="use-a-state-parameter"></a>상태 매개 변수 사용

여러 하위 도메인을 사용하는 경우 그리고 인증이 성공하면 사용자를 인증이 시작된 동일한 페이지로 리디렉션해야 하는 시나리오에서는 상태 매개 변수를 사용하는 것이 도움이 될 수 있습니다.

이 방법의 경우 다음을 수행합니다.

1. 애플리케이션당 "공유" 리디렉션 URI를 만들어 권한 부여 엔드포인트에서 수신하는 보안 토큰을 처리합니다.
1. 애플리케이션은 관련 매개 변수에서 애플리케이션 특정 매개 변수(예: 사용자가 시작한 하위 도메인 URL 또는 브랜딩 정보와 같은 항목)를 보낼 수 있습니다. 상태 매개 변수를 사용하는 경우 [RFC 6749의 섹션 10.12](https://tools.ietf.org/html/rfc6749#section-10.12)에 지정된 대로 CSRF 보호를 제공합니다.
1. 애플리케이션 관련 매개 변수에는 애플리케이션이 사용자에 대한 올바른 환경을 렌더링하는 데 필요한, 즉 적절한 애플리케이션 상태를 생성하는 데 필요한 모든 정보가 포함됩니다. Azure AD 권한 부여 엔드포인트는 상태 매개 변수에서 HTML을 제거하므로 이 매개 변수에 HTML 콘텐츠를 전달하지 않아야 합니다.
1. Azure AD가 "공유" 리디렉션 URI에 대한 응답을 보내면 이 URI가 상태 매개 변수를 다시 애플리케이션으로 보냅니다.
1. 그런 다음 애플리케이션은 상태 매개 변수의 값을 사용하여 사용자를 추가로 보낼 URL을 결정할 수 있습니다. CSRF 보호의 유효성을 검사해야 합니다.

> [!WARNING]
> 이 접근 방식에서는 손상된 클라이언트가 상태 매개 변수에 전송된 추가 매개 변수를 수정하여 사용자를 다른 URL로 리디렉션할 수 있습니다. 이것이 RFC 6819에 설명된 [개방형 리디렉터 위협](https://tools.ietf.org/html/rfc6819#section-4.2.4)입니다. 따라서 클라이언트는 상태를 암호화하거나 토큰에 대해 리디렉션 URI의 도메인 이름을 확인하는 등의 다른 방법으로 상태를 확인하여 이러한 매개 변수를 보호해야 합니다.

## <a name="next-steps"></a>다음 단계

앱 등록 [애플리케이션 매니페스트](reference-app-manifest.md)에 대해 알아봅니다.
