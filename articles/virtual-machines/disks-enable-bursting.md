---
title: 주문형 디스크 버스팅 사용
description: 관리 디스크에서 주문형 디스크 버스팅을 사용하도록 설정합니다.
author: roygara
ms.author: rogarana
ms.date: 11/09/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 27a832f7f02fd10cde7f3c9f603f38d0d5e466b6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136188"
---
# <a name="enable-on-demand-bursting"></a>주문형 버스트 사용

프리미엄 SSD(반도체 드라이브)에는 크레딧 기반 버스팅과 주문형 버스팅이라는 사용 가능한 두 가지 버스팅 모델이 있습니다. 이 문서에서는 주문형 버스팅으로 전환하는 방법을 설명합니다. 주문형 모델을 사용하는 디스크는 원래 프로비전된 대상을 벗어나 버스트할 수 있습니다. 주문형 버스팅은 최대 버스트 대상까지 워크로드에 필요한 만큼 자주 발생합니다. 주문형 버스팅에는 추가 요금이 발생합니다.

디스크 버스팅에 대한 자세한 내용은 [관리 디스크 버스팅](disk-bursting.md)을 참조하세요. 

지원 되는 각 디스크의 최대 버스트 목표는 [VM 디스크의 확장성 및 성능 목표](disks-scalability-targets.md#premium-ssd-managed-disks-per-disk-limits)를 참조 하세요.

> [!IMPORTANT]
> 크레딧 기반 버스팅을 사용하기 위해 이 문서의 단계를 따를 필요가 없습니다. 기본적으로 크레딧 기반 버스팅은 모든 적격 디스크에서 활성화됩니다.

주문형 버스팅을 사용하도록 설정하기 전에 다음 사항을 이해해야 합니다.

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>시작

주문형 버스팅은 Azure PowerShell 모듈, Azure CLI 또는 Azure Resource Manager 템플릿 중 하나를 사용하여 활성화할 수 있습니다. 다음 예제에서는 주문형 버스팅이 활성화된 새 디스크를 생성하고 기존 디스크에서 주문형 버스팅을 활성화하는 방법을 설명합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

주문형 버스팅 cmdlet은 버전 5.5.0 이상의 Az 모듈에서 사용할 수 있습니다. 또는 [Azure Cloud Shell](https://shell.azure.com/)을 사용할 수 있습니다.
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>주문형 버스팅을 사용하여 빈 데이터 디스크 만들기

주문형 버스팅을 사용하려면 관리 디스크가 512GiB보다 커야 합니다. `<myResourceGroupDisk>` 및 `<myDataDisk>` 매개 변수를 바꾼 후, 다음 스크립트를 실행하여 주문형 버스트팅으로 프리미엄 SSD를 만듭니다.

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>기존 디스크에서 주문형 버스팅 사용

주문형 버스팅을 사용하려면 관리 디스크가 512GiB보다 커야 합니다. `<myResourceGroupDisk>`, `<myDataDisk>` 매개 변수를 바꾸고 이 명령을 실행하여 기존 디스크에서 주문형 버스팅을 사용하도록 설정합니다.

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

주문형 버스팅 cmdlet은 버전 2.19.0 이상의 [Azure CLI 모듈](/cli/azure/install-azure-cli)에서 사용할 수 있습니다. 또는 [Azure Cloud Shell](https://shell.azure.com/)을 사용할 수 있습니다.

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>주문형 버스팅 데이터 디스크 만들기 및 연결

주문형 버스팅을 사용하려면 관리 디스크가 512GiB보다 커야 합니다. `<yourDiskName>`, `<yourResourceGroup>` 및 `<yourVMName>` 매개 변수를 바꾼 후, 다음 명령을 실행하여 주문형 버스트팅으로 프리미엄 SSD를 만듭니다.

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>기존 디스크에서 주문형 버스팅 사용 - CLI

주문형 버스팅을 사용하려면 관리 디스크가 512GiB보다 커야 합니다. `<myResourceGroupDisk>` 및 `<yourDiskName>` 매개 변수를 바꾸고 이 명령을 실행하여 기존 디스크에서 주문형 버스팅을 사용하도록 설정합니다.

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

`2020-09-30` 디스크 API를 사용하면 새로 만들거나 512GiB보다 큰 기존 프리미엄 SSD에서 주문형 버스팅을 활성화할 수 있습니다. `2020-09-30` API에서는 새로운 속성인 `burstingEnabled`를 도입했습니다. 기본적으로 이 속성은 false로 설정됩니다. 다음 샘플 템플릿은 미국 중서부에서 디스크 버스팅이 활성화된 1TiB 프리미엄 SSD를 만듭니다.

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>다음 단계

버스팅 리소스에 대한 인사이트를 얻는 방법을 알아보려면 [디스크 버스팅 메트릭](disks-metrics.md)을 참조하세요.
