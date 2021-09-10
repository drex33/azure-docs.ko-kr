---
title: Apple ID로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Apple ID로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c150fdae1820dee3ae440f4d4acdacff04e14e66
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530519"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Azure Active Directory B2C를 사용하여 Apple ID로 등록 설정 및 로그인(미리 보기)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Apple ID 애플리케이션 만들기

Azure Active Directory B2C(Azure AD B2C)에서 사용자가 Apple ID로 로그인할 수 있도록 설정하려면 [https://developer.apple.com](https://developer.apple.com)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [Apple로 로그인](https://developer.apple.com/sign-in-with-apple/get-started/)을 참조하세요. 이미 Apple 개발자 계정이 없는 경우 [Apple 개발자 프로그램](https://developer.apple.com/programs/enroll/)에서 등록할 수 있습니다.

1. 계정 자격 증명으로 [Apple 개발자 포털](https://developer.apple.com/account)에 로그인합니다.
1. 메뉴에서 **인증서, ID, 프로필** 을 선택한 다음 **(+)** 를 선택합니다.
1. **새 식별자 등록** 의 경우 **App ID** 를 선택한 다음 **계속** 을 선택합니다.
1. **유형 선택** 의 경우 **앱** 을 선택한 다음 **계속** 을 선택합니다.
1. **앱 ID 등록** 의 경우:
    1. **설명** 을 입력 
    1. `com.contoso.azure-ad-b2c` 등의 **번들 ID** 를 입력합니다. 
    1. **기능** 의 경우 기능 목록에서 **Apple로 로그인** 을 선택합니다. 
    1. 이 단계에서 **팀 ID**(앱 ID 접두사)를 기록해 둡니다. 나중에 필요합니다.
    1. **계속** 과 **등록** 을 차례로 선택합니다.
1. 메뉴에서 **인증서, ID, 프로필** 을 선택한 다음 **(+)** 를 선택합니다.
1. **새 식별자 등록** 의 경우 **서비스 ID** 를 선택한 다음 **계속** 을 선택합니다.
1. **서비스 ID 등록** 의 경우:
    1. **설명** 을 입력합니다. 이 설명은 동의 화면에서 사용자에게 표시됩니다.
    1. **식별자** 를 입력합니다. 예: `com.consoto.azure-ad-b2c-service` **서비스ID** 식별자를 기록해 둡니다. 식별자는 OpenID Connect 흐름을 위한 **클라이언트 ID** 입니다.
    1. **계속** 을 선택한 다음 **등록** 을 선택합니다.
1. **식별자** 에서 만든 식별자를 선택합니다.
1. **Apple로 로그인** 을 선택한 다음 **구성** 을 선택합니다.
    1. Apple로 로그인을 구성하려는 **기본 앱 ID** 를 선택합니다.
    1. **도메인 및 하위 도메인** 에서 `your-tenant-name.b2clogin.com`을 입력합니다. your-tenant-name을 테넌트 이름으로 바꿉니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name`을 입력합니다.
    1. **반환 URL** 에서 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로, `your-domain-name`을 사용자 지정 도메인으로 바꿉니다. 반환 URL은 모두 소문자여야 합니다.
    1. **다음** 을 선택한 다음 **완료** 를 선택합니다.
    1. 팝업 창이 닫히면 **계속** 을 선택한 다음 **저장** 을 선택합니다.

## <a name="creating-an-apple-client-secret"></a>Apple 클라이언트 암호 만들기

1. Apple 개발자 포털 메뉴에서 **키** 를 선택한 다음 **(+)** 를 선택합니다.
1. **새 키 등록** 의 경우:
    1. **키 이름** 을 입력합니다.
    1. **Apple로 로그인** 을 선택한 다음 **구성** 을 선택합니다.
    1. **기본 앱 ID** 의 경우 이전에 만든 앱을 선택한 다음 **저장** 을 선택합니다.
    1. **구성** 을 선택한 다음 **등록** 을 선택하여 키 등록 프로세스를 마칩니다. **키 ID** 를 적어둡니다. 사용자 흐름을 구성할 때 이 키가 필요 합니다.
1. **키 다운로드** 의 경우 **다운로드** 를 선택하여 키가 포함된 .p8 파일을 다운로드합니다.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Apple을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **ID 공급자** 를 선택한 다음, **Apple(미리 보기)** 을 선택합니다.
1. **이름** 은 **Apple로 로그인** 을 입력합니다. 
1. **Apple 개발자 ID(팀 ID)** 를 입력합니다.
1. **Apple 서비스 ID(클라이언트 ID)** 를 입력합니다.
1. **Apple 키 ID** 를 [Apple 클라이언트 암호 만들기](#creating-an-apple-client-secret)단계에서 입력합니다.
1. **Apple 인증서 데이터** 를 선택하고 업로드합니다.
1. **저장** 을 선택합니다.


> [!IMPORTANT] 
> - Apple로 로그인하려면 관리자가 6개월마다 클라이언트 암호를 갱신해야 합니다. 
> - 이 기능의 공개 미리 보기 기간 중에는 Apple 클라이언트 암호가 만료되면 수동으로 갱신해야 합니다. Apple ID 공급자 소셜 IDP 구성 페이지에서 사전에 경고가 표시되지만 미리 알림을 설정하는 것이 좋습니다. 
> - 비밀을 갱신해야 하는 경우 Azure Portal에서 Azure AD B2C를 열고 **ID 공급자** > **Apple** 로 이동한 다음 **비밀 갱신** 을 선택합니다.
> - [Apple에 로그인 단추를 제공](#customize-your-user-interface)하는 방법에 대한 지침을 따르세요.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>사용자 흐름에 Apple ID 공급자 추가

사용자가 Apple ID를 사용하여 로그인할 수 있도록 하려면 사용자 흐름에 Apple ID 공급자를 추가해야 합니다. Apple로 로그인은 사용자 흐름의 **추천** 버전에만 구성할 수 있습니다. 사용자 흐름에 Apple ID 공급자를 추가하는 방법은 다음과 같습니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Apple ID 공급자를 추가하고자 하는 사용자 흐름을 선택합니다. 
1. **소셜 ID 공급자** 에서 **Apple(미리 보기)** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. Apple ID로 로그인하려면 등록이나 로그인 페이지에서 **Apple** 을 선택합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, 이는 Azure AD B2C에서 반환된 토큰의 내용을 표시합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>클라이언트 암호에 서명

이전에 다운로드한 .p8 파일을 사용하여 JWT 토큰에 클라이언트 암호를 서명합니다. 생성하고 JWT를 서명할 수 있는 라이브러리가 많이 있습니다. [토큰을 만드는 Azure Function](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function)을 사용합니다. 

1. [Azure Function](../azure-functions/functions-create-function-app-portal.md)을 만듭니다.
1. **개발자** 에서 **코드 + 테스트** 를 선택합니다. 
1. [run.csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) 파일의 콘텐츠를 복사하여 편집기에 붙여넣습니다.
1. **저장** 을 선택합니다.
1. HTTP `POST` 요청을 하고 다음 정보를 입력합니다.

    - **appleTeamId**: Apple 개발자 팀 ID
    - **appleServiceId**: Apple 서비스 ID(및 클라이언트 ID)
    - **p8key**: PEM 형식 키. .p8 파일을 텍스트 편집기에서 열고 `-----BEGIN PRIVATE KEY-----`와 `-----END PRIVATE KEY-----` 사이에 있는 모든 내용을 줄 바꿈 없이 복사하면 이를 가져올 수 있습니다.
 
다음 json은 Azure 기능 호출의 예제입니다.

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Azure 기능은 적절한 형식의 서명된 클라이언트 암호 JWT로 응답합니다. 예:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
2. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **개요** 페이지에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 에서 **수동** 을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예: "AppleSecret". "B2C_1A_" 접두사가 키의 이름에 자동으로 추가됩니다.
1. **비밀** 에서 Azure Function을 통해 반환된 토큰의 값(JWT 토큰)을 입력합니다.
1. **키 사용** 으로는 **서명** 을 선택합니다.
1. **만들기** 를 선택합니다.

> [!IMPORTANT] 
> - Apple로 로그인하려면 관리자가 6개월마다 클라이언트 암호를 갱신해야 합니다.
> - Apple 클라이언트 암호가 만료되면 수동으로 갱신하고 정책 키에 새 값을 저장해야 합니다.
> - 직접 6개월 뒤에 미리 알림을 설정하여 새 클라이언트 암호를 생성하는 것이 좋습니다. 
> - [Apple에 로그인 단추를 제공](#customize-your-user-interface)하는 방법에 대한 지침을 따르세요.

## <a name="configure-apple-as-an-identity-provider"></a>Apple을 ID 공급자로 구성

사용자가 Apple ID를 사용하여 로그인하도록 설정하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Apple ID를 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Sign in with Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** 를 서비스 식별자로 설정합니다. 예들 들어 `com.consoto.azure-ad-b2c-service`입니다.
5. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. Apple ID로 로그인하려면 등록이나 로그인 페이지에서 **Apple** 을 선택합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, 이는 Azure AD B2C에서 반환된 토큰의 내용을 표시합니다.

::: zone-end

## <a name="customize-your-user-interface"></a>사용자 인터페이스 사용자 지정

[Apple으로 로그인을 제공](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/introduction/)하는 방법에 대한 지침을 따르세요. Apple은 사용자가 계정을 설정하고 로그인 하는 데 사용할 수 있는 몇가지 **Apple로 로그인하기**  단추들을 제공합니다. 필요한 경우 Apple으로 로그인을 할 수 있는 사용자 지정 단추를 만듭니다. [Apple 로그인 단추를 표시](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/buttons/)하는 방법을 알아봅니다.

Apple 사용자 인터페이스 지침에 맞게 조정 하려면 다음을 수행합니다.

- [HTML 템플릿을 사용하여 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)
- ID 공급자 이름을 [지역화](language-customization.md)합니다.

