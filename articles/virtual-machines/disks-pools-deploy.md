---
title: Azure 디스크 풀(미리 보기) 배포
description: Azure 디스크 풀을 배포하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/29/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 72a25b6bc51732ac9b598cbcb6b45f9ac84fc21b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351060"
---
# <a name="deploy-an-azure-disk-pool-preview"></a>Azure 디스크 풀(미리 보기) 배포

이 문서에서는 Azure 디스크 풀(미리 보기)을 배포하고 구성하는 방법을 설명합니다. 디스크 풀을 배포하기 전에 [개념](disks-pools.md) 및 [계획](disks-pools-planning.md) 문서를 읽어 보세요.

디스크 풀이 제대로 작동하려면 다음 단계를 완료해야 합니다.
- 미리 보기 구독을 등록합니다.
- 서브넷을 디스크 풀에 위임합니다.
- 디스크 리소스 관리를 위해 디스크 풀 RBAC(역할 기반 액세스 제어) 권한의 리소스 공급자를 할당합니다.
- 디스크를 만듭니다.
    - 디스크를 디스크 풀에 추가합니다.


## <a name="prerequisites"></a>필수 구성 요소

디스크 풀을 성공적으로 배포하려면 다음이 있어야 합니다.

- 디스크 풀에 추가하려는 관리 디스크 세트
- 디스크 풀에 사용할 전용 서브넷이 있는 가상 네트워크

Azure PowerShell 모듈을 사용하려면 [버전 6.1.0 이상](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)을 설치하세요.

Azure CLI를 사용하려면 [최신 버전](/cli/azure/disk-pool)을 설치하세요.

## <a name="register-your-subscription-for-the-preview"></a>미리 보기 구독 등록

디스크 풀을 만들고 사용할 수 있도록 **Microsoft.StoragePool** 공급자에 구독을 등록합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Portal 메뉴에서 **구독** 을 검색하여 선택합니다.
1. 디스크 풀에 사용하려는 구독을 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **리소스 공급자** 를 선택합니다.
1. 리소스 공급자 **Microsoft.StoragePool** 을 찾아서 **등록** 을 선택합니다.

구독이 등록되었으면 디스크 풀을 배포할 수 있습니다.

## <a name="delegate-subnet-permission"></a>서브넷 권한 위임

디스크 풀이 클라이언트 머신과 함께 작동하려면 Azure 디스크 풀에 서브넷을 위임해야 합니다. 디스크 풀을 만들 때 가상 네트워크와 위임된 서브넷을 지정합니다. 새 서브넷을 만들 수도 있고 기존 서브넷을 사용하여 **Microsoft.StoragePool/diskPools** 리소스 공급자에게 위임할 수도 있습니다.

1. Azure Portal의 가상 네트워크 창으로 이동하여 디스크 풀에 사용할 가상 네트워크를 선택합니다.
1. 가상 네트워크 창에서 **서브넷** 을 선택하고 **+서브넷** 을 선택합니다.
1. **서브넷 추가** 창에서 다음 필수 필드를 완료하여 새 서브넷을 만듭니다. 서브넷 위임: Microsoft.StoragePool 선택

서브넷 위임에 대한 자세한 내용은 [서브넷 위임 추가 또는 제거](../virtual-network/manage-subnet-delegation.md)를 참조하세요.

## <a name="assign-storagepool-resource-provider-permissions"></a>StoragePool 리소스 공급자 권한 할당

디스크를 디스크 풀에서 사용하려면 디스크가 다음 요구 사항을 충족해야 합니다.

- **StoragePool** 리소스 공급자에는 디스크 풀의 모든 관리 디스크에 대한 **읽기** 및 **쓰기** 권한이 있는 RBAC 역할이 할당되어야 합니다.
- 디스크 풀과 동일한 가용성 영역에 있는 프리미엄 SSD 또는 울트라 디스크여야 합니다.
    - 울트라 디스크인 경우 디스크 섹터 크기가 512바이트여야 합니다.
- maxShares 값이 2 이상인 공유 디스크여야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 디스크를 포함하는 리소스 그룹 또는 각 디스크 자체를 검색하여 선택합니다.
1. **액세스 제어(IAM)** 를 선택합니다.
1. **역할 할당 추가(미리 보기)** 를 선택하고 역할 목록에서 **디스크 풀 운영자를** 선택합니다.

    원한다면 고유의 사용자 지정 역할을 만들 수도 있습니다. 디스크 풀의 사용자 지정 역할이 작동하려면 **Microsoft.Compute/disks/write** 및 **Microsoft.Compute/disks/read** RBAC 권한이 있어야 합니다.

1. **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.
1. **+ 구성원 선택** 을 선택하고, **StoragePool 리소스 공급자** 를 검색하여 선택하고 저장합니다.

