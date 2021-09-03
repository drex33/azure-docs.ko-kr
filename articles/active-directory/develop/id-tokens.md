---
title: Microsoft ID 플랫폼 ID 토큰 | Azure
titleSuffix: Microsoft identity platform
description: Azure AD v1.0 및 Microsoft ID 플랫폼(v2.0) 엔드포인트에서 내보내는 id_tokens를 사용하는 방법을 알아봅니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: c1e125127cf4376eb96e267c11e35085a4a27f18
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458902"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft ID 플랫폼 ID 토큰

ID 토큰은 [OpenID Connect](v2-protocols-oidc.md)가 OAuth 2.0에 대해 만드는 핵심 확장 기능입니다. ID 토큰은 권한 부여 서버에서 발급되며 사용자에 대한 정보를 전달하는 클레임을 포함합니다. 액세스 토큰 대신 또는 함께 전송할 수 있습니다. ID 토큰의 정보를 통해 클라이언트는 사용자가 자신이 클레임하는 사용자인지 확인할 수 있습니다. ID 토큰은 타사 애플리케이션에서 해석할 수 있습니다. ID 토큰은 권한 부여 목적으로 사용하면 안 됩니다. [액세스 토큰](access-tokens.md)은 권한 부여에 사용됩니다. ID 토큰이 제공하는 클레임은 [데이터베이스의 키](#using-claims-to-reliably-identify-a-user-subject-and-object-id)로 애플리케이션 내부 UX에 사용할 수 있으며 클라이언트 애플리케이션에 대한 액세스를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 문서는 이 문서를 읽기 전에 읽으면 유용합니다.

* [OAuth 2.0 및 OpenID Connect 프로토콜](active-directory-v2-protocols.md)의 Microsoft ID 플랫폼에서의 활용


## <a name="claims-in-an-id-token"></a>ID 토큰의 클레임

ID 토큰은 [JWT(JSON 웹 토큰)](https://wikipedia.org/wiki/JSON_Web_Token)입니다. ID 토큰은 헤더, 페이로드 및 서명으로 구성됩니다. 헤더와 서명을 사용하여 토큰의 신뢰성을 확인할 수 있으며, 페이로드에는 클라이언트가 요청한 사용자에 관한 정보가 포함됩니다. V1.0 및 v2.0 ID 토큰은 전달되는 정보의 양에 차이가 있습니다. 이 버전은 요청된 위치의 엔드포인트를 기준으로 합니다. 기존 애플리케이션은 Azure AD 엔드포인트(v1.0)를 사용할 가능성이 높은 반면, 새 애플리케이션은 “Microsoft ID 플랫폼” 엔드포인트(v2.0)를 사용해야 합니다.

* v1.0: Azure AD 엔드포인트: `https://login.microsoftonline.com/common/oauth2/authorize`
* v2.0: Microsoft ID 플랫폼 엔드포인트: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="sample-v10-id-token"></a>샘플 v1.0 ID 토큰

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

[jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q)에서 이 v1.0 샘플 토큰을 확인합니다.

### <a name="sample-v20-id-token"></a>샘플 v2.0 ID 토큰

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

[jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)에서 이 v2.0 샘플 토큰을 확인합니다.

아래에 나열된 모든 JWT 클레임은 달리 명시되지 않는 한 v1.0 및 v2.0 토큰 모두에 표시됩니다.

### <a name="header-claims"></a>헤더 클레임

아래 표에서는 ID 토큰에 있는 헤더 클레임을 보여 줍니다.

|클레임 | 형식 | Description |
|-----|--------|-------------|
|`typ` | 문자열 - 항상 "JWT" | 토큰이 JWT 토큰임을 나타냅니다.|
|`alg` | String | 토큰을 서명하는 데 사용된 알고리즘을 나타냅니다. 예: "RS256" |
| `kid` | String | 이 토큰의 서명 유효성을 검사하는 데 사용할 수 있는 퍼블릭 키의 지문을 지정합니다. v1.0 및 v2.0 ID 토큰으로 내보냅니다. |
| `x5t` | String | 사용 및 값에서 `kid`와 동일하게 작동합니다. `x5t`는 호환성을 위해 v1.0 ID 토큰으로만 내보내는 레거시 클레임입니다. |

### <a name="payload-claims"></a>페이로드 클레임

아래 표에는 기본값으로 대부분의 ID 토큰에 있는 클레임이 표시됩니다(명시된 경우 제외).  그러나 사용자의 앱은 [선택적 클레임](active-directory-optional-claims.md)을 사용하여 ID 토큰의 추가 클레임을 요청할 수 있습니다. 선택적 클레임은 `groups` 클레임부터 사용자의 이름에 대한 정보까지 다양할 수 있습니다.

|클레임 | 형식 | Description |
|-----|--------|-------------|
|`aud` |  문자열, 앱 ID GUID | 토큰의 의도한 수신자를 식별합니다. `id_tokens`에서 액세스 토큰에서 대상은 Azure Portal에서 앱에 할당된 앱의 애플리케이션 ID입니다. 이 값의 유효성을 검사해야 합니다. 앱의 애플리케이션 ID와 일치하지 않는 경우 토큰을 거부해야 합니다. |
|`iss` |  문자열, 발급자 URI | 토큰을 생성하고 반환하는 발급자 또는 “권한 부여 서버”를 식별합니다. 또한 사용자가 인증된 Azure AD 테넌트도 식별합니다. v2.0 엔드포인트에서 토큰을 발급한 경우 URI는 `/v2.0`에서 종료됩니다.  사용자가 Microsoft 계정의 소비자 사용자임을 나타내는 GUID는 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. 앱은 클레임의 GUID 부분을 사용하여 앱에 로그인할 수 있는 테넌트 집합을 제한할 수 있습니다(해당되는 경우). |
|`iat` |  int, UNIX 타임스탬프 | "발급 시간"은 이 토큰에 대한 인증이 발생한 시간을 나타냅니다.  |
|`idp`|문자열, 대개 STS URI입니다. | 토큰의 주체를 인증한 ID 공급자를 기록합니다. 이 값은 사용자 계정이 발행자(예: 게스트)와 동일한 테넌트에 속하지 않는 경우 발행자 클레임의 값과 동일합니다. 클레임이 없는 경우 `iss` 값을 대신 사용할 수 있음을 의미합니다.  조직 컨텍스트(예: Azure AD 테넌트에 초대된 개인 계정)에서 사용되는 개인 계정의 경우, `idp` 클레임은 ‘live.com’ 또는 Microsoft 계정 테넌트 `9188040d-6c67-4c5b-b112-36a304b66dad`가 포함된 STS URI일 수 있습니다. |
|`nbf` |  int, UNIX 타임스탬프 | "nbf"(not before) 클레임은 JWT가 그 이전에는 처리를 허용하지 않아야 하는 시간을 식별합니다.|
|`exp` |  int, UNIX 타임스탬프 | "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다.  특정 상황에서는 리소스가 이 시간 전에 토큰을 거부할 수 있다는 점에 유의해야 합니다. 예를 들어 인증 변경이 필요하거나 토큰 해지가 감지된 경우입니다. |
| `c_hash`| String |코드 해시는 OAuth 2.0 권한 부여 코드와 함께 ID 토큰이 발급된 경우에만 ID 토큰에 포함됩니다. 이 코드 해시는 인증 코드의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법을 이해하려면 [OpenID Connect 사양](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken)을 참조하세요. |
|`at_hash`| String |액세스 토큰 해시는 OAuth 2.0 액세스 토큰을 사용하여 `/authorize` 엔드포인트에서 ID 토큰이 발급된 경우에만 ID 토큰에 포함됩니다. 액세스 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법을 이해하려면 [OpenID Connect 사양](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken)을 참조하세요. 이는 `/token` 엔드포인트의 ID 토큰에서 반환되지 않습니다. |
|`aio` | 불투명 문자열 | Azure AD에서 토큰을 다시 사용하기 위해 데이터를 기록하는 데 사용하는 내부 클레임입니다. 무시됩니다.|
|`preferred_username` | String | 사용자를 나타내는 기본 사용자 이름입니다. 메일 주소, 전화 번호 또는 지정된 형식이 없는 일반 사용자 이름일 수 있습니다. 해당 값은 변경 가능하며 시간이 지남에 따라 변경될 수 있습니다. 해당 값은 변경 가능하므로 권한 부여 결정을 내리는 데 사용되지 않아야 합니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다.|
|`email` | String | `email` 클레임은 이메일 주소가 있는 게스트 계정에 대해 기본적으로 제공됩니다.  사용자 앱은 `email` [선택적 클레임](active-directory-optional-claims.md)을 사용하여 관리되는 사용자(리소스와 동일한 테넌트의 사용자)에 대한 이메일 클레임을 요청할 수 있습니다.  v2.0 엔드포인트에서 사용자 앱은 `email` OpenID Connect 범위를 요청할 수도 있지만, 클레임을 가져오기 위해 선택적 클레임 및 범위를 모두 요청할 필요는 없습니다.  이메일 클레임은 사용자의 프로필 정보에서 주소 지정이 가능한 메일만 지원합니다. |
|`name` | String | `name` 클레임은 토큰의 주체를 식별하는, 사람이 읽을 수 있는 값을 제공합니다. 이 값은 반드시 고유한 것은 아니며, 변경 가능하고 표시 용도로만 사용하도록 디자인되었습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
|`nonce`| String | Nonce는 원본에 포함된 매개 변수와 일치하며 IDP에 대한 요청을 인증합니다. 일치하지 않는 경우 애플리케이션이 토큰을 거부해야 합니다. |
|`oid` | 문자열, GUID | 이 경우에 Microsoft ID 시스템에 있는 개체의 변경할 수 없는 식별자는 사용자 계정입니다. 이 ID는 애플리케이션에서 사용자를 고유하게 식별합니다. 동일한 사용자가 로그인한 두 개의 다른 애플리케이션은 `oid` 클레임에서 동일한 값을 받습니다. Microsoft Graph는 이 ID를 지정된 사용자 계정에 대한 `id` 속성으로 반환합니다. `oid`를 사용하면 여러 앱에서 사용자의 상관 관계를 지정하기 때문에 이 클레임을 수신하기 위해 `profile` 범위가 필요합니다. 단일 사용자가 여러 테넌트에 존재하는 경우 사용자는 각 테넌트에서 다른 개체 ID를 포함합니다. 사용자가 동일한 자격 증명으로 각 계정에 로그인하더라도 서로 다른 계정으로 간주됩니다. `oid` 클레임은 GUID이며 다시 사용할 수 없습니다. |
|`roles`| 문자열 배열 | 로그인하는 사용자에게 할당된 역할의 집합입니다. |
|`rh` | 불투명 문자열 |Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 무시됩니다. |
|`sub` | String | 앱 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 주체는 쌍으로 된 식별자이며 특정 애플리케이션 ID에 고유합니다. 단일 사용자가 두 개의 다른 클라이언트 ID를 사용하여 두 개의 다른 앱에 로그인하는 경우 해당 앱은 주체 클레임에 두 개의 다른 값을 받게 됩니다. 이는 아키텍처 및 개인 정보 요구 사항에 따라 필요하거나 필요하지 않을 수 있습니다. |
|`tid` | 문자열, GUID | 사용자가 로그인하는 테넌트를 나타냅니다. 회사 및 학교 계정의 경우 GUID는 사용자가 로그인하는 조직의 테넌트 ID로, 변경할 수 없습니다. 개인 Microsoft 계정 테넌트(Xbox, Teams for Life 또는 Outlook 같은 서비스)에 로그인하는 경우 값은 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. 이 클레임을 받으려면 앱에서 `profile` 범위를 요청해야 합니다. |
|`unique_name` | String | 토큰의 주체를 식별하는, 사람이 인식할 수 있는 값을 제공합니다. 이 값은 특정 시점에는 고유하지만, 이메일 및 기타 식별자를 다시 사용할 수 있을 때 다른 계정에 다시 나타날 수 있습니다. 따라서 이 값은 표시 목적으로만 사용해야 합니다. v1.0 `id_tokens`로만 발급됩니다. |
|`uti` | 불투명 문자열 | Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 무시됩니다. |
|`ver` | 문자열, 1.0 또는 2.0 | id_token의 버전을 나타냅니다. |
|`hasgroups`|부울|있는 경우 항상 true로, 사용자 하나 이상의 그룹에 있음을 나타냅니다. 전체 groups 클레임이 URI 조각을 URL 길이 한도(현재 6개 이상 그룹)를 초과하여 확장할 경우 암시적 권한 부여 흐름에서 JWT에 대해 그룹 클레임 대신 사용됩니다. 클라이언트가 Microsoft Graph API를 사용하여 사용자 그룹(`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)을 결정해야 함을 표시합니다.|
|`groups:src1`|JSON 개체 | 토큰 요청의 길이는 제한되지 않으나(위의 `hasgroups` 참조) 여전히 토큰이 감당하기에는 너무 큰 토큰 요청의 경우, 사용자의 전체 그룹 목록이 포함됩니다. 분산된 클레임으로서의 JWT인 경우 SAML이 `groups` 클레임 대신에 새 클레임이 됩니다. <br><br>**JWT 값 예제**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> 자세한 내용은 [Groups 초과분 클레임](#groups-overage-claim)을 참조하세요.|

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>클레임을 사용하여 사용자를 안정적으로 식별(주체 및 개체 ID)

사용자를 식별하는 경우(예를 들어 데이터베이스에서 조회하거나 해당 사용자가 보유한 권한을 결정하는 경우)에는 시간이 지나도 일정하고 고유하게 유지되는 정보를 사용하는 것이 중요합니다. 레거시 애플리케이션은 이메일 주소, 전화 번호 또는 UPN과 같은 필드를 사용하는 경우도 있습니다.  시간 경과에 따라 모두 변경될 수 있으며 다시 사용될 수도 있습니다. 예를 들어 직원이 개명하거나 현재 직원에게 이제 더 이상 재직 중이 아닌 이전 직원의 이메일 주소를 주는 경우가 이에 포함됩니다. 따라서 애플리케이션이 사용자를 식별하기 위해 사람이 읽을 수 있는 데이터를 사용하지 않는 것이 **중요** 합니다. 사람이 읽을 수 있다면 누군가가 바꿀 수 있기 때문입니다. 대신 OIDC 표준에서 제공하는 클레임 또는 Microsoft에서 제공하는 확장 클레임(`sub` 및 `oid` 클레임)을 사용합니다.

사용자별 정보를 올바로 저장하려면 `sub` 또는 `oid`를 독립적으로 사용하거나(이 경우 GUID는 고유함) 필요한 경우 라우팅 또는 분할에 사용되는 `tid`를 사용합니다.  서비스 간에 데이터를 공유해야 하는 경우 모든 앱이 지정된 테넌트에서 작업하는 지정된 사용자에 대해 동일한 `oid` 및 `tid` 클레임을 얻게 되므로 `oid`+`tid`가 가장 좋습니다.  Microsoft ID 플랫폼의 `sub` 클레임은 “쌍으로 된 조합”입니다. 토큰 수신자, 테넌트 및 사용자의 조합을 기준으로 고유합니다.  따라서 지정된 사용자에 대한 ID 토큰을 요청하는 두 개의 앱은 다른 `sub` 클레임을 수신하지만 해당 사용자에 대해서는 `oid` 클레임으로 동일합니다.

>[!NOTE]
> 사용자에 대한 정보를 저장하여 테넌트 간의 상관 관계를 지정하기 위해 `idp` 클레임을 사용하지 마세요.  애플리케이션이 테넌트 간에 사용자를 추적할 수 없도록 설계를 통해 테넌트 간 사용자에 대한 `oid` 및 `sub` 클레임이 변경되기 때문에 작동하지 않습니다.  
>
> 사용자가 한 테넌트에 위치하고 있지만 다른 테넌트에서 인증하는 게스트 시나리오는 사용자가 서비스에 대한 새 사용자인 것처럼 사용자를 처리해야 합니다.  Contoso 테넌트의 문서와 권한은 Fabrikam 테넌트에서 적용되지 않습니다. 이 제한은 테넌트 간에 실수로 데이터가 유출되고 데이터 수명 주기가 적용되는 것을 방지하는 데 중요합니다.  테넌트에서 게스트를 제거하면 해당 테넌트에서 게스트가 만든 데이터에 대한 액세스 권한도 제거됩니다. 

### <a name="groups-overage-claim"></a>그룹 초과분 클레임
토큰 크기가 HTTP 헤더 크기 제한을 초과하지 않도록 하기 위해 Azure AD는 `groups` 클레임에 포함되는 개체 ID 수를 제한합니다. 사용자가 초과분 제한(SAML 토큰의 경우 150, JWT 토큰의 경우 200)보다 많은 그룹의 멤버인 경우 Azure AD는 토큰의 그룹 클레임을 내보내지 않습니다. 대신 애플리케이션에 Microsoft Graph API를 쿼리하여 사용자의 그룹 멤버 자격을 검색하도록 가리키는 토큰의 초과분 클레임이 포함됩니다.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="id-token-lifetime"></a>ID 토큰 수명

기본값으로 ID 토큰은 1시간 동안 유효합니다. 1시간 후 클라이언트는 새 ID 토큰을 획득해야 합니다.

ID 토큰의 수명을 조정하여 클라이언트 애플리케이션이 애플리케이션 세션을 만료하는 빈도 및 사용자에게 자동으로 또는 대화형으로 다시 인증하도록 요구하는 빈도를 제어할 수 있습니다. 자세한 내용은 [구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조하세요.

## <a name="validating-an-id-token"></a>ID 토큰 유효성 검사

ID 토큰의 유효성 검사는 [액세스 토큰의 유효성을 검사](access-tokens.md#validating-tokens)하는 첫 번째 단계와 유사합니다. 클라이언트는 토큰의 변조 여부를 확인할 수 있습니다. 발급자의 유효성을 검사하여 올바른 발급자가 토큰을 다시 보냈는지 확인할 수도 있습니다. ID 토큰은 항상 JWT 토큰이므로 이러한 토큰의 유효성을 검사하는 많은 라이브러리가 있습니다. 직접 수행하지 않고 이러한 라이브러리 중 하나를 사용하는 것이 좋습니다.  기밀 클라이언트(암호 포함)만 ID 토큰의 유효성을 검사해야 합니다.  공용 애플리케이션(사용자의 브라우저 또는 홈 네트워크처럼 사용자가 제어하지 않는 디바이스 또는 네트워크에서 실행되는 코드)에서 ID 토큰 유효성을 검사하는 것은 비효율적입니다. 악의적인 사용자가 토큰의 유효성 검사에 사용되는 키를 가로채고 편집할 수 있기 때문입니다.

수동으로 토큰 유효성을 검사하려면 [액세스 토큰의 유효성 검사](access-tokens.md#validating-tokens)에서 자세한 단계를 참조하세요. 토큰에서 서명의 유효성을 검사한 후에는 ID 토큰에서 다음 JWT 클레임의 유효성을 검사 해야 합니다. 토큰 유효성 검사 라이브러리에서 이러한 클레임의 유효성을 검사할 수도 있습니다.

* 타임스탬프: `iat`, `nbf` 및 `exp` 타임스탬프는 모두 적절히 현재 시간 이전이거나 이후여야 합니다.
* 대상: `aud` 클레임은 애플리케이션의 앱 ID와 일치해야 합니다.
* Nonce: 페이로드의 `nonce` 클레임은 최초 요청 중에 /authorize 엔드포인트로 전달된 nonce 매개 변수와 일치해야 합니다.

## <a name="next-steps"></a>다음 단계

* ID 토큰을 내보내는 프로토콜을 정의하는 [OpenID Connect](v2-protocols-oidc.md) 흐름을 검토합니다. 
* [액세스 토큰](access-tokens.md)에 대해 자세히 알아보기
* ID 토큰의 JWT 클레임을 [선택적 클레임](active-directory-optional-claims.md)을 사용하여 사용자 지정합니다.
