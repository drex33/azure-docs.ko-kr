---
title: PIM에서 관리할 Azure 리소스 검색 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 관리할 Azure 리소스를 검색하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/19/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d9214936073106424301d8e7b85867d51cbeb22
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253698"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Privileged Identity Management에서 관리할 Azure 리소스 검색

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하여 Azure 리소스에 대한 보호를 향상시킬 수 있습니다. 도움이 되는 곳은 다음과 같습니다.

- 이미 Privileged Identity Management을 사용하여 Azure AD 역할을 보호하는 조직
- 프로덕션 리소스를 보호하려는 관리 그룹 및 구독 소유자

Azure 리소스에 대한 Privileged Identity Management를 처음 설정할 때 Privileged Identity Management에서 보호할 리소스를 검색하고 선택해야 합니다. Privileged Identity Management로 관리할 수 있는 리소스 수에는 제한이 없습니다. 그러나 가장 중요한 프로덕션 리소스부터 시작하는 것을 추천합니다.

## <a name="required-permissions"></a>필요한 사용 권한

사용자 액세스 관리자 또는 소유자 역할과 같은 Microsoft.Authorization/roleAssignments/쓰기 권한이 있는 관리 그룹 또는 구독을 보고 관리할 수 있습니다. 구독 소유자가 아니지만 전역 관리자이며 관리할 Azure 구독 또는 관리 그룹이 표시되지 않는 경우 [액세스 권한을 높여 리소스를 관리](../../role-based-access-control/elevate-access-global-admin.md)할 수 있습니다.

## <a name="discover-resources"></a>리소스 검색

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure 리소스** 를 선택합니다.

    처음으로 Azure 리소스에 대해 Privileged Identity Management를 사용하는 경우 **리소스 검색** 페이지가 표시됩니다.

    ![최초 환경에 대해 나열된 리소스가 없는 리소스 검색 창](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    조직의 다른 관리자가 이미 Privileged Identity Management에서 Azure 리소스를 관리하고 있는 경우 현재 관리되는 리소스 목록이 표시됩니다.

    ![현재 관리 중인 리소스를 나열하는 리소스 검색 창](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. **리소스 검색** 을 선택하여 검색 환경을 시작합니다.

    ![검색 창에는 구독 및 관리 그룹과 같이 관리할 수 있는 리소스가 나열됩니다.](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. **검색** 페이지에서 **리소스 상태 필터** 를 사용하고 **리소스 종류 선택** 을 사용하여 쓰기 권한이 있는 관리 그룹 또는 구독을 필터링합니다. 처음에는 **모두** 로 시작하는 것이 가장 쉬울 수 있습니다.

   Privileged Identity Management에서 관리할 관리 그룹 또는 구독 리소스를 검색하고 선택할 수 있습니다. Privileged Identity Management에서 관리 그룹 또는 구독을 관리하는 경우 해당 자식 리소스도 관리할 수 있습니다.

   > [!Note]
   > 새 자식 Azure 리소스를 PIM 관리형 그룹에 추가하는 경우 PIM에서 검색하여 자식 리소스를 관리 대상으로 가져올 수 있습니다.

1. 관리하려는 리소스를 선택합니다.

1. **리소스 관리** 를 선택하여 리소스 관리를 시작합니다.

    > [!NOTE]
    > 일단 관리 그룹 또는 구독이 관리형으로 설정되면 비관리형으로 설정될 수 없습니다. 따라서 다른 리소스 관리자가 Privileged Identity Management 설정을 제거하지 못합니다.

    ![리소스가 선택되고 리소스 관리 옵션이 강조 표시된 검색 창](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 관리용 리소스의 온보딩을 확인하라는 메시지가 표시되면 **예** 를 선택합니다. 그러면 PIM이 리소스의 모든 새 자식 개체와 기존 자식 개체를 관리하도록 구성됩니다.

    ![관리를 위해 선택한 리소스를 온보딩하도록 확인하는 메시지](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
