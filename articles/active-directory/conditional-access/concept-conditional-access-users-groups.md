---
title: 조건부 액세스 정책의 사용자 및 그룹 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 사용자 및 그룹
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5e49e3e545e7536d9f0d9bc49b7fd791ea52b8d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627637"
---
# <a name="conditional-access-users-and-groups"></a>조건부 액세스: 사용자 및 그룹

조건부 액세스 정책은 의사 결정 프로세스의 신호 중 하나로 사용자 할당을 포함해야 합니다. 조건부 액세스 정책에서 사용자를 포함하거나 제외할 수 있습니다. Azure Active Directory는 모든 정책을 평가하고, 사용자에게 액세스 권한을 부여하기 전에 모든 요구 사항이 충족되는지 확인합니다. 

> [!VIDEO https://www.youtube.com/embed/5DsW1hB3Jqs]

## <a name="include-users"></a>사용자 포함

해당 사용자 목록에는 일반적으로 조직이 조건부 액세스 정책에서 대상으로 하는 모든 사용자가 포함됩니다. 

조건부 액세스 정책을 만들 때 포함하는 데 사용할 수 있는 옵션은 다음과 같습니다.

- 없음
   - 선택된 사용자 없음
- 모든 사용자가 액세스할 수 있습니다.
   - B2B 게스트를 비롯하여 디렉터리에 있는 모든 사용자.
- 사용자 및 그룹 선택
   - 모든 게스트 및 외부 사용자
      - 해당 선택 항목에는 모든 B2B 게스트와 `user type` 특성이 `guest`로 설정된 모든 사용자를 포함하는 외부 사용자가 포함됩니다. 해당 선택 항목은 CSP(클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인한 모든 외부 사용자에게도 적용됩니다. 
   - 디렉터리 역할
      - 관리자가 정책 할당을 확인하는 데 사용되는 특정 기본 제공 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할이 할당된 사용자에 대해 더 제한적인 정책을 만들 수 있습니다. 관리 단위 범위 역할 및 사용자 지정 역할을 비롯한 다른 역할 유형은 지원되지 않습니다.
   - 사용자 및 그룹
      - 특정 사용자 집합을 대상으로 지정할 수 있습니다. 예를 들어 조직은 HR 앱이 클라우드 앱으로 선택된 경우 HR 부서의 모든 구성원을 포함하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다. 정책은 중첩된 사용자 및 그룹에 적용됩니다.

> [!IMPORTANT]
> 조건부 액세스 정책에 포함되는 사용자 및 그룹을 선택하는 경우 조건부 액세스 정책에 직접 추가할 수 있는 개별 사용자 수에 제한이 있습니다. 조건부 액세스 정책에 직접 추가해야 하는 개별 사용자가 많은 경우 사용자를 그룹에 배치하고 대신 그룹을 조건부 액세스 정책에 할당하는 것이 좋습니다.

> [!WARNING]
> 사용자 또는 그룹이 2,048개가 넘는 그룹의 구성원으로 속해 있는 경우 해당 액세스는 차단될 수 있습니다. 해당 제한은 직접 및 중첩 그룹 멤버 자격에 모두 적용됩니다.

> [!WARNING]
> 조건부 액세스 정책은 [관리 단위로 범위가 지정된](../roles/admin-units-assign-roles.md) 디렉터리 역할이나 [사용자 지정 역할](../roles/custom-create.md)을 통해 개체로 직접 지정된 디렉터리 역할이 할당된 사용자를 지원하지 않습니다.

## <a name="exclude-users"></a>사용자 제외

조직에서 사용자 또는 그룹을 포함하고 제외하는 경우 제외 작업이 정책에서 포함 작업을 재정의하므로 사용자 또는 그룹이 정책에서 제외됩니다. 제외는 일반적으로 응급 액세스 또는 비상 계정에 사용됩니다. 응급 액세스 계정에 대한 자세한 내용과 해당 계정이 중요한 이유는 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../roles/security-emergency-access.md)
* [Azure Active Directory를 사용하여 복원력 있는 액세스 제어 관리 전략 수립](../authentication/concept-resilient-controls.md)

조건부 액세스 정책을 만들 때 제외하는 데 사용할 수 있는 옵션은 다음과 같습니다.

- 모든 게스트 및 외부 사용자
   - 해당 선택 항목에는 모든 B2B 게스트와 `user type` 특성이 `guest`로 설정된 모든 사용자를 포함하는 외부 사용자가 포함됩니다. 해당 선택 항목은 CSP(클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인한 모든 외부 사용자에게도 적용됩니다. 
- 디렉터리 역할
   - 관리자가 할당을 확인하는 데 사용되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할이 할당된 사용자에 대해 더 제한적인 정책을 만들 수 있습니다.
- 사용자 및 그룹
   - 특정 사용자 집합을 대상으로 지정할 수 있습니다. 예를 들어 조직은 HR 앱이 클라우드 앱으로 선택된 경우 HR 부서의 모든 구성원을 포함하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

### <a name="preventing-administrator-lockout"></a>관리자 잠금 방지

**모든 사용자** 및 **모든 앱** 에 적용되는 정책을 만들 때 관리자가 자신의 디렉터리에서 잠기지 않도록 하기 위해 다음과 같은 경고가 표시됩니다.

> 계정이 잠기지 않도록 주의하세요. 먼저 적은 수의 사용자에게 정책을 적용하여 정책이 예상대로 작동하는지 확인하는 것이 좋습니다. 또한 해당 정책에서 한 명 이상의 관리자를 제외하는 것이 좋습니다. 그러면 계속 액세스하면서도 변경이 필요할 때 정책을 업데이트할 수 있습니다. 영향을 받는 사용자 및 앱을 확인하세요.

기본적으로 정책은 현재 사용자를 정책에서 제외하는 옵션을 제공하지만, 다음 이미지와 같이 관리자가 해당 기본값을 재정의할 수 있습니다. 

![경고! 계정이 잠기지 않도록 주의하세요.](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

관리자 계정이 잠긴 경우 [Azure Portal에서 계정이 잠긴 경우 어떻게 해야 하나요?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
