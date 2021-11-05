---
title: 세션 동작 구성 - Azure Active Directory B2C
description: Azure Active Directory B2C에서 세션 동작을 구성하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/05/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 59d14afdbe6f4949f2761ddccb3a48a4770c3bd1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028180"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 세션 동작 구성

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

SSO(Single Sign-On)는 사용자가 Azure AD B2C(Azure Active Directory B2C)의 애플리케이션 간에 로그인할 때 보안 및 편리함을 제공합니다. 이 문서는 Azure AD B2C에 사용되는 Single Sign-On 방법을 설명하고, 정책을 구성할 때 가장 적합한 SSO 방법을 선택하는 데 유용합니다.

Single Sign-On을 통해 사용자는 단일 계정으로 한 번 로그인하고 여러 애플리케이션에 액세스할 수 있습니다. 애플리케이션은 플랫폼 또는 도메인 이름에 관계 없이 웹, 모바일 또는 단일 페이지 애플리케이션일 수 있습니다.

사용자가 애플리케이션에 처음으로 로그인하면 Azure AD B2C는 쿠키 기반 세션을 유지합니다. 후속 인증 요청 시 Azure AD B2C는 쿠키 기반 세션을 읽고 유효성을 검사한 후, 사용자에게 다시 로그인하라는 메시지를 표시하지 않고 액세스 토큰을 발급합니다. 쿠키 기반 세션이 만료되거나 유효하지 않게 되면 사용자에게 다시 로그인하라는 메시지가 표시됩니다.  

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Azure AD B2C 세션 개요

Azure AD B2C와의 통합에는 세 가지 유형의 SSO 세션이 포함됩니다.

- **Azure AD B2C** - Azure AD B2C에서 관리하는 세션
- **페더레이션 ID 공급자** - ID 공급자(예: Facebook, Salesforce 또는 Microsoft 계정)가 관리하는 세션
- **애플리케이션** - 웹, 모바일 또는 단일 페이지 애플리케이션에서 관리하는 세션

