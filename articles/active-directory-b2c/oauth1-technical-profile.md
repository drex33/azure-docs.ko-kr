---
title: 사용자 지정 정책에서 OAuth1 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 OAuth 1.0 기술 프로필을 정의하세요.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 3b8fa42195a7752ce3ff1708a9a414d0284c63ac
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131028142"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 OAuth1 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C는 [OAuth 1.0 프로토콜](https://tools.ietf.org/html/rfc5849) ID 에 공급자에 대한 지원을 제공합니다. 이 문서에서는 이 표준 프로토콜을 지원하는 클레임 공급자와 상호 작용하기 위한 기술 프로필에 대해 구체적으로 설명합니다. OAuth1 기술 프로필을 사용하면 Twitter와 같은 OAuth1 기반 ID 공급자와 페더레이션할 수 있습니다. ID 공급자와 페더레이션하면 사용자는 기존의 소셜 또는 엔터프라이즈 ID로 로그인할 수 있습니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `OAuth1`로 설정해야 합니다. 예를 들어 **Twitter-OAUTH1** 기술 프로필의 프로토콜은 `OAuth1`입니다.

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>입력 클레임

**InputClaims** 및 **InputClaimsTransformations** 요소가 비어 있거나 없습니다.

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 OAuth1 ID 공급자가 반환한 클레임 목록을 포함합니다. 정책에 정의된 클레임 이름을 ID 공급자에 정의된 이름에 매핑해야 할 수도 있습니다. **DefaultValue** 특성만 설정하면, ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

다음 예제는 Twitter ID 공급자가 반환한 클레임을 보여 줍니다.

- **issueUserId** 클레임에 매핑된 **user_id** 클레임입니다.
- **displayName** 클레임에 매핑된 **screen_name** 클레임입니다.
- 이름 매핑이 없는 **email** 클레임입니다.

기술 프로필은 ID 공급자가 반환하지 않은 클레임도 반환합니다.

- ID 공급자의 이름을 포함하는 **identityProvider** 클레임입니다.
- 기본값이 `socialIdpAuthentication`인 **authenticationSource** 클레임입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| client_id | 예 | ID 공급자의 애플리케이션 식별자입니다. |
| ProviderName | 예 | ID 공급자의 이름입니다. |
| request_token_endpoint | 예 | RFC 5849에 따른 요청 토큰 엔드포인트의 URL입니다. |
| authorization_endpoint | 예 | RFC 5849에 따른 권한 부여 엔드포인트의 URL입니다. |
| access_token_endpoint | 예 | RFC 5849에 따른 토큰 엔드포인트의 URL입니다. |
| ClaimsEndpoint | 예 | 사용자 정보 엔드포인트의 URL입니다. |
| ClaimsResponseFormat | 예 | 클레임 응답 형식입니다.|

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| client_secret | 예 | ID 공급자 애플리케이션의 클라이언트 암호입니다.   |

## <a name="redirect-uri"></a>리디렉션 URI

ID 공급자의 리디렉션 URI를 구성할 때 `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/{policy-id}/oauth1/authresp`를 입력합니다. `{tenant-name}`을 테넌트 이름(예: contosob2c)으로 변경하고 `{policy-id}`을 정책 식별자(예: b2c_1a_policy)로 변경해야 합니다. 리디렉션 URI는 모두 소문자여야 합니다. ID 공급자 로그인을 사용하는 모든 정책에 대해 리디렉션 URL을 추가하세요.

예:

- [사용자 지정 정책을 사용하여 OAuth1 ID 공급자로 Twitter 추가](identity-provider-twitter.md)
