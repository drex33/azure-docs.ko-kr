---
title: Azure AD 조직에 대한 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인을 설정합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a63c89174103613c722d463e32085600019e9ea0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026010"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인 설정

이 문서에서는 Azure AD B2C의 사용자 흐름을 사용하여 특정 Azure AD 조직의 사용자에 대한 로그인을 설정하는 방법을 보여 줍니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>애플리케이션의 게시자 도메인 확인
2020년 11월부터 [애플리케이션의 게시자 도메인이 확인](../active-directory/develop/howto-configure-publisher-domain.md)***되고*** 회사의 ID가 Microsoft 파트너 네트워크로 확인되고 애플리케이션과 연결되지 않는 한 새 애플리케이션 등록이 사용자 동의 프롬프트에 확인되지 않은 것으로 표시됩니다. (이 변경 사항에 대해 [자세히 알아보기](../active-directory/develop/publisher-verification-overview.md)) Azure AD B2C 사용자 흐름의 경우 게시자 도메인은 [Microsoft 계정](../active-directory-b2c/identity-provider-microsoft-account.md) 또는 기타 Azure AD 테넌트를 ID 공급자로 사용할 때만 나타납니다. 이러한 새로운 요구 사항을 충족하려면 다음을 수행합니다.

1. [MPN(Microsoft 파트너 네트워크) 계정을 사용하여 회사 ID를 확인](/partner-center/verification-responses)합니다. 이 프로세스는 회사 및 회사의 기본 연락처에 대한 정보를 확인합니다.
1. 다음 옵션 중 하나를 사용하여 MPN 계정을 앱 등록과 연결하려면 게시자 확인 프로세스를 완료합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD 테넌트에 있는 경우 [앱 등록 포털에서 앱을 확인](../active-directory/develop/mark-app-as-publisher-verified.md)합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD B2C 테넌트에 있는 경우 [Microsoft Graph API를 사용하여 앱을 게시자 확인으로 표시합니다](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)(예: Graph Explorer 사용). 앱의 확인된 게시자를 설정하기 위한 UI는 현재 Azure AD B2C 테넌트에서 사용할 수 없습니다.

## <a name="register-an-azure-ad-app"></a>Azure AD 앱 등록

