---
title: 로그인 흐름 설정
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C에서 로그인 흐름을 설정하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 5177d06ce6507f23dfbc31b9825ce38d85422bb6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008178"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 로그인 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>로그인 흐름 개요

로그인 정책을 통해 사용자는 다음을 수행할 수 있습니다.

* 사용자가 Azure AD B2C 로컬 계정으로 로그인할 수 있음
* 소셜 계정을 사용하여 가입 또는 로그인
* 암호 재설정
* 사용자는 Azure AD B2C 로컬 계정에 가입할 수 없습니다. 관리자는 계정을 만들기 위해 [Azure Portal](manage-users-portal.md#create-a-consumer-user) 또는 [MS Graph API](microsoft-graph-operations.md)를 사용할 수 있습니다.

![프로필 편집 흐름](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>사전 요구 사항

- 아직 등록하지 않았다면 [Azure Active Directory B2C에 웹 애플리케이션을 등록](tutorial-register-applications.md)합니다.
- [Azure Active Directory B2C에서 사용자 흐름 및 사용자 지정 정책 만들기](tutorial-create-user-flows.md) 단계를 완료합니다.

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>로그인 사용자 흐름 만들기

로그인 정책을 추가하려면 다음을 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 아래에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.
1. **사용자 흐름 만들기** 페이지에서 **로그인** 사용자 흐름을 선택합니다.
1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다. (사용자 흐름 버전에 대해 [자세히 알아보세요](user-flow-versions.md).)
1. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *signupsignin1* 과 같습니다.
1. **ID 공급자** 에서 하나 이상의 ID 공급자를 선택합니다.

   * **로컬 계정** 에서 **메일 로그인**, **사용자 ID 로그인**, **전화 로그인**, **전화/메일 로그인** 또는 **사용자 ID/메일 로그인** 또는 **없음** 중 하나를 선택합니다. [자세히 알아보기](sign-in-options.md).
   * **소셜 ID 공급자** 에서 설정한 외부 소셜 또는 엔터프라이즈 ID 공급자 주 하나를 선택합니다. [자세히 알아보기](add-identity-provider.md).
1. **다단계 인증** 에서 사용자가 두 번째 인증 방법으로 ID를 확인하도록 하려면 방법 유형 및 MFA(다단계 인증)를 적용할 시기를 선택해야 합니다. [자세히 알아보기](multi-factor-authentication.md).
1. **조건부 액세스** 에서 Azure AD B2C 테넌트의 조건부 액세스 정책을 구성했고 이 사용자 흐름에 대해 조건부 액세스 정책을 사용하도록 설정하려면 **조건부 액세스 정책 적용** 확인란을 선택합니다. 정책 이름은 지정하지 않아도 됩니다. [자세히 알아보기](conditional-access-user-flow.md?pivots=b2c-user-flow).
1. **애플리케이션 클레임** 아래에서 토큰의 애플리케이션에 반환하려는 클레임을 선택합니다. 전체 값 목록에서 **자세히 표시** 를 선택하고 값, **확인** 을 차례로 선택합니다.
   > [!NOTE]
   > Azure AD B2C 테넌트에서 사용할 [사용자 지정 특성을 만들 수](user-flow-custom-attributes.md?pivots=b2c-user-flow)도 있습니다.
1. **만들기** 를 클릭하여 사용자 흐름을 추가합니다. *B2C_1* 이라는 접두사가 이름 앞에 자동으로 붙습니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭합니다.
1. 가입 링크를 통하지 않고 만든 계정(MS Graph API 사용)을 사용하여 로그인할 수 있어야 합니다. 반환된 토큰에는 선택한 클레임이 포함됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>가입 링크 제거

**SelfAsserted-LocalAccountSignin-Email** 기술 프로필은 가입 또는 로그인 흐름 중에 호출되는 [자체 어설션](self-asserted-technical-profile.md)입니다. 가입 링크를 제거하려면 `setting.showSignupLink` 메타데이터를 `false`로 설정합니다. 확장 파일에서 SelfAsserted-LocalAccountSignin-Email 기술 프로필을 재정의합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어 _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ 입니다.
1. `ClaimsProviders` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. `ClaimsProviders` 요소에 다음 클레임 공급자를 추가합니다.

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. `<BuildingBlocks>` 요소 내에 다음 [ContentDefinition](contentdefinitions.md)을 추가하여 버전 1.2.0 또는 최신 데이터 URI를 참조합니다.

    ```xml
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>정책 업데이트 및 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택하여 Azure AD 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
1. **ID 경험 프레임워크** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택하고 변경한 정책 파일인 *TrustFrameworkExtensions.xml* 을 업로드합니다.
1. 업로드한 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 가입 링크를 통하지 않고 만든 계정(MS Graph API 사용)을 사용하여 로그인할 수 있어야 합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

* [소셜 ID 공급자를 사용하여 로그인](add-identity-provider.md)을 추가합니다.
* [암호 재설정 흐름](add-password-reset-policy.md)을 설정합니다.
