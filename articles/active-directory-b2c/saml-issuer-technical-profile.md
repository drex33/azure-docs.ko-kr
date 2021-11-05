---
title: 사용자 지정 정책에서 SAML 발급자의 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 SAML(Security Assertion Markup Language) 토큰 발급자에 대한 기술 프로필을 정의합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 03e67143c0eb0e4a6d57d928d65becc519271f4b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131044402"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 SAML 토큰 발급자의 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)는 각 인증 흐름을 처리할 때 여러 형식의 보안 토큰을 내보냅니다. SAML 토큰 발급자의 기술 프로필은 다시 신뢰 당사자 애플리케이션(서비스 공급자)으로 반환되는 SAML 토큰을 내보냅니다. 일반적으로 이 기술 프로필은 사용자 경험의 마지막 오케스트레이션 단계입니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `SAML2`로 설정해야 합니다. **OutputTokenFormat** 요소를 `SAML2`로 설정합니다.

다음 예제는 `Saml2AssertionIssuer`의 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>입력, 출력 및 유지 클레임

**InputClaims**, **OutputClaims** 및 **PersistClaims** 요소가 비어 있거나 없습니다. **InutputClaimsTransformations** 및 **OutputClaimsTransformations** 요소도 없습니다.

## <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| IssuerUri | 예 | SAML 응답에 표시되는 발급자 이름입니다. 이 값은 신뢰 당사자 애플리케이션에 구성된 이름과 같아야 합니다. |
| XmlSignatureAlgorithm | 예 | Azure AD B2C에서 SAML 어설션에 서명하는 데 사용하는 메서드입니다. 가능한 값은 `Sha256`, `Sha384`, `Sha512` 또는 `Sha1`입니다. 양쪽의 서명 알고리즘을 같은 값으로 구성해야 합니다. 인증서가 지원하는 알고리즘만 사용하세요. SAML 응답을 구성하려면 [SAML 애플리케이션을 등록하는 옵션](saml-service-provider.md)을 참조하세요.|
|TokenNotBeforeSkewInSeconds| 아니요| 유효 기간의 시작을 표시하는 타임스탬프에 대한 오차(정수)를 지정합니다. 이 수가 클수록 신뢰 당사자에 대해 클레임이 발급된 시간과 관련하여 유효 기간이 시작되는 시간 이후의 시간을 반환합니다. 예를 들어 TokenNotBeforeSkewInSeconds가 60초로 설정된 경우 토큰이 13:05:10 UTC에 발급되면 토큰은 13:04:10 UTC부터 유효합니다. 기본값은 0입니다. 최댓값은 3600(1시간)입니다. |
|TokenLifeTimeInSeconds| 아니요| SAML 어설션의 수명을 지정합니다. 이 값은 위에서 참조된 NotBefore 값의 초 단위입니다. 기본값은 300 초(5분)입니다. |


## <a name="cryptographic-keys"></a>암호화 키

CryptographicKeys 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| MetadataSigning | 예 | SAML 메타데이터에 서명을 하는 데 사용할 X509 인증서(RSA 키 집합)입니다. Azure AD B2C는 이 키를 사용하여 메타데이터에 서명을 합니다. |
| SamlMessageSigning| 예| SAML 메시지에 서명을 하는 데 사용할 X509 인증서(RSA 키 세트)를 지정합니다. Azure AD B2C는 이 키를 사용하여 신뢰 당사자에게 보낼 응답 `<samlp:Response>`에 서명합니다.|

## <a name="session-management"></a>세션 관리

신뢰 당사자 애플리케이션 간에 Azure AD B2C SAML 세션을 구성하기 위해 `UseTechnicalProfileForSessionManagement` 요소의 특성은 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO 세션을 참조합니다.

## <a name="next-steps"></a>다음 단계

SAML 발급자 기술 프로필을 사용하는 예제는 다음 문서를 참조하세요.

- [Azure AD B2C에 SAML 애플리케이션 등록](saml-service-provider.md)

