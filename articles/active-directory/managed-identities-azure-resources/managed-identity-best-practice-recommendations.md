---
title: 관리형 시스템 ID에 대한 모범 사례 권장 사항
description: 사용자 할당 및 시스템 할당 관리 ID를 사용하는 경우의 권장 사항
services: active-directory
documentationcenter: ''
author: barclayn
manager: karenh444
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/15/2021
ms.author: barclayn
ms.openlocfilehash: b1dbdf7f7798458ec3ea3a7487f69a9dee244dda
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474291"
---
# <a name="managed-identity-best-practice-recommendations"></a>관리 ID 모범 사례 권장 사항

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="choosing-system-or-user-assigned-managed-identities"></a>시스템 또는 사용자 할당 관리 ID 선택

사용자 할당 관리 ID는 시스템 할당 관리 ID보다 광범위한 시나리오에서 좀 더 효율적입니다. 사용자 할당 또는 시스템 할당에 대한 몇 가지 시나리오와 권장 사항은 아래 표를 참조하세요.

사용자 할당 ID는 여러 리소스에서 사용할 수 있으며 해당 수명 주기는 연결된 리소스의 수명 주기에서 분리됩니다. [관리 ID를 지원하는 리소스를 읽습니다](./services-support-managed-identities.md).

이 수명 주기를 사용하면 리소스 생성 및 ID 관리 책임을 구분할 수 있습니다. 사용자 할당 ID 및 해당 역할 할당은 이러한 항목을 요구하는 리소스보다 먼저 구성할 수 있습니다. 리소스를 만드는 사용자에게는 새 ID 또는 역할 할당을 만들 필요 없이 사용자 할당 ID를 할당하기 위한 액세스 권한만 있으면 됩니다.  

시스템 할당 ID는 리소스와 함께 생성 및 삭제되므로 역할 할당은 미리 만들 수 없습니다. 리소스를 만드는 사용자에게 역할 할당을 만들 수 있는 권한이 없는 경우 이 시퀀스를 사용하면 인프라를 배포하는 동안 오류가 발생할 수 있습니다.

인프라에서 여러 리소스가 동일한 리소스에 액세스하도록 요구하는 경우 단일 사용자 할당 ID를 할당할 수 있습니다. 관리할 고유 ID와 역할 할당이 더 적으므로 관리 오버헤드가 줄어듭니다.

각 리소스에 고유한 ID가 있거나 고유한 권한 집합이 필요한 리소스를 사용해야 하는 경우 리소스를 삭제할 때 ID도 삭제하려면 시스템 할당 ID를 사용해야 합니다.


| 시나리오| 권장|메모|
|---|---|---|
| 관리 ID를 사용하여 빠르게 리소스(예: 임시 컴퓨팅) 만들기 |  사용자 할당 ID | 짧은 시간 안에 여러 개의 관리 ID를 만들려고 하는 경우, 예를 들어 자체 시스템 할당 ID를 사용하여 여러 가상 머신을 배포하는 경우 Azure Active Directory 개체 만들기에 대한 속도 제한을 초과하게 되어 요청이 HTTP 429 오류를 나타내며 실패합니다. <br/><br/>리소스를 신속하게 만들거나 삭제하는 경우 시스템 할당 ID를 사용할 때 Azure Active Directory의 리소스 수 제한을 초과할 수도 있습니다. 삭제된 시스템 할당 ID는 리소스에서 더 이상 액세스할 수 없지만 30일 후에 완전히 제거될 때까지 제한 계산에 포함됩니다.<br/><br/>단일 사용자 할당 ID와 연결된 리소스를 배포하는 경우에는 Azure Active Directory에서 서비스 주체를 하나만 만들도록 요구하여 속도 제한을 피합니다. 또한 미리 생성된 단일 ID를 사용하면 자체 ID를 사용하여 여러 리소스를 만들 때 발생할 수 있는 복제 지연 위험이 줄어듭니다.<br/><br/>[Azure 구독 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits)에 대해 자세히 알아보세요. |
| 복제된 리소스/애플리케이션 | 사용자 할당 ID  |  동일 작업을 수행하는 리소스(예: 중복 웹 서버 또는 동일한 기능을 앱 서비스 및 가상 머신의 애플리케이션에서 실행)는 일반적으로 동일한 권한이 필요합니다. <br/><br/>동일한 사용자 할당 ID를 사용하여 역할 할당을 줄임으로써 관리 오버헤드를 줄여야 합니다. 리소스가 동일한 형식일 필요는 없습니다. 
|규정 준수| 사용자 할당 ID  | 조직에서 모든 ID 생성이 승인 프로세스를 거쳐야 하는 경우에는 여러 리소스에 대해 단일 사용자 할당 ID를 사용하면 새 리소스가 만들어질 때 생성되는 시스템 할당 ID보다 더 낮은 승인이 필요합니다. |
리소스를 배포하기 전에 액세스 필요 |사용자 할당 ID| 일부 리소스의 경우 배포의 일부로 특정 Azure 리소스에 액세스해야 할 수 있습니다.<br/><br/>이 경우 시스템 할당 ID를 시간 내에 만들지 못할 수 있으므로 기존 사용자 할당 ID를 사용해야 합니다.|
감사 로깅|시스템 할당 ID|ID가 아니라 동작을 수행한 특정 리소스를 기록해야 하는 경우 시스템 할당 ID를 사용합니다.|
사용 권한 수명 주기 관리|시스템 할당 ID|리소스에 대한 사용 권한을 리소스와 함께 제거해야 하는 경우 시스템 할당 ID를 사용합니다.

