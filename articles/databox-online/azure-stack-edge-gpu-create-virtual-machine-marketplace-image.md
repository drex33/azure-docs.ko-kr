---
title: Azure Marketplace 이미지를 사용하여 Azure Stack Edge Pro GPU 디바이스용 VM 이미지 만들기
description: Azure Marketplace 이미지를 사용하여 Azure Stack Edge Pro GPU 디바이스에서 사용할 VM 이미지를 만드는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/14/2021
ms.author: alkohli
ms.openlocfilehash: 13d020ed0ea6310f49cbe37555fc2a9048b9f042
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112465406"
---
# <a name="use-azure-marketplace-image-to-create-vm-image-for-your-azure-stack-edge-pro-gpu"></a>Azure Marketplace 이미지를 사용하여 Azure Stack Edge Pro GPU용 VM 이미지 만들기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스에 VM을 배포하려면 VM을 만드는 데 사용할 수 있는 VM 이미지를 만들어야 합니다. 이 문서에서는 Azure Marketplace 이미지에서 시작하는 VM 이미지를 만드는 데 필요한 단계를 설명합니다. 그런 다음, 이 VM 이미지를 사용하여 Azure Stack Edge Pro GPU 디바이스에 VM을 배포할 수 있습니다.

## <a name="vm-image-workflow"></a>VM 이미지 워크플로

다음 단계에서는 Azure Marketplace 워크플로를 사용하는 VM 이미지 워크플로에 대해 설명합니다.

1.  Azure CLI가 설치된 Azure Cloud Shell 또는 클라이언트에 연결합니다.
2.  Azure Marketplace를 검색하고 원하는 이미지를 식별합니다.
3.  Marketplace 이미지에서 새 관리 디스크를 만듭니다.
4.  관리 디스크에서 Azure Storage 계정으로 VHD를 내보냅니다.
5.  관리 디스크를 정리합니다.


자세한 내용은 [Azure PowerShell을 사용하여 Azure Stack Edge Pro 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge에 대한 Azure Marketplace 이미지를 사용하려면 먼저 다음 방법 중 하나를 사용하여 Azure에 연결해야 합니다.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]


## <a name="search-for-azure-marketplace-images"></a>Azure Marketplace 이미지 검색

이제 사용하려는 특정 Azure Marketplace 이미지를 식별합니다. Azure Marketplace는 수천 개의 VM 이미지를 호스팅합니다. 

검색 조건과 일치하는 가장 일반적으로 사용되는 Marketplace 이미지 중 일부를 찾으려면 다음 명령을 실행합니다.  

```azurecli
az vm image list --all [--publisher <Publisher>] [--offer <Offer>] [--sku <SKU>]
```
마지막 세 플래그는 선택 사항이지만 제외하면 긴 목록이 반환됩니다.

다음은 몇 가지 쿼리 예입니다.

```azurecli
#Returns all images of type “Windows Server”
az vm image list --all --publisher "MicrosoftWindowsserver" --offer "WindowsServer" 

#Returns all Windows Server 2019 Datacenter images from West US published by Microsoft
az vm image list --all --location "westus" --publisher "MicrosoftWindowsserver" --offer "WindowsServer" --sku "2019-Datacenter"

#Returns all VM images from a publisher  
az vm image list --all --publisher "Canonical" 
```

다음은 특정 게시자, 제품 및 SKU의 VM 이미지를 쿼리한 경우의 출력 예입니다.

```output
PS /home/user> az vm image list --all --publisher "Canonical" --offer "UbuntuServer" --sku "12.04.4-LTS"
[
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201402270",
    "version": "12.04.201402270"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404080",
    "version": "12.04.201404080"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404280",
    "version": "12.04.201404280"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201405140",
    "version": "12.04.201405140"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406060",
    "version": "12.04.201406060"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406190",
    "version": "12.04.201406190"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407020",
    "version": "12.04.201407020"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407170",
    "version": "12.04.201407170"
  }
]
PS /home/user>
```

