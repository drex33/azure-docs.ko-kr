---
title: 사용자 지정 정책에서 SAML 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 SAML 기술 프로필을 정의하는 방법을 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: ef68bf0b3f56adde4fa18a35912e109701e50cb7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131012779"
---
# <a name="define-a-saml-identity-provider-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 SAML ID 공급자 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)에서는 SAML 2.0 ID 공급자를 지원합니다. 이 문서에서는 이 표준 프로토콜을 지원하는 클레임 공급자와 상호 작용하기 위한 기술 프로필에 대해 구체적으로 설명합니다. SAML 기술 프로필을 사용하여 [ADFS](./identity-provider-adfs.md) 및 [Salesforce](identity-provider-salesforce-saml.md)와 같은 SAML 기반 ID 공급자를 페더레이션할 수 있습니다. 이 페더레이션을 사용하여 기존 소셜 또는 엔터프라이즈 ID로 로그인할 수 있습니다.

## <a name="metadata-exchange"></a>메타데이터 교환

메타데이터는 서비스 공급자 또는 ID 공급자와 같은 SAML 당사자의 구성을 표시하기 위해 SAML 프로토콜에서 사용되는 정보입니다. 메타데이터는 로그인/로그아웃, 인증서, 로그인 방법 등과 같은 서비스의 위치를 정의합니다. ID 공급자는 메타데이터를 사용하여 Azure AD B2C와 통신하는 방법을 확인합니다. 메타데이터는 XML 형식으로 구성되며, 다른 당사자가 메타데이터 무결성의 유효성을 검사할 수 있도록 디지털 서명으로 서명될 수 있습니다. Azure AD B2C는 SAML ID 공급자와 페더레이션될 때 SAML 요청을 시작하며 SAML 응답을 기다리는 서비스 공급자 역할을 합니다. 그리고 경우에 따라서는 원치 않는 SAML 인증(ID 공급자가 시작하는 인증이라고도 함)을 수락하기도 합니다.

양 당사자에서 “정적 메타데이터” 또는 “동적 메타데이터”로 메타데이터를 구성할 수 있습니다. 정적 모드에서는 전체 메타데이터를 한 당사자에서 복사하여 다른 당사자에서 설정합니다. 동적 모드에서는 상대 당사자가 동적으로 구성을 읽는 동안 메타데이터에 대한 URL을 설정합니다. 두 모드의 원칙은 동일합니다. 즉, ID 공급자에서 Azure AD B2C 기술 프로필의 메타데이터를 설정하고 Azure AD B2C에서 ID 공급자의 메타데이터를 설정합니다.

각 SAML ID 공급자에서는 서로 단계를 수행하여 서비스 공급자(여기서는 Azure AD B2C)를 표시 및 설정하고 ID 공급자에서 Azure AD B2C 메타데이터를 설정합니다. 이 단계를 수행하는 방법의 지침은 사용 중인 ID 공급자의 설명서를 참조하세요.

다음 예제에서는 Azure AD B2C 기술 프로필의 SAML 메타데이터에 대한 URL 주소를 보여 줍니다.

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

다음 값을 바꿉니다.

- fabrikam.b2clogin.com과 같은 테넌트 이름이 있는 **your-tenant-name**.
- **your-policy** 를 정책 이름으로. SAML 공급자 기술 프로필을 구성한 정책 또는 해당 정책에서 상속하는 정책을 사용합니다.
- SAML ID 공급자 기술 프로필 이름이 있는 **your-technical-profile**.

## <a name="digital-signing-certificates-exchange"></a>디지털 서명 인증서 교환

Azure AD B2C와 SAML ID 공급자 간의 트러스트를 구축하려면 유효한 X509 인증서에 프라이빗 키를 제공해야 합니다. 이렇게 하려면 프라이빗 키(.pfx 파일)가 포함된 인증서를 Azure AD B2C 정책 키 저장소에 업로드합니다. Azure AD B2C는 제공된 인증서를 사용하여 SAML 로그인 요청에 디지털 서명을 합니다.

인증서는 다음과 같은 방식으로 사용됩니다.