### <a name="using-user-assigned-identities-to-reduce-administration"></a>사용자 할당 ID를 사용하여 관리 줄이기

다이어그램은 시스템 할당 ID와 사용자 할당 ID를 여러 가상 머신에서 두 개의 스토리지 계정에 액세스할 수 있도록 하는 데 사용할 경우의 차이점을 보여 줍니다. 

다이어그램에는 시스템 할당 ID를 사용하는 네 개의 가상 머신이 표시됩니다. 각 가상 머신에는 두 개의 스토리지 계정에 대한 액세스 권한을 부여하는 동일한 역할 할당이 있습니다.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities.png" alt-text="스토리지 계정 및 Key Vault에 액세스하기 위해 시스템 할당 ID를 사용하는 4개의 가상 머신":::

사용자 할당 ID가 4개의 가상 머신과 연결된 경우 시스템 할당 ID를 사용한 8개 가상 머신과 비교할 때 역할 할당이 2개만 필요합니다. 가상 머신의 ID가 추가 역할 할당을 요구하는 경우 이 ID와 연결된 모든 리소스에 부여됩니다.

:::image type="content" source="media/managed-identity-best-practice-recommendations/user-assigned-identities.png" alt-text="사용자 할당 ID를 사용하여 스토리지 계정 및 Key Vault에 액세스 하는 4개의 가상 머신":::

보안 그룹을 사용하여 필요한 역할 할당의 수를 줄일 수도 있습니다. 이 다이어그램에서는 시스템 할당 ID는 보안 그룹에 추가되었고 시스템 할당 ID 대신 역할 할당이 그룹에 추가된 4개의 가상 머신을 보여 줍니다. 결과가 유사하지만 이 구성은 사용자 할당 ID와 동일한 Resource Manager 템플릿 기능을 제공하지 않습니다.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities-in-a-group.png" alt-text="역할 할당을 포함하는 보안 그룹에 시스템 할당 ID가 추가된 4개의 가상 머신":::

### <a name="multiple-managed-identities"></a>여러 관리 ID

관리 ID를 지원하는 리소스에는 시스템 할당 ID와 하나 이상의 사용자 할당 ID가 포함될 수 있습니다. 

이 모델은 공유 사용자 할당 ID를 사용하고 필요한 경우 세부적인 사용 권한을 적용할 수 있는 유연성을 제공합니다.

아래 예제에서 "가상 머신 3" 및 "가상 머신 4"는 인증하는 동안 사용하는 사용자 할당 ID에 따라 스토리지 계정 및 Key Vault 모두에 액세스할 수 있습니다.

:::image type="content" source="media/managed-identity-best-practice-recommendations/multiple-user-assigned-identities.png" alt-text="사용자 할당 ID가 여러 개 있는 가상 머신 4개":::

아래 예제에서 "가상 머신 4"에는 인증하는 동안 사용되는 ID에 따라, 스토리지 계정과 Key Vault 둘 다에 액세스할 수 있도록 하는 사용자 할당 ID가 있습니다. 시스템 할당 ID에 대한 역할 할당은 해당 가상 머신에만 적용됩니다.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-and-user-assigned-identities.png" alt-text="시스템 할당 ID와 사용자 할당 ID를 모두 포함하는 4개의 가상 머신":::

