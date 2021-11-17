---
title: 사용자 지정 정책을 사용하여 다중 테넌트 Azure AD 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 다중 테넌트 Azure AD ID 공급자를 추가합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 553608a5574edaf904e9c9ac0986a3d0f8af9278
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227970"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 다중 테넌트 Azure Active Directory에 대한 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

이 문서에서는 Azure AD(Azure Active Directory)에 대해 다중 테넌트 엔드포인트를 사용하여 사용자 로그인을 사용 설정하는 방법에 대해 설명합니다. 여러 Azure AD 테넌트의 사용자가 각 테넌트에 대한 ID 공급자를 구성할 필요 없이 Azure AD B2C를 사용하여 로그인할 수 있도록 합니다. 그러나 이러한 테넌트의 게스트 멤버는 로그인할 수 **없습니다**. 이렇게 하려면 [각 테넌트를 개별적으로 구성](identity-provider-azure-ad-single-tenant.md)해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

> [!NOTE]
> 이 문서에서는 필수 구성 요소에서 언급된 이전 단계에서 **SocialAndLocalAccounts** 스타터 팩을 사용한다고 가정합니다.  

## <a name="register-an-azure-ad-app"></a>Azure AD 앱 등록

Azure AD B2C(Azure Active Directory B2C)에서 사용자가 Azure AD 계정으로 로그인할 수 있도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 조직 Azure AD 테넌트(예: Contoso)가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
1. **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예들 들어 `Azure AD B2C App`입니다.
1. 이 애플리케이션에 대해 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트)** 을 선택합니다.
1. **리디렉션 URI** 의 경우, **웹** 의 값을 수락하고 다음 URL을 모두 소문자로 입력합니다. 여기서 `your-B2C-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.

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

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록** 을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **토큰 구성** 을 선택합니다.
1. **선택적 클레임 추가** 를 선택합니다.
1. **토큰 형식** 으로 **ID** 를 선택합니다.
1. 추가할 선택적 클레임으로 `family_name` 및 `given_name`을 선택합니다.
1. **추가** 를 선택합니다. **Microsoft Graph 이메일 사용 권한 켜기(토큰에 클레임을 표시하는 데 필요)** 가 표시되면 이를 사용하도록 설정한 다음, **추가** 를 다시 선택합니다.

## <a name="optional-verify-your-app-authenticity"></a>[선택 사항] 앱 신뢰성 확인

[게시자 확인](../active-directory/develop/publisher-verification-overview.md)을 통해 사용자는 [등록된](#register-an-azure-ad-app) 앱의 신뢰성을 파악할 수 있습니다. 확인된 앱은 앱의 게시자가 MPN(Microsoft 파트너 네트워크)을 사용하여 ID를 [확인했음](/partner-center/verification-responses)을 의미합니다. [앱을 게시자 확인으로 표시](../active-directory/develop/mark-app-as-publisher-verified.md)하는 방법을 알아봅니다. 

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 애플리케이션 키를 Azure AD B2C 테넌트에 저장해야 합니다.

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `AADAppSecret`입니다.  접두사 `B2C_1A_`는 생성될 때 키의 이름에 자동으로 추가되므로 다음 섹션의 XML 참조는 *B2C_1A_AADAppSecret* 이 됩니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="configure-azure-ad-as-an-identity-provider"></a>테넌트에서 Azure AD를 ID 공급자로 구성

사용자가 Azure AD 계정을 사용하여 로그인하도록 설정하려면 Azure AD를 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에 있는 **ClaimsProvider** 요소에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. **ClaimsProvider** 요소 아래의 **Domain** 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다.
1. **TechnicalProfile** 요소 아래의 **DisplayName** 값(예: `Multi-Tenant AAD`)을 업데이트합니다. 이 값은 로그인 페이지의 로그인 단추에 표시됩니다.
1. 이전에 등록했던 Azure AD 다중 테넌트 애플리케이션의 애플리케이션 ID에 **client_id** 를 설정합니다.
1. **CryptographicKeys** 에서 **StorageReferenceId** 의 값을 이전에 만든 정책 키의 이름으로 업데이트합니다. 예들 들어 `B2C_1A_AADAppSecret`입니다.

### <a name="restrict-access"></a>액세스 제한

**ValidTokenIssuerPrefixes** 의 값으로 `https://login.microsoftonline.com/`를 사용하면 모든 Azure AD 사용자가 애플리케이션에 로그인할 수 있습니다. 유효한 토큰 발급자 목록을 업데이트하고 로그인할 수 있는 특정 Azure AD 테넌트 사용자 목록으로 액세스를 제한합니다.

값을 구하기 위해 사용자가 로그인하게 하려는 특정 Azure AD 테넌트 각각에 대해 OpenID Connect 검색 메타데이터를 확인합니다. 메타데이터 URL의 형식은 `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`과 유사한데, 여기서 `your-tenant`는 사용자의 Azure AD 테넌트 이름입니다. 예를 들면 다음과 같습니다.

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

로그인에 사용해야 할 각 Azure AD 테넌트에 대해 다음 단계를 수행합니다.

1. 브라우저를 열고 테넌트에 대한 OpenID Connect 메타데이터 URL로 이동합니다. **발급자** 개체를 찾아 해당 값을 기록합니다. 이는 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/.well-known/openid-configuration`과 같이 표시됩니다.
1. **ValidTokenIssuerPrefixes** 키에 값을 복사하여 붙여넣습니다. 여러 발급자를 쉼표로 구분합니다. 이전의 `ClaimsProvider` XML 샘플에서는 두 발급자의 예를 볼 수 있습니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Common AAD** 를 선택하여 Azure AD 계정으로 로그인합니다.

다중 테넌트 로그인 기능을 테스트하기 위해 다른 Azure AD 테넌트에 존재하는 사용자의 자격 증명을 사용하여 마지막 두 단계를 수행합니다. **지금 실행 엔드포인트** 를 복사하고 개인 브라우저 창(예: Google Chrome의 Incognito 모드 또는 Microsoft Edge의 InPrivate 창)에서 엽니다. 개인 브라우저 창에서 열면 현재 캐시된 Azure AD 자격 증명을 사용하는 일 없이 전체 사용자 경험을 테스트할 수 있습니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션됩니다. 여기에는 Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 토큰을 애플리케이션에 전달](idp-pass-through-user-flow.md)하는 방법을 알아봅니다.

::: zone-end
