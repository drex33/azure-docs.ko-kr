---
title: 권한 있는 액세스 그룹에 적격 소유자 및 구성원 할당 - Azure Active Directory
description: Azure AD PIM(Privileged Identity Management)에서 역할 할당 가능 그룹의 적격 소유자 또는 구성원을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f2978f2ee0eca3aef438d05f1e8aebbee3eeb1
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667690"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Privileged Identity Management에서 권한 있는 액세스 그룹(미리 보기)의 자격 할당

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)은 Azure AD에서 권한 있는 액세스 그룹의 자격 및 할당 활성화를 관리하는 데 도움이 됩니다. 그룹의 구성원이나 소유자에게 자격을 할당할 수 있습니다.

>[!NOTE]
>권한 있는 액세스 그룹의 멤버 자격 또는 소유권을 받을 수 있는 모든 사용자에게 Azure AD Premium P2 라이선스가 있어야 합니다. 자세한 내용은 [Privileged Identity Management를 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)을 참조하세요.

## <a name="assign-an-owner-or-member-of-a-group"></a>그룹의 소유자 또는 구성원 할당

사용자가 권한 있는 액세스 그룹의 구성원이나 소유자가 될 수 있도록 하려면 다음 단계를 수행합니다.

1. 전역 관리자 또는 그룹 소유자 권한을 사용하여 [Azure AD에 로그인](https://aad.portal.azure.com)합니다.
1. **그룹** 을 선택한 다음 관리할 역할 할당 가능 그룹을 선택합니다. 목록을 검색하거나 필터링할 수 있습니다.

    ![PIM에서 관리할 역할 할당 가능 그룹 찾기](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. 그룹을 열고 **권한 있는 액세스(미리 보기)** 를 선택합니다.

    ![Privileged Identity Management 환경 열기](./media/groups-assign-member-owner/groups-discover-groups.png)

1. **할당 추가** 를 선택합니다.

    ![새 할당 창](./media/groups-assign-member-owner/groups-add-assignment.png)

1. 권한 있는 액세스 그룹에 적격하도록 설정할 구성원 또는 소유자를 선택합니다.

    ![“구성원 또는 그룹 선택” 창이 열려 있고 “선택” 단추가 강조 표시된 “할당 추가” 페이지 스크린샷](./media/groups-assign-member-owner/add-assignments.png)

1. **다음** 을 선택하여 멤버 자격 또는 소유권 기간을 설정합니다.

    ![멤버 또는 그룹 선택 창](./media/groups-assign-member-owner/assignment-duration.png)

1. **할당 유형** 목록에서 **적격** 또는 **활성** 을 선택합니다. 권한 있는 액세스 그룹은 다음 두 가지 고유한 할당 유형을 제공합니다.

    - **적격** 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 구성원이 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다. 

      > [!Important]
      > Azure AD 역할로 승격하는 데 사용되는 권한 있는 액세스 그룹의 경우 Microsoft는 적격 멤버 할당에 대한 승인 프로세스를 요구할 것을 권장합니다. 승인 없이 활성화할 수 있는 할당은 적격 사용자의 암호를 재설정할 수 있는 권한이 있는 다른 관리자의 보안 위험에 취약해질 수 있습니다.

    - **활성** 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 할당을 영구적으로 지정하려면(영구적으로 적격 또는 영구적으로 할당됨) **영구적으로** 확인란을 선택합니다. 조직의 설정에 따라 확인란이 표시되지 않거나 편집 불가능할 수도 있습니다.

1. 작업을 마쳤으면 **할당하기** 를 선택합니다.

1. 새 역할 할당을 만들려면 **추가** 를 선택합니다. 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. 전역 관리자 또는 그룹 소유자 권한을 사용하여 [Azure AD에 로그인](https://aad.portal.azure.com)합니다.
1. **그룹** 을 선택한 다음 관리할 역할 할당 가능 그룹을 선택합니다. 목록을 검색하거나 필터링할 수 있습니다.

    ![PIM에서 관리할 역할 할당 가능 그룹 찾기](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. 그룹을 열고 **권한 있는 액세스(미리 보기)** 를 선택합니다.

    ![Privileged Identity Management 환경 열기](./media/groups-assign-member-owner/groups-discover-groups.png)

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. **업데이트** 또는 **제거** 를 선택하여 역할 할당을 업데이트하거나 제거합니다.

    역할 할당을 확장하는 방법에 대한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 할당 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