## <a name="create-a-disk-pool"></a>디스크 풀 만들기

최적의 성능을 위해 클라이언트의 동일한 가용성 영역에 디스크 풀을 배포해야 합니다. Azure VMware Solution 클라우드에 사용할 디스크 풀을 배포하려는데 가용성 영역을 확인하는 방법에 대한 지침이 필요한 경우 이 [양식](https://aka.ms/DiskPoolCollocate)을 작성하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. **디스크 풀** 을 검색하여 선택합니다.
1. **+추가** 를 선택하여 새 디스크 풀을 만듭니다.
1. 요청된 세부 정보를 입력하고, 디스크 풀을 사용할 클라이언트와 동일한 지역 및 가용성 영역을 선택합니다.
1. **StoragePool** 리소스 공급자에 위임된 서브넷, 그리고 이 서브넷과 연결된 가상 네트워크를 선택합니다.
1. **다음** 을 선택하여 디스크를 디스크 풀에 추가합니다.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool.png" alt-text="디스크 풀을 만드는 기본 창의 스크린샷":::

### <a name="add-disks"></a>디스크 추가

#### <a name="prerequisites"></a>필수 구성 요소

디스크를 추가하려면 디스크가 다음 요구 사항을 충족해야 합니다.

- 디스크 풀과 동일한 가용성 영역에 있는 프리미엄 SSD 또는 울트라 디스크여야 합니다.
    - 현재는 포털에서만 프리미엄 SSD를 추가할 수 있습니다. 울트라 디스크는 Azure PowerShell 모듈이나 Azure CLI를 사용하여 추가해야 합니다.
    - 울트라 디스크인 경우 디스크 섹터 크기가 512바이트여야 합니다.
- maxShares 값이 2 이상인 공유 디스크여야 합니다.
- 추가하려는 디스크를 관리할 수 있도록 디스크 풀의 리소스 공급자에게 RBAC 권한을 부여해야 합니다.

디스크가 이러한 요구 사항을 충족하는 경우 디스크 풀 창에서 **+디스크 추가** 를 선택하여 디스크 풀에 디스크를 추가할 수 있습니다.

:::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-disks-blade.png" alt-text="디스크 풀을 만드는 디스크 창의 스크린샷. 디스크 추가 옵션이 강조 표시된 모습":::

### <a name="enable-iscsi"></a>iSCSI 사용

1. **iSCSI** 창을 선택합니다.
1. **iSCSI 사용** 을 선택합니다.
1. iSCSI 대상의 이름을 입력합니다. iSCSI 대상 IQN은 이 이름을 기반으로 생성됩니다.
    - 개별 디스크에 iSCSI 대상을 사용하지 않으려면 개별 디스크의 **상태** 에서 **사용 안 함** 을 선택합니다.
    - ACL 모드는 기본적으로 **동적** 으로 설정됩니다. 디스크 풀을 Azure VMware Solution의 스토리지 솔루션으로 사용하려면 ACL 모드를 **동적** 으로 설정해야 합니다.
1. **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-iscsi-blade.png" alt-text="디스크 풀을 만드는 iscsi 창의 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

제공된 스크립트는 다음 작업을 수행합니다.
- 디스크 풀을 만들고 사용하는 데 필요한 모듈을 설치합니다.
- 디스크를 만들고 이 디스크에 RBAC 권한을 할당합니다. 이미 이 작업을 수행한 경우 스크립트의 해당 섹션을 주석으로 처리할 수 있습니다.
- 디스크 풀을 만들고 디스크 풀에 추가합니다.
- iSCSI 대상을 만들고 사용하도록 설정합니다.

스크립트의 변수를 각자 적절한 변수로 바꾸고 스크립트를 실행합니다. 또한 울트라 디스크 양식을 작성한 경우에는 기존 울트라 디스크를 사용하도록 스크립트를 수정해야 합니다.

```azurepowershell
# Install the required module for Disk Pool
Install-Module -Name Az.DiskPool -RequiredVersion 0.1.1 -Repository PSGallery

# Sign in to the Azure account and setup the variables
$subscriptionID = "<yourSubID>"
Set-AzContext -Subscription $subscriptionID
$resourceGroupName= "<yourResourceGroupName>"
$location = "<desiredRegion>"
$diskName = "<desiredDiskName>"
$availabilityZone = "<desiredAvailabilityZone>"
$subnetId='<yourSubnetID>'
$diskPoolName = "<desiredDiskPoolName>"
$iscsiTargetName = "<desirediSCSITargetName>" # This will be used to generate the iSCSI target IQN name
$lunName = "<desiredLunName>"

# You can skip this step if you have already created the disk and assigned proper RBAC permission to the resource group the disk is deployed to
$diskconfig = New-AzDiskConfig -Location $location -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -zone $availabilityZone -MaxSharesCount 2
$disk = New-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -Disk $diskconfig
$diskId = $disk.Id
$scopeDef = "/subscriptions/" + $subscriptionId + "/resourceGroups/" + $resourceGroupName
$rpId = (Get-AzADServicePrincipal -SearchString "StoragePool Resource Provider").id

New-AzRoleAssignment -ObjectId $rpId -RoleDefinitionName "Virtual Machine Contributor" -Scope $scopeDef

# Create a Disk Pool
New-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $subnetId -AvailabilityZone $availabilityZone -SkuName Standard
$diskpool = Get-AzDiskPool -ResourceGroupName $resourceGroupName -Name $DiskPoolName

# Add disks to the Disk Pool
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskId
$lun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $diskId -Name $lunName

# Create an iSCSI Target and expose the disks as iSCSI LUNs
New-AzDiskPoolIscsiTarget -DiskPoolName $diskPoolName -Name $iscsiTargetName -ResourceGroupName $resourceGroupName -Lun $lun -AclMode Dynamic

Write-Output "Print details of the iSCSI target exposed on Disk Pool"

Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName | fl
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

제공된 스크립트는 다음 작업을 수행합니다.
- 디스크 풀을 만들고 사용하는 데 필요한 확장을 설치합니다.
- 디스크를 만들고 이 디스크에 RBAC 권한을 할당합니다. 이미 이 작업을 수행한 경우 스크립트의 해당 섹션을 주석으로 처리할 수 있습니다.
- 디스크 풀을 만들고 디스크 풀에 추가합니다.
- iSCSI 대상을 만들고 사용하도록 설정합니다.

스크립트의 변수를 각자 적절한 변수로 바꾸고 스크립트를 실행합니다. 또한 울트라 디스크 양식을 작성한 경우에는 기존 울트라 디스크를 사용하도록 스크립트를 수정해야 합니다.

```azurecli
# Add disk pool CLI extension
az extension add -n diskpool

#az extension add -s https://zuhdefault.blob.core.windows.net/cliext/diskpool-0.1.1-py3-none-any.whl

#Select subscription
az account set --subscription "<yourSubscription>"

##Initialize input parameters
resourceGroupName='<yourRGName>'
location='<desiredRegion>'
zone=<desiredZone>
subnetId='<yourSubnetID>'
diskName='<desiredDiskName>'
diskPoolName='<desiredDiskPoolName>'
targetName='<desirediSCSITargetName>'
lunName='<desiredLunName>'

#You can skip this step if you have already created the disk and assigned permission in the prerequisite step. Below is an example for premium disks.
az disk create --name $diskName --resource-group $resourceGroupName --zone $zone --location $location --sku Premium_LRS --max-shares 2 --size-gb 1024

#You can deploy all your disks into one resource group and assign StoragePool Resource Provider permission to the group
storagePoolObjectId=$(az ad sp list --filter "displayName eq 'StoragePool Resource Provider'" --query "[0].objectId" -o json)
storagePoolObjectId="${storagePoolObjectId%"}"
storagePoolObjectId="${storagePoolObjectId#"}"

