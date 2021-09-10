---
title: SAML ID 공급자 옵션을 통한 로그인 설정
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C에서 로그인 SAML ID 공급자(IdP) 옵션을 구성합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c245fef005be5937887a3b8af1a5264e6520a6e8
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967786"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 SAML ID 공급자 옵션 구성

Azure AD B2C(Azure Active Directory B2C)는 SAML 2.0 ID 공급자와의 페더레이션을 지원합니다. 이 문서에서는 SAML ID 공급자를 통한 로그인 사용 설정 시 사용할 수 있는 구성 옵션을 설명합니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>클레임 매핑

**OutputClaims** 요소는 SAML ID 공급자가 반환한 클레임 목록을 포함합니다. 정책에 정의된 클레임의 이름을 ID 공급자에 정의된 이름에 매핑해야 합니다. ID 공급자에 클레임(어설션) 목록이 있는지 확인합니다. ID 공급자가 반환하는 SAML 응답의 콘텐츠를 확인할 수도 있습니다. 자세한 내용은 [SAML 메시지 디버그](#debug-saml-protocol)를 참조하세요. 클레임을 추가하려면 먼저 [클레임을 정의](claimsschema.md)한 다음, 클레임을 출력 클레임 컬렉션에 추가합니다.

`DefaultValue` 특성만 설정하면, ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다. 기본값은 [컨텍스트 클레임](#enable-use-of-context-claims)을 사용하여 고정적 또는 동적이 될 수 있습니다.

출력 클레임 요소에는 다음 특성이 포함됩니다.

- **ClaimTypeReferenceId** 는 클레임 유형에 대한 참조입니다. 
- **PartnerClaimType** 은 SAML 어설션에 나타나는 속성의 이름입니다. 
- **DefaultValue** 는 미리 정의된 기본값입니다. 클레임이 비어 있으면 기본값이 사용됩니다. 상관 관계 ID 또는 사용자 IP 주소와 같은 컨텍스트 값을 사용하여 [클레임 확인자](claim-resolver-overview.md)를 사용할 수도 있습니다.

### <a name="subject-name"></a>주체 이름

**주체** 에서 SAML 어설션 **NameId** 를 정규화된 클레임으로 읽으려면 클레임 **PartnerClaimType** 을 `SPNameQualifier` 특성 값으로 설정합니다. `SPNameQualifier` 특성이 표시되지 않으면 클레임 **PartnerClaimType** 을 `NameQualifier` 특성 값으로 설정합니다.

SAML 어설션:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

출력 클레임:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

`SPNameQualifier` 또는 `NameQualifier` 특성이 모두 SAML 어설션에 표시되지 않으면 클레임 **PartnerClaimType** 을 `assertionSubjectName`으로 설정합니다. **NameId** 가 어설션 XML의 첫 번째 값인지 확인합니다. 둘 이상의 어설션을 정의하면 Azure AD B2C가 마지막 어설션에서 주체 값을 선택합니다.


## <a name="configure-saml-protocol-bindings"></a>SAML 프로토콜 바인딩 구성

SAML 요청은 ID 공급자의 메타데이터 `SingleSignOnService` 요소에 지정된 바와 같이 ID 공급자에게 전송됩니다. 대부분의 ID 공급자 권한 부여 요청은 메시지가 상대적으로 짧기 때문에 HTTP GET 요청의 URL 쿼리 문자열에서 직접 전달됩니다. 두 SAML 요청에 대한 바인딩을 구성하는 방법은 ID 공급자 설명서를 참조하세요.

다음은 두 개의 바인딩을 포함하는 Azure AD 메타데이터 Single Sign-On 서비스의 예제입니다. `HTTP-Redirect`는 SAML ID 공급자 메타데이터에 먼저 표시되기 때문에 `HTTP-POST`보다 우선적으로 적용됩니다.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>Assertion Consumer Service

Assertion Consumer Service(또는 ACS)는 Azure AD B2C가 ID 공급자 SAML 응답을 보내고 받을 수 있는 위치입니다. SAML 응답은 HTTP POST 바인딩을 통해 Azure AD B2C로 전송됩니다. ACS 위치는 신뢰 당사자의 기본 정책을 가리킵니다. 예를 들어, 신뢰 정책이 *B2C_1A_signup_signin* 인 경우 ACS는 *B2C_1A_TrustFrameworkBase* 와 같은 *B2C_1A_signup_signin* 의 기본 정책입니다.

다음은 Azure AD B2C 정책 메타데이터 Assertion Consumer Service 요소의 예입니다. 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>SAML 요청 서명 구성

Azure AD B2C는 **SamlMessageSigning** 암호화 키를 사용하여 나가는 모든 인증 요청을 서명합니다. SAML 요청 서명을 사용하지 않도록 설정하려면 **WantsSignedRequests** 를 `false`으로 설정합니다. 메타데이터를 `false`로 설정하는 경우에는 **SigAlg** 및 **Signature** 매개 변수(쿼리 문자열 또는 게시 매개 변수)가 요청에서 생략됩니다.

이 메타데이터는 **AuthnRequestsSigned** 특성도 제어합니다. 해당 특성은 ID 공급자와 공유되는 Azure AD B2C 기술 프로필의 메타데이터와 함께 포함됩니다. 기술 프로필 메타데이터에서 **WantsSignedRequests** 값이 `false`로 설정되고 ID 공급자 메타데이터 **WantAuthnRequestsSigned** 가 `false`로 설정되거나 지정되지 않은 경우 Azure AD B2C는 요청에 서명하지 않습니다.

다음 예에서는 SAML 요청에서 서명을 제거합니다.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>서명 알고리즘

Azure AD B2C는 `Sha1`를 사용하여 SAML 요청에 서명합니다. **XmlSignatureAlgorithm** 메타데이터를 사용하여 사용할 알고리즘을 구성합니다. 가능한 값은 `Sha256`, `Sha384`, `Sha512` 또는 `Sha1`(기본값)입니다. 이 메타데이터는 SAML 요청의 **SigAlg** 매개 변수(쿼리 문자열 또는 게시 매개 변수) 값을 제어합니다. 양쪽의 서명 알고리즘을 같은 값으로 구성해야 합니다. 인증서가 지원하는 알고리즘만 사용하세요.

### <a name="include-key-info"></a>키 정보 포함

ID 공급자가 Azure AD B2C 바인딩이 `HTTP-POST`로 설정된 것을 표시하면 Azure AD B2C는 SAML 요청의 본문에 서명과 알고리즘을 포함시킵니다. 바인딩이 `HTTP-POST`로 설정된 경우 인증서의 공개 키를 포함하도록 Azure AD를 구성할 수도 있습니다. **IncludeKeyInfo** 메타데이터를 `true` 또는 `false`에 사용합니다. 다음 예제에서는 Azure AD에 인증서의 공개 키가 포함되지 않습니다.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>SAML 요청 이름 ID 구성

SAML 권한 부여 요청 `<NameID>` 요소는 SAML 이름 식별자 형식을 나타냅니다. 이 섹션에서는 기본 구성과 이름 ID 요소를 사용자 지정하는 방법을 설명합니다.

## <a name="preferred-username"></a>기본 설정된 사용자 이름

로그인 사용자 경험이 진행되는 동안, 신뢰 당사자 애플리케이션은 특정 사용자를 대상으로 지정할 수 있습니다. Azure AD B2C를 사용하면 SAML ID 공급자에게 기본 설정된 사용자 이름을 전송할 수 있습니다. **InputClaims** 요소는 SAML 권한 부여 요청의 **주체** 내에 있는 **NameId** 를 보내는 데 사용됩니다.

권한 부여 요청 내의 주체 이름 ID를 포함하려면 다음 `<InputClaims>` 요소를 `<CryptographicKeys>` 바로 뒤에 추가합니다. **PartnerClaimType** 은 `subject`로 설정해야 합니다.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

이 예제에서 Azure AD B2C는 SAML 권한 부여 요청의 **주체** 내에 있는 **NameId** 이 포함된 **signInName** 클레임 값을 보냅니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

`{OIDC:LoginHint}`과 같은 [컨텍스트 클레임](claim-resolver-overview.md)을 사용하여 클레임 값을 채울 수 있습니다.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>이름 ID 정책 형식

기본적으로 SAML 권한 부여 요청은 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 정책을 지정합니다. 즉, 요청된 주체의 ID 공급자가 지원하는 어떤 형식의 식별자도 사용할 수 있습니다.

이러한 동작을 변경하려면ID 공급자의 설명서에 설명되어 있는 지원되는 이름 ID 정책에 관한 지침을 참고하세요. 그런 다음 해당 정책 형식으로 `NameIdPolicyFormat` 메타데이터를 추가합니다. 예를 들면 다음과 같습니다.

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

다음 SAML 권한 부여 요청은 이름 ID 정책을 포함합니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>새 계정 만들기 허용

[이름 ID 정책 형식](#name-id-policy-format)을 지정하는 경우 `AllowCreate` **NameIDPolicy** 의 속성을 지정하여 ID 공급자가 로그인 흐름 중에 새 계정을 만들 수 있는지 여부를 나타낼 수도 있습니다. 지침은 ID 공급자의 설명서를 참조하세요.

Azure AD B2C은 기본적으로 `AllowCreate` 속성을 생략합니다. 이 동작은 `NameIdPolicyAllowCreate` 메타데이터를 사용하여 변경할 수 있습니다. 이 메타데이터의 값은 `true` 또는 `false`입니다.

다음 예제에서는 `NameIDPolicy`의 `AllowCreate` 속성을 `true`으로 설정하는 방법을 보여 줍니다.

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


다음 예제에서는 **NameIDPolicy** 요소의 **AllowCreate** 를 포함하는 권한 부여 요청을 보여 줍니다.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="force-authentication"></a>인증 강제 적용

SAML 인증 요청에 `ForceAuthN` 속성을 전달하여 외부 SAML IDP가 사용자에게 인증하라는 메시지를 표시하도록 강제할 수 있습니다. 사용자의 ID 공급자도 이 속성을 지원해야 합니다.

`ForceAuthN` 속성은 부울 `true` 또는 `false` 값입니다. 기본값으로 Azure AD B2C는 ForceAuthN 값을 `false`로 설정합니다. 그 후 세션이 초기화되면 경우(OIDC에서 `prompt=login`를 사용하는 예제) ForceAuthN 값이 `true`로 설정됩니다. 아래 표시된 것처럼 메타데이터 항목을 설정하면 외부 IDP에 대한 모든 요청 값이 강제로 적용됩니다.

다음 예제에서는 `ForceAuthN` 속성 집합을 `true`로 설정하는 방법을 보여줍니다.

```xml
<Metadata>
  ...
  <Item Key="ForceAuthN">true</Item>
  ...
</Metadata>
```

다음 예제에서는 권한 부여 요청에서 `ForceAuthN` 속성을 보여줍니다.


```xml
<samlp:AuthnRequest AssertionConsumerServiceURL="https://..."  ...
                    ForceAuthN="true">
  ...
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>인증 컨텍스트 클래스 참조 포함

SAML 권한 부여 요청에는 권한 부여 요청 컨텍스트를 지정하는 **AuthnContext** 요소가 포함될 수 있습니다. 이 요소는 인증 컨텍스트 클래스 참조를 포함할 수 있습니다. 인증 컨텍스트 클래스 참조는 사용자에게 제공할 인증 메커니즘을 SAML ID 공급자에게 알려 줍니다.

인증 컨텍스트 클래스 참조를 구성하려면 **IncludeAuthnContextClassReferences** 메타데이터를 추가합니다. 해당 값에서 인증 컨텍스트 클래스를 식별하는 하나 이상의 URI 참조를 지정합니다. 여러 URI를 쉼표로 구분된 목록으로 지정합니다. 지원되는 **AuthnContextClassRef** URI에 대한 지침은 ID 공급자 설명서를 확인하세요.

다음 예에서는 사용자가 사용자 이름과 암호를 모두 사용하여 로그인하고 보호된 세션(SSL/TLS)을 통해 사용자 이름 및 암호를 사용하여 로그인할 수 있습니다.

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

다음 SAML 권한 부여 요청에는 인증 컨텍스트 클래스 참조가 포함됩니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>권한 부여 요청에 사용자 지정 데이터 포함하기

Azure AD BC와 ID 공급자 모두가 동의한 프로토콜 메시지 확장 요소를 선택에 따라 포함시킬 수 있습니다. 확장은 XML 형식으로 표시됩니다. CDATA 요소 `<![CDATA[Your Custom XML]]>` 내에 XML 데이터를 추가하여 확장 요소를 포함합니다. ID 공급자의 설명서를 확인하여 확장 요소가 지원되는지 검토합니다.

다음 예제에서는 확장 데이터를 사용하는 방법을 보여 줍니다.

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

> [!NOTE]
> SAML 사양에 따라 확장 데이터는 네임스페이스로 한정된 XML(예: 위의 샘플에 표시된 'urn:ext:custom')이어야 하며 SAML 관련 네임스페이스 중 하나가 아니어야 합니다.

SAML 프로토콜 메시지 확장을 사용하는 경우 SAML 응답은 다음 예제와 같습니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>서명된 SAML 응답 필요

Azure AD B2C에는 들어오는 모든 어설션이 서명되어야 합니다. **WantsSignedAssertions** 를 `false`로 설정하면 이 요구 사항을 제거할 수 있습니다. 이 경우 ID 공급자가 어설션에 서명을 하지 않아야 하며, 서명을 하더라도 Azure AD B2C가 서명의 유효성을 검사하지 않습니다.

**WantsSignedAssertions** 메타데이터는 ID 공급자와 공유되는 Azure AD B2C 기술 프로필의 메타데이터에 포함되는 SAML 메타데이터 플래그 **WantAssertionsSigned** 를 제어합니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

어설션 유효성 검사를 사용하지 않도록 설정하는 경우에는 응답 메시지 서명 유효성 검사도 사용하지 않도록 설정하는 것이 좋습니다. **ResponsesSigned** 메타데이터를 `false`로 설정합니다. 이 경우 ID 공급자가 SAML 응답 메시지에 서명을 하지 않아야 하지만, 서명을 하더라도 Azure AD B2C가 서명의 유효성을 검사하지 않습니다.

다음 예제에서는 메시지와 어설션 서명을 모두 제거합니다.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>암호화된 SAML 응답 필요

들어오는 모든 어설션을 암호화하도록 요구하려면 **WantsEncryptedAssertions** 메타데이터를 설정합니다. 암호화가 필요한 경우 ID 공급자는 Azure AD B2C 기술 프로필에 있는 암호화 인증서의 공개 키를 사용합니다. Azure AD B2C는 암호화 인증서의 비공개 부분을 사용하여 응답 어설션의 암호를 해독합니다.

어설션 암호화를 사용하도록 설정하는 경우에는 응답 서명 유효성 검사도 사용하지 않도록 설정해야 할 수 있습니다. 자세한 내용은 [서명된 SAML 응답 필요](#require-signed-saml-responses)를 참조하세요.

**WantsEncryptedAssertions** 메타데이터를 `true`로 설정하는 경우에는 Azure AD B2C 기술 프로필의 메타데이터에 **암호화** 섹션이 포함됩니다. ID 공급자는 메타데이터를 읽고 Azure AD B2C 기술 프로필의 메타데이터에서 제공되는 공개 키를 사용하여 SAML 응답 어설션을 암호화합니다.

다음 예제에서는 암호화에 사용되는 SAML 메타데이터의 키 설명자 섹션을 보여줍니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

SAML 응답 어설션을 암호화하려면 다음 단계를 수행합니다.

1. 고유 식별자를 사용하여 [정책 키를 만듭니다](identity-provider-generic-saml.md#create-a-policy-key). 예들 들어 `B2C_1A_SAMLEncryptionCert`입니다.
2. SAML 기술 프로필 **CryptographicKeys** 컬렉션에서 **StorageReferenceId** 를 1단계에서 만든 정책 키의 이름으로 설정합니다. `SamlAssertionDecryption` ID는 SAML 응답의 어설션을 암호화하고 암호 해독하는 암호화 키의 사용을 나타냅니다.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. 기술 프로필 메타데이터 **WantsEncryptedAssertions** 를 `true`로 설정합니다.
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. 새 Azure AD B2C 기술 프로필 메타데이터로 ID 공급자를 업데이트합니다. 인증서의 공개 키를 포함하며 **use** 속성이 `encryption`으로 설정된 **KeyDescriptor** 가 표시되어야 합니다.

## <a name="enable-use-of-context-claims"></a>컨텍스트 클레임 사용의 사용 설정

입력 및 출력 클레임 컬렉션에서는 `DefaultValue` 특성만 설정하면 ID 공급자가 반환하지 않은 클레임을 포함할 수 있습니다. [컨텍스트 클레임](claim-resolver-overview.md)을 사용하여 기술 프로필에 포함할 수도 있습니다. 컨텍스트 클레임을 사용하는 방법

1. [BuildingBlocks](buildingblocks.md) 내의 [ClaimsSchema](claimsschema.md) 요소에 클레임 유형을 추가합니다.
2. 입력 또는 출력 컬렉션에 출력 클레임을 추가합니다. 다음 예에서는 첫 번째 클레임이 ID 공급자의 값을 설정합니다. 두 번째 클레임은 사용자 IP 주소 [컨텍스트 클레임](claim-resolver-overview.md)을 사용합니다.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>단일 로그아웃 사용 안 함

애플리케이션 로그아웃 요청 시 Azure AD B2C는 SAML ID 공급자에서 로그아웃을 시도합니다. 자세한 내용은 [Azure AD B2C 세션 로그아웃](session-behavior.md#sign-out)을 참조하세요.  단일 로그아웃 동작을 사용하지 않도록 설정하려면 **SingleLogoutEnabled** 메타데이터를 `false`로 설정합니다.

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>SAML 프로토콜 디버그

SAML ID 공급자와의 페더레이션을 구성하고 디버그하는 데 도움이 되도록 Chrome용 [SAML DevTools 확장](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio), FireFox용 [SAML 추적자](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) 또는 [Edge나 IE 개발자 도구](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957) 등 SAML 프로토콜에 대한 브라우저 확장을 사용할 수 있습니다.

이러한 도구를 사용하여 Azure AD B2C와 SAML ID 공급자 간의 통합을 확인할 수 있습니다. 예를 들면 다음과 같습니다.

* SAML 요청에 서명이 포함되어 있는지 확인하고 권한 부여 요청에 서명하는 데 사용되는 알고리즘을 결정합니다.
* `AttributeStatement` 섹션 아래에서 클레임(어설션)을 가져옵니다.
* ID 공급자에서 오류 메시지를 반환하는지 확인합니다.
* 어설션 섹션의 암호화 여부를 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Application Insights](troubleshoot-with-application-insights.md)를 사용하여 사용자 지정 정책으로 문제를 진단하는 방법을 알아봅니다. 

::: zone-end
