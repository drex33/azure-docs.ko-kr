---
title: Azure CLI - 연결되지 않은 관리/비관리 디스크를 찾아서 삭제
description: Azure CLI를 사용하여 연결되지 않은 Azure 관리 및 비관리(VHD/페이지 Blob) 디스크를 찾고 삭제하는 방법입니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 64bc85cde09c21c9fe1f6677f0211b6338311941
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690381"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Azure CLI를 사용하여 연결되지 않은 Azure 관리 및 비관리 디스크를 찾아서 삭제

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure에서 VM(가상 머신)을 삭제할 때 기본적으로 VM에 연결된 디스크는 삭제되지 않습니다. 이 기능은 의도하지 않은 VM 삭제로 인한 데이터 손실을 방지하는 데 도움이 됩니다. VM을 삭제한 후에도 연결되지 않은 디스크에 대한 요금을 계속 지불합니다. 이 문서에서는 연결되지 않은 디스크를 찾아서 삭제하여 불필요한 비용을 줄이는 방법을 보여줍니다. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>관리 디스크: 연결되지 않은 디스크 찾기 및 삭제 

다음 스크립트는 **ManagedBy** 속성 값을 검사하여 연결되지 않은 [관리 디스크](../managed-disks-overview.md)를 찾습니다. 관리 디스크가 VM에 연결되어 있으면 **ManagedBy** 속성에 해당 VM의 리소스 ID가 포함됩니다. 관리 디스크가 연결되어 있지 않으면 **ManagedBy** 속성이 null입니다. 스크립트는 Azure 구독의 모든 관리 디스크를 검사합니다. 스크립트에서 **ManagedBy** 속성이 null로 설정된 관리 디스크를 찾을 경우 디스크가 연결되어 있지 않다고 결정합니다.

>[!IMPORTANT]
>먼저 **deleteUnattachedDisks** 변수를 0으로 설정하여 스크립트를 실행합니다. 이 작업을 통해 연결되지 않은 모든 관리 디스크를 찾아서 볼 수 있습니다.
>
>연결되지 않은 모든 디스크를 검토한 후 스크립트를 다시 실행하고 **deleteUnattachedDisks** 변수를 1로 설정합니다. 이 작업을 통해 연결되지 않은 모든 관리 디스크를 삭제할 수 있습니다.
>

```azurecli
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0
unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>비관리 디스크: 연결되지 않은 디스크 찾기 및 삭제 

비관리 디스크는 [Azure Storage 계정](../../storage/common/storage-account-overview.md)에 [페이지 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs)으로 저장된 VHD 파일입니다. 다음 스크립트는 **LeaseStatus** 속성 값을 검사하여 연결되지 않은 비관리 디스크(페이지 Blob)를 찾습니다. 비관리 디스크가 VM에 연결되어 있으면 **LeaseStatus** 속성이 **Locked** 로 설정됩니다. 비관리 디스크가 연결되어 있지 않으면 **LeaseStatus** 속성이 **Unlocked** 로 설정됩니다. 스크립트는 Azure 구독의 모든 Azure Storage 계정에 있는 모든 비관리 디스크를 검사합니다. 스크립트에서 **LeaseStatus** 속성이 **Unlocked** 로 설정된 비관리 디스크를 찾을 경우 디스크가 연결되어 있지 않다고 결정합니다.

>[!IMPORTANT]
>먼저 **deleteUnattachedVHDs** 변수를 0으로 설정하여 스크립트를 실행합니다. 이 작업을 통해 연결되지 않은 모든 비관리 VHD를 찾아서 볼 수 있습니다.
>
>연결되지 않은 모든 디스크를 검토한 후 스크립트를 다시 실행하고 **deleteUnattachedVHDs** 변수를 1로 설정합니다. 이 작업을 통해 연결되지 않은 모든 비관리 VHD를 삭제할 수 있습니다.
>

```azurecli
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0
storageAccountIds=$(az storage account list --query [].[id] -o tsv)
for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [스토리지 계정 삭제](../../storage/common/storage-account-create.md#delete-a-storage-account)를 참조하세요.