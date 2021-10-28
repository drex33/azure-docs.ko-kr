---
title: Azure Active Directory B2C를 사용하여 QQ 계정으로 등록 설정 및 로그인
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 QQ 계정으로 등록 및 로그인을 제공합니다.
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
ms.openlocfilehash: ea4be3d86c45982b61c4e3df80e6fa4f5144fb92
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130227784"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 QQ 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>QQ 애플리케이션 만들기

Azure AD B2C(Azure Active Directory B2C)에서 사용자가 QQ 계정으로 로그인할 수 있도록 설정하려면 [QQ 개발자 포털](http://open.qq.com)에서 애플리케이션을 만들어야 합니다. QQ 계정이 아직 없는 경우 [https://ssl.zc.qq.com](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)에서 가입할 수 있습니다.

### <a name="register-for-the-qq-developer-program"></a>QQ 개발자 프로그램 등록

1. QQ 계정 자격 증명으로 [QQ 개발자 포털](http://open.qq.com)에 로그인합니다.
1. 로그인한 후에 [https://open.qq.com/reg](https://open.qq.com/reg)로 이동하여 개발자인 사용자를 직접 등록합니다.
1. **个人**(개별 개발자)를 선택합니다.
1. 필요한 정보를 입력하고 **下 一 步**(다음 단계)를 선택합니다.
1. 전자 메일 확인 프로세스를 완료합니다. 개발자로 등록한 후 승인이 되기까지 몇 일 동안 기다려야 합니다.

### <a name="register-a-qq-application"></a>QQ 애플리케이션 등록

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html)으로 이동합니다.
1. **应用管理**(앱 관리)를 선택합니다.
1. **创建应用**(앱 만들기)를 선택한 후 필수 정보를 입력합니다.
1. **授权回调域**(콜백 URL)에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꾸고 `your-domain-name`을 사용자 지정 도메인으로 바꿉니다.
1. **创建应用**(앱 만들기)를 선택합니다.
1. 확인 페이지에서 **应用管理**(앱 관리)를 선택하여 앱 관리 페이지로 돌아갑니다.
1. 만든 앱 옆에 있는 **查看**(보기)를 선택합니다.
1. **修改**(편집)을 선택합니다.
1. **앱 ID** 및 **앱 키** 를 복사합니다. 테넌트에 ID 공급자를 추가하려면 두 값이 모두 필요합니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>QQ를 ID 공급자로 구성

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **ID 공급자** 를 선택한 다음, **QQ(미리 보기)** 을 선택합니다.
1. **이름** 을 입력합니다. 예를 들면, *QQ* 과 같습니다.
1. **클라이언트 ID** 에 대해 이전에 만든 QQ 애플리케이션의 앱 ID를 입력합니다.
1. **클라이언트 암호** 에는 기록했던 앱 키를 입력합니다.
1. **저장** 을 선택합니다.

## <a name="add-qq-identity-provider-to-a-user-flow"></a>사용자 흐름에 QQ ID 공급자 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. QQ ID 공급자를 추가할 사용자 흐름을 클릭합니다.
1. **소셜 ID 공급자** 아래에서 **QQ** 를 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. QQ ID로 로그인하려면 등록이나 로그인 페이지에서 **QQ** 를 선택합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션됩니다. 여기에는 Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `QQSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-qq-as-an-identity-provider"></a>QQ를 ID 공급자로 구성

사용자가 QQ 계정을 사용하여 로그인할 수 있도록 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 QQ 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAuth2">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
5. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. QQ ID로 로그인하려면 등록이나 로그인 페이지에서 **QQ** 를 선택합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션됩니다. 여기에는 Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

::: zone-end
