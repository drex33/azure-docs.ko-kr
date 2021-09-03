---
title: 내 직원을 사용하여 사용자 관리 위임 - Azure AD | Microsoft Docs
description: 내 직원과 관리 단위를 사용하여 사용자 관리 위임
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: d3ec7c9786a9b5d3774c05669ae6d51ad443e71d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529063"
---
# <a name="manage-your-users-with-my-staff"></a>내 직원으로 사용자 관리

내 직원을 사용하면 매장 관리자 또는 팀 리더와 같은 권위 있는 사람에게 권한을 위임하여 직원이 자신의 Azure AD 계정에 액세스하도록 할 수 있습니다. 조직에서는 암호 재설정 또는 전화번호 변경과 같은 일반적인 작업을 중앙 기술 지원팀에 의존하는 대신 로컬 팀 관리자에게 위임할 수 있습니다. 내 직원을 사용하면 기술 지원팀이나 IT 직원의 도움 없이도 자신의 계정에 액세스하지 못하는 사용자가 몇 번만 클릭하면 액세스 권한을 다시 얻을 수 있습니다.

조직에 내 직원을 구성하기 전에 이 설명서와 [사용자 설명서](../user-help/my-staff-team-manager.md)를 검토하여 내 직원의 작동 방식 및 사용자에게 미치는 영향을 이해하는 것이 좋습니다. 사용자 설명서를 활용하여 사용자에게 새로운 경험에 대한 학습과 준비의 기회를 제공하고 성공적인 출시를 지원할 수 있습니다.

## <a name="how-my-staff-works"></a>내 직원의 작동 방식

