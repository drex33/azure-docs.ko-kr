---
title: 삭제된 스토리지 계정 복구
titleSuffix: Azure Storage
description: Azure Portal에서 삭제된 스토리지 계정을 복구하는 방법에 대해 알아보세요.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 69d95ea67064ad45e60a6c38e37ab425bed27b76
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616881"
---
# <a name="recover-a-deleted-storage-account"></a>삭제된 스토리지 계정 복구

삭제된 스토리지 계정은 경우에 따라 Azure Portal에서 복구될 수 있습니다. 스토리지 계정을 복구하려면 다음 조건이 충족되어야 합니다.

- 스토리지 계정이 최근 14일 이내에 삭제되었습니다.
- 스토리지 계정은 Azure Resource Manager 배포 모델로 생성되었습니다.
- 원래 계정이 삭제된 후 이름이 같은 새 스토리지 계정이 만들어지지 않았습니다.
- 스토리지 계정을 복구하는 사용자는 **Microsoft.Storage/storageAccounts/write** 권한을 제공하는 Azure RBAC 역할이 할당되어야 합니다. 이 권한을 제공하는 기본 제공 Azure RBAC 역할에 관한 내용은 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

삭제된 스토리지 계정을 복구하기 전에 해당 계정의 리소스 그룹이 있는지 확인하세요. 리소스 그룹을 삭제한 경우 다시 생성해야 합니다. 리소스 그룹을 복구할 수 없습니다. 자세한 내용은 [리소스 그룹 관리](../../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.

삭제된 스토리지 계정에서 Azure Key Vault와 고객 관리형 키가 함께 사용되고 키 자격 증명 모음도 삭제된 경우, 스토리지 계정을 복원하기 전에 키 자격 증명 모음을 복원해야 합니다. 자세한 내용은 [Azure Key Vault 복구 개요](../../key-vault/general/key-vault-recovery.md)를 참조하세요.

> [!IMPORTANT]
> 삭제된 스토리지 계정은 복구는 보장되지 않습니다. 복구는 최선의 시도입니다. Microsoft는 실수로 인한 계정 삭제를 방지하기 위해 리소스 잠금을 권장합니다. 리소스 잠금에 대한 자세한 내용은 [변경 방지를 위한 리소스 잠금](../../azure-resource-manager/management/lock-resources.md)을 참조하세요.
>
> 실수로 인한 계정 삭제를 방지하기 위한 또 다른 모범 사례는 역할 기반 액세스 제어(Azure RBAC)를 통해 계정을 삭제할 수 있는 권한을 가진 사용자 수를 제한하는 것입니다. 자세한 내용은 [Azure RBAC에 대한 모범 사례](../../role-based-access-control/best-practices.md)를 참조하세요.

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Azure Portal에서 삭제된 계정 복구

다른 스토리지 계정에서 삭제된 스토리지 계정을 복구하려면 다음 단계를 수행하세요.

1. Azure Portal에서 기존 스토리지 계정의 개요 페이지로 이동하세요.
1. **지원 + 문제 해결** 섹션에서 **삭제된 계정 복구** 를 선택하세요.
1. 드롭다운에서 다음 이미지에 표시된 것처럼 복구할 계정을 선택하세요. 복구할 스토리지 계정이 드롭다운에 없는 경우 복구할 수 없습니다.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Azure Portal에서 스토리지 계정 복구 방법을 보여 주는 스크린샷":::

1. **복구** 버튼을 선택하여 계정을 복원하세요. 포털에 복구가 진행 중이라는 알림이 표시됩니다.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>지원 티켓을 통해 삭제된 계정 복구

1. Azure Portal에서 **도움말+ 지원** 으로 이동하세요.
1. **새 지원 요청** 을 선택합니다.
1. **기본** 탭의 **이슈 유형** 필드에서 **기술** 을 선택하세요.
1. **구독** 필드에서 삭제된 스토리지 계정이 포함된 구독을 선택하세요.
1. **서비스** 필드에서 **스토리지 계정 관리** 를 선택하세요.
1. **리소스** 필드에서 모든 스토리지 계정 리소스를 선택하세요. 삭제된 스토리지 계정은 목록에 표시되지 않습니다.
1. 문제에 대한 간략한 요약을 추가하세요.
1. **문제 유형** 필드에서 **삭제 및 복구** 를 선택하세요.
1. **문제 하위 유형** 필드에서 **삭제된 스토리지 계정 복구** 를 선택하세요. 다음 이미지는 **기본** 탭이 입력되는 예를 보여줍니다.

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="지원 티켓을 통해 스토리지 계정을 복구하는 방법을 보여주는 스크린샷 - 기본 탭":::

1. 다음으로, **솔루션** 탭으로 이동하여 다음 이미지에 표시된 것처럼 **고객 제어 스토리지 계정 복구** 를 선택하세요.

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="지원 티켓을 통해 스토리지 계정을 복구하는 방법을 보여주는 스크린샷 - 솔루션 탭":::

1. 드롭다운에서 다음 이미지에 표시된 것처럼 복구할 계정을 선택하세요. 복구할 스토리지 계정이 드롭다운에 없는 경우 복구할 수 없습니다.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="을 통해 스토리지 계정 복구 방법을 보여주는 스크린샷":::

1. **복구** 버튼을 선택하여 계정을 복원하세요. 포털에 복구가 진행 중이라는 알림이 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](storage-account-overview.md)
- [스토리지 계정을 만드는](storage-account-create.md)
- [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)
- [Azure Storage 계정을 다른 지역으로 이동](storage-account-move.md)