- Azure AD B2C가 인증서의 Azure AD B2C 프라이빗 키를 사용하여 SAML 요청을 생성하고 서명을 합니다. SAML 요청이 ID 공급자에게 전송되면 ID 공급자가 인증서의 Azure AD B2C 공개 키를 사용하여 요청 유효성을 검사합니다. 기술 프로필 메타데이터를 통해 Azure AD B2C 공용 인증서에 액세스할 수 있습니다. .cer 파일을 SAML ID 공급자에 수동으로 업로드할 수도 있습니다.
- ID 공급자가 인증서의 ID 공급자 프라이빗 키를 사용하여 Azure AD B2C로 전송된 데이터에 서명을 합니다. Azure AD B2C가 ID 공급자의 공용 인증서를 사용하여 데이터 유효성을 검사합니다. 각 ID 공급자의 설정 단계는 서로 다릅니다. 설정 방법의 지침은 사용 중인 ID 공급자의 설명서를 참조하세요. Azure AD B2C에서 정책은 ID 공급자 메타데이터를 사용하여 인증서 공개 키에 액세스해야 합니다.

대부분의 시나리오에서는 자체 서명된 인증서를 사용하면 됩니다. 프로덕션 환경에서는 인증 기관에서 발급한 X509 인증서를 사용하는 것이 좋습니다. 또한 이 문서 뒷부분에 설명되어 있는 것처럼 비프로덕션 환경에서는 양쪽에서 모두 SAML 서명을 사용하지 않도록 설정할 수 있습니다.

아래 다이어그램에는 메타데이터 및 인증서 교환 과정이 나와 있습니다.

