---
title: 사용자 지정 정책을 사용하여 OpenID Connect ID 공급자로 AD FS 추가
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 OpenID Connect 프로토콜 및 사용자 지정 정책을 사용하여 AD FS 2016 설정
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ad00a74871373338a2b4e8b557297a808e8186fc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007299"
---
# <a name="add-ad-fs-as-an-openid-connect-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 OpenID Connect ID 공급자로 AD FS 추가

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]


## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-an-ad-fs-application"></a>AD FS 애플리케이션 만들기

Azure AD B2C(Azure Active Directory B2C)에서 사용자가 AD FS 계정으로 로그인할 수 있도록 설정하려면 AD FS에서 애플리케이션 그룹을 만듭니다. 자세한 내용은 [AD FS 2016 이상에서 OpenID Connect를 사용한 웹 애플리케이션 빌드](/windows-server/identity/ad-fs/development/enabling-openid-connect-with-ad-fs)를 참조하세요.

애플리케이션 그룹을 만들려면 다음 단계를 수행합니다.

1. **서버 관리자** 에서 **도구** 를 선택한 다음, **AD FS 관리** 를 선택합니다.
1. AD FS 관리에서 **애플리케이션 그룹** 을 마우스 오른쪽 단추로 클릭하고 **애플리케이션 그룹 추가** 를 선택합니다.
1. 애플리케이션 그룹 마법사 **시작** 화면에서 다음을 수행합니다.
    1. 애플리케이션의 **이름** 을 입력합니다. 예: *Azure AD B2C 애플리케이션*.
    1. **Client-Server 애플리케이션** 에서 **웹 애플리케이션에 액세스하는 웹 브라우저** 템플릿을 선택합니다.
    1. **다음** 을 선택합니다.
1. 애플리케이션 그룹 마법사 **네이티브 애플리케이션** 화면에서 다음을 수행합니다.
    1. 복사는 **클라이언트 식별자** 값입니다. 클라이언트 ID는 사용자의 AD FS **애플리케이션 ID** 입니다. 이 문서의 후반부에 애플리케이션 ID가 필요합니다.
    1. **리디렉션 URI** 로 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로, `your-domain-name`을 사용자 지정 도메인으로 바꿉니다.
    1. **다음**, **다음** 을 차례로 선택하여 앱 등록 마법사를 완료합니다. 
    1. **닫기** 를 선택합니다.


### <a name="configure-the-app-claims"></a>앱 클레임 구성

이 단계에서는 애플리케이션이 Azure AD B2C에 반환할 클레임 AD FS를 구성합니다.  

1. **애플리케이션 그룹** 에서 생성한 애플리케이션을 선택합니다.
1. 애플리케이션 속성 창에서 **애플리케이션** 아래에 있는 **웹 애플리케이션** 을 선택합니다. 그런 다음 **편집** 을 선택합니다.
    :::image type="content" source="./media/identity-provider-adfs/ad-fs-edit-app.png" alt-text="웹 애플리케이션을 편집하는 방법을 보여 주는 스크린샷.":::
1. **발급 변환 규칙** 탭을 선택한 다음, **규칙 추가** 를 선택합니다.
1. **클레임 규칙 템플릿** 에서 **LDAP 특성을 클레임으로 전송** 을 선택합니다.
1. **클레임 규칙 이름** 을 제공합니다. **특성 저장소** 에서 **Active Directory 선택** 을 선택합니다. 다음과 같은 클레임을 추가합니다.

    | LDAP 특성 | 나가는 클레임 형식 |
    | -------------- | ------------------- |
    | User-Principal-Name | upn |
    | Surname | family_name |
    | Given-Name | given_name |
    | Display-Name | name |

    나가는 클레임 형식 드롭다운에 일부 이름이 표시되지 않습니다. 수동으로 입력해야 합니다. (드롭다운은 편집이 가능합니다.)

1. **마침** 을 선택한 다음, **닫기** 를 선택합니다.


::: zone pivot="b2c-user-flow"

## <a name="configure-ad-fs-as-an-identity-provider"></a>AD FS를 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. **이름** 을 입력합니다. 예를 들면 *Contoso* 입니다.
1. **메타데이터 URL** 의 경우 [AD FS OpenID Connect 구성 문서](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints)의 URL을 입력합니다. 예를 들면 다음과 같습니다.

    ```http
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```

1. **Client ID** 에 대해 이전에 기록한 애플리케이션 ID를 입력합니다.
1. **범위** 에 `openid`을 입력합니다.
1. **응답 형식** 의 경우 **id_token** 을 선택합니다.
1. (선택 사항) **도메인 힌트** 에 `contoso.com`을 입력합니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요.
1. **ID 공급자 클레임 매핑** 에서 다음 클레임을 선택합니다.

    - **사용자 ID**: `upn`
    - **표시 이름**: `unique_name`
    - **이름**: `given_name`
    - **성**: `family_name`

1. **저장** 을 선택합니다.

## <a name="add-ad-fs-identity-provider-to-a-user-flow"></a>사용자 흐름에 AD FS ID 공급자 추가 

이 시점에서 AD FS(Contoso) ID 공급자가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. AD FS ID 공급자를 사용자 흐름에 추가하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. AD FS ID 공급자(Contoso)를 추가하려는 사용자 흐름을 선택합니다.
1. **소셜 ID 공급자** 아래에서 **Contoso** 를 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Contoso** 를 선택하여 Contoso 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="configure-ad-fs-as-an-identity-provider"></a>AD FS를 ID 공급자로 구성

사용자가 AD FS 계정을 사용하여 로그인하도록 설정하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 AD FS를 정의해야 합니다. 

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://your-adfs-domain/adfs/.well-known/openid-configuration</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your AD FS application ID</Item>
          </Metadata>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="unique_name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **메타데이터 URL** 의 경우 [AD FS OpenID Connect 구성 문서](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints)의 URL을 입력합니다. 예를 들면 다음과 같습니다.

    ```
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```
5. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
6. 파일을 저장합니다.

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
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Contoso** 를 선택하여 Contoso 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.


::: zone-end

## <a name="next-steps"></a>다음 단계

[애플리케이션에 AD-FS 토큰을 전달](idp-pass-through-user-flow.md)하는 방법에 대해 알아봅니다.
