---
title: Microsoft 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Microsoft 계정으로 등록 및 로그인을 제공합니다.
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
ms.openlocfilehash: 5da695eeee6ef9ed26dcdbd29cf5b5684e277b12
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130227757"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>애플리케이션의 게시자 도메인 확인
2020년 11월부터 [애플리케이션의 게시자 도메인이 확인](../active-directory/develop/howto-configure-publisher-domain.md)***되고*** 회사 ID가 Microsoft 파트너 네트워크로 확인되고 애플리케이션과 연결되지 않는 한 새 애플리케이션 등록이 사용자 동의 프롬프트에 확인되지 않은 것으로 표시됩니다. (이 변경 사항에 대해 [자세히 알아보기](../active-directory/develop/publisher-verification-overview.md)) Azure AD B2C 사용자 흐름의 경우 게시자 도메인은 [Microsoft 계정](../active-directory-b2c/identity-provider-azure-ad-single-tenant.md) 또는 기타 Azure AD 테넌트를 ID 공급자로 사용할 때만 나타납니다. 이러한 새로운 요구 사항을 충족하려면 다음을 수행합니다.

1. [MPN(Microsoft 파트너 네트워크) 계정을 사용하여 회사 ID를 확인](/partner-center/verification-responses)합니다. 이 프로세스는 회사 및 회사의 기본 연락처에 대한 정보를 확인합니다.
1. 다음 옵션 중 하나를 사용하여 MPN 계정을 앱 등록과 연결하려면 게시자 확인 프로세스를 완료합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD 테넌트에 있는 경우 [앱 등록 포털에서 앱을 확인](../active-directory/develop/mark-app-as-publisher-verified.md)합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD B2C 테넌트에 있는 경우 [Microsoft Graph API를 사용하여 앱을 게시자 확인으로 표시합니다](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)(예: Graph Explorer 사용). 앱의 확인된 게시자를 설정하기 위한 UI는 현재 Azure AD B2C 테넌트에서 사용할 수 없습니다.

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 애플리케이션 만들기

Azure Active Directory B2C(Azure AD B2C)에서 사용자가 Microsoft 계정으로 로그인할 수 있도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)을 참조하세요. Microsoft 계정이 없는 경우 [https://www.live.com/](https://www.live.com/)에서 하나의 계정을 얻을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
1. **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. **지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트) 및 개인 Microsoft 계정(예: Skype, Xbox)** 을 선택합니다.

   다른 계정 유형 선택에 대한 자세한 내용은 다음을 참조하세요. [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)
1. **리디렉션 URI(선택 사항)** 에서 **웹** 을 선택하고 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로, `your-domain-name`을 사용자 지정 도메인으로 바꿉니다.
1. **등록** 을 선택합니다.
1. 애플리케이션 개요 페이지에 표시된 **애플리케이션(클라이언트) ID** 를 기록합니다. 클라이언트 ID는 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.
1. **인증서 및 암호** 를 선택합니다.
1. **새 클라이언트 비밀** 을 클릭합니다.
1. 비밀에 대한 **설명**(예: *애플리케이션 암호 1*)을 입력한 다음 **추가** 를 클릭합니다.
1. **값** 열에 표시된 애플리케이션 암호를 기록합니다. 클라이언트 암호는 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Microsoft를 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음 **Microsoft 계정** 을 선택합니다.
1. **이름** 을 입력합니다. 예: *MSA*
1. **클라이언트 ID** 에 이전에 만든 Azure AD 애플리케이션의 애플리케이션(클라이언트) ID를 입력합니다.
1. **클라이언트 비밀** 에는 기록한 클라이언트 비밀을 입력합니다.
1. **저장** 을 선택합니다.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>사용자 흐름에 Microsoft ID 공급자 추가 

이 시점에서 Microsoft ID 공급자가 설정되었지만 아직 로그인 페이지에서 사용할 수는 없습니다. 사용자 흐름에 Microsoft ID 공급자를 추가하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Microsoft ID 공급자를 추가할 사용자 흐름을 클릭합니다.
1. **소셜 ID 공급자** 에서 **Microsoft 계정** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Microsoft** 를 선택하여 Microsoft 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

Azure AD에서 `family_name` 및 `given_name` 클레임을 가져오려는 경우 Azure Portal UI 또는 애플리케이션 매니페스트에서 애플리케이션에 대한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 앱에 선택적 클레임을 제공하는 방법](../active-directory/develop/active-directory-optional-claims.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록** 을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **토큰 구성(미리 보기)** 을 선택합니다.
1. **선택적 클레임 추가** 를 선택합니다.
1. 구성하려는 토큰 유형을 선택합니다.
1. 추가할 선택적 클레임을 선택합니다.
1. **추가** 를 클릭합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

이제 Azure AD 테넌트에서 애플리케이션을 만들었으므로 Azure AD B2C 테넌트에 해당 애플리케이션의 클라이언트 암호를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `MSASecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. **암호** 에 이전 섹션에서 기록한 클라이언트 암호를 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-microsoft-as-an-identity-provider"></a>Microsoft를 ID 공급자로 구성

사용자가 Microsoft 계정을 사용하여 로그인하도록 설정하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProvider** 요소를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 정책 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **client_id** 의 값을 이전에 기록한 Azure AD 애플리케이션의 애플리케이션(클라이언트) ID로 바꿉니다.
1. 파일을 저장합니다.

이제 Azure AD B2C가 Azure AD의 Microsoft 계정 애플리케이션과 통신하는 방법을 알 수 있도록 정책을 구성했습니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Microsoft** 를 선택하여 Microsoft 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end