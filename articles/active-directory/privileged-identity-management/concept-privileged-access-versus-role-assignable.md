---
title: 권한 있는 액세스 그룹과 역할 할당 가능 그룹 간의 차이점 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 권한 있는 액세스 그룹과 역할 할당 가능 그룹 간의 차이점을 파악하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/02/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5f9d968c114c28dcff8e247435c40960040c4e9
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568031"
---
# <a name="whats-the-difference-between-privileged-access-groups-and-role-assignable-groups"></a>권한 있는 액세스 그룹과 역할 할당 가능 그룹 간의 차이점

PIM(Privileged Identity Management)은 역할 할당 가능 그룹에서 권한 있는 액세스를 사용하도록 설정하는 기능을 지원합니다. 그러나 사용 가능한 역할 할당 가능 그룹은 권한 있는 액세스 그룹을 만들기 위한 필수 조건이므로 이 문서에서는 차이점과 이를 활용하는 방법을 설명합니다.

## <a name="what-are-azure-ad-role-assignable-groups"></a>Azure AD 역할 할당 가능 그룹이란?

Azure AD를 사용하면 클라우드 Azure AD 보안 그룹을 Azure AD 역할에 할당할 수 있습니다. 전역 관리자 및 권한 있는 역할 관리자는 새 보안 그룹을 만들고 생성 시 그룹 역할을 할당할 수 있도록 해야 합니다. 전역 관리자, 권한 있는 역할 관리자 또는 그룹의 소유자 역할의 사용자만 그룹의 멤버 자격을 변경할 수 있습니다. 또한 다른 사용자는 그룹의 멤버인 사용자의 암호를 재설정할 수 없습니다. 이 기능을 사용하면 관리자가 요청 및 승인 절차를 거치지 않고 더 높은 권한의 역할로 승격되는 것을 방지할 수 있습니다.

## <a name="what-are-privileged-access-groups"></a>권한 있는 액세스 그룹이란?

권한 있는 액세스 그룹을 사용하면 사용자가 Azure AD 보안 그룹의 소유자 또는 멤버 역할로 승격할 수 있습니다. 이 기능을 사용하면 Azure AD 및 Azure 역할에 대한 Just-In-Time 워크플로를 일괄적으로 설정할 수 있으며 Azure SQL, Azure Key Vault, Intune 또는 기타 애플리케이션 역할과 같은 다른 사용 사례에 대한 Just-in-time 시나리오를 사용할 수 있습니다. 자세한 내용은 [권한 있는 액세스 그룹의 관리 기능](groups-features.md)을 참조하세요.

>[!Note]
>Azure AD 역할로 승격하는 데 사용되는 권한 있는 액세스 그룹의 경우 Microsoft는 적격 멤버 할당에 대한 승인 프로세스를 요구할 것을 권장합니다. 승인 없이 활성화할 수 있는 할당은 권한이 낮은 관리자의 보안 위험에 대해 취약해질 수 있습니다. 예를 들어 기술 지원팀 관리자는 적격 사용자의 암호를 재설정할 수 있는 권한이 있습니다.

## <a name="when-to-use-each-type-of-group"></a>각 유형의 그룹을 사용하는 경우

Azure AD 및 Azure Resource 이외의 권한 및 역할에 대한 Just-In-Time 액세스를 설정할 수 있습니다. Azure AD 보안 그룹(Azure Key Vault, Intune, Azure SQL 또는 기타 앱 및 서비스의 경우)에 인증을 연결할 수 있는 다른 리소스가 있는 경우, 그룹에 대한 권한 있는 액세스를 사용하도록 설정하고 사용자를 그룹의 멤버십 자격으로 할당해야 합니다.

Azure AD 또는 Azure 리소스 역할에 그룹을 할당하고 PIM 프로세스를 통해 권한 상승이 필요한 경우 두 가지 방법으로 수행할 수 있습니다.

- **역할에 그룹을 영구적으로 할당** 합니다. 그런 다음, 사용자에게 PIM의 그룹에 대한 적격 구성원 액세스 권한을 부여합니다. 적격 사용자는 역할에 영구적으로 할당된 그룹에 들어가려면 멤버 자격을 활성화해야 합니다. 이 경로를 사용하려면 역할 할당 가능 그룹이 Azure AD 역할에 대한 권한 있는 액세스 그룹으로 PIM에서 사용하도록 설정되어야 합니다.
- PIM을 통해 **그룹을 역할에 적합한 것으로 할당** 합니다. 그룹의 모든 사람이 역할에 액세스하려면 할당을 활성화해야 합니다. 이 경로에는 Azure AD 역할에 대한 역할 할당 가능 그룹과 Azure 리소스에 대한 보안 그룹이 필요합니다.

    ![PIM에서 권한 있는 액세스 그룹을 사용하여 역할을 할당하는 두 가지 방법을 보여주는 다이어그램](./media/concept-privileged-access-versus-role-assignable/concept-privileged-access.png)

이러한 방법 중 하나는 엔드투엔드 시나리오에서 작동됩니다. 대부분의 경우 첫 번째 방법을 사용하는 것이 좋습니다. 다음과 같은 경우에만 두 번째 방법을 사용해야 합니다.

- 여러 Azure AD 또는 Azure 리소스 역할에 그룹을 할당하고 사용자가 한 번 활성화하여 여러 역할에 액세스하도록 합니다.
- Azure AD 또는 Azure 리소스 역할에 액세스하기 위해 다양한 사용자 집합에 대해 서로 다른 활성화 정책을 유지 관리합니다. 예를 들어 일부 사용자는 전역 관리자가 되기 전에 승인하고 다른 사용자는 자동 승인되도록 하려면 2개의 권한 있는 액세스 그룹을 설정하고 둘 다 전역 관리자 역할에 영구적으로 할당(Privileged Identity Management의 "영구" 할당)한 다음, 각 그룹의 구성원 역할에 대한 다른 활성화 정책을 사용하면 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 역할에 대한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
- [Azure 리소스 역할에 대한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
- [권한 있는 액세스 그룹 구성원 및 소유자에 대한 활성화 요청 승인(미리 보기)](groups-approval-workflow.md)
