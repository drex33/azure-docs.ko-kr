---
title: 사용자 지정 정책을 사용하여 SAML ID 공급자로 AD FS 추가
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 SAML 프로토콜 및 사용자 지정 정책을 사용하여 AD FS 2016 설정
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 90e16f89a6b591841ed28942c44f7dc1223e975a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566237"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SAML ID 공급자로 AD FS 추가

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)의 [사용자 지정 정책](custom-policy-overview.md)을 사용하여 AD FS 사용자 계정에 대한 로그인을 사용하도록 설정하는 방법을 설명합니다. 사용자 지정 정책에 [SAML ID 공급자](identity-provider-generic-saml.md)를 추가하여 로그인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>정책 키 만들기

Azure AD B2C 테넌트에 인증서를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
5. **정책 키**, **추가** 를 차례로 선택합니다.
6. **옵션** 으로는 `Upload`을 선택합니다.
7. 정책 키의 **이름** 을 입력합니다. `SAMLSigningCert`)을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 프라이빗 키가 있는 인증서 .pfx 파일을 찾아 선택합니다.
9. **만들기** 를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 AD FS 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 AD FS 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다. 자세한 내용은 [SAML ID 공급자 정의](identity-provider-generic-saml.md)를 참조하세요.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. `your-AD-FS-domain`은 AD FS 도메인의 이름으로 바꾸고 **identityProvider** 출력 클레임의 값은 DNS(도메인을 나타내는 임의 값)로 바꿉니다.

1. `<ClaimsProviders>` 섹션을 찾은 후 다음 XML 코드 조각을 추가합니다. 정책에 이미 `SM-Saml-idp` 기술 프로필이 포함되어 있는 경우 다음 단계로 건너뜁니다. 자세한 내용은 [Single Sign-On 세션 관리](custom-policy-reference-sso.md)를 참조하세요.

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>AD FS 신뢰 당사자 트러스트 구성

Azure AD B2C에서 AD FS를 ID 공급자로 사용하려면 Azure AD B2C SAML 메타데이터를 사용하여 AD FS 신뢰 당사자 트러스트를 만들어야 합니다. 다음 예제에서는 Azure AD B2C 기술 프로필의 SAML 메타데이터에 대한 URL 주소를 보여 줍니다.

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

[사용자 지정 도메인](custom-domain.md)을 사용하는 경우 다음 형식을 사용합니다.

