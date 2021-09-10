---
title: PIM에서 Azure AD 디렉터리 역할 할당 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/03/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: a741ce7fff528fbe1f4120f4138a88d7b6e2e915
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233010"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할 할당

전역 관리자는 Azure AD(Azure Active Directory)를 사용하여 **영구** Azure AD 관리자 역할 할당을 만들 수 있습니다. 이러한 역할은 [Azure Portal](../roles/permissions-reference.md) 또는 [PowerShell 명령](/powershell/module/azuread#directory_roles)을 사용하여 할당할 수 있습니다.

Azure AD PIM(Privileged Identity Management) 서비스를 사용하여 권한 있는 역할 관리자는 영구 디렉터리 역할을 할당할 수도 있습니다. 또한 권한이 있는 역할 관리자는 사용자의 Azure AD 관리자 역할을 **적격** 으로 만들 수 있습니다. 적격인 관리자는 필요할 때 역할을 활성화할 수 있으며 작업을 완료하고 나면 권한이 만료됩니다.

Privileged Identity Management는 기본 제공 및 사용자 지정 Azure AD 역할을 모두 지원합니다. Azure AD 사용자 지정 역할에 대한 자세한 내용은 [Azure Active Directory의 역할 기반 액세스 제어](../roles/custom-overview.md)를 참조하세요.

## <a name="assign-a-role"></a>역할 할당

사용자를 Azure AD 관리자 역할에 대해 적격 사용자로 지정하려면 다음 단계를 따릅니다.

1. [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **역할** 을 선택하여 Azure AD 권한에 대한 역할 목록을 확인합니다.

    !["할당 추가" 작업이 선택된 "역할" 페이지 스크린샷](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **할당 추가** 를 선택하여 **할당 추가** 페이지를 엽니다.

1. **역할 선택** 을 선택하여 **역할 선택** 페이지를 엽니다.

    ![새 할당 창](./media/pim-how-to-add-role-to-user/select-role.png)

1. 할당할 역할을 선택하고 역할에 할당할 구성원을 선택한 후 **다음** 을 선택합니다.

1. **멤버 자격 설정** 창의 **할당 유형** 목록에서 **적격** 또는 **활성** 을 선택합니다.

    - **적격** 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 구성원이 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 특정 할당 기간을 지정하려면 시작 및 종료 날짜와 시간 상자를 추가합니다. 완료되면 **할당** 을 선택하여 새 역할 할당을 만듭니다.

    ![멤버 자격 설정 - 날짜 및 시간](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 역할이 할당된 후에는 할당 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>제한된 범위의 역할 할당

특정 역할의 경우 부여된 사용 권한의 범위는 단일 관리 단위, 서비스 주체 또는 애플리케이션으로 제한될 수 있습니다. 이 절차는 관리 단위의 범위를 포함하는 역할을 할당하는 경우의 예입니다. 관리 단위를 통해 범위를 지원하는 역할 목록은 [관리 단위에 범위가 지정된 역할 할당](../roles/admin-units-assign-roles.md)을 참조하세요. 이 기능은 현재 Azure AD 조직에 롤아웃되고 있습니다.

1. 권한 있는 역할 관리자 권한으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택합니다.

1. **사용자 관리자** 를 선택합니다.

    ![Portal에서 역할을 열 때 할당 추가 명령을 사용할 수 있습니다.](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. **할당 추가** 를 선택합니다.

    ![역할에서 범위를 지원하는 경우 범위를 선택할 수 있습니다.](./media/pim-how-to-add-role-to-user/add-scope.png)

1. **할당 추가** 페이지에서 다음을 수행할 수 있습니다.

   - 역할에 할당할 사용자 또는 그룹 선택
   - 역할 범위(이 경우에는 관리 단위)를 선택합니다.
   - 범위에 대한 관리 단위를 선택

관리 단위를 만드는 방법에 대한 자세한 내용은 [관리 단위 추가 및 제거](../roles/admin-units-manage.md)를 참조하세요.

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다. Azure AD P2 사용이 허가된 고객만 해당: Azure AD 및 PIM(Privileged Identity Management)을 통해 그룹을 활성으로 역할에 할당하지 않습니다. 자세한 설명은 [알려진 문제](../roles/groups-concept.md#known-issues)를 참조하세요.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **역할** 을 선택하여 Azure AD에 대한 역할 목록을 확인합니다.

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. **업데이트** 또는 **제거** 를 선택하여 역할 할당을 업데이트하거나 제거합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 관리자 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)