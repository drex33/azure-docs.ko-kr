---
title: Azure Managed Disks의 성능 변경 - CLI/PowerShell
description: Azure PowerShell 모듈 또는 Azure CLI를 사용하여 기존 관리 디스크의 성능 계층을 변경하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 783299359b1b7b9cbe75fd36f534ac18563c0fee
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102931"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>Azure PowerShell 모듈 또는 Azure CLI를 사용하여 성능 계층 변경

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="prerequisites"></a>필수 구성 요소
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
최신 [Azure PowerShell 버전](/powershell/azure/install-az-ps)을 설치하고 `Connect-AzAccount`를 사용하여 Azure 계정에 로그인합니다.

---

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>기준 계층보다 높은 계층으로 빈 데이터 디스크 만들기
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Azure Marketplace 이미지에서 기준 계층보다 높은 계층으로 OS 디스크 만들기

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk-without-downtime"></a>가동 중지 시간 없이 디스크 계층 업데이트

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>필수 구성 요소

가동 중지 시간 없이 디스크의 성능 계층을 변경하려면 먼저 구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계에서 구독의 기능을 사용하도록 설정합니다.

1.  다음 명령을 실행하여 구독에 대한 기능 등록

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  기능을 사용해보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 소요될 수 있음)인지 확인합니다.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

가동 중지 시간 없이 디스크의 성능 계층을 변경하려면 먼저 구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계에서 구독의 기능을 사용하도록 설정합니다.

1.  다음 명령을 실행하여 구독에 대한 기능 등록

    ```azurepowershell
     Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
    ```
 
1.  기능을 사용해보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 소요될 수 있음)인지 확인합니다.

    ```azurepowershell
    Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
    ```

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>디스크 계층 표시

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>다음 단계

더 높은 성능 계층을 활용하기 위해 디스크 크기를 조정해야 하는 경우 다음 문서를 참조하세요.

- [Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크 확장](linux/expand-disks.md)
- [Windows 가상 머신에 연결된 관리 디스크 확장](windows/expand-os-disk.md)
