---
title: Microsoft ID 플랫폼 액세스 토큰 | Azure
titleSuffix: Microsoft identity platform
description: Azure AD v1.0 및 Microsoft ID 플랫폼(v2.0) 엔드포인트에서 내보내는 액세스 토큰에 대해 알아봅니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: marsma
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: d633f52d739552a02999295ec083a965e0fa45fd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447031"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft ID 플랫폼 액세스 토큰

액세스 토큰은 클라이언트가 보호된 웹 API를 안전하게 호출할 수 있도록 하며 웹 API가 인증 및 권한 부여를 수행하는 데 사용됩니다. OAuth 사양에 따라 액세스 토큰은 정해진 형식이 없는 불투명 문자열입니다. 일부 IDP(ID 공급자)는 GUID를 사용하고 다른 일부는 암호화된 BLOB을 사용합니다. Microsoft ID 플랫폼은 토큰을 허용하는 API의 구성에 따라 다양한 액세스 토큰 형식을 사용합니다. Microsoft ID 플랫폼의 [개발자가 등록한 사용자 지정 API](quickstart-configure-app-expose-web-apis.md)는 "v1" 및 "v2"라고 하는 두 가지 다른 형식의 JWT(JSON 웹 토큰) 중 선택할 수 있으며, Azure의 Microsoft Graph 또는 API와 같은 Microsoft에서 개발한 API에는 독점적 추가 토큰 형식이 있습니다. 이러한 독점적 형식은 암호화된 토큰, JWT 또는 유효성 검사를 수행하지 않을 특수 JWT 유사 토큰일 수 있습니다.