az role assignment create --assignee-object-id $storagePoolObjectId --role "Virtual Machine Contributor" --resource-group $resourceGroupName

#Create a disk pool 
az disk-pool create --name $diskPoolName \
--resource-group $resourceGroupName \
--location $location \
--availability-zones $zone \
--subnet-id $subnetId \
--sku name="Standard"

#Initialize an iSCSI target. You can have 1 iSCSI target per disk pool
az disk-pool iscsi-target create --name $targetName \
--disk-pool-name $diskPoolName \
--resource-group $resourceGroupName \
--acl-mode Dynamic

#Add the disk to disk pool
diskId=$(az disk show --name $diskName --resource-group $resourceGroupName --query "id" -o json)
diskId="${diskId%"}"
diskId="${diskId#"}"

az disk-pool update --name $diskPoolName --resource-group $resourceGroupName --disks $diskId

#Expose disks added in the Disk Pool as iSCSI Lun 
az disk-pool iscsi-target update --name $targetName \
 --disk-pool-name $diskPoolName \
 --resource-group $resourceGroupName \
 --luns name=$lunName managed-disk-azure-resource-id=$diskId
```
---

## <a name="next-steps"></a>다음 단계

- 디스크 풀을 배포하는 동안 문제가 발생하는 경우 [Azure 디스크 풀(미리 보기) 문제 해결](disks-pools-troubleshoot.md)을 참조하세요.
- [Azure VMware Solution 호스트에 디스크 풀을 연결](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md)합니다(미리 보기).
- [디스크 풀을 관리](disks-pools-manage.md)합니다.