```
https://your-domain-name/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

다음 값을 바꿉니다.

- **your-tenant-name** 을 테넌트 이름으로(예: your-tenant.onmicrosoft.com).
- **your-domain-name** 을 사용자 지정 도메인 이름으로(예: login.contoso.com).
- **your-policy** 를 정책 이름으로. 예를 들어 B2C_1A_signup_signin_adfs로 바꿉니다.
- **your-technical-profile** 을 SAML ID 공급자 기술 프로필로. 예를 들어 Contoso-SAML2로 바꿉니다.

브라우저를 열고 URL로 이동합니다. 올바른 URL을 입력했는지와 XML 메타데이터 파일에 대한 액세스 권한이 있는지 확인합니다. AD FS 관리 스냅인을 사용하여 새 신뢰 당사자 트러스트를 추가하고 설정을 수동으로 구성하려면 페더레이션 서버에서 다음 절차를 수행합니다. 로컬 컴퓨터에서 **관리자** 또는 동등한 멤버 자격은 최소한 이 절차를 완료해야 합니다.

1. 서버 관리자에서 **도구** 를 선택하고 **AD FS 관리** 를 선택합니다.
2. **신뢰 당사자 트러스트 추가** 를 선택합니다.
3. **시작** 페이지에서 **클레임 인식** 을 선택하고 **시작** 을 선택합니다.
4. **데이터 원본 선택** 페이지에서 **신뢰 당사자 온라인 또는 로컬 네트워크 게시에 대한 정보 가져오기** 를 선택하고 Azure AD B2C 메타데이터 URL을 제공한 후 **다음** 을 선택합니다.
5. **표시 이름 지정** 페이지에서 **표시 이름** 을 입력하고 **메모** 아래에 이 신뢰 당사자 트러스트에 대한 설명을 입력한 후 **다음** 을 선택합니다.
6. **액세스 제어 정책 선택** 페이지에서 정책을 선택하고 **다음** 을 선택합니다.
7. **트러스트 추가 준비 완료** 페이지에서 설정을 검토하고 **다음** 을 선택하여 신뢰 당사자 트러스트 정보를 저장합니다.
8. **마침** 페이지에서 **닫기** 를 선택하면 이 작업이 **클레임 규칙 편집** 대화 상자를 자동으로 표시합니다.
9. **규칙 추가** 를 선택합니다.
10. **클레임 규칙 템플릿** 에서 **LDAP 특성을 클레임으로 전송** 을 선택합니다.
11. **클레임 규칙 이름** 을 제공합니다. **특성 저장소** 에서 **Active Directory 선택** 을 선택합니다. 다음과 같은 클레임을 추가한 후 **마침** 및 **확인** 을 선택합니다.

    | LDAP 특성 | 나가는 클레임 형식 |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | 이메일 |
    | Display-Name | name |

    나가는 클레임 형식 드롭다운에 일부 이름이 표시되지 않습니다. 수동으로 입력해야 합니다. (드롭다운은 편집이 가능합니다.)

12.  인증서 유형에 따라 해시 알고리즘을 설정해야 할 수 있습니다. 신뢰 당사자 트러스트(B2C 데모) 속성 창에서 **고급** 탭을 선택하고 **보안 해시 알고리즘** 을 `SHA-256`으로 변경하고 **확인** 을 선택합니다.
13. 서버 관리자에서 **도구** 를 선택하고 **AD FS 관리** 를 선택합니다.
14. 만든 신뢰 당사자 트러스트를 선택하고 **페더레이션 메타데이터에서 업데이트** 를 선택한 후 **업데이트** 를 선택합니다.

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Contoso AD FS** 를 선택하여 Contoso AD FS ID 공급자에 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

## <a name="troubleshooting-ad-fs-service"></a>AD FS 서비스 문제 해결  

AD FS는 Windows 애플리케이션 로그를 사용하도록 구성됩니다. Azure AD B2C에서 사용자 지정 정책을 사용하여 AD FS를 SAML ID 공급자로 설정하는 데 문제가 있는 경우 AD FS 이벤트 로그를 확인하는 것이 좋습니다.

1. Windows **검색 표시줄** 에 **이벤트 뷰어** 를 입력한 다음, **이벤트 뷰어** 데스크톱 앱을 선택합니다.
1. 다른 컴퓨터의 로그를 보려면 **이벤트 뷰어(로컬)** 를 마우스 오른쪽 단추로 클릭합니다. **다른 컴퓨터에 연결** 을 선택하고 필드를 입력하여 **컴퓨터 선택** 대화 상자를 완료합니다.
1. **이벤트 뷰어** 에서 **애플리케이션 및 서비스 로그** 를 엽니다.
1. **AD FS** 를 선택하고 **관리자** 를 선택합니다. 
1. 이벤트에 대한 자세한 내용을 보려면 해당 이벤트를 두 번 클릭합니다.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>SAML 요청이 필요한 서명 알고리즘 이벤트로 서명되어 있지 않dma

이 오류는 Azure AD B2C에서 보낸 SAML 요청이 AD FS에 구성된 필요한 서명 알고리즘으로 서명되지 않았음을 나타냅니다. 예를 들어, SAML 요청은 서명 알고리즘 `rsa-sha256`을 사용하여 서명되었지만 필요한 서명 알고리즘은 `rsa-sha1`입니다. 이 문제를 해결하려면 Azure AD B2C와 AD FS 모두 동일한 서명 알고리즘을 사용하여 구성되었는지 확인합니다.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>옵션 1: Azure AD B2C에서 서명 알고리즘 설정  

Azure AD B2C에서 SAML 요청에 서명하는 방법을 구성할 수 있습니다. [XmlSignatureAlgorithm](identity-provider-generic-saml.md) 메타데이터는 SAML 요청의 `SigAlg` 매개 변수(쿼리 문자열 또는 게시 매개 변수) 값을 제어합니다. 다음 예제에서는 `rsa-sha256` 서명 알고리즘을 사용하도록 Azure AD B2C를 구성합니다.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>옵션 2: AD FS에서 서명 알고리즘 설정 

또는 AD FS에서 필요한 SAML 요청 서명 알고리즘을 구성할 수 있습니다.

1. 서버 관리자에서 **도구** 를 선택하고 **AD FS 관리** 를 선택합니다.
1. 이전에 만든 **신뢰 당사자 트러스트** 를 선택합니다.
1. **속성** 을 선택하고 **고급** 을 선택합니다.
1. **보안 해시 알고리즘** 을 구성하고 **확인** 을 선택하여 변경 내용을 저장합니다.

::: zone-end