Azure AD B2C(Azure Active Directory B2C)에서 특정 Azure AD 조직의 Azure AD 계정을 통한 사용자 로그인을 지원하려면 [Azure Portal](https://portal.azure.com)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 조직 Azure AD 테넌트(예: Contoso)가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **앱 등록** 을 선택하거나 **앱 등록** 을 검색하여 선택합니다.
1. **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예들 들어 `Azure AD B2C App`입니다.
1. 이 애플리케이션에 대해 기본적으로 선택되는 **이 조직 디렉터리의 계정만(기본 디렉터리만 - 단일 테넌트)** 를 수락합니다.
1. **리디렉션 URI** 의 경우, **Web** 의 값을 수락하고 다음 URL을 모두 소문자로 입력합니다. 여기서 `your-B2C-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    예들 들어 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`입니다.

    [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-domain-name`을 사용자 지정 도메인으로, `your-tenant-name`를 테넌트의 이름으로 바꿉니다.

1. **등록** 을 선택합니다. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID** 를 기록합니다.
1. **인증서 및 암호** 를 선택하고 **새 클라이언트 암호** 를 선택합니다.
1. 비밀에 **설명** 을 입력하고 만료를 선택한 다음 **추가** 를 선택합니다. 이후 단계에서 사용할 비밀의 **값** 을 기록합니다.

### <a name="configuring-optional-claims"></a>선택적 클레임 구성

Azure AD에서 `family_name` 및 `given_name` 클레임을 가져오려는 경우 Azure Portal UI 또는 애플리케이션 매니페스트에서 애플리케이션에 대한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 앱에 선택적 클레임을 제공하는 방법](../active-directory/develop/active-directory-optional-claims.md)을 참조하세요.

1. 조직 Azure AD 테넌트를 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록** 을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **토큰 구성** 을 선택합니다.
1. **선택적 클레임 추가** 를 선택합니다.
1. **토큰 형식** 으로 **ID** 를 선택합니다.
1. 추가할 선택적 클레임으로 `family_name` 및 `given_name`을 선택합니다.
1. **추가** 를 선택합니다. **Microsoft Graph 이메일 사용 권한 켜기(토큰에 클레임을 표시하는 데 필요)** 가 표시되면 이를 사용하도록 설정한 다음, **추가** 를 다시 선택합니다.

## <a name="optional-verify-your-app-authenticity"></a>[선택 사항] 앱 신뢰성 확인

[게시자 확인](../active-directory/develop/publisher-verification-overview.md)을 통해 사용자는 [등록된](#register-an-azure-ad-app) 앱의 신뢰성을 파악할 수 있습니다. 확인된 앱은 앱의 게시자가 MPN(Microsoft 파트너 네트워크)을 사용하여 ID를 [확인했음](/partner-center/verification-responses)을 의미합니다. [앱을 게시자 확인으로 표시](../active-directory/develop/mark-app-as-publisher-verified.md)하는 방법을 알아봅니다. 

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>테넌트에서 Azure AD를 ID 공급자로 구성

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. **이름** 을 입력합니다. 예를 들어 *Contoso Azure AD* 를 입력합니다.
1. **메타데이터 URL** 에 대해 다음 URL을 입력합니다. 여기서 `{tenant}`은 Azure AD 테넌트의 도메인 이름으로 바꿉니다.

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

 예들 들어 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`입니다. 사용자 지정 도메인을 사용하는 경우 `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`에서 `contoso.com`을 사용자 지정 도메인으로 바꿉니다.

1. **Client ID** 에 대해 이전에 기록한 애플리케이션 ID를 입력합니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **클라이언트 암호** 에 입력합니다.
1. **범위** 에 `openid profile`을 입력합니다.
1. **응답 유형** 및 **응답 모드** 에 대한 기본값을 그대로 둡니다.
1. (선택 사항) **도메인 힌트** 에 `contoso.com`을 입력합니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요.
1. **ID 공급자 클레임 매핑** 에서 다음 클레임을 선택합니다.

    - **사용자 ID**: *oid*
    - **표시 이름**: *name*
    - **지정된 이름**: *given_name*
    - **성**: *family_name*
    - **메일**: *email*

1. **저장** 을 선택합니다.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>사용자 흐름에 Azure AD ID 공급자 추가 

이 시점에서 Azure AD ID 공급자가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. 사용자 흐름에 Azure AD ID 공급자를 추가하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Azure AD ID 공급자를 추가하려는 사용자 흐름을 클릭합니다.
1. **설정** 아래에서 **ID 공급자** 를 선택합니다.
1. **사용자 지정 ID 공급자** 아래에서 **Contoso Azure AD** 를 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다. 
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Contoso Azure AD** 를 선택하여 Azure AD Contoso 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 애플리케이션 키를 Azure AD B2C 테넌트에 저장해야 합니다.

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `ContosoAppSecret`입니다.  접두사 `B2C_1A_`는 생성될 때 키의 이름에 자동으로 추가되므로 다음 섹션의 XML 참조는 *B2C_1A_ContosoAppSecret* 이 됩니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="configure-azure-ad-as-an-identity-provider"></a>테넌트에서 Azure AD를 ID 공급자로 구성

사용자가 Azure AD 계정을 사용하여 로그인하도록 설정하려면 Azure AD를 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에 있는 **ClaimsProvider** 요소에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **ClaimsProvider** 요소 아래의 **Domain** 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다. 예: `Contoso`. 이 도메인 설정의 끝에 `.com`을 붙이지 않습니다.
5. **ClaimsProvider** 요소 아래에서 **DisplayName** 값을 클레임 공급자에게 친숙한 이름으로 업데이트합니다. 이 값은 현재 사용되지 않습니다.

### <a name="update-the-technical-profile"></a>기술 프로필 업데이트

Azure AD 엔드포인트에서 토큰을 가져오려면 Azure AD B2C에서 Azure AD와 통신하는 데 사용해야 하는 프로토콜을 정의해야 합니다. 이 작업은 **ClaimsProvider** 의 **TechnicalProfile** 요소 내에서 수행됩니다.

1. **TechnicalProfile** 요소의 ID를 업데이트합니다. 이 ID는 정책의 다른 부분에서 이 기술 프로필을 참조하는 데 사용합니다(예: `AADContoso-OpenIdConnect`).
1. **DisplayName** 의 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
1. **설명** 값을 업데이트합니다.
1. Azure AD는 OpenID Connect 프로토콜을 사용하므로 **Protocol** 값이 `OpenIdConnect`인지 확인합니다.
1. **METADATA** 값을 `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`으로 설정합니다. 여기서 `tenant-name`는 Azure AD 테넌트 이름입니다. 예를 들어 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
1. **CryptographicKeys** 에서 **StorageReferenceId** 의 값을 이전에 만든 정책 키의 이름으로 업데이트합니다. 예들 들어 `B2C_1A_ContosoAppSecret`입니다.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Contoso Employee** 를 선택하여 Azure AD Contoso 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

[Azure AD 토큰을 애플리케이션에 전달](idp-pass-through-user-flow.md)하는 방법을 알아봅니다.