![메타데이터 및 인증서 교환](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>디지털 암호화

SAML 응답 어설션을 암호화하기 위해 ID 공급자는 항상 Azure AD B2C 기술 프로필 내 암호화 인증서의 공개 키를 사용합니다. Azure AD B2C는 데이터 암호를 해독해야 할 때 암호화 인증서의 프라이빗 부분을 사용합니다.

SAML 응답 어설션을 암호화하려면 다음 단계를 수행합니다.

1. 프라이빗 키(.pfx 파일)가 포함된 유효한 X509 인증서를 Azure AD B2C 정책 키 저장소에 업로드합니다.
2. 식별자가 `SamlAssertionDecryption`인 **CryptographicKey** 요소를 기술 프로필 **CryptographicKeys** 컬렉션에 추가합니다. **StorageReferenceId** 를 1단계에서 만든 정책 키의 이름으로 설정합니다.
3. 기술 프로필 메타데이터 **WantsEncryptedAssertions** 를 `true`로 설정합니다.
4. 새 Azure AD B2C 기술 프로필 메타데이터로 ID 공급자를 업데이트합니다. 인증서의 공개 키를 포함하며 **use** 속성이 `encryption`으로 설정된 **KeyDescriptor** 가 표시되어야 합니다.

다음 예제에서는 암호화에 사용되는 SAML 메타데이터의 키 설명자 섹션을 보여줍니다.

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>프로토콜

Protocol 요소의 **Name** 특성은 `SAML2`로 설정해야 합니다.

## <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 SAML AuthN 요청의 **주체** 내에서 **NameId** 를 보내는 데 사용됩니다. 이를 수행하려면 아래 표시된 대로 `subject`로 설정된 **PartnerClaimType** 으로 입력 클레임을 추가합니다.

```xml
<InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="subject" />
</InputClaims>
```

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 `AttributeStatement` 섹션 아래에 SAML ID 공급자가 반환한 클레임 목록을 포함합니다. 정책에 정의된 클레임 이름을 ID 공급자에 정의된 이름에 매핑해야 할 수도 있습니다. `DefaultValue` 특성만 설정하면, ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다.

### <a name="subject-name-output-claim"></a>주체 이름 출력 클레임

**주체** 에서 SAML 어설션 **NameId** 를 정규화된 클레임으로 읽으려면 클레임 **PartnerClaimType** 을 `SPNameQualifier` 특성 값으로 설정합니다. `SPNameQualifier` 특성이 표시되지 않으면 클레임 **PartnerClaimType** 을 `NameQualifier` 특성 값으로 설정합니다. 


SAML 어설션: 

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

출력 클레임:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

`SPNameQualifier` 또는 `NameQualifier` 특성이 모두 SAML 어설션에 표시되지 않으면 클레임 **PartnerClaimType** 을 `assertionSubjectName`으로 설정합니다. **NameId** 가 어설션 XML의 첫 번째 값인지 확인합니다. 둘 이상의 어설션을 정의하면 Azure AD B2C가 마지막 어설션에서 주체 값을 선택합니다.

다음 예제는 SAML ID 공급자가 반환한 클레임을 보여 줍니다.

- **issuerUserId** 클레임은 **assertionSubjectName** 클레임에 매핑됩니다.
- **first_name** 클레임은 **givenName** 클레임에 매핑됩니다.
- **last** 클레임은 **surname** 클레임에 매핑됩니다.
- **displayName** 클레임은 **name** 클레임에 매핑됩니다.
- 이름 매핑이 없는 **email** 클레임입니다.

기술 프로필은 ID 공급자가 반환하지 않은 클레임도 반환합니다.

- ID 공급자의 이름을 포함하는 **identityProvider** 클레임입니다.
- 기본값이 **socialIdpAuthentication** 인 **authenticationSource** 클레임입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

## <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| PartnerEntity | 예 | SAML ID 공급자의 메타데이터 URL입니다. 또는 ID 공급자 메타데이터를 복사하여 CDATA 요소 `<![CDATA[Your IDP metadata]]>` 안에 포함합니다. ID 공급자 메타데이터를 포함하지 않는 것이 좋습니다. ID 공급자는 설정을 변경하거나 인증서를 업데이트할 수 있습니다. ID 공급자 메타데이터가 변경된 경우 새 메타데이터를 가져오고 정책을 새 메타데이터로 업데이트합니다. |
| WantsSignedRequests | 예 | 기술 프로필에서 모든 발신 인증 요청에 서명을 해야 하는지 여부를 나타냅니다. 가능한 값은 `true` 또는 `false`입니다. 기본값은 `true`입니다. 값을 `true`로 설정하는 경우에는 **SamlMessageSigning** 암호화 키를 지정하고 모든 발신 인증 요청에 서명을 해야 합니다. 값을 `false`로 설정하는 경우에는 **SigAlg** 및 **Signature** 매개 변수(쿼리 문자열 또는 게시 매개 변수)가 요청에서 생략됩니다. 이 메타데이터는 메타데이터 **AuthnRequestsSigned** 특성도 제어합니다. 해당 특성은 ID 공급자와 공유되는 Azure AD B2C 기술 프로필의 메타데이터 출력입니다. 기술 프로필 메타데이터에서 **WantsSignedRequests** 값이 `false`로 설정되고 ID공급자 메타데이터가 **WantAuthnRequestsSigned** 가 `false`로 설정되는 경우 Azure AD B2C는 요청을 서명하지 않습니다. |
| XmlSignatureAlgorithm | 예 | Azure AD B2C가 SAML 요청에 서명하는 데 사용하는 메서드입니다. 이 메타데이터는 SAML 요청의 **SigAlg** 매개 변수(쿼리 문자열 또는 게시 매개 변수) 값을 제어합니다. 가능한 값은 `Sha256`, `Sha384`, `Sha512` 또는 `Sha1`(기본값)입니다. 양쪽의 서명 알고리즘을 같은 값으로 구성해야 합니다. 인증서가 지원하는 알고리즘만 사용하세요. |
| WantsSignedAssertions | 예 | 기술 프로필에서 모든 수신 어설션에 서명을 해야 하는지 여부를 나타냅니다. 가능한 값은 `true` 또는 `false`입니다. 기본값은 `true`입니다. 값을 `true`로 설정하는 경우 ID 공급자가 Azure AD B2C로 전송하는 모든 어설션 섹션 `saml:Assertion`에 서명을 해야 합니다. 값을 `false`로 설정하는 경우에는 ID 공급자가 어설션에 서명을 하지 않아야 하며, 서명을 하더라도 Azure AD B2C가 서명의 유효성을 검사하지 않습니다. 이 메타데이터는 메타데이터 플래그 **WantsAssertionsSigned** 도 제어합니다. 해당 플래그는 ID 공급자와 공유되는 Azure AD B2C 기술 프로필의 메타데이터 출력입니다. 어설션 유효성 검사를 사용하지 않도록 설정하는 경우에는 응답 서명 유효성 검사도 사용하지 않도록 설정하는 것이 좋습니다. 자세한 내용은 **ResponsesSigned** 를 참조하세요. |
| ResponsesSigned | 예 | 가능한 값은 `true` 또는 `false`입니다. 기본값은 `true`입니다. 값을 `false`로 설정하는 경우에는 ID 공급자가 SAML 응답에 서명을 하지 않아야 하며, 서명을 하더라도 Azure AD B2C가 서명의 유효성을 검사하지 않습니다. 값을 `true`로 설정하는 경우에는 ID 공급자가 Azure AD B2C로 전송하는 SAML 응답에 서명이 되며 해당 서명의 유효성을 검사해야 합니다. SAML 응답 유효성 검사를 사용하지 않도록 설정하는 경우에는 어설션 서명 유효성 검사도 사용하지 않도록 설정하는 것이 좋습니다. 자세한 내용은 **WantsSignedAssertions** 를 참조하세요. |
| WantsEncryptedAssertions | 예 | 기술 프로필에서 모든 수신 어설션을 암호화해야 하는지 여부를 나타냅니다. 가능한 값은 `true` 또는 `false`입니다. 기본값은 `false`입니다. 값을 `true`로 설정하는 경우에는 ID 공급자가 Azure AD B2C로 전송하는 어설션에 서명을 해야 하며 **SamlAssertionDecryption** 암호화 키를 지정해야 합니다. 값을 `true`로 설정하는 경우에는 Azure AD B2C 기술 프로필의 메타데이터에 **encryption** 섹션이 포함됩니다. ID 공급자는 메타데이터를 읽고 Azure AD B2C 기술 프로필의 메타데이터에서 제공되는 공개 키를 사용하여 SAML 응답 어설션을 암호화합니다. 어설션 암호화를 사용하도록 설정하는 경우에는 응답 서명 유효성 검사도 사용하지 않도록 설정해야 할 수 있습니다. 자세한 내용은 **ResponsesSigned** 를 참조하세요. |
| NameIdPolicyFormat | 예 | 요청된 주체를 나타내는 데 사용할 이름 식별자에 제약 조건을 지정합니다. 생략하면 요청된 주체의 ID 공급자가 지원하는 어떤 형식의 식별자도 사용할 수 있습니다. 예: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** 은 **NameIdPolicyAllowCreate** 와 함께 사용할 수 있습니다. 지원되는 이름 ID 정책에 대한 지침은 ID 공급자의 설명서를 살펴봅니다. |
| NameIdPolicyAllowCreate | 예 | **NameIdPolicyFormat** 을 사용하는 경우 **NameIDPolicy** 의 `AllowCreate` 속성을 지정할 수도 있습니다. 이 메타데이터의 값은 ID 공급자가 로그인 흐름 동안 새 계정을 만들도록 허용되는지 여부를 나타내기 위해 `true` 또는 `false`입니다. 이 단계를 수행하는 방법의 지침은 사용 중인 ID 공급자의 설명서를 참조하세요. |
| AuthenticationRequestExtensions | 예 | Azure AD BC와 ID 공급자 간에 동의된 선택적 프로토콜 메시지 확장 요소입니다. 확장은 XML 형식으로 표시됩니다. CDATA 요소 `<![CDATA[Your IDP metadata]]>` 내부에 XML 데이터를 추가합니다. ID 공급자의 설명서를 확인하여 확장 요소가 지원되는지 검토합니다. |
| IncludeAuthnContextClassReferences | 예 | 인증 컨텍스트 클래스를 식별하는 하나 이상의 URI 참조를 지정합니다. 예를 들어, 사용자 이름 및 암호만으로 로그인할 수 있도록 하려면 이 값을 `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`로 설정합니다. 보호된 세션(SSL/TLS)을 통해 로그인 사용자 이름 및 암호로 로그인할 수 있도록 하려면 `PasswordProtectedTransport`를 지정합니다. 지원되는 **AuthnContextClassRef** URI에 대한 지침은 ID 공급자 설명서를 확인하세요. 여러 URI를 쉼표로 구분된 목록으로 지정합니다. |
| IncludeKeyInfo | 예 | 바인딩이 `HTTP-POST`로 설정된 경우 SAML 인증 요청이 인증서의 공개 키를 포함하는지 여부를 나타냅니다. 가능한 값은 `true` 또는 `false`입니다. |
| IncludeClaimResolvingInClaimsHandling  | 예 | 입력 및 출력 클레임의 경우 기술 프로필에 [클레임 해결](claim-resolver-overview.md)이 포함되는지 여부를 지정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. 기술 프로필에서 클레임 확인 프로그램을 사용하려는 경우 이 값을 `true`로 설정합니다. |
|SingleLogoutEnabled| 예| 로그인 중에 기술 프로필에서 페더레이션 ID 공급자에 대한 로그아웃을 시도하는지 여부를 나타냅니다. 자세한 내용은 [Azure AD B2C 세션 로그아웃](session-behavior.md#sign-out)을 참조하세요. 가능한 값은 `true`(기본값) 또는 `false`입니다.|
|ForceAuthN| No| SAML 인증 요청에서 ForceAuthN 값을 전달하여 외부 SAML IDP가 사용자에게 인증을 요청하는지 여부를 확인합니다. 기본값으로, Azure AD B2C 초기 로그인 시 ForceAuthN 값을 false로 설정합니다. 그 후 세션이 초기화되면 경우(OIDC에서 `prompt=login`를 사용하는 예제) ForceAuthN 값이 `true`로 설정됩니다. 아래 표시된 것처럼 메타데이터 항목을 설정하면 외부 IDP에 대한 모든 요청 값이 강제로 적용됩니다.  가능한 값은 `true` 또는 `false`입니다.|


## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute |필수 | 설명 |
| --------- | ----------- | ----------- |
| SamlMessageSigning |예 | SAML 메시지에 서명을 하는 데 사용할 X509 인증서(RSA 키 집합)입니다. Azure AD B2C는 이 키를 사용해 요청에 서명을 한 다음 ID 공급자에 요청을 전송합니다. |
| SamlAssertionDecryption |예 | X509 인증서(RSA 키 집합) SAML ID 공급자는 SAML 응답의 어설션을 암호화하기 위해 인증서의 공개 부분을 사용합니다. Azure AD B2C는 어설션의 암호를 해독하기 위해 인증서의 비공개 부분을 사용합니다. |
| MetadataSigning |예 | SAML 메타데이터에 서명을 하는 데 사용할 X509 인증서(RSA 키 집합)입니다. Azure AD B2C는 이 키를 사용하여 메타데이터에 서명을 합니다.  |

## <a name="saml-entityid-customization"></a>SAML entityID 사용자 지정

다른 entityID 값에 종속된 여러 SAML 애플리케이션이 있는 경우 신뢰 당사자 파일의 `issueruri` 값을 재정의할 수 있습니다. 이렇게 하려면 기본 파일에서 "Saml2AssertionIssuer" ID를 사용하여 기술 프로필을 복사하고 `issueruri` 값을 재정의합니다.

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

## <a name="next-steps"></a>다음 단계

Azure AD B2C에서 SAML ID 공급자를 사용하는 예제는 다음 문서를 참조하세요.

- [사용자 지정 정책을 사용하여 SAML ID 공급자로 ADFS 추가](identity-provider-adfs.md)
- [SAML을 통해 Salesforce 계정을 사용하여 로그인](identity-provider-salesforce-saml.md)
