---
title: 다른 Azure AD B2C 테넌트의 Azure AD B2C 계정을 사용하여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 통해 애플리케이션 내 다른 테넌트의 Azure AD B2C 계정을 사용하여 등록 및 로그인을 고객에게 제공합니다.
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
ms.openlocfilehash: 8aa68b99c4deaec0e20aedd663a3bfc7451d0742
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130228001"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>다른 Azure AD B2C 테넌트의 Azure AD B2C 계정을 사용하여 등록 및 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>개요

이 문서에서는 다른 Azure AD B2C 테넌트를 사용하여 페더레이션을 설정하는 방법을 설명합니다. 애플리케이션이 Azure AD B2C로 보호되는 경우 다른 Azure AD B2C의 사용자가 기존 계정으로 로그인할 수 있습니다. 다음 다이어그램에서 사용자는 *Contoso* 의 Azure AD B2C에 의해 보호되는 애플리케이션에 *Fabrikam* 의 Azure AD B2C 테넌트에서 관리되는 계정으로 로그인할 수 있습니다. 

![다른 Azure AD B2C 테넌트를 통한 Azure AD B2C 페더레이션](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>애플리케이션의 게시자 도메인 확인
2020년 11월부터 [애플리케이션의 게시자 도메인이 확인](../active-directory/develop/howto-configure-publisher-domain.md)***되고*** 회사의 ID가 Microsoft 파트너 네트워크로 확인되고 애플리케이션과 연결되지 않는 한 새 애플리케이션 등록이 사용자 동의 프롬프트에 확인되지 않은 것으로 표시됩니다. (이 변경 사항에 대해 [자세히 알아보기](../active-directory/develop/publisher-verification-overview.md)) Azure AD B2C 사용자 흐름의 경우 게시자 도메인은 [Microsoft 계정](../active-directory-b2c/identity-provider-microsoft-account.md) 또는 기타 Azure AD 테넌트를 ID 공급자로 사용할 때만 나타납니다. 이러한 새로운 요구 사항을 충족하려면 다음을 수행합니다.

1. [MPN(Microsoft 파트너 네트워크) 계정을 사용하여 회사 ID를 확인](/partner-center/verification-responses)합니다. 이 프로세스는 회사 및 회사의 기본 연락처에 대한 정보를 확인합니다.
1. 다음 옵션 중 하나를 사용하여 MPN 계정을 앱 등록과 연결하려면 게시자 확인 프로세스를 완료합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD 테넌트에 있는 경우 [앱 등록 포털에서 앱을 확인](../active-directory/develop/mark-app-as-publisher-verified.md)합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD B2C 테넌트에 있는 경우 [Microsoft Graph API를 사용하여 앱을 게시자 확인으로 표시합니다](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)(예: Graph Explorer 사용). 앱의 확인된 게시자를 설정하기 위한 UI는 현재 Azure AD B2C 테넌트에서 사용할 수 없습니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C(예: Contoso)에서 다른 Azure AD B2C 테넌트(예: Fabrikam)의 계정이 있는 사용자에 대한 로그인을 사용 설정하려면 다음 단계를 따릅니다.

1. [사용자 흐름](tutorial-create-user-flows.md?pivots=b2c-user-flow) 또는 [사용자 지정 정책](tutorial-create-user-flows.md?pivots=b2c-custom-policy)을 만듭니다.
1. 그런 다음 이 섹션에 설명된 대로 Azure AD B2C에서 애플리케이션을 만듭니다. 

애플리케이션을 만들려면 다음을 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 다른 Azure AD B2C 테넌트(예: Fabrikam.com)가 포함된 디렉터리를 사용하고 있는지 확인합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들면 *ContosoApp* 입니다.
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다.
1. **URI 리디렉션** 에서 **웹** 을 선택하고, 다음 URL을 모두 소문자로 입력합니다. 여기서 `your-B2C-tenant-name`은 Azure AD B2C 테넌트의 이름(예: Contoso)으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    예: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-domain-name`을 사용자 지정 도메인으로, `your-tenant-name`을 테넌트의 이름으로 바꿉니다.

1. 사용 권한에서 **openid 및 offline_access 권한에 대한 관리자 동의 부여** 확인란을 선택합니다.
1. **등록** 을 선택합니다.
1. **Azure AD B2C - 앱 등록** 페이지에서 만든 애플리케이션을 선택합니다(예: *ContosoApp*).
1. 애플리케이션 개요 페이지에 표시된 **애플리케이션(클라이언트) ID** 를 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.
1. 왼쪽 메뉴의 **관리** 에서 **인증서 및 비밀** 을 선택합니다.
1. **새 클라이언트 비밀** 을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대한 설명을 입력합니다. 예: *clientsecret1*.
1. **만료** 에서 암호가 유효한 기간을 선택한 다음 **추가** 를 선택합니다.
1. 비밀의 **값** 을 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C를 ID 공급자로 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 페더레이션을 구성하려는 Azure AD B2C 테넌트가 포함된 디렉터리(예: Contoso)를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. **이름** 을 입력합니다. 예를 들어 *Fabrikam* 을 입력합니다.
1. **메타데이터 URL** 의 경우 다음 URL을 입력합니다. 여기서 `{tenant}`은 Azure AD B2C 테넌트(예: Fabrikam)의 도메인 이름으로 바꿉니다. `{policy}`를 다른 테넌트에서 구성한 정책 이름으로 바꿉니다.

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    예들 들어 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`입니다.

1. **Client ID** 에 대해 이전에 기록한 애플리케이션 ID를 입력합니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **클라이언트 암호** 에 입력합니다.
1. **범위** 에 `openid`을 입력합니다.
1. **응답 유형** 및 **응답 모드** 에 대한 기본값을 그대로 둡니다.
1. (선택 사항) **도메인 힌트** 의 경우 [직접 로그인](direct-signin.md#redirect-sign-in-to-a-social-provider)에 사용할 도메인 이름을 입력합니다. *fabrikam.com* 을 예로 들 수 있습니다.
1. **ID 공급자 클레임 매핑** 에서 다음 클레임을 선택합니다.

    - **사용자 ID**: *sub*
    - **표시 이름**: *name*
    - **지정된 이름**: *given_name*
    - **성**: *family_name*
    - **메일**: *email*

1. **저장** 을 선택합니다.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>사용자 흐름에 Azure AD B2C ID 공급자 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Azure AD B2C ID 공급자를 추가하려는 사용자 흐름을 클릭합니다.
1. **소셜 ID 공급자** 에서 **Fabrikam** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Fabrikam** 을 선택하여 다른 Azure AD B2C 테넌트로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 만든 애플리케이션 키를 Azure AD B2C 테넌트에 저장해야 합니다.

1. 페더레이션을 구성하려는 Azure AD B2C 테넌트가 포함된 디렉터리(예: Contoso)를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `FabrikamAppSecret`입니다.  접두사 `B2C_1A_`는 생성될 때 키의 이름에 자동으로 추가되므로 다음 섹션의 XML 참조는 *B2C_1A_FabrikamAppSecret* 이 됩니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **암호** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C를 ID 공급자로 구성

사용자가 다른 Azure AD B2C 테넌트(Fabrikam)의 계정을 사용하여 로그인할 수 있도록 하려면 다른 Azure AD B2C를 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에 있는 **ClaimsProvider** 요소에 Azure AD B2C를 추가하여 Azure AD B2C를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 다음 XML 요소를 관련 값으로 업데이트합니다.

    |XML 요소  |값  |
    |---------|---------|
    |ClaimsProvider\Domain  | [직접 로그인](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)에 사용되는 도메인 이름입니다. 직접 로그인에 사용하려는 도메인 이름을 입력합니다. *fabrikam.com* 을 예로 들 수 있습니다. |
    |TechnicalProfile\DisplayName|이 값은 로그인 화면의 로그인 단추에 표시됩니다. 예를 들면 *Fabrikam* 입니다. |
    |Metadata\client_id|ID 공급자의 애플리케이션 식별자입니다. 다른 Azure AD B2C 테넌트에서 이전에 만든 애플리케이션 ID를 사용하여 클라이언트 ID를 업데이트합니다.|
    |Metadata\METADATA|OpenID의 잘 알려진 구성 엔드포인트라고도 하는 OpenID Connect ID 공급자 구성 문서를 가리키는 URL입니다. 다른 Azure AD B2C 테넌트(Fabrikam)의 도메인 이름으로 `{tenant}`를 대체하는 다음 URL을 입력합니다. `{tenant}`를 다른 테넌트에서 구성하는 정책 이름으로 바꾸고, `{policy]`를 정책 이름(`https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration`)으로 바꿉니다. 예: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| **StorageReferenceId** 의 값을 이전에 만든 정책 키의 이름으로 업데이트합니다. 예: `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Fabrikam** 을 선택하여 다른 Azure AD B2C 테넌트로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

[다른 Azure AD B2C 토큰을 애플리케이션에 전달](idp-pass-through-user-flow.md)하는 방법을 알아봅니다.