>[!IMPORTANT]
> Gen 1 이미지만 사용합니다. Gen 2로 지정된 모든 이미지(일반적으로 sku에는 "-g2" 접미사가 있음)는 Azure Stack Edge에서 작동하지 않습니다.

이 예에서는 Windows Server 2019 Datacenter Core 버전 2019.0.20190410을 선택합니다. 이 이미지는 URN(Universal Resource Number)으로 식별됩니다. 
 
:::image type="content" source="media/azure-stack-edge-create-virtual-machine-marketplace-image/marketplace-image-1.png" alt-text="Marketplace 이미지 목록":::

### <a name="commonly-used-marketplace-images"></a>일반적으로 사용되는 Marketplace 이미지

다음은 가장 일반적으로 사용되는 이미지 중 일부에 대한 URL 목록입니다. 특정 OS의 최신 버전만 원하는 경우 URN에서 버전 번호를 최신 버전으로 바꿀 수 있습니다. 예: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”. 


| OS              | SKU                                     | 버전               | URN                                                                                       |
|-----------------|-----------------------------------------|-----------------------|-------------------------------------------------------------------------------------------|
| Windows Server  | 2019 Datacenter                         | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter:17763.1879.2104091832                |
| Windows Server  | 2019 Datacenter(30GB 소형 디스크)      | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-smalldisk:17763.1879.2104091832      |
| Windows Server  | 2019 Datacenter Core                    | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core:17763.1879.2104091832           |
| Windows Server  | 2019 Datacenter Core(30GB 소형 디스크) | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-smalldisk:17763.1879.2104091832 |
| Windows 데스크톱 | Windows 10 20H2 Pro                     | 19042.928.2104091209  | MicrosoftWindowsDesktop:Windows-10:20h2-pro:19042.928.2104091209                          |
| Ubuntu Server   | Canonical Ubuntu Server 18.04 LTS       | 18.04.202002180       | Canonical:UbuntuServer:18.04-LTS:18.04.202002180                                          |
| Ubuntu Server   | Canonical Ubuntu Server 16.04 LTS       | 16.04.202104160       | Canonical:UbuntuServer:16.04-LTS:16.04.202104160                                          |
| CentOS          | CentOS 8.1                              | 8.1.2020062400        | OpenLogic:CentOS:8_1:8.1.2020062400                                                       |
| CentOS          | CentOS 7.7                              | 7.7.2020062400        | OpenLogic:CentOS:7.7:7.7.2020062400                                                       |



## <a name="create-a-new-managed-disk-from-the-marketplace-image"></a>Marketplace 이미지에서 새 관리 디스크 만들기

선택한 Marketplace 이미지에서 Azure Managed Disk를 만듭니다. 

1. 일부 매개 변수를 설정합니다.

    ```azurecli
    $urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
    $diskName = <disk name> #Name for new disk to be created
    $diskRG = <resource group> #Resource group that contains the new disk
    ```


1. 디스크를 만들고 SAS 액세스 URL을 생성합니다.

    ```azurecli
    az disk create -g $diskRG -n $diskName --image-reference $urn
    $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
    $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas 
    ```

출력의 예제는 다음과 같습니다.

