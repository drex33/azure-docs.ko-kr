---
title: 사용자 지정 정책에서 OAuth2 사용자 지정 오류 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 OAuth2 사용자 지정 오류 기술 프로필을 정의합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 1126ce99c660860f7ee0f4abc9afcb9ed5cd1afc
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036249"
---
# <a name="define-an-oauth2-custom-error-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 OAuth2 사용자 지정 오류 기술 프로필 정의

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)를 사용하여 OAuth2 사용자 지정 오류를 처리하는 방법을 설명합니다. 정책 내에서 문제가 발생하는 경우 이 기술 프로필을 사용합니다. 기술 프로필은 OAuth2 또는 OpenId Connect 신뢰 당사자 애플리케이션에 오류를 반환합니다.

사용자 지정 OAuth2 오류 메시지를 처리하려면

1. OAuth2 오류 기술 프로필을 정의합니다.
1. 오류 코드 및 오류 메시지 클레임을 설정합니다.
1. 사용자 경험에서 OAuth2 오류 기술 프로필을 호출합니다.

## <a name="oauth2-error"></a>OAuth2 오류

오류는 다음 데이터와 함께 반환됩니다.

- **error** - `access_denied`
- **error_description** - `AAD_Custom_<errorCode>: <errorMessage>` 규칙을 사용하는 오류 메시지입니다.
- **Correlation ID** -Azure AD B2C 상관 관계 ID입니다.
- **Timestamp** - 오류의 타임스탬프입니다.

다음 예제에서는 https://jwt.ms 앱에 반환되는 사용자 지정 오류 메시지를 보여 줍니다.

```http
https://jwt.ms/#error=access_denied&error_description=AAD_Custom_1234%3a+My+custom+error+message%0d%0aCorrelation+ID%3a+233bf9bd-747a-4800-9062-6236f3f69a47%0d%0aTimestamp%3a+2021-03-25+14%3a01%3a23Z%0d%0a
```
  
## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `None`로 설정해야 합니다. **OutputTokenFormat** 요소를 `OAuth2Error`로 설정합니다.

다음 예제는 `ReturnOAuth2Error`의 기술 프로필을 보여 줍니다.

```xml
<!--
 <ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ReturnOAuth2Error">
        <DisplayName>Return OAuth2 error</DisplayName>
        <Protocol Name="None" />
        <OutputTokenFormat>OAuth2Error</OutputTokenFormat>
        <CryptographicKeys>
          <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
        </CryptographicKeys>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="errorCode" />
          <InputClaim ClaimTypeReferenceId="errorMessage" />
        </InputClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 OAuth2 오류를 반환하는 데 필요한 클레임 목록을 포함합니다. 

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| errorCode | 예 | 오류 코드입니다. | 
| errorMessage | 예 | 오류 메시지입니다. |

## <a name="cryptographic-keys"></a>암호화 키

CryptographicKeys 요소는 다음 키를 포함합니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| issuer_secret | 예  | X509 인증서(RSA 키 집합). [사용자 지정 정책 시작](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)에서 구성하는 `B2C_1A_TokenSigningKeyContainer` 키를 사용합니다.|
|

## <a name="invoke-the-technical-profile"></a>기술 프로필 호출

사용자 경험 또는 하위 경험에서 OAuth2 오류 기술 프로필을 호출할 수 있습니다. OAuth2 오류 기술 프로필에 대한 참조를 사용하여 [오케스트레이션 단계](userjourneys.md#orchestrationsteps) 유형을 `SendClaims`로 설정합니다.

사용자 경험 또는 하위 경험에 이미 다른 `SendClaims` 오케스트레이션 단계가 있는 경우 `DefaultCpimIssuerTechnicalProfileReferenceId` 특성을 토큰 발급자 기술 프로필로 설정합니다.

다음 예제에서는

-  사용자 경험 `SignUpOrSignIn-Custom`은 `DefaultCpimIssuerTechnicalProfileReferenceId`를 토큰 발급자 기술 프로필 `JwtIssuer`로 설정합니다. 
- 8번째 오케스트레이션 단계는 `errorCode`가 있는지 여부를 확인합니다. 이 코드가 있으면 `ReturnOAuth2Error` 기술 프로필을 호출하여 오류를 반환합니다.
- `errorCode`가 없으면 9번째 오케스트레이션 단계가 토큰을 발급합니다.

```xml
<UserJourney Id="SignUpOrSignIn-Custom" DefaultCpimIssuerTechnicalProfileReferenceId="JwtIssuer">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="ReturnOAuth2Error">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>errorCode</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
    </OrchestrationStep>

    <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />

  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="next-steps"></a>다음 단계

[UserJourneys](userjourneys.md) 에 대한 자세한 정보