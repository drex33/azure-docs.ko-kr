---
title: 호스트 - Azure Portal - 관리 디스크에서 암호화를 사용하여 엔드투엔드 암호화 사용
description: 호스트에서 암호화를 사용하여 Azure 관리 디스크 - Azure Portal에서 엔드투엔드 암호화를 사용하도록 설정합니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 518ce933938003584c106cc569d31b64e0d26b05
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132760317"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure Portal을 사용하여 호스트에서 암호화를 사용하는 엔드투엔드 암호화를 사용하도록 설정

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VM

호스트에서 암호화를 사용하도록 설정하면 VM 호스트에 저장된 데이터는 미사용 및 스토리지 서비스로 암호화된 흐름으로 암호화됩니다. 호스트에서 암호화 및 기타 관리 디스크 암호화 유형에 대한 개념 정보는 [호스트에서 암호화 - VM 데이터에 대한 엔드투엔드 암호화](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)를 참조하세요.

엔드투엔드 암호화를 사용하는 경우 임시 디스크와 임시 OS 디스크는 플랫폼 관리형 키를 사용하여 미사용 시 암호화됩니다. OS 및 데이터 디스크 캐시는 선택한 디스크 암호화 유형에 따라 고객 관리형 키 또는 플랫폼 관리형 키를 사용하여 미사용 데이터를 암호화합니다. 예를 들어 디스크가 고객 관리형 키를 사용하여 암호화된 경우 디스크의 캐시는 고객 관리형 키를 사용하여 암호화되고, 디스크가 플랫폼 관리형 키를 사용하여 암호화된 경우 디스크의 캐시는 플랫폼 관리형 키를 사용하여 암호화됩니다.

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>지원되는 VM 크기

레거시 VM 크기는 지원되지 않습니다. [Azure PowerShell 모듈을](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) [사용하거나](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes)Azure CLI 지원되는 VM 크기 목록을 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

VM/VMSS에 대한 EncryptionAtHost 속성을 사용하기 전에 구독에 이 기능을 사용하도록 설정해야 합니다. 구독 기능을 사용하도록 설정하려면 아래 단계를 따릅니다.

1. **Azure Portal**: [Azure Portal](https://portal.azure.com)에서 Cloud Shell 아이콘을 선택합니다.

    ![Azure Portal에서 Cloud Shell을 시작하는 아이콘](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
1.  다음 명령을 실행하여 구독 기능을 등록합니다.

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  기능을 사용해 보기 전에 아래 명령을 사용하여 등록 상태가 **등록됨**(몇 분 소요)인지 확인합니다.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


[제공된 링크](https://aka.ms/diskencryptionupdates)를 사용하여 Azure Portal에 로그인합니다.

> [!IMPORTANT]
> [제공된 링크](https://aka.ms/diskencryptionupdates)를 사용하여 Azure Portal에 액세스해야 합니다. 현재 호스트에서 암호화는 링크를 사용하지 않고 공용 Azure Portal에 표시되지 않습니다.

## <a name="deploy-a-vm-with-platform-managed-keys"></a>플랫폼 관리형 키를 통해 VM 배포

1. [Azure Portal](https://aka.ms/diskencryptionupdates)에 로그인합니다.
1. **가상 머신** 을 검색하고 **+ 추가** 를 선택하여 VM을 만듭니다.
1. 새 가상 머신을 만들고 적절한 지역과 지원되는 VM 크기를 선택합니다.
1. 원하는 대로 **기본** 창에 다른 값을 입력한 다음 **디스크** 창으로 진행합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="가상 머신 생성하기 기본 창, 지역 및 VM 크기가 강조 표시된 스크린샷.":::

1. **디스크** 창에서 **호스트에서 암호화** 를 선택합니다.
1. 원하는 대로 나머지 항목을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/host-based-encryption-platform-keys.png" alt-text="호스트의 암호화가 강조 표시된 가상 머신 만들기 디스크 창의 스크린샷.":::

1. VM 배포 프로세스를 마치고 환경에 맞는 선택을 합니다.

이제 호스트에서 암호화를 사용하도록 설정된 VM을 배포했으며, 디스크의 캐시는 플랫폼 관리형 키를 사용하여 암호화됩니다.

## <a name="deploy-a-vm-with-customer-managed-keys"></a>고객 관리형 키를 통해 VM 배포

또는 고객 관리형 키를 사용하여 디스크 캐시를 암호화할 수 있습니다.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault 및 디스크 암호화 집합 만들기

기능을 사용하도록 설정한 후에는 Azure Key Vault 및 디스크 암호화 집합을 아직 설정하지 않은 경우 설정해야 합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM 배포

이제 Azure Key Vault 및 디스크 암호화 집합을 설정했으므로 VM을 배포할 수 있으며 호스트에서 암호화를 사용합니다.

1. [Azure Portal](https://aka.ms/diskencryptionupdates)에 로그인합니다.
1. **가상 머신** 을 검색하고 **+ 추가** 를 선택하여 VM을 만듭니다.
1. 새 가상 머신을 만들고 적절한 지역과 지원되는 VM 크기를 선택합니다.
1. 원하는 대로 **기본** 창에 다른 값을 입력한 다음 **디스크** 창으로 진행합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="가상 머신 생성하기 기본 창, 지역 및 VM 크기가 강조 표시된 스크린샷.":::

1. **디스크** 창에서 **SSE 암호화 유형** 에 대한 **고객 관리형 키에 대해 미사용 데이터 암호화** 를 선택하고 디스크 암호화 집합을 선택합니다.
1. **호스트에서 암호화** 를 선택합니다.
1. 원하는 대로 나머지 항목을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-host-based-encryption-customer-managed-keys.png" alt-text="가상 머신 생성하기 디스크 창, 호스트의 암호화가 강조 표시되고 고객 관리형 키가 선택된 스크린샷.":::

1. VM 배포 프로세스를 마치고 환경에 맞는 선택을 합니다.

호스트에서 암호화를 사용하도록 설정된 VM을 배포했습니다.

## <a name="disable-host-based-encryption"></a>호스트 기반 암호화 해제

VM의 할당이 먼저 취소되었는지 확인합니다. VM의 할당을 취소하지 않으면 호스트에서 암호화를 사용하지 않도록 설정할 수 없습니다.

1. VM에서, **디스크** 를 선택하고 **추가 설정** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-host-based-encryption-additional-settings.png" alt-text="추가 설정 강조 표시되어 있는 VM의 디스크 창 스크린샷.":::

1. **호스트에서 암호화에** 대해 **아니요** 를 선택한 다음, **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
