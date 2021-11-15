---
title: 고객 등록 시 메일 확인 사용 안 함
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 고객 등록 시 메일 확인 사용을 중지하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 902b7575f7da414abcd34acbfbcd426e0415a948
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130222612"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 고객 등록 시 메일 확인 사용 안 함

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

기본적으로 Azure Active Directory B2C(Azure AD B2C)는 로컬 계정(메일 주소 또는 사용자 이름으로 등록하는 사용자의 계정)에 대한 고객의 메일 주소를 확인합니다. Azure AD B2C는 등록 프로세스 중에 고객에게 메일 주소를 확인하도록 요구하여 메일 주소의 유효성을 보장합니다. 또한 악의적인 행위자가 자동화된 프로세스를 사용하여 애플리케이션에서 사기성 계정을 생성하는 것을 방지합니다.

일부 애플리케이션 개발자는 등록 프로세스 동안 메일 확인을 건너뛰고 고객에게 나중에 메일 주소를 확인하도록 합니다. 이를 지원하는 경우 전자 메일 확인을 사용하지 않도록 Azure AD B2C를 구성할 수 있습니다. 이를 통해 등록 프로세스가 좀 더 원활해지고 개발자는 메일 주소를 확인한 고객과 아직 메일 주소를 확인하지 않은 고객을 구분할 수 있습니다.

> [!WARNING]
> 등록 프로세스에서 전자 메일 확인을 사용하지 않도록 설정하면 스팸 메일이 수신될 수 있습니다. 기본 Azure AD B2C 제공 메일 확인을 사용하지 않는 경우 교체 확인 시스템을 실행하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>메일 확인을 사용하지 않도록 설정

::: zone pivot="b2c-user-flow"

메일 확인을 사용하지 않으려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 메일 확인을 사용하지 않을 사용자 흐름을 선택합니다.
1. **페이지 레이아웃** 을 선택합니다.
1. **로컬 계정 회원가입 페이지** 를 선택합니다.
1. **사용자 특성** 에서 **메일 주소** 를 선택합니다.
1. **확인 필요** 드롭다운에서 **아니요** 를 선택합니다.
1. **저장** 을 선택합니다. 이제 이 사용자 흐름에 대해 메일 확인을 사용하지 않습니다.

::: zone-end

::: zone pivot="b2c-custom-policy"
**LocalAccountSignUpWithLogonEmail** 기술 프로필은 등록 흐름 중에 호출되는 [자체 어설션](self-asserted-technical-profile.md) 기술 프로필입니다. 메일 확인을 사용하지 않으려면 `EnforceEmailVerification` 메타데이터를 false로 설정합니다. 확장 파일에서 LocalAccountSignUpWithLogonEmail 기술 프로필을 재정의합니다. 

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.
1. `ClaimsProviders` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. `ClaimsProviders` 요소에 다음 클레임 공급자를 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>정책 테스트 

1. [Azure 포털](https://portal.azure.com)
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 메일 확인을 사용하지 않을 사용자 흐름을 선택합니다. 예를 들어 *B2C_1_signinsignup* 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭합니다.
1. 유효성 검사 없이 메일 주소를 사용하여 등록할 수 있습니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
1. **ID 경험 프레임워크** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택한 후 변경한 두 정책 파일을 업로드합니다.
1. 업로드한 등록 또는 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 유효성 검사 없이 메일 주소를 사용하여 등록할 수 있습니다.

::: zone-end


## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 인터페이스를 사용자 지정](customize-ui-with-html.md)하는 방법 알아보기

