---
title: Privileged Identity Management에서 관리 그룹 식별-Azure AD | Microsoft Docs
description: 역할 할당 그룹을 Privileged Identity Management(PIM)에서 권한 있는 액세스 그룹으로 관리하도록 온보딩하는 방법에 대해 알아보세요.
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
ms.date: 08/03/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d10ab5c5f1e92a9414cde9b0fffac3c29176cf0
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669837"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>권한 있는 액세스 그룹(미리 보기)을 Privileged Identity Management로 가져오기

Azure Active Directory(Azure AD)에서는 Azure AD 기본 제공 역할을 클라우드 그룹에 할당하여 역할 할당 관리 방법을 단순화할 수 있습니다. Azure AD 역할을 보호하고 액세스를 확보하기 위해 이제 Privileged Identity Management(PIM)을 사용하여 이러한 그룹의 구성원 또는 소유자에 대한 적시 액세스를 관리할 수 있습니다. Azure AD 역할 할당 그룹을 Privileged Identity Management에서 권한 있는 액세스 그룹으로 관리하려면 해당 그룹을 PIM의 관리 하에 두어야 합니다.

## <a name="identify-groups-to-manage"></a>관리할 그룹 확인

[Azure Active Directory에서 역할 할당 가능 그룹 생성](../roles/groups-create-eligible.md)에 설명된 대로 Azure AD에서 역할 할당 가능 그룹을 만들 수 있습니다. Privileged Identity Management를 통해 그룹의 소유자가 그룹을 관리하도록 합니다.

1. 권한 있는 역할 관리자 사용 권한으로 [Azure Active Directory에 로그인합니다](https://aad.portal.azure.com).
1. **그룹** 을 선택한 다음 PIM에서 관리할 역할 할당 가능 그룹을 선택하십시오. 목록을 검색하거나 필터링할 수 있습니다.

    ![PIM에서 관리할 역할 할당 가능 그룹 찾기](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 그룹을 열고 **권한 있는 액세스(미리 보기)** 를 선택합니다.

    ![Privileged Identity Management 환경 열기](./media/groups-discover-groups/groups-discover-groups.png)

1. PIM에서 할당 관리를 시작하십시오.

    ![Privileged Identity Management에서 할당 관리](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 권한 있는 액세스 그룹을 관리하면 해당 그룹을 관리에서 제거할 수 없습니다. 따라서 다른 리소스 관리자가 Privileged Identity Management 설정을 제거하지 못합니다.
>

> [!IMPORTANT]
> Azure Active Directory에서 권한 있는 액세스 그룹을 삭제하는 경우 권한 있는 액세스 그룹(미리 보기) 블레이드에서 제거되는 데 최대 24 시간이 걸릴 수 있습니다. 
>


## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 권한 있는 액세스 그룹 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 권한 있는 액세스 그룹 할당](pim-resource-roles-assign-roles.md)