토큰의 내용은 리소스(API)만을 위한 것이므로 클라이언트는 액세스 토큰을 불투명 문자열로 처리해야 합니다. 개발자는 *오직* 유효성 검사 및 디버깅 목적으로만 [jwt.ms](https://jwt.ms)와 같은 사이트를 사용하여 JWT를 디코딩할 수 있습니다. 그러나 Microsoft API에 대해 수신하는 토큰은 항상 JWT인 것은 아니며 항상 디코딩할 수 있는 것은 아닙니다.

액세스 토큰 내에 있는 항목의 세부 정보를 위해서 클라이언트는 액세스 토큰과 함께 클라이언트에 반환되는 토큰 응답 데이터를 사용해야 합니다. 클라이언트가 액세스 토큰을 요청하는 경우 Microsoft ID 플랫폼은 앱이 사용할 수 있도록 액세스 토큰에 대한 일부 메타데이터도 반환합니다. 이 정보에는 액세스 토큰의 만료 시간 및 유효 범위가 포함되어 있습니다. 이 데이터를 사용하면 앱에서 액세스 토큰 자체를 구문 분석하지 않고도 액세스 토큰의 인텔리전트 캐싱을 수행할 수 있습니다.

API의 유효성을 검사하고 액세스 토큰 내에서 클레임을 사용하는 방법에 대해 알아보려면 다음 섹션을 참조하세요.  

> [!NOTE]
> 명시된 경우를 제외하고 이 페이지의 모든 문서는 등록한 API에 대해 발행된 토큰에만 적용됩니다.  Microsoft 소유의 API에 대해 발급된 토큰에는 적용되지 않으며, 이러한 토큰은 Microsoft ID 플랫폼이 사용자가 만든 API에 대한 토큰을 발급하는 방법의 유효성을 검사하는 데 사용될 수 없습니다.  

## <a name="token-formats-and-ownership"></a>토큰 형식 및 소유권

### <a name="v10-and-v20"></a>v1.0 및 v2.0 

Microsoft ID 플랫폼에서 사용할 수 있는 액세스 토큰에는 v1.0 및 v2.0의 두 가지 버전이 있습니다.  이러한 버전은 토큰에 있는 클레임을 관리하여 웹 API가 토큰의 모양을 제어할 수 있도록 합니다. 웹 API는 등록 중 기본값으로 이 중 하나가 선택됩니다. Azure AD 전용 앱을 위해서는 v1.0, 소비자 계정을 지원하는 앱의 경우 v2.0이 선택됩니다.  이는 [앱 매니페스트](reference-app-manifest.md#manifest-reference)의 `accessTokenAcceptedVersion` 설정을 사용하는 애플리케이션에 의해 제어됩니다. 여기서 `null` 및 `1`은 v1.0 토큰이 되고 `2`는 v2.0 토큰이 됩니다.

### <a name="what-app-is-a-token-for"></a>토큰은 어떤 앱을 “위한” 것인가요?

액세스 토큰 요청에는 토큰을 요청하는 클라이언트와 API를 호출할 때 토큰을 허용하는 리소스(API)가 포함됩니다. 토큰의 `aud` 클레임은 토큰이 의도하는 대상 리소스(*대상*)를 나타냅니다. 클라이언트는 토큰을 사용하지만 이해하거나 구문 분석을 시도하지 않아야 합니다. 리소스는 토큰을 허용합니다.  

Microsoft ID 플랫폼은 모든 버전 엔드포인트에서 모든 토큰 버전을 발급하는 것을 지원합니다. 이는 서로 관련이 없습니다. 따라서 리소스의 `accessTokenAcceptedVersion`을 `2`로 설정하면 해당 API에 대한 토큰을 가져오기 위해 v1.0 엔드포인트를 호출하는 클라이언트가 v2.0 액세스 토큰을 받게 됩니다.  리소스는 항상 토큰을 소유하며(`aud` 클레임을 사용하는 경우) 토큰 세부 정보를 변경할 수 있는 유일한 애플리케이션입니다. Microsoft Graph 리소스가 소유한 `user.read`에 대해 토큰이 요청될 때 *클라이언트* 에 대한 액세스 토큰 [선택적 클레임](active-directory-optional-claims.md)을 변경해도 수신된 액세스 토큰이 변경되지 않는 이유도 여기에 있습니다.

### <a name="sample-tokens"></a>샘플 토큰

v1.0 및 v2.0 토큰은 유사하며 많은 동일한 클레임을 포함합니다. 각각의 예는 다음과 같습니다. 그러나 게시 전에 키는 회전되고 개인 정보는 제거되기 때문에 이러한 예제 토큰은 [유효성을 검사](#validating-tokens)하지 않습니다.

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

[JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd)에서 이 v1.0 토큰을 확인합니다.

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

[JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)에서 이 v2.0 토큰을 확인합니다.

## <a name="claims-in-access-tokens"></a>액세스 토큰의 클레임

JWT(JSON Web Token)는 세 부분으로 분할됩니다.

* **헤더** - 토큰 유형과 서명 방법에 대한 정보를 포함하여 [토큰의 유효성을 검사](#validating-tokens)하는 방법에 대한 정보를 제공합니다.
* **페이로드** - 서비스를 호출하려고 하는 사용자 또는 앱에 대한 모든 중요 데이터가 포함되어 있습니다.
* **서명** - 토큰의 유효성을 검사하는 데 사용되는 원시 자료입니다.

각 부분은 마침표(`.`)로 구분되며 개별적으로 Base64로 인코딩됩니다.

클레임은 채울 값이 존재하는 경우에만 제공됩니다. 앱은 제공되는 클레임에 대해 종속성이 없어야 합니다. 예로는 `pwd_exp`(모든 테넌트가 암호 만료될 필요가 없음) 및 `family_name`([클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) 흐름이 이름 없는 애플리케이션을 대신함)이 있습니다. 액세스 토큰 유효성 검사에 사용되는 클레임은 항상 제공됩니다.

일부 클레임은 Azure AD에서 토큰을 다시 사용하는 경우 안전하게 보호하는 데 사용됩니다. 이러한 클레임은 설명에 "불투명"으로 되어 공개 사용되지 않는 것으로 표시됩니다. 이러한 클레임은 토큰에 표시되거나 표시되지 않을 수 있으며, 통지 없이 새로 추가될 수도 있습니다.

### <a name="header-claims"></a>헤더 클레임

|클레임 | 형식 | Description |
|--------|--------|-------------|
| `typ` | 문자열 - 항상 "JWT" | 토큰이 JWT임을 나타냅니다.|
| `alg` | String | 토큰에 서명하는 데 사용된 알고리즘을 나타냅니다(예: "RS256"). |
| `kid` | String | 이 토큰의 서명 유효성을 검사하는 데 사용할 수 있는 퍼블릭 키의 지문을 지정합니다. v1.0 및 v2.0 액세스 토큰으로 내보냅니다. |
| `x5t` | String | 사용 및 값에서 `kid`와 동일하게 작동합니다. `x5t`는 호환성을 위해 v1.0 액세스 토큰으로만 내보내는 레거시 클레임입니다. |

### <a name="payload-claims"></a>페이로드 클레임

| 클레임 | 형식 | Description |
|-----|--------|-------------|
| `aud` | 문자열, 앱 ID URI, GUID | 토큰의 의도된 수신자(대상)를 식별합니다.  API는 이 값의 유효성을 검사하고 값이 일치하지 않는 경우 토큰을 거부해야 합니다. v2.0 토큰에서 이는 항상 API의 클라이언트 ID이고, v1.0 토큰에서는 클라이언트가 토큰을 요청한 방법에 따라 요청에 사용되는 클라이언트 ID 또는 리소스 URI가 될 수 있습니다.|
| `iss` | 문자열, STS URI | 토큰을 생성하고 반환하는 STS(보안 토큰 서비스) 및 사용자가 인증된 Azure AD 테넌트를 식별합니다. 발급된 토큰은 v2.0 토큰이며(`ver` 클레임 참조), URI는 `/v2.0`에서 종료됩니다. 사용자가 Microsoft 계정의 소비자 사용자임을 나타내는 GUID는 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. 앱은 클레임의 GUID 부분을 사용하여 앱에 로그인할 수 있는 테넌트 집합을 제한할 수 있습니다(해당되는 경우). |
|`idp`| 문자열, 대개 STS URI입니다. | 토큰의 주체를 인증한 ID 공급자를 기록합니다. 이 값은 사용자 계정이 발행자(예: 게스트)와 동일한 테넌트에 속하지 않는 경우 발행자 클레임의 값과 동일합니다. 클레임이 없는 경우 `iss` 값을 대신 사용할 수 있음을 의미합니다.  조직 컨텍스트(예: Azure AD 테넌트에 초대된 개인 계정)에서 사용되는 개인 계정의 경우, `idp` 클레임은 ‘live.com’ 또는 Microsoft 계정 테넌트 `9188040d-6c67-4c5b-b112-36a304b66dad`가 포함된 STS URI일 수 있습니다. |
| `iat` | int, UNIX 타임스탬프 | "발급 시간"은 이 토큰에 대한 인증이 발생한 시간을 나타냅니다. |
| `nbf` | int, UNIX 타임스탬프 | "nbf"(not before) 클레임은 JWT가 그 이전에는 처리를 허용하지 않아야 하는 시간을 식별합니다. |
| `exp` | int, UNIX 타임스탬프 | "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. 인증 변경이 필요한 경우나 토큰 해지가 감지된 경우에도 이 시간 전에는 리소스가 토큰을 거부할 수 있습니다. |
| `aio` | 불투명 문자열 | Azure AD에서 토큰을 다시 사용하기 위해 데이터를 기록하는 데 사용하는 내부 클레임입니다. 리소스에서는 이 클레임을 사용하지 않아야 합니다. |
| `acr` | 문자열, "0" 또는 "1" | v1.0 토큰에만 제공됩니다. “인증 컨텍스트 클래스” 클레임. 값 "0"은 최종 사용자 인증이 ISO/IEC 29115 요구 사항을 충족하지 못했다는 뜻입니다. |
| `amr` | 문자열의 JSON 배열 | v1.0 토큰에만 제공됩니다. 토큰의 주체가 인증된 방법을 식별합니다. 자세한 내용은 [amr 클레임 섹션](#the-amr-claim)을 참조하세요. |
| `appid` | 문자열, GUID | v1.0 토큰에만 제공됩니다. 토큰을 사용하는 클라이언트의 애플리케이션 ID입니다. 애플리케이션은 자체적으로 작동할 수도 있고 사용자를 대신하여 작동할 수도 있습니다. 애플리케이션 ID는 일반적으로 애플리케이션 개체를 나타내지만 Azure AD의 서비스 사용자 개체를 나타낼 수도 있습니다. |
| `azp` | 문자열, GUID | `appid`의 대체 항목인 v2.0 토큰에만 있습니다. 토큰을 사용하는 클라이언트의 애플리케이션 ID입니다. 애플리케이션은 자체적으로 작동할 수도 있고 사용자를 대신하여 작동할 수도 있습니다. 애플리케이션 ID는 일반적으로 애플리케이션 개체를 나타내지만 Azure AD의 서비스 사용자 개체를 나타낼 수도 있습니다. |
| `appidacr` | "0", "1" 또는 "2" | v1.0 토큰에만 제공됩니다. 클라이언트가 인증된 방법을 나타냅니다. 공용 클라이언트의 경우 값은 "0"입니다. 클라이언트 ID 및 클라이언트 비밀이 사용되는 경우 값은 "1"입니다. 클라이언트 인증서가 인증에 사용된 경우 값은 "2"입니다. |
| `azpacr` | "0", "1" 또는 "2" | `appidacr`의 대체 항목인 v2.0 토큰에만 있습니다. 클라이언트가 인증된 방법을 나타냅니다. 공용 클라이언트의 경우 값은 "0"입니다. 클라이언트 ID 및 클라이언트 비밀이 사용되는 경우 값은 "1"입니다. 클라이언트 인증서가 인증에 사용된 경우 값은 "2"입니다. |
| `preferred_username` | String | 사용자를 나타내는 기본 사용자 이름입니다. 메일 주소, 전화 번호 또는 지정된 형식이 없는 일반 사용자 이름일 수 있습니다. 해당 값은 변경 가능하며 시간이 지남에 따라 변경될 수 있습니다. 해당 값은 변경 가능하므로 권한 부여 결정을 내리는 데 사용되지 않아야 합니다.  그러나 사용자 이름 힌트 및 사람이 읽을 수 있는 UI에서 사용자 이름으로 사용할 수 있습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. v2.0 토큰에만 표시됩니다. |
| `name` | String | 사람이 인식할 수 있으며 토큰의 주체를 식별하는 값을 제공합니다. 이 값은 반드시 고유한 것은 아니며 변경 가능하고 표시 용도로만 사용하도록 디자인되었습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| `scp` | 문자열, 공백으로 구분된 범위 목록 | 클라이언트 애플리케이션이 동의를 요청(및 수신)한 애플리케이션에 의해 노출된 범위 집합입니다. 앱은 이러한 범위가 앱에 의해 노출된 유효한 범위인지 확인하고 이러한 범위의 값을 기반으로 권한 부여 결정을 해야 합니다. [사용자 토큰](#user-and-application-tokens)에 대해서만 포함됩니다. |
| `roles` | 문자열 배열, 권한 목록 | 요청 애플리케이션 또는 사용자가 호출할 수 있는 권한을 받은 사용자 애플리케이션에 의해 노출된 권한 집합입니다. [애플리케이션 토큰](#user-and-application-tokens)의 경우 클라이언트 자격 증명 흐름([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) 중에 사용자 범위 대신 사용됩니다.  [사용자 토큰](#user-and-application-tokens)의 경우 대상 애플리케이션에서 사용자에게 할당된 역할로 채워집니다. |
| `wids` | [RoleTemplateID](../roles/permissions-reference.md#all-roles) GUID의 배열 | [Azure AD 기본 제공 역할](../roles/permissions-reference.md#all-roles)에 있는 역할 섹션에서 이 사용자에게 할당된 테넌트 전체 역할을 나타냅니다.  이 클레임은 [애플리케이션 매니페스트](reference-app-manifest.md)의 `groupMembershipClaims` 속성을 통해 애플리케이션별로 구성됩니다.  "All" 또는 "DirectoryRole"로 설정해야 합니다.  암시적 흐름을 통해 가져온 토큰에는 토큰 길이 문제로 인해 없을 수 있습니다. |
| `groups` | GUID의 JSON 배열 | 주체의 그룹 멤버 자격을 나타내는 개체 ID를 제공합니다. 이러한 값은 고유하며(개체 ID 참조) 리소스 액세스 시 강제로 인증하게 하는 경우처럼 액세스 관리에 안전하게 사용할 수 있습니다. 그룹 클레임에 포함된 그룹은 [애플리케이션 매니페스트](reference-app-manifest.md)의 `groupMembershipClaims` 속성을 통해 애플리케이션별로 구성됩니다. null 값은 모든 그룹을 제외하고, "SecurityGroup" 값은 Active Directory 보안 그룹 구성원만 포함하며, "All" 값은 보안 그룹과 Microsoft 365 배포 목록을 모두 포함합니다. <br><br>암시적 권한 부여가 있는 `groups` 클레임 사용에 대한 자세한 내용은 아래 `hasgroups` 클레임을 참조하세요. <br>다른 흐름에서 그룹 수가 한도(SAML은 150, JWT는 200)를 넘어설 경우 초과분 클레임은 해당 사용자에 대한 그룹 목록을 포함하는 Microsoft Graph 엔드포인트를 가리키는 클레임 원본에 추가됩니다. |
| `hasgroups` | 부울 | 있는 경우 항상 `true`로, 사용자 하나 이상의 그룹에 있음을 나타냅니다. 전체 그룹 클레임이 URI 조각을 URL 길이 한도(현재 6개 이상 그룹)를 초과하여 확장할 경우 암시적 권한 부여 흐름에서 JWT에 대해 `groups` 클레임 대신 사용됩니다. 클라이언트가 Microsoft Graph API를 사용하여 사용자 그룹(`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)을 결정해야 함을 표시합니다. |
| `groups:src1` | JSON 개체 | 길이는 제한되지 않으나(위의 `hasgroups` 참조) 토큰에게는 너무 큰 토큰 요청의 경우 사용자의 전체 그룹 목록에 대한 링크가 포함됩니다. 분산된 클레임으로서의 JWT인 경우 SAML이 `groups` 클레임 대신에 새 클레임이 됩니다. <br><br>**JWT 값 예제**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | String | 앱 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 예를 들어, 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행하고 데이터베이스 테이블에서 키로 사용할 수 있습니다. Azure AD에서 발급하는 토큰에는 항상 주체가 있기 때문에 이 값을 일반 용도의 인증 시스템에 사용하는 것이 좋습니다. 그러나 주체는 쌍으로 된 식별자이며 특정 애플리케이션 ID에 고유합니다. 따라서 단일 사용자가 두 개의 다른 클라이언트 ID를 사용하여 두 개의 다른 앱에 로그인하는 경우 해당 앱은 주체 클레임에 두 개의 다른 값을 받게 됩니다. 아키텍처 및 개인 정보 보호 요구 사항에 따라 적합할 수도 있고 적합하지 않을 수도 있습니다. `oid` 클레임도 참조하세요(테넌트 내의 여러 앱에서 동일하게 유지됨). |
| `oid` | 문자열, GUID | 요청의 “주체”에 대한 변경할 수 없는 식별자 - ID가 확인된 사용자 또는 서비스 주체입니다.  ID 토큰과 앱+사용자 토큰에서 사용자의 개체 ID입니다.  앱 전용 토큰에서 호출하는 서비스 주체의 개체 ID입니다. 데이터베이스 테이블의 키로써 안전하게 권한 부여 검사를 수행하는 데 사용할 수도 있습니다. 이 ID는 애플리케이션에서 보안 주체를 고유하게 식별합니다. 동일한 사용자가 로그인한 두 개의 다른 애플리케이션은 `oid` 클레임에서 동일한 값을 받습니다. 즉, Microsoft Graph와 같은 Microsoft 온라인 서비스에 대한 쿼리를 수행할 때 `oid`를 사용할 수 있습니다. Microsoft Graph는 이 ID를 지정된 [사용자 계정](/graph/api/resources/user)에 대한 `id` 속성으로 반환합니다. `oid`를 사용하면 여러 앱에서 보안 주체의 상관 관계를 지정하기 때문에 사용자에 대해 이 클레임을 수신하기 위해 `profile` 범위가 필요합니다. 단일 사용자가 여러 테넌트에 존재하는 경우 사용자는 각 테넌트에서 다른 개체 ID를 포함합니다. 사용자가 동일한 자격 증명으로 각 계정에 로그인하더라도 서로 다른 계정으로 간주됩니다. |
|`tid` | 문자열, GUID | 사용자가 로그인하는 테넌트를 나타냅니다. 회사 및 학교 계정의 경우 GUID는 사용자가 로그인하는 조직의 테넌트 ID로, 변경할 수 없습니다. 개인 Microsoft 계정 테넌트(Xbox, Teams for Life 또는 Outlook 같은 서비스)에 로그인하는 경우 값은 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. 이 클레임을 받으려면 앱에서 `profile` 범위를 요청해야 합니다. |
| `unique_name` | String | v1.0 토큰에만 제공됩니다. 토큰의 주체를 식별하는, 사람이 인식할 수 있는 값을 제공합니다. 이 값은 테넌트 내에서 반드시 고유한 것은 아니며 표시 용도로만 사용해야 합니다. |
| `uti` | 불투명 문자열 | Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 리소스에서는 이 클레임을 사용하지 않아야 합니다. |
| `rh` | 불투명 문자열 | Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 리소스에서는 이 클레임을 사용하지 않아야 합니다. |
| `ver` | 문자열, `1.0` 또는 `2.0` | 액세스 토큰의 버전을 나타냅니다. |

**그룹 초과분 클레임**

토큰 크기가 HTTP 헤더 크기 제한을 초과하지 않도록 하기 위해 Azure AD는 그룹 클레임에 포함되는 개체 ID의 수를 제한합니다. 사용자가 초과분 제한(SAML 토큰의 경우 150, JWT 토큰의 경우 200, 암시적 흐름을 통해 발급된 경우 6)보다 많은 그룹의 구성원인 경우 Azure AD는 토큰의 그룹 클레임을 내보내지 않습니다. 대신 애플리케이션에 Microsoft Graph API를 쿼리하여 사용자의 그룹 멤버 자격을 검색하도록 가리키는 토큰의 초과분 클레임이 포함됩니다.

```JSON
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

[앱 생성 스크립트](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) 폴더에 제공되는 `BulkCreateGroups.ps1`을 사용하여 초과분 시나리오를 테스트할 수 있습니다.

#### <a name="v10-basic-claims"></a>v1.0 기본 클레임

다음 클레임은 해당되는 경우 v1.0 토큰에 포함되지만 기본적으로 v2.0 토큰에는 포함되지 않습니다. v2.0을 사용하고 이러한 클레임 중 하나가 필요한 경우 [선택적 클레임](active-directory-optional-claims.md)을 사용하여 요청합니다.

| 클레임 | 형식 | Description |
|-----|--------|-------------|
| `ipaddr`| String | 사용자가 인증된 IP 주소입니다. |
| `onprem_sid`| 문자열, [SID 형식](/windows/desktop/SecAuthZ/sid-components) | 사용자에게 온-프레미스 인증이 있는 경우 이 클레임이 해당 SID를 제공합니다. 레거시 애플리케이션에서 권한 부여에 `onprem_sid`를 사용할 수 있습니다.|
| `pwd_exp`| int, UNIX 타임스탬프 | 사용자의 암호가 만료되는 시기를 나타냅니다. |
| `pwd_url`| String | 암호를 재설정하도록 사용자에게 보낼 수 있는 URL입니다. |
| `in_corp`| boolean | 클라이언트가 회사 네트워크에서 로그인하는 경우 알립니다. 그렇지 않으면 클레임이 포함되지 않습니다. |
| `nickname`| String | 이름 및 성과는 별개인 사용자의 추가 이름입니다.|
| `family_name` | String | 사용자 개체에 정의된 대로 사용자의 성을 제공합니다. |
| `given_name` | String | 사용자 개체에 설정된 대로 사용자의 이름을 제공합니다. |
| `upn` | String | 사용자의 사용자 이름입니다. 전화 번호, 이메일 주소 또는 형식이 지정되지 않은 문자열일 수 있습니다. 표시 목적 및 재인증 시나리오에서 사용자 이름 힌트를 제공하기 위해서만 사용해야 합니다. |

#### <a name="the-amr-claim"></a>`amr` 클레임

Microsoft ID는 여러 방법으로 인증할 수 있으며 사용자 애플리케이션과 관련될 수 있습니다. `amr` 클레임은 암호 및 Authenticator 앱을 모두 사용하는 인증을 위해 여러 항목(예: `["mfa", "rsa", "pwd"]`)을 포함할 수 있는 배열입니다.

| 값 | Description |
|-----|-------------|
| `pwd` | 암호 인증으로, 사용자의 Microsoft 암호 또는 앱의 클라이언트 비밀 중 하나입니다. |
| `rsa` | 인증은 [Microsoft Authenticator 앱](https://aka.ms/AA2kvvu) 등을 사용하여 RSA 키 증명을 기반으로 수행되었습니다. X509 인증서를 소유한 서비스를 사용하여 자체 서명된 JWT에 의해 인증이 수행된 경우 포함됩니다. |
| `otp` | 이메일 또는 문자 메시지를 사용하는 OTP(일회성 암호)입니다. |
| `fed` | 페더레이션 인증 어설션(예: JWT 또는 SAML)이 사용되었습니다. |
| `wia` | Windows 통합 인증 |
| `mfa` | [다단계 인증](../authentication/concept-mfa-howitworks.md)이 사용되었습니다. 이 인증이 있는 경우 다른 인증 방법도 포함됩니다. |
| `ngcmfa` | `mfa`와 동일하며 특정 고급 자격 증명 유형의 프로비전에 사용됩니다. |
| `wiaormfa`| 사용자가 Windows 또는 MFA 자격 증명을 사용하여 인증했습니다. |
| `none` | 인증이 수행되지 않았습니다. |

## <a name="access-token-lifetime"></a>액세스 토큰 수명

액세스 토큰의 기본 수명은 토큰을 요청하는 클라이언트 애플리케이션에 따라 달라집니다. 예를 들어 CAE 인식 세션을 협상하는 CAE(지속적인 액세스 권한 평가) 지원 클라이언트는 오래 지속되는 토큰 수명(최대 28시간)을 볼 수 있습니다.  액세스 토큰이 만료되면 클라이언트는 새로 고침 토큰을 사용하여(보통 자동으로) 새로운 새로 고침 토큰을 획득하고 토큰에 액세스해야 합니다.

액세스 토큰의 수명을 조정하여 클라이언트 애플리케이션이 애플리케이션 세션을 만료하는 빈도 및 사용자에게 다시 인증하도록(자동으로 또는 대화형으로) 요구하는 빈도를 제어할 수 있습니다. 자세한 내용은 [구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조하세요.

## <a name="validating-tokens"></a>토큰 유효성 검사

모든 앱에서 토큰의 유효성을 검사해야 하는 것은 아닙니다. 특정 시나리오에서만 앱에서 토큰의 유효성을 검사해야 합니다.

* [웹 API](quickstart-configure-app-expose-web-apis.md)는 클라이언트에 의해 전송된 액세스 토큰의 유효성을 검사해야 합니다.  `aud` 클레임을 포함하는 토큰만 수락해야 합니다.
* ASP.NET Core와 같은 기밀 웹앱은 사용자 데이터에 대한 액세스를 허용하거나 세션을 설정하기 전에 하이브리드 흐름에서 사용자의 브라우저를 통해 전송된 ID 토큰의 유효성을 검사해야 합니다.

위의 시나리오 중 어느 것도 적용되지 않으면 애플리케이션은 토큰 유효성 검사의 이점을 얻지 못하며 토큰의 유효성을 기반으로 결정을 내릴 때 보안 및 안정성 위험을 초래할 수 있습니다.  네이티브 앱 또는 SPA와 같은 공용 클라이언트는 토큰의 유효성을 검사하지 않습니다. 앱은 IDP와 직접 통신하므로 SSL 보호를 통해 토큰이 유효한지 확인합니다.

API 및 웹앱은 해당 애플리케이션과 일치하는 `aud` 클레임이 있는 토큰만 유효성을 검사해야 합니다. 다른 리소스에는 사용자 지정 토큰 유효성 검사 규칙이 있을 수 있습니다. 예를 들어 Microsoft Graph 토큰은 독점적 형식으로 인해 이러한 규칙에 따라 유효성을 검사하지 않습니다. 다른 리소스에 대한 토큰의 유효성을 검사하고 허용하는 것은 [혼동된 대리인](https://cwe.mitre.org/data/definitions/441.html) 문제의 예입니다.

애플리케이션에서 위에 따라 id_token 또는 access_token의 유효성을 검사해야 하는 경우 앱은 먼저 OpenID 검색 문서의 값에 대해 토큰의 서명과 발급자의 유효성을 검사해야 합니다. 예를 들어 문서의 테넌트 독립적 버전은 [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)에 위치합니다.

기본 프로세스를 이해하려는 사용자를 위해 다음 정보가 제공됩니다. Azure AD 미들웨어에는 액세스 토큰의 유효성을 검사하는 기본 제공 기능이 있으며 [샘플](sample-v2-code.md)을 탐색하여 원하는 언어로 찾을 수 있습니다. JWT 유효성 검사에 사용할 수 있는 여러 타사 오픈 소스 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대한 옵션이 하나 이상 있습니다. Azure AD 인증 라이브러리 및 코드 샘플에 대한 자세한 내용은 [인증 라이브러리](reference-v2-libraries.md)를 참조하세요.

### <a name="validating-the-signature"></a>서명 유효성 검사

JWT에는 `.` 문자로 구분된 세 개의 세그먼트가 포함되어 있습니다. 첫 번째 세그먼트는 **헤더**, 두 번째 세그먼트는 **본문**, 세 번째 세그먼트는 **서명** 이라고 합니다. 서명 세그먼트는 앱이 신뢰할 수 있도록 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다.

Azure AD에서 발급된 토큰은 RS256 등의 업계 표준 비대칭 암호화 알고리즘을 사용하여 서명됩니다. JWT의 헤더에는 토큰 서명에 사용된 키 및 암호화 방법에 대한 정보가 들어 있습니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 클레임은 토큰 서명에 사용된 알고리즘을 나타내고 `kid` 클레임은 토큰 유효성 검사에 사용된 특정 공개 키를 나타냅니다.

특정 시점에 Azure AD는퍼블릭-프라이빗 키 쌍의 특정 집합 중 하나를 사용하여 id_token에 서명할 수 있습니다. Azure AD는 주기적으로 가능한 키 집합을 순환하므로 이러한 키 변경을 자동으로 처리하도록 앱을 작성해야 합니다. Azure AD에서 사용된 공개 키에 대한 업데이트를 확인하는 적절한 빈도는 24시간마다입니다.

다음 위치에 있는 [OpenID Connect 메타데이터 문서](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)를 사용하여 서명 유효성 검사에 필요한 서명 키 데이터를 얻을 수 있습니다.

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 브라우저에서 이 [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)을 사용해 보세요!

이 메타데이터 문서는 다음과 같은 특징이 있습니다.

* OpenID Connect 인증을 수행하는 데 필요한 다양한 엔드포인트의 위치 등 여러 유용한 정보를 포함하는 JSON 개체입니다.
* 토큰 서명에 사용된 프라이빗 키에 해당하는 퍼블릭 키 세트의 위치를 제공하는 `jwks_uri`를 포함합니다. `jwks_uri`에 있는 JWK(JSON 웹 키)는 해당 특정 시점에 사용 중인 공개 키 정보를 모두 포함합니다. JWK 형식은 [RFC 7517](https://tools.ietf.org/html/rfc7517)에서 설명합니다. 앱은 JWT 헤더에 `kid` 클레임을 사용하여 특정 토큰 서명에 사용된 프라이빗 키에 해당하는 퍼블릭 키를 이 문서에서 선택할 수 있습니다. 그런 다음, 올바른 공개 키와 표시된 알고리즘을 사용하여 서명 유효성 검사를 수행할 수 있습니다.

> [!NOTE]
> `kid` 클레임을 사용하여 토큰의 유효성을 검사하는 것이 좋습니다. v1.0 토큰에는 `x5t` 및 `kid` 클레임이 있지만 v2.0 토큰에는 `kid` 클레임만 포함됩니다.

서명 유효성 검사는 이 문서의 범위를 벗어납니다. 필요한 경우 이 작업에 도움이 되는 다양한 오픈 소스 라이브러리가 있습니다.  그러나 Microsoft ID 플랫폼에는 표준 사용자 지정 서명 키에 대한 하나의 토큰 서명 확장이 있습니다.

[claims-mapping](active-directory-claims-mapping.md) 기능을 사용한 결과 앱에 사용자 지정 서명 키가 적용된 경우, 앱 ID를 포함하는 `appid` 쿼리 매개 변수를 추가해야 유효성 검사에 사용해야 하는 앱의 서명 키 정보를 가리키는 `jwks_uri`를 얻을 수 있습니다. 예를 들어 `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e`는 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`의 `jwks_uri`를 포함합니다.

### <a name="claims-based-authorization"></a>클레임 기반 권한 부여

애플리케이션의 비즈니스 논리에 따라 이 단계가 결정되며 몇 가지 일반적인 권한 부여 방법이 아래에 나와 있습니다.

* `aud` 클레임을 사용하여 사용자가 애플리케이션을 호출하려고 했는지 확인합니다.  `aud` 클레임에 없는 리소스 식별자는 거부합니다.
* `scp` 클레임을 사용하여 사용자가 API를 호출할 수 있는 권한을 호출 앱에 부여했는지 확인합니다.
* `roles` 및 `wids` 클레임을 사용하여 사용자 자신에게 API를 호출할 수 있는 권한이 있는지 확인합니다.  예를 들어 관리자는 API에 대한 쓰기 권한이 있지만 일반 사용자는 쓰기 권한이 없을 수 있습니다.
* 호출하는 클라이언트가 `appid` 클레임을 사용하여 API를 호출할 수 있는지 확인합니다.
* `tid`가 API를 호출할 수 있는 테넌트와 일치하는지 확인합니다.
* 사용자가 MFA를 수행했는지 확인하려면 `amr` 클레임을 사용합니다. [조건부 액세스](../conditional-access/overview.md)를 사용하여 강제 적용되어야 합니다.
* 액세스 토큰의 `roles` 또는 `groups` 클레임을 요청한 경우 사용자가 이 작업을 수행하도록 허용된 그룹에 있는지 확인합니다.
  * 암시적 흐름을 사용하여 검색한 토큰의 경우 종종 너무 커서 토큰에 맞지 않으므로 이 데이터에 대해 [Microsoft Graph](https://developer.microsoft.com/graph/)를 쿼리해야 할 수 있습니다.

## <a name="user-and-application-tokens"></a>사용자 및 애플리케이션 토큰

애플리케이션은 사용자에 대한 토큰(일반적으로 설명하는 흐름)을 받거나 애플리케이션에서 직접([클라이언트 자격 증명 흐름](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 통해) 받을 수 있습니다. 이러한 앱 전용 토큰은 이 호출이 애플리케이션에서 발생하며 사용자가 이를 지원하는 것이 아님을 나타냅니다. 이러한 토큰은 거의 동일하게 처리됩니다.

* `roles`를 사용하여 토큰의 주체에 부여된 권한을 확인합니다.
* `oid` 또는 `sub`를 사용하여 호출하는 서비스 주체가 필요한지 확인합니다.

앱에서 앱 전용 액세스 토큰과 사용자에 대한 액세스 토큰을 구분해야 하는 경우 `idtyp` [선택적 클레임](active-directory-optional-claims.md)을 사용합니다.  `accessToken` 필드에 `idtyp` 클레임을 추가하고 `app` 값을 확인하여 앱 전용 액세스 토큰을 검색할 수 있습니다.  사용자의 ID 토큰 및 액세스 토큰에는 `idtyp` 클레임이 포함되어 있지 않습니다.

## <a name="token-revocation"></a>토큰 해지

여러 이유로 언제든지 새로 고침 토큰이 무효화되거나 취소될 수 있습니다. 이러한 이유는 시간 제한 및 해지 등 두 가지 주요 범주로 나뉩니다.

### <a name="token-timeouts"></a>토큰 제한 시간

[토큰 수명 구성](active-directory-configurable-token-lifetimes.md)을 사용하여 새로고침 토큰의 수명을 변경할 수 있습니다.  일부 토큰이 사용되지 않고(예를 들어 사용자가 3개월 동안 앱을 열지 않음) 그 결과 만료되는 경우는 일상적으로 발생합니다.  앱에서는 로그인 서버가 새로 고침 토큰의 기간 탓에 토큰을 거부하는 시나리오가 발생합니다.

* MaxInactiveTime: MaxInactiveTime에 의해 결정된 시간 내에 새로 고침 토큰을 사용하지 않은 경우 새로 고침 토큰은 더 이상 유효하지 않습니다.
* MaxSessionAge: MaxAgeSessionMultiFactor 또는 MaxAgeSessionSingleFactor가 (해지될 때까지) 기본값 이외의 값으로 설정된 경우 MaxAgeSession*에 설정된 시간이 경과된 후에 다시 인증해야 합니다.
* 예제:
  * 테넌트에 5일의 MaxInactiveTime이 설정되고 사용자가 1주일 동안 휴가를 갔습니다. 따라서 Azure AD에는 7일 동안 사용자의 새 토큰 요청이 없습니다. 다음에 사용자가 새 토큰을 요청하는 경우 해당 새로 고침 토큰이 해지되었음을 발견하고 자격 증명을 다시 입력해야 합니다.
  * 중요한 애플리케이션에는 1일의 MaxAgeSessionSingleFactor가 지정됩니다. 사용자가 월요일 및 화요일(25시간이 경과된 후)에 로그인하는 경우 다시 인증해야 합니다.

### <a name="revocation"></a>해지

자격 증명의 변경이나 사용 또는 관리 작업으로 인해 서버에서 새로 고침 토큰을 해지할 수 있습니다.  새로 고침 토큰은 기밀 클라이언트에 발급된 토큰(가장 오른쪽 열)과 퍼블릭 클라이언트에 발급된 토큰(다른 모든 열)의 두 클래스에 속합니다.

| 변경 | 암호 기반 쿠키 | 암호 기반 토큰 | 비암호 기반 쿠키 | 비암호 기반 토큰 | 기밀 클라이언트 토큰 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 암호 만료 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자에 의해 암호가 변경됨 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 SSPR 수행 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 관리자가 암호 재설정 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 [PowerShell을 통해](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) 새로 고침 토큰 해지 | 해지됨 | 해지됨 | 해지됨 | 해지됨 | 해지됨 |
| 관리자가 [PowerShell을 통해](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 사용자에 대한 모든 새로 고침 토큰 해지 | 해지됨 | 해지됨 |해지됨 | 해지됨 | 해지됨 |
| 웹의 SSO(Single Sign-Out)([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0](v2-protocols-oidc.md#single-sign-out) ) | 해지됨 | 활성 상태 | 해지됨 | 활성 상태 | 활성 상태 |

#### <a name="non-password-based"></a>비 암호 기반

*비 암호 기반* 로그인은 사용자가 가져오도록 암호를 입력하지 않은 위치입니다. 다음은 비 암호 기반 로그인의 예입니다.

- Windows Hello에서 얼굴 사용
- FIDO2 키
- sms
- 음성
- PIN

기본 새로 고침 토큰에 대한 자세한 내용은 [기본 새로 고침 토큰](../devices/concept-primary-refresh-token.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure AD에서 `id_tokens`](id-tokens.md)에 대해 알아봅니다.
* 권한 및 동의에 대해 알아봅니다([v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
