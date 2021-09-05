---
title: Azure Active Directory B2C의 Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C에서 보호하는 소비자 지향 애플리케이션에서 Multi-Factor Authentication을 사용하는 방법입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 42feac542760bbebc703cabc4ecc114b0ab4259d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535633"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 다단계 인증 사용 | Microsoft Docs

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C(Azure Active Directory B2C)는 [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)에 직접 통합되어 애플리케이션의 가입 및 로그인 환경에 두 번째 보안 계층을 추가할 수 있습니다. 코드 작성 없이도 다단계 인증을 사용하도록 설정할 수 있습니다. 이미 가입 및 로그인 사용자 흐름을 만든 경우에도 다단계 인증을 사용할 수 있습니다.

이 기능은 애플리케이션에서 다음과 같은 시나리오를 처리하는 데 도움이 됩니다.

- 하나의 애플리케이션에 액세스하려는 경우 다단계 인증이 필요하지 않지만 다른 애플리케이션에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정을 사용하여 자동차 보험 애플리케이션에 로그인할 수 있지만 동일한 디렉터리에 등록된 주택 보험 애플리케이션에 액세스하기 전에 전화번호를 확인해야 합니다.
- 일반적으로 애플리케이션에 액세스하는 데 다단계 인증이 필요하지 않지만 그 안에 중요한 부분에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정으로 은행 애플리케이션에 로그인하고 계정 잔액을 확인할 수 있지만 유선 전송을 시도하기 전에 전화번호를 확인해야 합니다.

### <a name="verification-methods"></a>확인 방법

[조건부 액세스](conditional-access-identity-protection-overview.md)를 사용하면 관리자로서 결정할 수 있는 구성에 따라 사용자에게 MFA에 대한 문제가 발생할 수도 있고 그렇지 않을 수도 있습니다. 다단계 인증의 방법은 다음과 같습니다.

- 메일
- sms
- 전화 통화

## <a name="set-multi-factor-authentication"></a>다단계 인증 설정

::: zone pivot="b2c-user-flow"

1. [Azure 포털](https://portal.azure.com)
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 사용하여 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. MFA를 사용하도록 설정할 사용자 흐름을 선택합니다. 예를 들어 *B2C_1_signinsignup* 을 선택합니다.
1. **속성** 을 선택합니다.
1. **다단계 인증** 섹션에서 원하는 **방법 유형** 을 선택합니다. 그런 다음 **MFA 시행** 에서 옵션을 선택합니다.

   - **꺼짐** -MFA는 로그인 중에 적용되지 않으며 등록 또는 로그인하는 동안 사용자에게 MFA에 등록하라는 메시지가 표시되지 않습니다.
   - **Always On** - 조건부 액세스 설정에 관계없이 MFA가 항상 필요합니다. 등록하는 동안 사용자에게 MFA에 등록하라는 메시지가 표시됩니다. 로그인하는 동안 사용자가 아직 MFA에 등록하지 않은 경우 등록하라는 메시지가 표시됩니다.
   - **조건부** - 등록 및 로그인하는 동안 사용자에게 MFA에 등록하라는 메시지가 표시됩니다(새 사용자와 MFA에 등록되지 않은 기존 사용자 모두). 로그인하는 동안 MFA는 활성 조건부 액세스 정책 평가에 필요한 경우에만 적용됩니다.

    - 결과가 위험이 없는 MFA 체인지인 경우 MFA가 적용됩니다. 사용자가 MFA에 아직 등록되지 않은 경우 등록하라는 메시지가 표시됩니다.
    - 위험 *및* MFA에 등록하지 않은 사용자로 인해 결과가 MFA 챌린지이면 로그인이 차단됩니다.

   > [!NOTE]
   >
   > - Azure AD B2C에서 조건부 액세스가 일반 공급됨에 따라 이제 사용자에게 등록하는 동안 MFA 메서드에 등록하라는 메시지가 표시됩니다. 일반 공급 이전에 만든 등록 사용자 흐름은 이 새로운 동작을 자동으로 반영하지 않지만 새 사용자 흐름을 만들어 동작을 포함할 수 있습니다.
   > - **조건부** 를 선택하는 경우 [사용자 흐름에 조건부 액세스를 추가](conditional-access-user-flow.md)하고 정책을 적용할 앱을 지정해야 합니다.
   > - MFA(다단계 인증)는 가입 사용자 흐름에서 기본적으로 사용되지 않습니다. 전화 등록을 통해 사용자 흐름에서 MFA를 사용할 수 있으나, 여기에는 전화 번호가 기본 식별자로 사용되기 때문에 일회용 이메일 암호가 두 번째 인증 요소에 사용할 수 있는 유일한 옵션이 됩니다.

1. **저장** 을 선택합니다. 이제 이 사용자 흐름에 대해 MFA를 사용할 수 있습니다.

**사용자 흐름 실행** 을 사용하여 환경을 확인할 수 있습니다. 다음 시나리오를 확인합니다.

다단계 인증 단계가 실행되기 전에 테넌트에 고객 계정이 만들어집니다. 이 단계에서 고객에게 자신의 전화 번호를 제공하고 확인하라는 메시지가 표시됩니다. 확인에 성공한 경우 전화번호는 나중에 사용하도록 고객 계정에 연결됩니다. 고객이 취소 또는 삭제하더라도 다음에 로그인하는 동안 다시 전화 번호를 확인하도록 요청할 수 있습니다(다단계 인증을 사용하도록 설정한 경우).

::: zone-end

::: zone pivot="b2c-custom-policy"

Multi-Factor Authentication을 사용하도록 설정하려면 GitHub에서 사용자 지정 정책 스타터 팩을 다운로드한 다음, **SocialAndLocalAccountsWithMFA** 스타터 팩의 XML 파일을 Azure AD B2C 테넌트 이름으로 업데이트합니다. **SocialAndLocalAccountsWithMFA** 는 소셜, 로컬, 다단계 인증 옵션을 사용하도록 설정합니다. 자세한 내용은 [Active Directory B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)을 참조하세요. 

::: zone-end