## <a name="limits"></a>제한 

[관리 ID](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits) 및 [사용자 지정 역할 및 역할 할당](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits)에 대한 제한을 확인합니다.

## <a name="follow-the-principle-of-least-privilege-when-granting-access"></a>액세스 권한을 부여할 때 최소 권한의 원칙을 따릅니다.

관리 ID를 포함하여 ID를 부여하는 경우 서비스에 액세스할 수 있는 권한은 항상 원하는 작업을 수행하는 데 필요한 최소 권한을 부여합니다. 예를 들어 관리 ID를 사용하여 스토리지 계정에서 데이터를 읽는 경우 해당 ID 권한이 스토리지 계정에 데이터를 기록하도록 허용할 필요가 없습니다. 추가 권한을 부여합니다. 예를 들어 관리 ID를 Azure 구독에 대한 기여자로 설정하는 것은 필요하지 않을 때 해당 ID와 연결된 보안 폭발 반지름이 늘어납니다. 해당 ID를 손상시키는 것으로 인해 최소 손상이 발생하도록 하기 위해 보안 폭발 반지름을 항상 최소화해야 합니다.

### <a name="consider-the-effect-of-assigning-managed-identities-to-azure-resources"></a>Azure 리소스에 관리 ID를 할당하는 효과 고려

Azure 논리 앱, Azure 함수 또는 Virtual Machine 등과 같은 Azure 리소스에 관리 ID가 할당되면 이제 관리 ID에 부여된 모든 사용 권한을 Azure 리소스에서 사용할 수 있습니다. 이는 사용자가 이 리소스에 대한 코드를 설치하거나 실행할 수 있는 권한이 있는 경우 사용자가 Azure 리소스에 할당/연결된 모든 ID에 액세스할 수 있기 때문에 특히 중요합니다. 관리 ID의 목적은 해당 액세스를 얻기 위해 개발자가 코드에 직접 자격 증명을 처리하거나 배치하지 않고도 다른 리소스에 대한 Azure 리소스 액세스를 실행하는 코드를 제공하는 것입니다.

예를 들어 관리 ID(ClientId = 1234)에 ***StorageAccount7755** _에 대한 읽기/쓰기 액세스 권한을 부여하고 _*_LogicApp3388_*_ 에 할당한 경우 관리 ID 또는 스토리지 계정에 대한 직접 권한이 없지만 _*_LogicApp3388_*_ 내에서 코드를 실행할 권한이 있는 Alice는 관리 ID를 사용하는 코드를 실행하여 _ *_StorageAccount7755_** 간에 데이터를 읽고 쓸 수도 있습니다.

:::image type="content" source="media/managed-identity-best-practice-recommendations/security-considerations.png" alt-text="보안 시나리오":::

일반적으로 사용자에게 코드를 실행할 수 있는 리소스에 대한 관리 액세스 권한을 부여하고(예: 논리 앱) 관리 ID를 사용하는 경우 사용자에게 할당되는 역할에서 리소스에 대한 코드를 설치하거나 실행할 수 있으며 사용자가 필요한 경우에만 해당 역할을 할당하는 경우를 고려해야 합니다.


## <a name="maintenance"></a>유지 관리

시스템 할당 ID는 리소스가 삭제될 때 자동으로 삭제되지만 사용자 할당 ID의 수명 주기는 연결된 리소스와는 별개입니다.

연결된 리소스가 없더라도 더 이상 필요하지 않은 사용자 할당 ID를 수동으로 삭제해야 합니다.

시스템 할당 또는 사용자 할당 관리 ID가 삭제되어도 역할 할당은 자동으로 삭제되지 않습니다. 이러한 역할 할당을 수동으로 삭제하여 구독당 역할 할당 제한을 초과하지 않도록 해야 합니다. 

삭제된 관리 ID와 연결된 역할 할당은 포털에서 볼 때 "ID를 찾을 수 없음"으로 표시됩니다. [자세히 알아봅니다](../../role-based-access-control/troubleshooting.md#role-assignments-with-identity-not-found).

:::image type="content" source="media/managed-identity-best-practice-recommendations/identity-not-found.png" alt-text="역할 할당에 대한 ID를 찾을 수 없음":::
