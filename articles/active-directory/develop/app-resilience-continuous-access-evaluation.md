---
title: 애플리케이션에서 지속적인 액세스 권한 평가를 사용하도록 설정된 API를 사용하는 방법 | Azure
titleSuffix: Microsoft identity platform
description: 지속적인 액세스 권한 평가 지원을 추가하여 앱 보안 및 복원력을 높이는 방법으로, 중요한 이벤트 및 정책 평가에 따라 철회할 수 있는 수명이 긴 액세스 토큰을 사용할 수 있습니다.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/09/2021
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 816975db5ee6fd613e077dd7d268825c6601b3fe
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444448"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>애플리케이션에서 지속적인 액세스 권한 평가를 사용하도록 설정된 API를 사용하는 방법

CAE[(Continuous Access Evaluation)](../conditional-access/concept-continuous-access-evaluation.md)는 수명을 기반으로 한 토큰 만료에 의존하지 않고 [중요한 이벤트](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) 및 [정책 평가](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)에 따라 액세스 토큰을 취소할 수 있는 Azure AD 기능입니다. 일부 리소스 API의 경우 위험 및 정책이 실시간으로 평가되기 때문에 토큰 수명이 최대 28시간까지 늘어날 수 있습니다. 이러한 수명이 긴 토큰은 MSAL(Microsoft Authentication Library)을 통해 사전에 새로 고침되므로 애플리케이션의 복원력이 향상됩니다.

이 문서에서는 애플리케이션에서 CAE 사용 API를 사용하는 방법을 보여 줍니다. MSAL을 사용하지 않는 애플리케이션은 CAE를 사용하기 위해 [클레임 챌린지, 클레임 요청 및 클라이언트 기능](claims-challenge.md)에 대한 지원을 추가할 수 있습니다.

## <a name="implementation-considerations"></a>구현 고려 사항

지속적인 액세스 권한 평가를 사용하려면 앱 및 액세스하는 리소스 API 모두 CAE를 사용하도록 설정해야 합니다. 그러나 CAE 사용 리소스를 사용하도록 코드를 준비해도 CAE가 사용되지 않는 API를 사용할 수는 있습니다.

리소스 API가 CAE을 구현하고 애플리케이션에서 CAE를 처리할 수 있다고 선언하는 경우 해당 리소스에 대한 CAE 토큰이 제공됩니다. 이러한 이유로 앱에서 CAE가 준비되었다고 선언하는 경우, 애플리케이션은 Microsoft Identity 액세스 토큰을 수락하는 모든 리소스 API에 대한 CAE 클레임 챌린지를 처리해야 합니다. 이러한 API 호출에서 CAE 응답을 처리하지 않는 경우 앱은 여전히 토큰의 반환된 수명 동안 토큰을 사용하여 API 호출을 다시 시도하지만 CAE로 인해 철회된 루프에서 종료될 수 있습니다.

## <a name="the-code"></a>코드

첫 번째 단계는 CAE로 인해 호출을 거부하는 리소스 API의 응답을 처리하는 코드를 추가하는 것입니다. CAE를 사용하는 경우 API는 액세스 토큰이 철회되었거나 API에서 사용되는 IP 주소 변경을 감지한 경우 401 상태 및 WWW-Authenticate 헤더를 반환합니다. WWW-Authenticate 헤더는 애플리케이션이 새 액세스 토큰을 획득하는 데 사용할 수 있는 클레임 챌린지를 포함합니다.

예를 들면 다음과 같습니다.

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

앱에서 다음을 확인합니다.

- 401 상태를 반환하는 API 호출
- 다음을 포함하는 WWW-Authenticate 헤더의 존재:
  - "insufficient_claims 값을 포함하는 "오류" 매개 변수
  - "클레임" 매개 변수

이러한 조건이 충족되면 앱에서 MSAL.NET `WwwAuthenticateParameters` 클래스를 사용하여 클레임 챌린지를 추출하고 디코딩할 수 있습니다.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        string claimChallenge = WwwAuthenticateParameters.GetClaimChallengeFromResponseHeaders(APIresponse.Headers);
```

그러면 앱에서 클레임 챌린지를 사용하여 리소스에 대한 새 액세스 토큰을 획득합니다.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

애플리케이션이 CAE 사용 리소스에서 반환된 클레임 챌린지를 처리할 준비가 되면 앱에 CAE가 준비되었다고 Microsoft Identity에 지시할 수 있습니다. MSAL 애플리케이션에서 이 작업을 수행하려면 "cp1"의 클라이언트 기능을 사용하여 공용 클라이언트를 빌드합니다.

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

애플리케이션에 사용자를 로그인하여 애플리케이션을 테스트한 다음 Azure Portal를 사용하여 사용자 세션을 철회할 수 있습니다. 다음에 앱에서 CAE 사용 API를 호출하면 사용자에게 다시 인증하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [지속적인 액세스 평가](../conditional-access/concept-continuous-access-evaluation.md) 개념 개요
- [클레임 챌린지, 클레임 요청 및 클라이언트 기능](claims-challenge.md)
