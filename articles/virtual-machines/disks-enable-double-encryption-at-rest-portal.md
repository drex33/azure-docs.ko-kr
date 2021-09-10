---
title: 미사용 데이터 이중 암호화 사용 - Azure Portal - 관리 디스크
description: Azure Portal을 사용하여 관리 디스크 데이터에 대해 미사용 데이터 이중 암호화를 사용하도록 설정합니다.
author: roygara
ms.date: 06/29/2021
ms.topic: how-to
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b00a88895032af4c0ef9dd7b034b5687899c6045
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696439"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>Azure Portal을 사용하여 관리 디스크에 대해 미사용 데이터 이중 암호화를 사용하도록 설정합니다.

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark:

Azure Disk Storage는 관리 디스크에 대해 미사용 데이터 이중 암호화를 지원합니다. 미사용 데이터 이중 암호화 및 기타 관리 디스크 암호화 유형에 대한 개념 정보는 디스크 암호화 문서의 [미사용 데이터 이중 암호화](disk-encryption.md#double-encryption-at-rest) 섹션을 참조하세요.

## <a name="getting-started"></a>시작

1. [Azure Portal](https://aka.ms/diskencryptionupdates)에 로그인합니다.

    > [!IMPORTANT]
    > [제공된 링크](https://aka.ms/diskencryptionupdates)를 사용하여 Azure Portal에 액세스해야 합니다. 링크를 사용하지 않으면 미사용 데이터 이중 암호화가 퍼블릭 Azure Portal에 표시되지 않습니다.

1. **디스크 암호화 집합** 을 검색하고 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="디스크 암호화 집합이 검색 창에서 강조 표시된 기본 Azure Portal의 스크린샷":::

1. **+추가** 를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="스크린샷 +추가가 강조 표시된 디스크 암호화 집합 블레이드의 스크린샷":::

1. 지원되는 영역 중 하나를 선택합니다.
1. **암호화 유형** 에서 **플랫폼 관리형 키 및 고객 관리형 키를 사용한 이중 암호화** 를 선택합니다.

    > [!NOTE]
    > 특정 암호화 유형을 사용하여 디스크 암호화 집합을 만든 후에는 변경할 수 없습니다. 다른 암호화 유형을 사용하려면 새 디스크 암호화 집합을 만들어야 합니다.

1. 나머지 정보를 입력합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="플랫폼 관리형 키 및 고객 관리형 키를 사용하는 지역 및 이중 암호화가 강조 표시된 디스크 암호화 집합 만들기 블레이드의 스크린샷":::

1. Azure Key Vault 및 키를 선택하거나 필요한 경우 새로 만듭니다.

    > [!NOTE]
    > Key Vault 인스턴스를 만드는 경우 일시 삭제 및 제거 방지를 사용하도록 설정해야 합니다. 해당 설정은 관리 디스크를 암호화하는 데 Key Vault를 사용하는 경우 필수이며 실수로 인한 삭제로 인해 데이터가 손실되지 않도록 보호합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Key Vault 만들기 블레이드의 스크린샷":::

1. **만들기** 를 선택합니다.
1. 만든 디스크 암호화 집합으로 이동하여 표시되는 오류를 선택합니다. 이렇게 하면 디스크 암호화 집합이 작동하도록 구성됩니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="‘디스크, 이미지 또는 스냅샷을 이 디스크 암호화 집합에 연결하려면 Key Vault에 대한 권한을 부여해야 합니다’라는 오류가 표시된 디스크 암호화 집합의 스크린샷":::

    알림이 표시되고 성공해야 합니다. 이렇게 하면 디스크 암호화 집합을 Key Vault와 함께 사용할 수 있습니다.
    
    ![Key Vault에 대한 성공적인 권한 및 역할 할당의 스크린샷](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. 디스크로 이동합니다.
1. **암호화** 를 선택합니다.
1. **암호화 유형** 에서 **플랫폼 관리형 키 및 고객 관리형 키를 사용한 이중 암호화** 를 선택합니다.
1. 디스크 암호화 집합을 선택합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="앞서 언급한 암호화 유형이 강조 표시된 관리 디스크에 대한 암호화 블레이드의 스크린샷":::

이제 관리 디스크에서 미사용 데이터 이중 암호화를 사용하도록 설정했습니다.


## <a name="next-steps"></a>다음 단계

- [Azure PowerShell - 서버 측 암호화를 사용하여 고객 관리형 키 사용 - 관리 디스크](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [서버 측 암호화를 사용하여 고객 관리형 키 사용 - 예](./linux/disks-enable-customer-managed-keys-cli.md#examples)