내 직원은 역할 할당의 관리 제어 범위를 제한하는 데 사용할 수 있는 리소스의 컨테이너인 관리 단위를 기반으로 합니다. 자세한 내용은 [Azure Active Directory의 관리 단위 관리](administrative-units.md)를 참조하세요. 내 직원에서 관리 단위는 매장 또는 부서의 사용자 그룹을 포함하는 데 사용할 수 있습니다. 그런 다음 하나 이상의 단위 범위에서 팀 관리자를 관리 역할에 할당할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.

  * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 구독과 연결된 Azure Active Directory 테넌트.

  * 필요한 경우 [Azure Active Directory 테넌트를 만들거나](../fundamentals/sign-up-organization.md)[Azure 구독을 계정에 연결합니다](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* SMS 기반 인증을 사용하도록 설정하려면 Azure AD 테넌트에 *전역 관리자* 권한이 필요합니다.
* 문자 메시지 인증 방법 정책에서 설정된 각 사용자는 이를 사용하지 않더라도 라이선스를 부여받아야 합니다. 설정된 각 사용자에게는 다음 Azure AD 또는 Microsoft 365 라이선스 중 하나가 있어야 합니다.

  * [Azure AD Premium P1 또는 P2](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)
  * [Microsoft 365 F1 또는 F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [EMS(Enterprise Mobility + Security) E3 또는 E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) 또는 [Microsoft 365 E3 또는 E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>내 직원을 사용하도록 설정하는 방법

관리 단위를 구성한 후에는 이 범위를 내 직원에 액세스하는 사용자에게 적용할 수 있습니다. 관리자 역할이 할당된 사용자만 내 직원에 액세스할 수 있습니다. 내 직원을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 사용자 관리자로 로그인합니다.

1. **Azure Active Directory** > **사용자 설정** > **사용자 기능 미리 보기** > **사용자 기능 미리 보기 설정 관리** 를 선택합니다.

1. **관리자가 내 직원에 액세스할 수 있음** 아래에서 모든 사용자 또는 선택한 사용자가 액세스할 수 있도록 설정하거나 어떤 사용자도 액세스하지 못하도록 설정할 수 있습니다.

> [!Note]
> 관리자 역할이 할당된 사용자만 내 직원에 액세스할 수 있습니다. 관리자 역할이 할당되지 않은 사용자에 대해 내 직원을 사용하도록 설정하더라도 해당 직원은 내 직원에 액세스할 수 없습니다.

## <a name="conditional-access"></a>조건부 액세스

Azure AD 조건부 액세스 정책을 사용하여 내 직원 포털을 보호할 수 있습니다. 내 직원에 액세스하기 전에 다단계 인증을 요구하는 것과 같은 작업에 이 정책을 사용하세요.

[AZURE AD 조건부 액세스 정책](../conditional-access/index.yml)을 사용하여 내 직원을 보호하는 것이 좋습니다. 내 직원에 조건부 액세스 정책을 적용하려면 먼저 내 직원 사이트를 한 번 방문하여 몇 분 동안 머물러 있어야 합니다. 이렇게 하면 테넌트에 있는 서비스 주체가 조건부 액세스의 사용 대상이 될 수 있도록 자동으로 프로비저닝됩니다.

내 직원 클라우드 애플리케이션에 적용되는 조건부 액세스 정책을 만들 때 서비스 주체가 표시됩니다.

![내 직원 앱의 조건부 액세스 정책 만들기](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>내 직원 사용

사용자가 내 직원으로 이동하면 자신에게 관리 권한이 있는 [관리 단위](administrative-units.md)의 이름이 표시됩니다. [내 직원 사용자 설명서](../user-help/my-staff-team-manager.md)에서 "위치"라는 용어는 관리 단위를 가리킵니다. 관리자의 권한에 관리 단위 범위가 없는 경우 해당 권한은 조직 전체에 적용됩니다. 내 직원을 사용하도록 설정한 후에는 사용 설정되고 관리 역할이 할당된 사용자는 [https://mystaff.microsoft.com](https://mystaff.microsoft.com)을 통해 내 직원에 액세스할 수 있습니다. 이들은 관리 단위를 선택하여 해당 단위에 있는 사용자를 보고 사용자를 선택하여 해당 사용자의 프로필을 열 수 있습니다.

## <a name="reset-a-users-password"></a>사용자의 암호 재설정

온-프레미스 사용자의 암호를 재설정하려면 먼저 다음 필수 조건을 충족해야 합니다. 자세한 지침은 [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr-writeback.md) 자습서를 참조하세요.

* 비밀번호 쓰기 저장에 대한 권한 구성
* Azure AD Connect에서 비밀번호 쓰기 저장 사용
* Azure AD SSPR(셀프 서비스 암호 재설정)에서 비밀번호 쓰기 저장 사용

다음 역할에는 사용자의 암호를 재설정할 수 있는 권한이 있습니다.

* [인증 관리자](permissions-reference.md#authentication-administrator)
* [권한 있는 인증 관리자](permissions-reference.md#privileged-authentication-administrator)
* 전역 관리자
* [기술 지원팀 관리자](permissions-reference.md#helpdesk-administrator)
* [사용자 관리자](permissions-reference.md#user-administrator)
* [암호 관리자](permissions-reference.md#password-administrator)

**내 직원** 에서 사용자의 프로필을 엽니다. **암호 다시 설정** 을 선택합니다.

* 사용자가 클라우드 전용 사용자인 경우 해당 사용자에게 제공할 수 있는 임시 암호를 볼 수 있습니다.
* 사용자가 온-프레미스 Active Directory에서 동기화된 사용자인 경우 온-프레미스 AD 정책을 충족하는 암호를 입력할 수 있습니다. 그런 다음 사용자에게 해당 암호를 제공할 수 있습니다.

    ![암호 재설정 진행률 표시기 및 성공 알림](./media/my-staff-configure/reset-password.png)

사용자는 다음에 로그인할 때 암호를 변경해야 합니다.

## <a name="manage-a-phone-number"></a>전화번호 관리

**내 직원** 에서 사용자의 프로필을 엽니다.

* **전화번호 추가** 섹션을 선택하여 사용자의 전화번호를 추가합니다.
* **전화번호 편집** 을 선택하여 전화번호를 변경합니다.
* **전화번호 제거** 를 선택하여 사용자의 전화번호를 제거합니다.

설정에 따라 사용자는 내가 설정한 전화번호를 사용하여 SMS로 로그인하고, 다단계 인증을 수행하고, 셀프 서비스 암호 재설정을 수행할 수 있습니다.

사용자의 전화번호를 관리하려면 내가 다음 역할 중 하나에 할당되어야 합니다.

* [인증 관리자](permissions-reference.md#authentication-administrator)
* [권한 있는 인증 관리자](permissions-reference.md#privileged-authentication-administrator)
* 전역 관리자

## <a name="search"></a>검색

내 직원의 검색 표시줄을 사용하여 조직의 관리 단위 및 사용자를 검색할 수 있습니다. 조직의 모든 관리 단위와 사용자를 검색할 수 있지만, 나에게 관리 권한이 있는 관리 단위에 속한 사용자에 대해서만 변경 작업을 수행할 수 있습니다.

관리 단위 내에서 사용자를 검색할 수도 있습니다. 이렇게 하려면 사용자 목록 상단의 검색 표시줄을 사용합니다.

## <a name="audit-logs"></a>감사 로그

내 직원에서 수행된 작업에 대한 감사 로그는 Azure Active Directory 포털에서 볼 수 있습니다. 내 직원에서 수행된 작업에서 감사 로그가 생성된 경우 감사 이벤트의 추가 세부 정보 아래에 나타납니다.

## <a name="next-steps"></a>다음 단계

[내 직원 사용자 설명서](../user-help/my-staff-team-manager.md)
[관리 단위 설명서](administrative-units.md)
