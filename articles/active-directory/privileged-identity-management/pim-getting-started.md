---
title: PIM을 사용하여 시작 - Azure Active Directory | Microsoft Docs
description: Azure Portal에서 Azure AD PIM(Privileged Identity Management)을 사용하도록 설정하고 시작하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 890f794846427b66081d97018e5590225142c45f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667291"
---
# <a name="start-using-privileged-identity-management"></a>Privileged Identity Management 사용 시작

이 문서에서는 PIM(Privileged Identity Management)을 사용하도록 설정하고 사용을 시작하는 방법을 설명합니다.

PIM(Privileged Identity Management)을 사용하여 Azure AD(Azure Active Directory) 조직 내에서 액세스를 관리, 제어, 모니터링합니다. PIM을 사용하면 Azure 리소스, Azure AD 리소스, 같은 기타 Microsoft 온라인 서비스(예: Microsoft 365 또는 Microsoft Intune)에 대한 액세스를 필요에 따라 적시에 제공할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Privileged Identity Management를 사용하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5

자세한 내용은 [Privileged Identity Management를 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)을 참조하세요.

> [!Note]
> Premium P2 라이선스를 사용하는 Azure AD 조직의 권한 있는 역할에서 활성 상태인 사용자가 Azure AD의 **역할 및 관리자** 로 이동하고 역할을 선택하는 경우(또는 단지 Privileged Identity Management를 방문하는 경우):
>
> - 조직에 대해 PIM을 자동으로 사용하도록 설정합니다.
> - 이제 사용자는 “일반” 역할 할당 또는 적격 역할 할당을 할당할 수 있습니다.
>
> PIM은 사용되는 경우 조직에 다른 영향을 주지 않으므로 걱정할 필요가 없습니다. 시작 및 종료 시간을 사용하여 활성 및 적격과 같은 추가 할당 옵션을 제공합니다. 또한 PIM을 사용하여 관리 단위와 사용자 지정 역할을 사용하여 역할 할당의 범위를 정의할 수 있습니다. 전역 관리자 또는 권한 있는 역할 관리자인 경우 PIM 주간 다이제스트와 같은 몇 가지 추가 메일을 받기 시작할 수 있습니다. 역할 할당과 관련된 감사 로그에 MS-PIM 서비스 주체가 표시될 수도 있습니다. 이는 워크플로에 영향을 주지 않는 예상되는 변경 내용입니다.

## <a name="prepare-pim-for-azure-ad-roles"></a>Azure AD 역할에 대한 PIM 준비

Azure AD 역할을 관리하기 위해 Privileged Identity Management를 준비하는 데 권장되는 작업은 다음과 같습니다.

1. [Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md).
1. [적격 할당 제공](pim-how-to-add-role-to-user.md).
1. [적격 사용자가 해당 Azure AD 역할을 적시에 활성화할 수 있도록 허용](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Azure 역할에 대한 PIM 준비

구독에 대한 Azure 역할을 관리하기 위해 Privileged Identity Management를 준비하는 데 권장되는 작업은 다음과 같습니다.

1. [Azure 리소스 검색](pim-resource-roles-discover-resources.md)
1. [Azure 역할 설정 구성](pim-resource-roles-configure-role-settings.md).
1. [적격 할당 제공](pim-resource-roles-assign-roles.md).
1. [적격 사용자가 해당 Azure 역할을 적시에 활성화할 수 있도록 허용](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>태스크로 이동합니다.

Privileged Identity Management가 설정된 후에 자세히 알아볼 수 있습니다.

![작업과 관리 옵션을 보여 주는 Privileged Identity Management의 탐색 창](./media/pim-getting-started/pim-quickstart-tasks.png)

| 작업 + 관리 | Description |
| --- | --- |
| **내 역할**  | 사용자에게 할당된 적격 및 활성 역할의 목록을 표시합니다. 여기서 할당된 적합한 역할을 활성화할 수 있습니다. |
| **보류 중인 요청** | 적격 역할 할당을 활성화할 보류 중인 요청을 표시합니다. |
| **요청 승인** | 승인하도록 지정된 디렉터리에서 사용자를 통해 적격 역할을 활성화하기 위한 요청 목록을 표시합니다. |
| **액세스 검토** | 사용자 자신 또는 다른 사용자에 대한 액세스를 검토하는지와 관계없이 사용자에게 수행하도록 할당된 활성 액세스 검토를 나열합니다. |
| **Azure AD 역할** | 권한 있는 역할 관리자가 Azure AD 역할 할당을 관리하기 위한 대시보드와 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. 내 보기 대시보드는 전체 조직이 아닌 대시보드에 액세스하는 사용자에 관한 정보만 표시합니다. |
| **Azure 리소스** | 권한 있는 역할 관리자가 Azure 리소스 역할 할당을 관리하기 위한 대시보드와 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. 내 보기 대시보드는 전체 조직이 아닌 대시보드에 액세스하는 사용자에 관한 정보만 표시합니다. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>대시보드에 PIM 타일 추가

Privileged Identity Management를 더 쉽게 열려면 Azure Portal 대시보드에 PIM 타일을 추가합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **모든 서비스** 를 선택하고 **Azure AD Privileged Identity Management** 서비스를 찾습니다.

    ![모든 서비스의 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. Privileged Identity Management **빠른 시작** 을 선택합니다.

1. **대시보드에 블레이드 고정** 을 선택하여 Privileged Identity Management **빠른 시작** 페이지를 대시보드에 고정합니다.

    ![대시보드에 Privileged Identity Management 페이지를 고정하는 압정 아이콘](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure 대시보드에 다음과 같은 타일이 표시됩니다.

    ![대시보드의 Privileged Identity Management 빠른 시작 타일](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management에서 Azure 리소스 액세스 관리](pim-resource-roles-discover-resources.md)