```output
PS /home/user> $urn = “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
PS /home/user> $diskName = "newmanageddisk1"
PS /home/user> $diskRG = "newrgmd1"
PS /home/user> az disk create -g $diskRG -n $diskName --image-reference $urn
{
  "burstingEnabled": null,
  "creationData": {
    "createOption": "FromImage",
    "galleryImageReference": null,
    "imageReference": {
      "id": "/Subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/Providers/Microsoft.Compute/Locations/eastus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2019-Datacenter/Versions/17763.1935.2105080716",
      "lun": null
    },
    "logicalSectorSize": null,
    "sourceResourceId": null,
    "sourceUniqueId": null,
    "sourceUri": null,
    "storageAccountId": null,
    "uploadSizeBytes": null
  },
  "diskAccessId": null,
  "diskIopsReadOnly": null,
  "diskIopsReadWrite": 500,
  "diskMBpsReadOnly": null,
  "diskMBpsReadWrite": 100,
  "diskSizeBytes": 136367308800,
  "diskSizeGb": 127,
  "diskState": "Unattached",
  "encryption": {
    "diskEncryptionSetId": null,
    "type": "EncryptionAtRestWithPlatformKey"
  },
  "encryptionSettingsCollection": null,
  "extendedLocation": null,
  "hyperVGeneration": "V1",
  "id": "/subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/resourceGroups/newrgmd1/providers/Microsoft.Compute/disks/NewManagedDisk1",
  "location": "eastus",
  "managedBy": null,
  "managedByExtended": null,
  "maxShares": null,
  "name": "NewManagedDisk1",
  "networkAccessPolicy": "AllowAll",
  "osType": "Windows",
  "propertyUpdatesInProgress": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "resourceGroup": "newrgmd1",
  "securityProfile": null,
  "shareInfo": null,
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "supportsHibernation": null,
  "tags": {},
  "tier": "P10",
  "timeCreated": "2021-06-08T00:39:34.205982+00:00",
  "type": "Microsoft.Compute/disks",
  "uniqueId": "1a649ad4-3b95-471e-89ef-1d2ed1f51525",
  "zones": null
}

PS /home/user> $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
PS /home/user>  $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
PS /home/user>
```

## <a name="export-a-vhd-from-the-managed-disk-to-azure-storage"></a>관리 디스크에서 Azure Storage로 VHD 내보내기 

이 단계에서는 관리 디스크에서 원하는 Azure Blob Storage 계정으로 VHD를 내보냅니다. 그런 다음 이 VHD를 사용하여 Azure Stack Edge에서 VM 이미지를 만들 수 있습니다.

1. VHD를 복사할 대상 스토리지 계정을 설정합니다.
    
    ```azurecli
    $storageAccountName = <destination storage account name>
    $containerName = <destination container name>
    $destBlobName = <blobname.vhd> #Blob that will be created, including .vhd extension
    $storageAccountKey = <storage account key>
    ```

1. VHD를 대상 스토리지 계정에 복사합니다.

    ```azurecli
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName
    ```

    VHD 복사를 완료하는 데는 몇 분 정도 걸립니다. 다음 명령을 실행하여 계속하기 전에 복사가 완료되었는지 확인합니다. 완료되면 상태 필드에 “성공”이 표시됩니다.
    
    ```azurecli
    Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName 
    ```

출력의 예제는 다음과 같습니다.

```output
PS /home/user> $storageAccountName = "edgeazurevmeus"
PS /home/user> $containerName = "azurevmmp"
PS /home/user> $destBlobName = "newblobmp.vhd"
PS /home/user> $storageAccountKey = "n9sCytWLdTBz0F4Sco9SkPGWp6BJBtf7BJBk79msf1PfxJGQdqSfu6TboZWZ10xyZdc4y+Att08cC9B79jB0YA=="

PS /home/user> $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
PS /home/user> Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName

   AccountName: edgeazurevmeus, ContainerName: azurevmmp

Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotTime                 IsDeleted  VersionId
----                 --------  ------          -----------                    ------------         ---------- ------------                 ---------  ---------
newblobmp.vhd        PageBlob  -1                                             2021-06-08 00:50:10Z                                         False

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          :
Status                  : Pending
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 696254464
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          : 6/8/2021 12:57:26 AM +00:00
Status                  : Success
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 136367309312
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :
```

## <a name="clean-up-the-managed-disk"></a>관리 디스크 정리

작성한 관리 디스크를 삭제하려면 다음 단계를 따르세요.

```azurecli
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes 
```
삭제 작업을 완료하는 데는 몇 분이 소요됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro GPU 디바이스에 VM을 배포합니다](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).