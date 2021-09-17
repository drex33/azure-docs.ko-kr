---
title: Azure Managed Disks에 대한 공유 디스크 사용
description: 여러 VM에서 공유할 수 있도록 공유 디스크가 있는 Azure Managed Disks 구성
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/01/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: bdf012d1ee6c1230e458b7b40e3130d8fa25e4a1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424782"
---
# <a name="enable-shared-disk"></a>공유 디스크 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

이 문서에서는 Azure Managed Disks에 대한 공유 디스크 기능을 사용하도록 설정하는 방법을 설명합니다. Azure 공유 디스크는 여러 VM(가상 머신)에 관리 디스크를 동시에 연결할 수 있게 해주는 Azure Managed Disks의 새로운 기능입니다. 관리 디스크를 여러 VM에 연결하면 새 클러스터된 애플리케이션을 배포하거나 기존 클러스터된 애플리케이션을 Azure로 마이그레이션할 수 있습니다. 

공유 디스크가 사용하도록 설정된 관리 디스크에 대한 개념 정보를 찾고 있는 경우 [Azure 공유 디스크](disks-shared.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 스크립트 및 명령에는 다음 중 하나가 필요합니다.

- Azure PowerShell 모듈의 버전 6.0.0 이상.

또는
- 최신 버전의 Azure CLI

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>지원되는 운영 체제

공유 디스크는 여러 운영 체제를 지원합니다. 지원되는 운영 체제에 대한 개념 문서의 [Windows](./disks-shared.md#windows) 및 [Linux](./disks-shared.md#linux) 섹션을 참조하세요.

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>공유 디스크 배포

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>프리미엄 SSD를 공유 디스크로 배포

공유 디스크 기능이 사용하도록 설정된 관리 디스크를 배포하려면 새 속성`maxShares`를 사용하고 1보다 큰 값을 정의합니다. 이렇게 하면 여러 VM에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> 모든 VM에서 디스크를 분리하는 경우에만 `maxShares` 값을 설정하거나 변경할 수 있습니다. `maxShares`에 허용되는 값은 [디스크 크기](#disk-sizes)를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에 로그인합니다. 
1. **디스크** 를 검색하고 선택합니다.
1. **+ 만들기** 를 선택하여 새 디스크를 만듭니다.
1. 세부 정보를 입력하고 적절한 지역을 선택한 다음 **크기 변경** 을 선택합니다.

    :::image type="content" source="media/disks-shared-enable/create-shared-disk-basics-pane.png" alt-text="관리 디스크 만들기 창의 스크린샷, 강조 표시된 크기 변경." lightbox="media/disks-shared-enable/create-shared-disk-basics-pane.png":::

1. 원하는 프리미엄 SSD 크기 및 SKU를 선택 하 고 **확인을** 선택 합니다.

    :::image type="content" source="media/disks-shared-enable/select-premium-shared-disk.png" alt-text="디스크 SKU, 프리미엄 LRS 및 ZRS SSD Sku의 스크린샷 강조 표시 됩니다." lightbox="media/disks-shared-enable/select-premium-shared-disk.png":::

1. **고급** 창에 도달할 때까지 배포를 진행합니다.
1. **공유 디스크 사용** 에 대해 **예** 를 선택하고 원하는 **최대 공유** 크기를 선택합니다.

    :::image type="content" source="media/disks-shared-enable/enable-premium-shared-disk.png" alt-text="고급 창의 스크린샷, 강조 표시된 공유 디스크를 사용하고 예로 설정합니다." lightbox="media/disks-shared-enable/enable-premium-shared-disk.png":::

1. **검토 + 만들기** 를 선택합니다.


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

다음 템플릿을 사용하기 전에 `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]` 및 `[parameters('maxShares')]`를 사용자 고유의 값으로 바꿉니다.

[프리미엄 SSD 공유 디스크 템플릿](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-a-standard-ssd-as-a-shared-disk"></a>표준 SSD를 공유 디스크로 배포

공유 디스크 기능이 사용하도록 설정된 관리 디스크를 배포하려면 새 속성`maxShares`를 사용하고 1보다 큰 값을 정의합니다. 이렇게 하면 여러 VM에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> 모든 VM에서 디스크를 분리하는 경우에만 `maxShares` 값을 설정하거나 변경할 수 있습니다. `maxShares`에 허용되는 값은 [디스크 크기](#disk-sizes)를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에 로그인합니다. 
1. **디스크** 를 검색하고 선택합니다.
1. **+ 만들기** 를 선택하여 새 디스크를 만듭니다.
1. 세부 정보를 입력하고 적절한 지역을 선택한 다음 **크기 변경** 을 선택합니다.

    :::image type="content" source="media/disks-shared-enable/create-shared-disk-basics-pane.png" alt-text="관리 디스크 만들기 창의 스크린샷, 강조 표시된 크기 변경." lightbox="media/disks-shared-enable/create-shared-disk-basics-pane.png":::

1. 원하는 표준 SSD 크기 및 SKU를 선택 하 고 **확인을** 선택 합니다.

    :::image type="content" source="media/disks-shared-enable/select-standard-ssd-shared-disk.png" alt-text="디스크 SKU의 스크린샷, 표준 SSD LRS 및 ZRS Sku가 강조 표시 됩니다." lightbox="media/disks-shared-enable/select-premium-shared-disk.png":::

1. **고급** 창에 도달할 때까지 배포를 진행합니다.
1. **공유 디스크 사용** 에 대해 **예** 를 선택하고 원하는 **최대 공유** 크기를 선택합니다.

    :::image type="content" source="media/disks-shared-enable/enable-premium-shared-disk.png" alt-text="고급 창의 스크린샷, 강조 표시된 공유 디스크를 사용하고 예로 설정합니다." lightbox="media/disks-shared-enable/enable-premium-shared-disk.png":::

1. **검토 + 만들기** 를 선택합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku StandardSSD_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType StandardSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

사용하기 전에 이 Azure Resource Manager 템플릿의 값을 자신의 값으로 바꿉니다.

```rest
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "StandardSSD_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>공유 디스크로 Ultra Disk 배포

공유 디스크 기능이 사용하도록 설정된 관리 디스크를 배포하려면 `maxShares` 매개 변수를 1보다 큰 값으로 변경합니다. 이렇게 하면 여러 VM에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> 모든 VM에서 디스크를 분리하는 경우에만 `maxShares` 값을 설정하거나 변경할 수 있습니다. `maxShares`에 허용되는 값은 [디스크 크기](#disk-sizes)를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에 로그인합니다. 
1. **디스크** 를 검색하고 선택합니다.
1. **+ 만들기** 를 선택하여 새 디스크를 만듭니다.
1. 세부 정보를 입력한 다음 **크기 변경** 을 선택합니다.
1. **디스크 SKU** 에 대해 울트라 디스크를 선택합니다.

    :::image type="content" source="media/disks-shared-enable/select-ultra-shared-disk.png" alt-text="디스크 SKU의 스크린샷, 강조 표시된 울트라 디스크.." lightbox="media/disks-shared-enable/select-ultra-shared-disk.png":::

1. 원하는 디스크 크기를 선택하고 **확인** 을 선택합니다.
1. **고급** 창에 도달할 때까지 배포를 진행합니다.
1. **공유 디스크 사용** 에 대해 **예** 를 선택하고 원하는 **최대 공유** 크기를 선택합니다.
1. **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="media/disks-shared-enable/enable-ultra-shared-disk.png" alt-text="고급 창의 스크린샷, 강조 표시된 공유 디스크 사용" lightbox="media/disks-shared-enable/enable-ultra-shared-disk.png":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>지역 디스크 예

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>영역 디스크 예

이 예제는 가용성 영역 1에 디스크를 생성한다는 점을 제외하면 이전 예제와 거의 동일합니다.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>지역 디스크 예

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>영역 디스크 예

이 예제는 가용성 영역 1에 디스크를 생성한다는 점을 제외하면 이전 예제와 거의 동일합니다.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>지역 디스크 예

다음 템플릿을 사용하기 전에 `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` 및 `[parameters('diskMBpsReadOnly')]`를 사용자 고유의 값으로 바꿉니다.

[지역 공유 Ultra Disks 템플릿](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>영역 디스크 예

다음 템플릿을 사용하기 전에 `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` 및 `[parameters('diskMBpsReadOnly')]`를 사용자 고유의 값으로 바꿉니다.

[영역 공유 Ultra Disks 템플릿](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>VM에서 Azure 공유 디스크 사용

`maxShares>1`로 공유 디스크를 배포한 후에는 하나 이상의 VM에 디스크를 탑재할 수 있습니다.

> [!NOTE]
> Ultra Disk를 배포하는 경우 필요한 요구 사항과 일치하는지 확인합니다. 자세한 내용은 [Azure Ultra Disks 사용](disks-enable-ultra-ssd.md)을 참조하세요.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>지원되는 SCSI PR 명령

공유 디스크를 클러스터의 VM에 탑재한 후에는 SCSI PR을 사용하여 쿼럼 및 디스크에 대한 읽기/쓰기를 설정할 수 있습니다. Azure 공유 디스크를 사용하는 경우 다음과 같은 PR 명령을 사용할 수 있습니다.

디스크와 상호 작용하려면 영구 예약 작업 목록으로 시작합니다.

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION 또는 PR_RELEASE_RESERVATION을 사용하는 경우 다음의 영구 예약 형식 중 하나를 제공합니다.

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION 또는 PR_RELEASE-RESERVATION을 사용하는 경우에도 영구 예약 키를 제공해야 합니다.


## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 사용하여 디스크를 배포하려는 경우 다음 샘플 템플릿을 사용할 수 있습니다.
- [프리미엄 SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [지역 Ultra Disks](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [영역 Ultra Disks](https://aka.ms/SharedUltraDiskARMtemplateZonal)
