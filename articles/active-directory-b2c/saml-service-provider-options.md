---
title: SAML 서비스 공급자 옵션 구성
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C SAML 서비스 공급자 옵션을 구성하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51672c7757e5c747bf2b243e32506159a468f2d5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222156"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에서 SAML 애플리케이션을 등록하는 옵션

이 문서에서는 SAML(Security Assertion Markup Language) 애플리케이션과 Azure AD B2C(Azure Active Directory B2C)를 연결할 때 사용할 수 있는 구성 옵션에 대해 설명합니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="specify-a-saml-response-signature"></a>SAML 응답 서명 지정

SAML 메시지에 서명하는 데 사용할 인증서를 지정할 수 있습니다. 메시지는 `<samlp:Response>` 애플리케이션에 전송된 SAML 응답에 포함된 요소입니다.

정책 키가 아직 없는 경우 [새로 만듭니다](saml-service-provider.md#create-a-policy-key). 그런 다음, SAML 토큰 발급자 기술 프로필에서`SamlMessageSigning` 메타데이터 항목을 구성합니다. `StorageReferenceId`는 정책 키 이름을 참조해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

### <a name="signature-algorithm"></a>서명 알고리즘

SAML 어설션에 서명하는 데 사용되는 서명 알고리즘을 구성할 수 있습니다. 가능한 값은 `Sha256`, `Sha384`, `Sha512` 및 `Sha1`입니다. 기술 프로필 및 애플리케이션에서 동일한 서명 알고리즘을 사용하는지 확인합니다. 인증서가 지원하는 알고리즘만 사용하세요.

신뢰 당사자 `Metadata` 요소 내에서 `XmlSignatureAlgorithm` 메타데이터 키를 사용하여 서명 알고리즘을 구성합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="check-the-saml-assertion-signature"></a>SAML 어설션 서명 확인

애플리케이션에서 SAML 어설션 섹션에 서명해야 하는 경우 SAML 서비스 공급자가 `WantAssertionsSigned`를 `true`로 설정했는지 확인합니다. `false`로 설정되어 있거나 존재하지 않는 경우 어설션 섹션은 서명되지 않습니다. 

다음 예는 `WantAssertionsSigned`가 `true`로 설정된 SAML 서비스 공급자에 대한 메타데이터를 보여 줍니다.

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="signature-certificate"></a>서명 인증서

정책은 SAML 응답의 SAML 어설션 섹션에 서명하는 데 사용할 인증서를 지정해야 합니다. 정책 키가 아직 없는 경우 [새로 만듭니다](saml-service-provider.md#create-a-policy-key). 그런 다음, SAML 토큰 발급자 기술 프로필에서`SamlAssertionSigning` 메타데이터 항목을 구성합니다. `StorageReferenceId`는 정책 키 이름을 참조해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="enable-encryption-in-saml-assertions"></a>SAML 어설션에서 암호화 사용

애플리케이션에서 SAML 어설션이 암호화된 형식이어야 하는 경우 Azure AD B2C 정책에 암호화가 설정되어 있는지 확인해야 합니다.

Azure AD B2C는 서비스 공급자의 공개 키 인증서를 사용하여 SAML 어설션을 암호화합니다. 다음 예제와 같이 `KeyDescriptor` `use` 값이 `Encryption`으로 설정되어 있고 SAML 애플리케이션의 메타데이터 엔드포인트에 공개 키가 있어야 합니다.

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Azure AD B2C에서 암호화된 어설션을 보내도록 하려면 [신뢰 당사자 기술 프로필](relyingparty.md#technicalprofile)에서 `WantsEncryptedAssertion` 메타데이터 항목을 `true`로 설정합니다. 또한 SAML 어설션을 암호화하는 데 사용되는 알고리즘을 구성할 수도 있습니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>암호화 방법

SAML 어설션 데이터를 암호화하는 데 사용되는 암호화 방법을 구성하려면 신뢰 당사자 내에서 `DataEncryptionMethod` 메타데이터 키를 설정합니다. 가능한 값은 `Aes256`(기본값), `Aes192`, `Sha512` 또는 `Aes128`입니다. 메타데이터는 SAML 응답의 `<EncryptedData>` 요소 값을 제어합니다.

SAML 어설션 데이터를 암호화하는 데 사용된 키 복사를 암호화하는 데 사용되는 암호화 방법을 구성하려면 신뢰 당사자 내에서 `KeyEncryptionMethod` 메타데이터 키를 설정합니다. 가능한 값은 다음과 같습니다.

- `Rsa15`(기본값): RSA PKCS(공개 키 암호화 표준) 버전 1.5 알고리즘입니다.
- `RsaOaep`: RSA OAEP(최적 비대칭 암호화 패딩) 암호화 알고리즘입니다.  

메타데이터는 SAML 응답의 `<EncryptedKey>` 요소 값을 제어합니다.

다음 예제에서는 SAML 어설션의 `EncryptedAssertion` 섹션을 보여 줍니다. 암호화된 데이터 메서드는 `Aes128`이고 암호화된 키 메서드는 `Rsa15`입니다.

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

암호화된 어설션의 형식을 변경할 수 있습니다. 암호화 형식을 구성하려면 신뢰 당사자 내에서 `UseDetachedKeys` 메타데이터 키를 설정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. 값이 `true`로 설정되면 분리된 키가 암호화된 어설션을 `EncryptedData`가 아닌 `EncryptedAssertion`의 자식으로 추가합니다.

암호화 방법 및 형식을 구성하고, [신뢰 당사자 기술 프로필](relyingparty.md#technicalprofile) 내에서 메타데이터 키를 사용합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="configure-idp-initiated-flow"></a>IdP 시작 흐름 구성

애플리케이션이 IdP(ID 공급자)에게 SAML AuthN 요청을 먼저 보내지 않고 SAML 어설션을 받아야 하는 경우, IdP가 시작한 흐름에 대한 Azure AD B2C를 구성해야 합니다.

IdP 시작 흐름에서 ID 공급자(Azure AD B2C)가 로그인 프로세스를 시작합니다. 자격 증명 공급자가 요청하지 않은 SAML 응답을 서비스 공급자(신뢰 당사자 애플리케이션)에 보냅니다.

Azure AD B2C와 페더레이션된 외부 ID 공급자(예: [Active Directory Federation Services](identity-provider-adfs.md) 또는 [Salesforce](identity-provider-salesforce-saml.md))가 흐름을 시작하는 시나리오는 현재 지원되지 않습니다. IdP 시작 흐름은 Azure AD B2C의 로컬 계정 인증에 대해서만 지원됩니다.

IdP 시작 흐름을 사용하도록 설정하려면 [신뢰 당사자 기술 프로필](relyingparty.md#technicalprofile)에서 `IdpInitiatedProfileEnabled` 메타데이터 항목을 `true`로 설정합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

IdP가 시작한 흐름을 통해 사용자를 로그인시키거나 등록하려면 다음 URL을 사용합니다.

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

다음 값을 바꿉니다.

* `<tenant-name>`를 해당하는 테넌트 이름으로 바꿉니다.
* `<policy-name>`을 SAML 신뢰 당사자 정책의 이름으로 바꿉니다.
* `app-identifier-uri`를 메타데이터 파일의 `identifierUris` 값(예: `https://contoso.onmicrosoft.com/app-name`)으로 바꿉니다.

### <a name="sample-policy"></a>샘플 정책

SAML 테스트 앱으로 테스트하기 위해 전체 샘플 정책을 사용할 수 있습니다.

1. [SAML-SP 시작 로그인 샘플 정책](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)을 다운로드합니다.
1. 테넌트 이름과 일치하도록 `TenantId`를 업데이트합니다. 이 문서에서는 *contoso.b2clogin.com* 의 예를 사용합니다.
1. 정책 이름 *B2C_1A_signup_signin_saml* 을 유지합니다.

## <a name="configure-the-saml-response-lifetime"></a>SAML 응답 수명 구성

SAML 응답이 유효한 상태로 유지되는 기간을 구성할 수 있습니다. SAML 토큰 발급자 기술 프로필 내에서 `TokenLifeTimeInSeconds` 메타데이터 항목을 사용하여 수명을 설정합니다. 이 값은 토큰 발급 시간에 계산된 `NotBefore` 타임스탬프로부터 경과할 수 있는 시간(초)입니다. 기본값은 300초(5분)입니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-the-time-skew-of-a-saml-response"></a>SAML 응답의 시간 기울이기 구성

SAML 응답 `NotBefore` 타임스탬프에 적용되는 시간차를 구성할 수 있습니다. 이 구성을 사용하면 두 플랫폼 간의 시간이 동기화되지 않을 경우 이 시간차 내에 있으면 SAML 어설션이 여전히 유효한 것으로 간주됩니다.

SAML 토큰 발급자 기술 프로필 내에서 `TokenNotBeforeSkewInSeconds` 메타데이터 항목을 사용하여 시간차를 설정합니다. 오차 값은 초 단위로 지정되며 기본값은 0입니다. 최댓값은 3600(1시간)입니다.

예를 들어 `TokenNotBeforeSkewInSeconds`가 `120`초로 설정된 경우:

- 토큰이 13:05:10 UTC에 발급되었습니다.
- 토큰이 13:03:10 UTC부터 유효합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-the-date-and-time"></a>날짜 및 시간에서 밀리초 제거

SAML 응답 내의 날짜 및 시간 값에서 밀리초를 제거할지 여부를 지정할 수 있습니다. (이러한 값에는 `IssueInstant`, `NotBefore`, `NotOnOrAfter` 및 `AuthnInstant`가 포함됩니다.) 밀리초를 제거하려면 신뢰 당사자 내에서 `RemoveMillisecondsFromDateTime` 메타데이터 키를 설정합니다. 가능한 값은 `false`(기본값) 또는 `true`입니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="use-an-issuer-id-to-override-an-issuer-uri"></a>발급자 ID를 사용하여 발급자 URI 재정의

다른 `entityID` 값에 종속된 여러 SAML 애플리케이션이 있는 경우 신뢰 당사자 파일의 `IssuerUri` 값을 재정의할 수 있습니다. 발급자 URI를 재정의하려면 기본 파일에서 `Saml2AssertionIssuer` ID를 사용하여 기술 프로필을 복사하고 `IssuerUri` 값을 재정의합니다.

> [!TIP]
> 기본에서 `<ClaimsProviders>` 섹션을 복사하고 클레임 공급자(`<DisplayName>Token Issuer</DisplayName>`, `<TechnicalProfile Id="Saml2AssertionIssuer">` 및 `<DisplayName>Token Issuer</DisplayName>`) 내에서 이러한 요소를 유지합니다.
 
예제:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="manage-a-session"></a>세션 관리

`UseTechnicalProfileForSessionManagement` 요소와 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider)를 사용하여 Azure AD B2C와 SAML 신뢰 당사자 애플리케이션 간의 세션을 관리할 수 있습니다.

## <a name="force-users-to-reauthenticate"></a>사용자가 다시 인증하도록 강제 

사용자가 다시 인증하도록 강제하기 위해 애플리케이션은 `ForceAuthn` SAML 인증 요청에 특성을 포함할 수 있습니다. `ForceAuthn` 특성은 부울 값입니다. `true`로 설정되면 Azure AD B2C에서 사용자의 세션이 무효화되고 사용자가 다시 인증해야 합니다. 

다음 SAML 인증 요청에서는 `ForceAuthn` 특성을 `true`로 설정하는 방법을 보여 줍니다. 

```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>Azure AD B2C IdP SAML 메타데이터에 서명

애플리케이션에서 요구하는 경우 SAML ID 공급자에 대한 메타데이터 문서에 서명하도록 Azure AD B2C에 지시할 수 있습니다. 정책 키가 아직 없는 경우 [새로 만듭니다](saml-service-provider.md#create-a-policy-key). 그런 다음, SAML 토큰 발급자 기술 프로필에서`MetadataSigning` 메타데이터 항목을 구성합니다. `StorageReferenceId`는 정책 키 이름을 참조해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="debug-the-saml-protocol"></a>SAML 프로토콜 디버그

서비스 공급자와의 통합을 구성하고 디버그하는 데 도움이 되도록 SAML 프로토콜에 대한 브라우저 확장을 사용할 수 있습니다. 브라우저 확장에는 [Chrome용 SAML DevTools 확장](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio), [Firefox용 SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) 및 [Edge 또는 Internet Explorer용 개발자 도구](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)가 있습니다.

이러한 도구를 사용하여 애플리케이션과 Azure AD B2C 간의 통합을 확인할 수 있습니다. 예를 들면 다음과 같습니다.

* SAML 요청에 서명이 포함되어 있는지 여부를 확인하고 권한 부여 요청에 서명하는 데 사용되는 알고리즘을 결정합니다.
* Azure AD B2C가 오류 메시지를 반환하는지 확인합니다.
* 어설션 섹션이 암호화되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [OASIS 웹 사이트](https://www.oasis-open.org/)에서 SAML 프로토콜에 대한 자세한 정보를 확인합니다.
- [Azure AD B2C GitHub 커뮤니티 리포지토리](https://github.com/azure-ad-b2c/saml-sp-tester)에서 SAML 테스트 웹앱을 가져옵니다.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
