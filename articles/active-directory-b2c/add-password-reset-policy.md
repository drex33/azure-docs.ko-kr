---
title: 암호 재설정 흐름 설정
titleSuffix: Azure AD B2C
description: Azure AD B2C(Azure Active Directory B2C)에서 암호 재설정 흐름을 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2d1c0dfd5a5fdb9573859c61421513c4dc91eb8e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130251208"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호 재설정 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[가입 및 로그인 경험](add-sign-up-and-sign-in-policy.md)에서 사용자는 **암호를 잊으셨나요?** 링크를 사용하여 자신의 암호를 다시 설정할 수 있습니다. 이 셀프 서비스 암호 재설정 흐름은 암호와 함께 [이메일 주소](sign-in-options.md#email-sign-in) 또는 [사용자 이름](sign-in-options.md#username-sign-in)을 로그인에 사용하는 Azure AD B2C(Azure Active Directory B2C)의 로컬 계정에 적용됩니다.

암호 재설정 흐름에는 다음 단계가 포함됩니다.

1. 가입 및 로그인 페이지에서 사용자가 **암호를 잊으셨나요?** 링크를 클릭합니다. Azure AD B2C가 암호 재설정 흐름을 시작합니다.
1. 표시되는 다음 대화 상자에서 사용자가 이메일 주소를 입력하고 **확인 코드 보내기** 를 선택합니다. Azure AD B2C에서 확인 코드를 사용자의 이메일 계정에 보냅니다. 사용자가 이메일에서 확인 코드를 복사하고, Azure AD B2C 암호 재설정 대화 상자에서 코드를 입력한 다음, **코드 확인** 을 선택합니다.
1. 그런 다음 사용자는 새 암호를 입력할 수 있습니다. (이메일이 확인된 후에도 사용자는 **이메일 변경** 단추를 선택할 수 있습니다. [이메일 변경 단추 숨기기](#hide-the-change-email-button)를 참조하세요.)

   ![암호 재설정 흐름에서 세 개의 대화 상자를 보여 주는 다이어그램](./media/add-password-reset-policy/password-reset-flow.png)

> [!TIP]
> 사용자가 암호를 잊어버리고 다시 설정하려는 경우 셀프 서비스 암호 재설정 흐름을 사용하여 암호를 변경할 수 있습니다. 다음 사용자 흐름 옵션 중 하나를 선택합니다.
> - 사용자가 암호를 알고 있지만 변경하려는 경우 [암호 변경 흐름](add-password-change-policy.md)을 사용합니다.
> - 사용자가 암호를 다시 설정하도록 강제하려는 경우(예: 처음 로그인하는 경우, 관리자가 암호를 다시 설정한 경우 또는 임의 암호를 사용하여 Azure AD B2C 마이그레이션된 후) [강제 암호 재설정](force-password-reset.md) 흐름을 사용합니다.

### <a name="hide-the-change-email-button"></a>이메일 변경 단추 숨기기

이메일이 확인된 후에도 사용자는 **이메일 변경** 을 선택하고, 다른 이메일 주소를 입력한 다음, 이메일 확인을 반복할 수 있습니다. **이메일 변경** 단추를 숨기려면 CSS를 수정하여 대화 상자에서 연결된 HTML 요소를 숨길 수 있습니다. 예를 들어 다음 CSS 항목을 selfAsserted.HTML에 추가하고, [HTML 템플릿을 사용하여 사용자 인터페이스를 사용자 지정](customize-ui-with-html.md)할 수 있습니다.

```html
<style type="text/css">
   .changeClaims
   {
     visibility: hidden;
   }
</style>
```

*selfAsserted.html* 에 있는 **이메일 변경** 단추의 기본 이름은 **changeclaims** 입니다. 단추 이름을 찾으려면 가입 페이지에서 [검사]와 같은 브라우저 도구를 사용하여 페이지 원본을 검사합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>셀프 서비스 암호 재설정(권장 사항)

현재 새 암호 재설정 환경은 가입 또는 로그인 정책의 일부입니다. 사용자가 **암호를 잊으셨나요?** 링크를 선택하면 즉시 암호 찾기 환경으로 보내집니다. 애플리케이션에서 더 이상 [AADB2C90118 오류 코드](#password-reset-policy-legacy)를 처리할 필요가 없으며 암호 재설정에 대한 별도의 정책이 필요하지 않습니다.

::: zone pivot="b2c-user-flow"

셀프 서비스 암호 재설정 환경은 로그인(권장) 또는 가입 및 로그인(권장) 사용자 흐름에 대해 구성할 수 있습니다. 이러한 사용자 흐름 중 하나를 설정하지 않은 경우 [가입 또는 로그인](add-sign-up-and-sign-in-policy.md) 사용자 흐름을 만듭니다.

가입 또는 로그인 사용자 흐름에 대한 셀프 서비스 암호 재설정을 설정하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 창의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 사용자 지정하려는 가입 또는 로그인 사용자 흐름(**권장** 형식)을 선택합니다.
1. 메뉴의 **설정** 아래에서 **속성** 을 선택합니다.
1. **암호 구성** 에서 **셀프 서비스 암호 재설정** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 왼쪽 메뉴의 **사용자 지정** 아래에서 **페이지 레이아웃** 을 선택합니다.
1. **페이지 레이아웃 버전** 에서 **2.1.3** 이상을 선택합니다.
1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

다음 섹션에서는 사용자 지정 정책에 셀프 서비스 암호 환경을 추가하는 방법에 대해 설명합니다. 이 샘플은 [사용자 지정 정책 시작 팩](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)에 포함된 정책 파일을 기반으로 합니다.

> [!TIP]
> [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset)에서 암호 재설정으로 가입 및 로그인 정책의 전체 샘플을 찾을 수 있습니다.

### <a name="indicate-that-a-user-selected-the-forgot-your-password-link"></a>사용자가 [암호를 잊으셨나요?] 링크를 선택했음을 나타내기

사용자가 **암호를 잊으셨나요?** 링크를 선택했음을 정책에 나타내려면 부울 클레임을 정의합니다. 클레임을 사용하여 사용자 경험을 **암호 찾기** 기술 프로필로 안내합니다. 또한 클레임을 토큰에 발급할 수 있으므로 애플리케이션에서 사용자가 암호 찾기 사용자 흐름을 사용하여 로그인했음을 검색합니다.

[클레임 스키마](claimsschema.md)에서 클레임을 선언합니다. 정책의 확장 파일(예: *SocialAndLocalAccounts/TrustFrameworkExtensions.xml*)을 엽니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 다음 클레임을 추가합니다.

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="isForgotPassword">
          <DisplayName>isForgotPassword</DisplayName>
          <DataType>boolean</DataType>
          <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
        </ClaimType>
      <!--
      </ClaimsSchema>
    </BuildingBlocks> -->
    ```

### <a name="upgrade-the-page-layout-version"></a>페이지 레이아웃 버전 업그레이드

[페이지 레이아웃 버전](contentdefinitions.md#migrating-to-page-layout) 2.1.2는 가입 또는 로그인 경험에서 셀프 서비스 암호 재설정 흐름을 사용하도록 설정하는 데 필요합니다. 페이지 레이아웃 버전을 업그레이드하려면 다음을 수행합니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ContentDefinitions](contentdefinitions.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. ID가 `api.signuporsignin`이 되도록 **ContentDefinition** 요소 내의 **DataURI** 요소를 수정합니다.

    ```xml
    <!-- 
    <BuildingBlocks>
      <ContentDefinitions> -->
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
        </ContentDefinition>
      <!-- 
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

클레임 변환 기술 프로필은 **isForgotPassword** 클레임을 시작합니다. 이 기술 프로필은 나중에 참조됩니다. 호출되면 **isForgotPassword** 클레임의 값을 `true`로 설정합니다. **ClaimsProviders** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다. 그 후에 다음 클레임 공급자를 추가합니다.  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

**SelfAsserted-LocalAccountSignin-Email** 기술 프로필의 **setting.forgotPasswordLinkOverride** 는 사용자 경험에서 실행되는 암호 재설정 클레임 교환을 정의합니다.

### <a name="add-the-password-reset-sub-journey"></a>암호 재설정 하위 경험 추가하기

이제 사용자는 사용자 경험에서 로그인, 가입 및 암호 재설정을 수행할 수 있습니다. 사용자 경험을 더 효율적으로 구성하기 위해 [하위 경험](subjourneys.md)을 사용하여 암호 재설정 흐름을 처리할 수 있습니다.

하위 경험은 사용자 경험에서 호출되며, 사용자에게 암호 재설정 환경을 제공하는 특정 단계를 수행합니다. 하위 경험이 완료되면 제어가 하위 경험을 시작한 오케스트레이션 단계에 반환되도록 `Call` 형식의 하위 경험을 사용합니다.

**SubJourneys** 요소를 찾습니다. 이 요소가 없으면 **User Journeys** 요소 뒤에 추가합니다. 그런 다음, 다음과 같은 하위 경험을 추가합니다.

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>사용자 경험 준비

다음으로, **암호를 잊으셨나요?** 링크를 [암호 찾기] 하위 경험에 연결합니다. **CombinedSignInAndSignUp** 단계의 **ClaimsProviderSelection** 요소에서 암호 찾기 하위 경험 ID를 참조합니다.

**CombinedSignInAndSignUp** 단계가 있는 사용자 고유의 사용자 지정 사용자 경험이 없는 경우 다음 단계를 완료하여 기존의 가입 또는 로그인 사용자 경험을 복제합니다. 그렇지 않으면 다음 섹션을 계속 진행합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
1. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
1. *TrustFrameworkExtensions.xml* 을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 2단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 붙여넣어 **UserJourneys** 요소의 자식 요소를 만듭니다.
1. 사용자 경험 ID의 이름을 바꿉니다. 예들 들어 `Id="CustomSignUpSignIn"`입니다.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>암호 찾기 링크를 암호 찾기 하위 경험에 연결

사용자 경험에서 암호 찾기 하위 경험을 **ClaimsProviderSelection** 으로 나타낼 수 있습니다. 이 요소를 추가하면 **암호를 잊으셨나요?** 링크가 암호 찾기 하위 경험에 연결됩니다.

1. 사용자 경험에서 `Type="CombinedSignInAndSignUp"` 또는 `Type="ClaimsProviderSelection"`을 포함하는 오케스트레이션 단계 요소를 찾습니다. 일반적으로 첫 번째 오케스트레이션 단계입니다. **ClaimsProviderSelections** 요소에는 사용자가 로그인하는 데 사용할 수 있는 ID 공급자의 목록이 포함되어 있습니다. 다음 줄을 추가합니다.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. 다음 오케스트레이션 단계에서 **ClaimsExchange** 요소를 추가합니다. 다음 줄을 추가합니다.

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```

1. 현재 단계와 다음 단계 사이에 다음 오케스트레이션 단계를 추가합니다. 추가하는 새 오케스트레이션 단계에서 **isForgotPassword** 클레임이 있는지 여부를 확인합니다. 클레임이 존재하는 경우 [암호 재설정 하위 경험](#add-the-password-reset-sub-journey)을 호출합니다. 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```

1. 새 오케스트레이션 단계를 추가한 후, 1부터 N까지의 정수를 건너뛰지 말고 순차적으로 단계 번호를 다시 매깁니다.

### <a name="set-the-user-journey-to-be-executed"></a>실행할 사용자 경험 설정

이제 사용자 경험을 수정했거나 만들었으므로 **신뢰 당사자** 섹션에서 Azure AD B2C를 통해 이 사용자 지정 정책에 대해 실행할 경험을 지정합니다. [RelyingParty](relyingparty.md) 요소에서 **DefaultUserJourney** 요소를 찾습니다. **ClaimsProviderSelections** 를 추가한 사용자 경험의 ID와 일치하도록 **DefaultUserJourney ReferenceId** 를 업데이트합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>앱에 암호 찾기 흐름 표시

애플리케이션에서 사용자가 암호 찾기 사용자 흐름을 사용하여 로그인했는지 여부를 검색해야 할 수 있습니다. **isForgotPassword** 클레임에는 사용자가 수행했음을 나타내는 부울 값이 포함되어 있습니다. 클레임은 애플리케이션에 보낸 토큰에서 발급될 수 있습니다. 필요한 경우 **isForgotPassword** 를 **Relying Party** 섹션의 출력 클레임에 추가합니다. 애플리케이션에서 **isForgotPassword** 클레임을 검사하여 사용자가 암호를 다시 설정하는지 여부를 확인할 수 있습니다.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>사용자 지정 정책 업로드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 창의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. 메뉴의 **정책** 아래에서 **Identity Experience Framework** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택합니다. 다음 순서에 따라 변경한 두 개의 정책 파일을 업로드합니다.
   1. 확장 정책(예: *TrustFrameworkExtensions.xml*)
   1. 신뢰 당사자 정책(예: *SignUpSignIn.xml*)

::: zone-end

### <a name="test-the-password-reset-flow"></a>암호 재설정 흐름 테스트

1. 테스트하려는 가입 또는 로그인 사용자 흐름(권장 형식)을 선택합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 에 대해 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. 가입 또는 로그인 페이지에서 **암호를 잊으셨나요?** 를 선택합니다.
1. 이전에 만든 계정의 이메일 주소를 확인한 다음, **계속** 을 선택합니다.
1. 표시되는 대화 상자에서 사용자의 암호를 변경한 다음, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`에 반환되고 브라우저에서 이를 표시합니다.
1. 반환 토큰의 **isForgotPassword** 클레임 값을 확인합니다. 이 값이 있고 `true`로 설정된 경우 사용자가 암호를 다시 설정한 것입니다.

## <a name="password-reset-policy-legacy"></a>암호 재설정 정책(레거시)

[셀프 서비스 암호 재설정](#self-service-password-reset-recommended) 환경이 사용하도록 설정되지 않은 경우 이 링크를 선택해도 암호 재설정 사용자 흐름을 자동으로 트리거하지 않습니다. 대신 `AADB2C90118` 오류 코드가 애플리케이션에 반환됩니다. Azure AD B2C 암호 재설정 사용자 흐름을 인증하려면 애플리케이션에서 인증 라이브러리를 다시 초기화하여 이 오류 코드를 처리해야 합니다.

다음 다이어그램에서는 이 프로세스를 보여 줍니다.

1. 애플리케이션에서 사용자가 **로그인** 을 선택합니다. 앱에서 권한 부여 요청을 시작하고 사용자가 로그인을 완료할 수 있도록 Azure AD B2C로 리디렉션합니다. 권한 부여 요청에서 *B2C_1_signup_signin* 과 같은 가입 또는 로그인 정책 이름을 지정합니다.
1. 사용자가 **암호를 잊으셨나요?** 링크를 선택합니다. Azure AD B2C에서 `AADB2C90118` 오류 코드를 애플리케이션에 반환합니다.
1. 애플리케이션이 오류 코드를 처리하고 새 권한 부여 요청을 시작합니다. 권한 부여 요청이 *B2C_1_pwd_reset* 과 같은 암호 재설정 정책 이름을 지정합니다.

    ![레거시 암호 재설정 사용자 흐름을 보여 주는 다이어그램](./media/add-password-reset-policy/password-reset-flow-legacy.png)

사용자 흐름이 연결되는 방식을 보여 주는 기본 [ASP.NET 샘플](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)을 볼 수 있습니다.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>암호 재설정 사용자 흐름 만들기

애플리케이션 사용자가 암호를 다시 설정할 수 있도록 하려면 암호 재설정 사용자 흐름을 만듭니다.

1. Azure Portal에서 Azure AD B2C 테넌트 개요로 이동합니다.
1. 왼쪽 메뉴의 **정책** 아래에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.
1. **사용자 흐름 만들기** 에서 **암호 재설정** 사용자 흐름을 선택합니다.
1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다.
1. **이름** 에 대해 사용자 흐름에 대한 이름을 입력합니다. 예를 들어 *passwordreset1* 과 같습니다.
1. **ID 공급자** 에서 **사용자 이름을 사용하여 암호 재설정** 또는 **이메일 주소를 사용하여 암호 재설정** 을 사용하도록 설정합니다.
1. **다단계 인증** 아래에서 사용자가 두 번째 인증 방법을 사용하여 ID를 확인하도록 하려면 방법 유형과 다단계 인증을 적용할 시기를 선택합니다. [자세히 알아봅니다](multi-factor-authentication.md).
1. **조건부 액세스** 아래에서 Azure AD B2C 테넌트에 대한 조건부 액세스 정책을 구성했고 이 사용자 흐름에서 이러한 정책을 사용하도록 하려면 **조건부 액세스 정책 적용** 확인란을 선택합니다. 정책 이름은 지정하지 않아도 됩니다. [자세히 알아보기](conditional-access-user-flow.md?pivots=b2c-user-flow).
1. **애플리케이션 클레임** 아래에서 **자세히 표시** 를 선택합니다. 애플리케이션에 다시 보내는 권한 부여 토큰에 반환되도록 하려는 클레임을 선택합니다. 예를 들어 **사용자의 개체 ID** 를 선택합니다.
1. **확인** 을 선택합니다.
1. **만들기** 를 선택하여 사용자 흐름을 추가합니다. *B2C_1* 이라는 접두사가 이름에 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

사용자 흐름을 테스트하려면 다음을 수행합니다.

1. 만든 사용자 흐름을 선택합니다. 사용자 흐름 개요 페이지에서 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 에 대해 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 은 `https://jwt.ms`이어야 합니다.
1. **사용자 흐름 실행** 을 선택하고, 이전에 만든 계정의 이메일 주소를 확인한 다음, **계속** 을 선택합니다.
1. 암호를 변경한 다음, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`에 반환되고 브라우저에서 이를 표시합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>암호 재설정 정책 만들기

사용자 지정 정책은 사용자 경험을 정의하기 위해 Azure AD B2C 테넌트에 업로드하는 XML 파일 세트입니다. 가입 및 로그인, 암호 재설정, 프로필 편집 정책을 포함하여 미리 작성된 여러 정책이 있는 시작 팩이 제공됩니다. 자세한 내용은 [Azure AD B2C에서 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)를 참조하세요.

::: zone-end

## <a name="troubleshoot-azure-ad-b2c-user-flows-and-custom-policies"></a>Azure AD B2C 사용자 흐름 및 사용자 지정 정책 문제 해결
애플리케이션은 Azure B2C 서비스에서 들어오는 특정 오류를 처리해야 합니다. [Azure AD B2C의 사용자 흐름 및 사용자 지정 정책 문제를 해결하는 방법](troubleshoot.md)을 알아보세요.

## <a name="next-steps"></a>다음 단계

[암호 재설정 강제 적용](force-password-reset.md)을 설정합니다.
