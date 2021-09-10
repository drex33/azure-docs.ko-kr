---
title: 사용자 지정 정책을 사용하여 암호 변경 구성
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 사용자가 암호를 변경할 수 있게 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f5376574dd0329641ebc68b906bc5342437be8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777705"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 변경 구성

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C(Azure Active Directory B2C)에서는 로컬 계정으로 로그인한 사용자가 메일 확인을 통해 ID를 증명할 필요 없이 암호를 변경할 수 있게 설정할 수 있습니다. 암호 변경 흐름에는 다음 단계가 포함됩니다.

1. 사용자가 로컬 계정에 로그인합니다. 세션이 아직 활성 상태인 경우 Azure AD B2C는 사용자에게 권한을 부여하고 다음 단계로 건너뜁니다.
1. 사용자가 **이전 암호** 를 확인한 다음 **새 암호** 를 만들고 확인합니다.

![암호 변경 흐름](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> 암호 변경 흐름을 사용하면 사용자가 암호를 알고 있고 해당 암호를 변경하려는 경우에만 암호를 변경할 수 있습니다. 사용자가 암호를 잊어버린 경우를 지원하기 위해 [셀프 서비스 암호 재설정](add-password-reset-policy.md)을 사용하는 것이 좋습니다.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>필수 구성 요소

* [Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)에 있는 단계를 완료합니다.
* 아직 등록하지 않았다면 [Azure Active Directory B2C에 웹 애플리케이션을 등록](tutorial-register-applications.md)합니다.

## <a name="add-the-elements"></a>요소 추가

1. TrustframeworkExtensions.xml 파일을 열고 `oldPassword` 식별자를 사용하여 다음 **ClaimType** 요소를 [ClaimsSchema](claimsschema.md) 요소에 추가합니다.

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. [ClaimsProvider](claimsproviders.md) 요소에는 사용자를 인증하는 기술 프로필이 포함됩니다. **ClaimsProviders** 요소에 다음 클레임 공급자를 추가합니다.

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

3. [UserJourney](userjourneys.md) 요소는 애플리케이션을 조작할 때 사용자가 사용하는 경로를 정의합니다. `PasswordChange`로 식별된 **UserJourney** 에 이 요소가 없는 경우 **UserJourneys** 요소를 추가합니다.

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. *TrustFrameworkExtensions.xml* 정책 파일을 저장합니다.
5. 시작 팩과 함께 다운로드한 *ProfileEdit.xml* 파일을 복사하고 이름을 *ProfileEditPasswordChange.xml* 로 변경합니다.
6. 새 파일을 열고 **PolicyId** 특성을 고유한 값으로 업데이트합니다. 이 값은 정책 이름입니다. 예를 들면, *B2C_1A_profile_edit_password_change* 입니다.
7. 만든 새 사용자 경험의 ID와 일치하도록 `<DefaultUserJourney>`의 **ReferenceId** 특성을 수정합니다. 예를 들면, *PasswordChange* 입니다.
8. 변경 내용을 저장합니다.

## <a name="upload-and-test-the-policy"></a>정책 업로드 및 테스트

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택하여 Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 경험 프레임워크** 를 선택합니다.
1. 사용자 지정 정책 페이지에서 **정책 업로드** 를 클릭합니다.
1. **정책이 있는 경우 덮어쓰기** 를 선택한 후 TrustFrameworkExtensions.xml 파일을 찾아서 선택합니다.
1. **업로드** 를 클릭합니다.
1. 신뢰 당사자 파일(예: *ProfileEditPasswordChange.xml*)에 대해 5~7단계를 반복합니다.

### <a name="run-the-policy"></a>정책 실행

1. 변경한 정책을 엽니다. 예를 들면, *B2C_1A_profile_edit_password_change* 입니다.
2. **애플리케이션** 은 이전에 등록한 애플리케이션을 선택합니다. 토큰을 보려면 **회신 URL** 에 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행** 을 클릭합니다. 열려 있는 새 탭의 URL에서 "&prompt=login"을 제거하고 탭을 새로 고칩니다. 그런 다음, 이전에 만든 계정으로 로그인합니다. 이제 암호를 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)에서 샘플 정책을 찾습니다.
- [Azure AD B2C에서 암호 복잡성을 구성](password-complexity.md)하는 방법에 대해 알아봅니다.
- [암호 재설정 흐름](add-password-reset-policy.md)을 설정합니다.

::: zone-end