![SSO 세션](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C 세션 

사용자가 로컬 또는 소셜 계정으로 성공적으로 인증하면 Azure AD B2C는 사용자의 브라우저에 쿠키 기반 세션을 저장합니다. 쿠키는 `https://contoso.b2clogin.com`과 같은 Azure AD B2C 테넌트 도메인 이름 아래에 저장됩니다.

사용자가 처음에 페더레이션된 계정으로 로그인한 후 세션 기간(TTL(Time-to-Live)) 동안 동일한 앱 또는 다른 앱에 로그인하는 경우 Azure AD B2C는 페더레이션된 ID 공급자로부터 새 액세스 토큰을 획득하려고 시도합니다. 페더레이션된 ID 공급자 세션이 만료되거나 잘못된 경우 페더레이션 ID 공급자는 사용자에게 자격 증명을 입력하라는 메시지를 표시합니다. 세션이 아직 활성 상태이면(또는 사용자가 페더레이션된 계정 대신 로컬 계정으로 로그인한 경우) Azure AD B2C는 사용자에게 권한을 부여하고 추가 프롬프트를 제거합니다.

세션 TTL, Azure AD B2C가 정책과 애플리케이션 간에 세션을 공유하는 방법을 비롯한 세션 동작을 구성할 수 있습니다.

### <a name="federated-identity-provider-session"></a>페더레이션된 ID 공급자 세션

소셜 또는 엔터프라이즈 ID 공급자는 자체 세션을 관리합니다. 쿠키는 ID 공급자의 도메인 이름(예: `https://login.salesforce.com`)으로 저장됩니다. Azure AD B2C는 페더레이션된 ID 공급자 세션을 제어하지 않습니다. 대신, 세션 동작은 페더레이션된 ID 공급자에 의해 결정됩니다. 

다음 시나리오를 고려하세요.

1. 사용자가 Facebook에 로그인하여 피드를 확인합니다.
2. 나중에 사용자가 애플리케이션을 열고 로그인 프로세스를 시작합니다. 애플리케이션은 사용자를 Azure AD B2C로 리디렉션하여 로그인 프로세스를 완료합니다.
3. Azure AD B2C 가입 또는 로그인 페이지에서 사용자는 Facebook 계정으로 로그인하도록 선택합니다. 사용자가 Facebook으로 리디렉션됩니다. Facebook에 활성 세션이 있는 경우 사용자에게 자격 증명을 제공하라는 메시지가 표시되지 않으며 Facebook 토큰을 사용하여 Azure AD B2C로 즉시 리디렉션됩니다.

### <a name="application-session"></a>애플리케이션 세션

웹, 모바일 또는 단일 페이지 애플리케이션은 OAuth2 액세스 토큰, ID 토큰 또는 SAML 토큰으로 보호할 수 있습니다. 사용자가 앱의 보호된 리소스에 액세스하려고 하면 앱은 애플리케이션 쪽에 활성 세션이 있는지 여부를 확인합니다. 앱 세션이 없거나 세션이 만료된 경우 앱은 사용자를 Azure AD B2C 로그인 페이지로 안내합니다.

애플리케이션 세션은 애플리케이션 도메인 이름(예: `https://contoso.com`)으로 저장된 쿠키 기반 세션이 될 수 있습니다. 모바일 애플리케이션은 다른 방식으로 세션을 저장할 수 있지만 비슷한 방법을 사용합니다.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Azure AD B2C 세션 동작 구성

다음을 포함하여 Azure AD B2C 세션 동작을 구성할 수 있습니다.

- **웹앱 세션 수명(분)** - 인증에 성공한 후 Azure AD B2C 세션 쿠키가 사용자 브라우저에 저장되는 시간입니다. 세션 수명은 최대 24시간으로 설정할 수 있습니다.

- **웹앱 세션 제한 시간** - 세션 수명이 세션 수명 설정 또는 KMSI(로그인 상태 유지) 설정에 의해 연장되는 방식을 나타냅니다.
  - **롤링** - 사용자가 쿠키 기반 인증(기본값)을 수행할 때마다 세션이 연장됨을 나타냅니다.
  - **절대** - 지정된 기간 후에 사용자가 다시 인증해야 함을 나타냅니다.

- **Single Sign-On 구성** - 다음 범위를 사용하여 Azure AD B2C 세션을 구성할 수 있습니다.
  - **테넌트** - 이 설정은 기본값입니다. 이 설정을 사용하여 B2C 테넌트의 여러 애플리케이션 및 사용자 흐름이 동일한 사용자 세션을 공유할 수 있습니다. 예를 들어 사용자가 애플리케이션에 로그인하면 액세스의 관점에서 볼 때 다른 앱에도 원활하게 로그인할 수 있습니다.
  - **애플리케이션** - 이 설정을 통해 다른 애플리케이션과 독립적으로 애플리케이션에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 Contoso Groceries에 이미 로그인했는지 여부에 관계 없이 Contoso Pharmacy에 로그인하도록 하려면 이 설정을 사용할 수 있습니다.
  - **정책** - 이 설정을 통해 이를 사용하는 애플리케이션과 독립적으로 사용자 흐름에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 이미 로그인하고 MFA(다단계 인증) 단계를 완료한 경우 사용자 흐름에 연결된 세션이 만료되지 않는 한 여러 애플리케이션의 높은 수준의 보안 부분에 대한 액세스를 부여 받을 수 있습니다.
  - **표시 안 함** - 사용자가 모든 정책 실행에서 전체 사용자 흐름을 통해 강제로 실행하도록 합니다.
- **KMSI(로그인 유지)** - 영구 쿠키를 사용하여 세션 수명을 연장합니다. 이 기능을 사용하도록 설정하고 사용자가 이 기능을 선택하면 브라우저를 닫았다가 다시 여는 경우에도 세션이 활성 상태로 유지됩니다. 사용자가 로그아웃하는 경우에만 세션이 해지됩니다. KMSI 기능은 로컬 계정으로 로그인하는 경우에만 적용됩니다. KMSI 기능이 세션 수명보다 우선적으로 적용됩니다.

::: zone pivot="b2c-user-flow"

세션 동작을 구성하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. 필요에 따라 **웹앱 세션 수명(분)** , **웹앱 세션 시간 제한**, **Single Sign-On 구성** 및 **로그아웃 요청에서 ID 토큰 요구** 를 구성합니다.
1. **저장** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

세션 동작 및 SSO 구성을 변경하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가합니다.  **UserJourneyBehaviors** 요소는 **DefaultUserJourney** 바로 뒤에 있어야 합니다. **UserJourneyBehavors** 요소는 다음 예와 같아야 합니다.

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>KMSI(로그인 유지) 사용

Azure AD B2C 디렉터리에 로컬 계정이 있는 웹 및 네이티브 애플리케이션 사용자에 대해 KMSI 기능을 사용하도록 설정할 수 있습니다. 이 기능을 사용하도록 설정하면 브라우저를 닫은 후에도 세션이 활성 상태로 유지되도록 로그인 상태 유지를 선택할 수 있습니다. 세션은 [영구 쿠키](cookie-definitions.md)를 설정하여 유지 관리됩니다. KMSI를 선택하는 사용자는 브라우저를 다시 열 수 있으며 사용자 이름과 암호를 다시 입력하라는 메시지가 표시되지 않습니다. 사용자가 로그아웃하면 이 액세스 권한(영구 쿠키)이 철회됩니다. 

![로그인 상태 유지 확인란을 표시하는 가입 로그인 페이지의 예](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

KMSI는 개별 사용자 흐름 수준에서 구성할 수 있습니다. 사용자 흐름에 대해 KMSI를 사용하도록 설정하기 전에 다음 사항을 고려합니다.

- KMSI는 **권장** 되는 버전의 가입 및 로그인(SUSI), 로그인 및 프로필 편집 사용자 흐름에 대해서만 지원됩니다. 현재 이러한 사용자 흐름의 **표준(레거시)** 또는 **레거시 미리 보기 - v2** 버전이 있고 KMSI를 사용하도록 설정하려는 경우 이러한 사용자 흐름의 새로운 **권장** 버전을 만들어야 합니다.
- KMSI는 암호 재설정 또는 가입 사용자 흐름에서 지원되지 않습니다.
- 테넌트의 모든 애플리케이션에 대해 KMSI를 사용하도록 설정하려면 테넌트의 모든 사용자 흐름에 대해 KMSI를 사용하도록 설정하는 것이 좋습니다. 세션 중에 사용자에게 여러 정책이 표시될 수 있기 때문에 KMSI를 사용하도록 설정하지 않은 정책을 찾을 수 있습니다. 이 경우 세션에서 KMSI 쿠키가 제거됩니다.
- 퍼블릭 컴퓨터에서는 KMSI를 사용하도록 설정하면 안 됩니다.

### <a name="configure-kmsi-for-a-user-flow"></a>사용자 흐름에 대해 KMSI 구성

사용자 흐름에 대해 KMSI를 사용하도록 설정하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서  **모든 서비스** 를 선택하고  **Azure AD B2C** 를 검색하여 선택합니다.
1.  **사용자 흐름(정책)** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1.  **속성** 을 선택합니다.

1.  **세션 동작** 에서 **로그인 상태 유지 세션** 을 선택합니다. **로그인 세션 유지(일)** 옆에 1에서 90 사이의 값을 입력하여 세션을 열어 둘 수 있는 일 수를 지정합니다.

   ![로그인 상태 유지 세션 사용](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

사용자는 공용 컴퓨터에서 이 옵션을 사용하면 안됩니다.

### <a name="configure-the-page-identifier"></a>페이지 식별자 구성

KMSI를 사용하도록 설정하려면 콘텐츠 정의 `DataUri` 요소를 [페이지 식별자](contentdefinitions.md#datauri) `unifiedssp` 및 [페이지 버전](page-layout.md) *1.1.0* 이상으로 설정합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다. 이 확장 파일은 사용자 지정 정책 시작 팩에 포함된 정책 파일 중 하나로, 필수 구성 요소인 [사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)에서 가져와야 합니다.
1. **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 정책의 **BuildingBlocks** 요소에 **contentdefinitions** 요소를 추가합니다.

    사용자 지정 정책은 다음 코드 조각과 비슷해야 합니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>자체 어설션된 기술 프로필에 메타데이터 추가

KMSI 확인란을 가입 및 로그인 페이지에 추가하려면 `setting.enableRememberMe` 메타데이터를 true로 설정합니다. 확장 파일에서 SelfAsserted-LocalAccountSignin-Email 기술 프로필을 재정의합니다.

1. ClaimsProviders 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. ClaimsProviders 요소에 다음 클레임 공급자를 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 확장 파일을 저장합니다.

### <a name="configure-a-relying-party-file"></a>신뢰 당사자 파일 구성

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다. KeepAliveInDays 매개 변수를 사용하여 KMSI(로그인 유지) 세션 쿠키가 유지되는 기간을 구성할 수 있습니다. 예를 들어, 이 값을 30으로 설정하면 KMSI 세션 쿠키가 30일 동안 유지됩니다. 값 범위는 1~90일입니다. 값을 0으로 설정하면 KMSI 기능이 해제됩니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
1. 아직 존재하지 않는 경우 자식 노드 `<UserJourneyBehaviors>`을 `<RelyingParty>` 노드에 추가합니다. `<DefaultUserJourney ReferenceId="User journey Id" />`의 바로 다음에 있어야 합니다(예: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`).
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

KeepAliveInDays의 값을 아래 예제에 표시된 것과 같이 비교적 오랜 기간(30일)으로 설정할 수 있는 반면 SessionExpiryInSeconds의 값을 짧은 기간(1200초)으로 설정하는 것이 좋습니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>로그아웃

애플리케이션에서 사용자를 로그아웃시키려는 경우 애플리케이션의 쿠키를 삭제하거나 그렇지 않은 경우 사용자로 세션을 지우는 것은 충분하지 않습니다. Azure AD B2C로 사용자를 리디렉션하여 로그아웃하도록 해야 합니다. 그러지 않으면 사용자가 자격 증명을 다시 입력하지 않고 애플리케이션을 다시 인증할 수 있습니다.

로그아웃 요청 시 Azure AD B2C는 다음을 수행합니다.

::: zone pivot="b2c-user-flow"
1. Azure AD B2C 쿠키 기반 세션을 무효화합니다.
1. 페더레이션된 ID 공급자에서 로그아웃하려고 합니다.
::: zone-end

::: zone pivot="b2c-custom-policy"
1. Azure AD B2C 쿠키 기반 세션을 무효화합니다.
1. 페더레이션된 ID 공급자에서 로그아웃하려고 합니다.
   - OpenId Connect - ID 공급자의 잘 알려진 구성 엔드포인트가 `end_session_endpoint` 위치를 지정하는 경우입니다. 로그아웃 요청은 `id_token_hint` 매개 변수를 전달하지 않습니다. 페더레이션된 ID 공급자에 이 매개 변수가 필요한 경우에는 로그아웃 요청이 실패합니다.
   - OAuth2 - [ID 공급자 메타데이터](oauth2-technical-profile.md#metadata)에 `end_session_endpoint` 위치가 포함된 경우입니다.
   - SAML - [ID 공급자 메타데이터](identity-provider-generic-saml.md)에 `SingleLogoutService` 위치가 포함된 경우입니다.
1. 필요에 따라 다른 애플리케이션에서 로그아웃합니다. 자세한 내용은 [단일 로그아웃](#single-sign-out) 섹션을 참조하세요.

> [!NOTE]
> ID 공급자 기술 프로필 메타데이터 `SingleLogoutEnabled`를 `false`로 설정하여 페더레이션 ID 공급자에서 로그아웃을 사용하지 않도록 설정할 수 있습니다.
::: zone-end

로그아웃할 경우 Azure AD B2C를 사용하여 사용자의 Single Sign-On 상태가 취소되지만 사용자의 소셜 IDP(ID 공급자) 세션에서 사용자가 로그아웃되지 않을 수 있습니다. 사용자가 후속 로그인 중에 동일한 ID 공급자를 선택하면 자격 증명을 입력하지 않고도 다시 인증될 수 있습니다. 사용자가 애플리케이션에서 로그아웃하려는 경우 반드시 Facebook 계정을 로그아웃하려는 것은 아닙니다. 그러나 로컬 계정을 사용하는 경우 사용자의 세션이 제대로 종료됩니다.

::: zone pivot="b2c-custom-policy"

## <a name="single-sign-out"></a>Single Sign-Out 

사용자를 [Azure AD B2C 로그아웃 엔드포인트](openid-connect.md#send-a-sign-out-request)(OAuth2 및 OpenID Connect 모두용)로 리디렉션하거나 `LogoutRequest`(SAML용)를 보내면 Azure AD B2C가 브라우저에서 사용자 세션을 지웁니다. 하지만 사용자는 인증을 위해 Azure AD B2C를 사용하는 다른 애플리케이션에 여전히 로그인되어 있을 수 있습니다. 활성 세션이 있는 모든 애플리케이션에서 사용자를 로그아웃하기 위해 Azure AD B2C는 *SLO(단일 로그아웃)* 라고도 하는 *단일 로그아웃* 을 지원합니다.

로그아웃하는 동안 Azure AD B2C는 사용자가 현재 로그인되어 있는 모든 애플리케이션의 등록된 로그아웃 URL에 HTTP 요청을 동시에 보냅니다.

### <a name="configure-your-custom-policy"></a>사용자 지정 정책 구성

단일 로그아웃을 지원하려면 JWT 및 SAML 토큰 발급자 기술 프로필에서 다음을 지정해야 합니다.

- 프로토콜 이름(예: `<Protocol Name="OpenIdConnect" />`)
- 세션 기술 프로필에 대한 참조(예: `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />`)입니다.

다음 예제에서는 단일 로그아웃을 사용하는 JWT 및 SAML 토큰 발급자를 보여 줍니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="configure-your-application"></a>애플리케이션 구성

애플리케이션이 아웃에 참여하려면:

- [SAML 서비스 공급자](saml-service-provider.md)의 경우 [SAML 메타데이터 문서의 SingleLogoutService 위치](saml-service-provider.md#override-or-set-the-logout-url-optional)로 애플리케이션을 구성합니다. 앱 등록 `logoutUrl`을 구성할 수도 있습니다. 자세한 내용은 [로그아웃 URL 설정](saml-service-provider.md#override-or-set-the-logout-url-optional)을 참조하세요.
- OpenID Connect 또는 OAuth2 애플리케이션의 경우 앱 등록 매니페스트의 `logoutUrl` 특성을 설정합니다. 로그아웃 URL을 구성하려면:
    1. Azure AD B2C 메뉴에서 **앱 등록** 을 선택합니다.
    1. 애플리케이션 등록을 선택합니다.
    1. **관리** 에서 **인증** 을 선택합니다.
    1. **프런트 채널 로그아웃 URL** 에서 로그아웃 URL을 구성합니다.

### <a name="handling-single-sign-out-requests"></a>싱글 사인아웃 요청 처리

Azure AD B2C는 로그아웃 요청을 수신하면 전면 채널 HTML iframe을 사용하여 사용자가 현재 로그인되어 있는 각 참여 애플리케이션의 등록된 로그아웃 URL로 HTTP 요청을 보냅니다. 로그아웃 요청을 트리거하는 애플리케이션은 이 로그아웃 메시지를 받지 않습니다. 애플리케이션은 사용자를 식별하는 애플리케이션 세션을 지워서 로그아웃 요청에 응답해야 합니다.

- OpenID Connect 및 OAuth2 애플리케이션의 경우 Azure AD B2C는 등록된 로그아웃 URL에 HTTP GET 요청을 보냅니다.
- SAML 애플리케이션의 경우 Azure AD B2C는 SAML 로그아웃 요청을 등록된 로그아웃 URL로 보냅니다.

모든 애플리케이션에 로그아웃 알림이 전송되면 Azure AD B2C는 다음 중 하나를 수행합니다.

- OpenID Connect 또는 OAuth2 애플리케이션의 경우 사용자는 초기 요청에 지정된(선택 사항) `state` 매개 변수를 포함하여 요청된 `post_logout_redirect_uri`로 리디렉션됩니다. 예: `https://contoso.com/logout?state=foo`.
- SAML 애플리케이션의 경우 HTTP POST를 통해 SAML 로그아웃 응답이 처음에 로그아웃 요청을 보낸 애플리케이션으로 전송됩니다.

::: zone-end

### <a name="secure-your-logout-redirect"></a>로그아웃 리디렉션 보안

로그아웃 후 사용자는 애플리케이션에 대해 지정된 회신 URL에 관계없이 `post_logout_redirect_uri` 매개 변수에 지정된 URI로 리디렉션됩니다. 그러나 유효한 `id_token_hint`가 전달되고 **로그아웃 요청에서 ID 토큰 요구** 가 켜져 있는 경우 Azure AD B2C는 리디렉션을 수행하기 전 `post_logout_redirect_uri` 값이 애플리케이션의 구성된 리디렉션 URI 중 하나와 일치하는지 확인합니다. 애플리케이션에 일치하는 회신 URL이 구성되지 않은 경우 오류 메시지가 표시되고 사용자는 리디렉션되지 않습니다. 

::: zone pivot="b2c-user-flow"

로그아웃 요청에 ID 토큰을 요구하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. **로그아웃 요청에 ID 토큰 필요** 를 사용하도록 설정합니다.
1. **Azure AD B2C** 로 돌아갑니다.
1. **앱 등록** 을 선택하고 애플리케이션을 선택합니다.
1. **인증** 을 선택합니다.
1. **로그아웃 URL** 텍스트 상자에 사후 로그아웃 리디렉션 URI를 입력하고 **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

로그아웃 요청에 ID 토큰을 요구하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가합니다. 그런 다음, **SingleSignOn** 요소의 **EnforceIdTokenHintOnLogout** 을 `true`로 설정합니다. **UserJourneyBehaviors** 요소는 다음 예제와 같이 표시됩니다.

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

애플리케이션 로그아웃 URL을 구성하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 을 선택하고 애플리케이션을 선택합니다.
1. **인증** 을 선택합니다.
1. **로그아웃 URL** 텍스트 상자에 사후 로그아웃 리디렉션 URI를 입력하고 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에서 토큰을 구성](configure-tokens.md)하는 방법을 알아봅니다.
