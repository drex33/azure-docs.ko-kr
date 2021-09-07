---
title: Azure PowerShell을 통해 Azure Stack Edge 디바이스에 VM 배포
description: Azure PowerShell을 사용하여 Azure Stack Edge 디바이스에서 가상 머신을 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32d2102bad49a3023df9b1d25f5b5378a3b30ad0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721500"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Azure PowerShell을 통해 Azure Stack Edge 디바이스에 VM 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure PowerShell을 사용하여 Azure Stack Edge 디바이스에서 VM(가상 머신)을 만들고 관리하는 방법을 설명합니다.

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

VM 배포의 개략적인 배포 워크플로는 다음과 같습니다.

1. 디바이스의 로컬 Azure Resource Manager에 연결합니다.
1. 디바이스의 기본 제공 구독 쿼리를 파악합니다.
1. VM 이미지를 가져옵니다.
1. 기본 제공 구독에 리소스 그룹을 만듭니다. 리소스 그룹에는 VM 및 관련된 모든 리소스가 포함됩니다.  
1. VM 이미지를 만드는 데 사용될 VHD를 저장할 로컬 스토리지 계정을 디바이스에 만듭니다.
1. Windows/Linux 소스 이미지를 스토리지 계정에 업로드하여 관리 디스크를 만듭니다.
1. 관리 디스크를 사용하여 VM 이미지를 만듭니다.
1. 디바이스 포트에서 컴퓨팅을 사용하도록 설정하여 가상 스위치를 만듭니다.
1. 그러면 컴퓨팅을 사용하도록 설정한 포트에 연결된 가상 스위치를 사용하여 가상 네트워크를 만듭니다.
1. 이전에 만든 VM 이미지, 가상 네트워크 및 가상 네트워크 인터페이스를 사용하여 VM을 만들어서, 가상 네트워크 내에서 통신하고 VM에 원격으로 액세스하는 공용 IP 주소를 할당합니다. 필요에 따라 VM에 더 많은 저장소를 제공하기 위해 데이터 디스크를 포함합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>디바이스의 기본 제공 구독 쿼리

Azure Resource Manager의 경우 사용자에게 표시되는 단일 고정 구독만 지원됩니다. 이 구독은 디바이스별로 고유하며 구독 이름과 구독 ID는 변경할 수 없습니다.

구독에는 VM 생성에 필요한 모든 리소스가 포함됩니다. 

> [!IMPORTANT]
> 구독은 Azure Portal에서 VM을 사용하도록 설정할 때 만들어지며 디바이스에서 로컬로 유지됩니다.

구독은 VM을 배포하는 데 사용됩니다.

### <a name="az"></a>[Az](#tab/az)

1.  구독을 나열하려면 다음 명령을 실행합니다.

    ```powershell
    Get-AzSubscription
    ```
    
    다음은 몇 가지 출력 예입니다.

    ```output
    PS C:\WINDOWS\system32> Get-AzSubscription
    
    Name                          Id                                   TenantId
    ----                          --                                   --------
    Default Provider Subscription ...                                  ...
    
    
    PS C:\WINDOWS\system32>
    ```
        
1. 디바이스에서 실행 중인 등록된 리소스 공급자 목록을 가져옵니다. 이 목록에는 일반적으로 컴퓨팅, 네트워크 및 스토리지가 포함됩니다.

    ```powershell
    Get-AzResourceProvider
    ```

    > [!NOTE]
    > 리소스 공급자는 사전 등록되어 있으며 수정하거나 변경할 수 없습니다.
    
    다음은 몇 가지 출력 예입니다.

    ```output
    PS C:\WINDOWS\system32>  Get-AzResourceProvider
        
    ProviderNamespace : Microsoft.AzureBridge
    RegistrationState : Registered
    ResourceTypes     : {locations, operations, locations/ingestionJobs}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

1.  구독을 나열하려면 다음 명령을 실행합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
    
    다음은 몇 가지 출력 예입니다.

    ```output
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription         ...                 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. 디바이스에서 실행 중인 등록된 리소스 공급자 목록을 가져옵니다. 이 목록에는 일반적으로 컴퓨팅, 네트워크 및 스토리지가 포함됩니다.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > 리소스 공급자는 사전 등록되어 있으며 수정하거나 변경할 수 없습니다.
    
    다음은 몇 가지 출력 예입니다.

    ```output
    PS C:\Windows\system32> Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
---
   
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

먼저 새 Azure 리소스 그룹을 만들고, 스토리지 계정, 디스크, 네트워크 인터페이스 및 관리 디스크와 같은 모든 VM 관련 리소스에 대한 논리적 컨테이너로 사용합니다.

> [!IMPORTANT]
> 모든 리소스는 디바이스와 동일한 위치에 만들어지며 위치는 **DBELocal** 로 설정됩니다.

### <a name="az"></a>[Az](#tab/az)

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $ResourceGroupName = "<Resource group name>" 
    ```
1. VM을 위해 만들 리소스에 대한 리소스 그룹을 만듭니다.
   
    ```powershell
    New-AzResourceGroup -Name $ResourceGroupName -Location DBELocal
    ```

    몇 가지 출력 예제는 다음과 같습니다.
    
    ```output
    PS C:\WINDOWS\system32> New-AzResourceGroup -Name myaseazrg -Location DBELocal
    
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/.../resourceGroups/myaseazrg
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

다음은 몇 가지 출력 예입니다.

```output
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```
---

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

이전 단계에서 만든 리소스 그룹을 사용하여 새 스토리지 계정을 만듭니다. VM에 대한 가상 디스크 이미지를 업로드하는 데 사용하는 로컬 스토리지 계정입니다.

### <a name="az"></a>[Az](#tab/az)

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $StorageAccountName = "<Storage account name>"    
    ```

1. 디바이스에서 새 로컬 스토리지 계정을 만듭니다.

    ```powershell
    New-AzStorageAccount -Name $StorageAccountName -ResourceGroupName $ResourceGroupName -Location DBELocal -SkuName Standard_LRS
    ```
    
    > [!NOTE]
    > Azure Resource Manager를 사용하면 로컬 중복 스토리지(표준 또는 프리미엄)와 같은 로컬 스토리지 계정만 만들 수 있습니다. 계층형 스토리지 계정을 만들려면 [자습서: GPU가 포함된 Azure Stack Edge Pro를 사용하여 스토리지 계정을 통해 데이터 전송](azure-stack-edge-gpu-deploy-add-storage-accounts.md)을 참조하세요.

    예제 출력은 다음과 같습니다.
    
    ```output
    PS C:\WINDOWS\system32> New-AzStorageAccount -Name myaseazsa -ResourceGroupName myaseazrg -Location DBELocal -SkuName Standard_LRS
    
    StorageAccountName ResourceGroupName PrimaryLocation SkuName      Kind    AccessTier CreationTime
    ------------------ ----------------- --------------- -------      ----    ---------- ------------
    myaseazsa          myaseazrg         DBELocal        Standard_LRS Storage            6/10/2021 11:45...
    
    PS C:\WINDOWS\system32>
    ```

1. 이전 단계에서 만든 계정에 대한 스토리지 계정 키를 가져옵니다. 메시지가 표시되면 리소스 그룹 이름 및 스토리지 계정 이름을 제공합니다.

    ```powershell
    Get-AzStorageAccountKey
    ``` 

    예제 출력은 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> Get-AzStorageAccountKey
    
    cmdlet Get-AzStorageAccountKey at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    ResourceGroupName: myaseazrg
    Name: myaseazsa
    
    KeyName Value                                                                                    Permissions
    ------- -----                                         ------
    key1    gv3OF57tuPDyzBNc1M7fhil2UAiiwnhTT6zgiwE3TlF/CD217Cvw2YCPcrKF47joNKRvzp44leUe5HtVkGx8RQ==   Full
    key2    kmEynIs3xnpmSxWbU41h5a7DZD7v4gGV3yXa2NbPbmhrPt10+QmE5PkOxxypeSqbqzd9si+ArNvbsqIRuLH2Lw==   Full
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager를 사용하면 로컬 중복 스토리지(표준 또는 프리미엄)와 같은 로컬 스토리지 계정만 만들 수 있습니다. 계층형 스토리지 계정을 만들려면 [자습서: GPU가 포함된 Azure Stack Edge Pro를 사용하여 스토리지 계정을 통해 데이터 전송](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)을 참조하세요.

다음은 몇 가지 출력 예입니다.

```output
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/.../resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

스토리지 계정 키를 얻으려면 `Get-AzureRmStorageAccountKey` 명령을 실행합니다. 다음은 몇 가지 출력 예입니다.

```output
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```
---

## <a name="add-the-blob-uri-to-the-host-file"></a>호스트 파일에 Blob URI 추가

**Azure Stack Edge 디바이스에서 Azure Resource Manager에 연결** 의 [엔드포인트 이름 확인을 위해 호스트 파일 수정](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)에서 Azure Blob Storage에 연결하는 데 사용하는 클라이언트의 호스트 파일에 Blob URI를 이미 추가했습니다. 이 항목은 Blob URI를 추가하는 데 사용되었습니다.

`<Device IP address>` `<storage name>.blob.<appliance name>.<dnsdomain>`

## <a name="install-certificates"></a>인증서 설치

HTTPS를 사용하는 경우 디바이스에 적절한 인증서를 설치해야 합니다. 여기에서 Blob 엔드포인트 인증서를 설치합니다. 자세한 내용은 [GPU 디바이스가 있는 Azure Stack Edge Pro에서 인증서 사용](azure-stack-edge-gpu-manage-certificates.md)을 참조하세요.

## <a name="upload-a-vhd"></a>VHD 업로드

이전에 만든 로컬 스토리지 계정의 페이지 Blob에 사용할 디스크 이미지를 복사합니다. [AzCopy](../storage/common/storage-use-azcopy-v10.md)와 같은 도구를 사용하여 스토리지 계정에 VHD(가상 하드 디스크)를 업로드할 수 있습니다. 


### <a name="az"></a>[Az](#tab/az)

AzCopy 10에서 다음 명령을 사용합니다.  

1. 해당하는 AzCopy용 API 버전을 포함하는 일부 매개 변수를 설정합니다. 이 예제에서는 AzCopy 10이 사용되었습니다.

    ```powershell
    $Env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2019-07-07"    
    $ContainerName = <Container name>
    $ResourceGroupName = <Resource group name>
    $StorageAccountName = <Storage account name>
    $VHDPath = "Full VHD Path"
    $VHDFile = <VHD file name>
    ```
1. 원본(이 경우 로컬 시스템)의 VHD를 이전 단계에서 디바이스에서 만든 스토리지 계정으로 복사합니다.

    ```powershell
    $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value    
    $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob    
    $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint    
    $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly        
    <Path to azcopy.exe> cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"    
    ```

    예제 출력은 다음과 같습니다. 
    
    ```output
    PS C:\windows\system32> $ContainerName = "testcontainer1"
    PS C:\windows\system32> $ResourceGroupName = "myaseazrg"
    PS C:\windows\system32> $StorageAccountName = "myaseazsa"
    PS C:\windows\system32> $VHDPath = "C:\Users\alkohli\Downloads\Ubuntu1604"           
    PS C:\windows\system32> $VHDFile = "ubuntu13.vhd"
    
    PS C:\windows\system32> $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value
    PS C:\windows\system32> $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob
    PS C:\windows\system32> $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint
    PS C:\windows\system32> $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

    PS C:\windows\system32> C:\azcopy\azcopy_windows_amd64_10.10.0\azcopy.exe cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
    INFO: Scanning...
    INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support
    
    Job 72a5e3dd-9210-3e43-6691-6bebd4875760 has started
    Log file is located at: C:\Users\alkohli\.azcopy\72a5e3dd-9210-3e43-6691-6bebd4875760.log
    
    INFO: azcopy.exe: A newer version 10.11.0 is available to download
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

AzCopy 10에서 다음 명령을 사용합니다.  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

다음은 몇 가지 출력 예입니다. 

```output
$ContainerName = <Container name>
$ResourceGroupName = <Resource group name>
$StorageAccountName = <Storage account name>
$VHDPath = "Full VHD path"
$VHDFile = <VHD file name>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```
---

## <a name="create-a-managed-disk-from-the-vhd"></a>VHD에서 관리 디스크를 만들려면

이제 업로드된 VHD에서 관리 디스크를 만듭니다.

### <a name="az"></a>[Az](#tab/az)

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $DiskName = "<Managed disk name>"
    ```

1. 업로드된 VHD에서 관리 디스크를 만듭니다. VHD에 대한 원본 URL을 가져오려면 Storage Explorer에서 VHD가 포함된 스토리지 계정의 컨테이너로 이동합니다. VHD를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **속성** 을 선택합니다. **Blob 속성** 대화 상자에서 **URI** 를 선택합니다. 

    ```powershell
    $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id    
    $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "Source URL for your VHD"
    New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    ```
    예제 출력은 다음과 같습니다.
    
    ```output
    PS C:\WINDOWS\system32> $DiskName = "myazmd"
    PS C:\WINDOWS\system32> $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id
    PS C:\WINDOWS\system32> $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "https://myaseazsa.blob.myasegpu.wdshcsso.com/testcontainer1/ubuntu13.vhd"
    PS C:\WINDOWS\system32> New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    
    ResourceGroupName            : myaseazrg
    ManagedBy                    :
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones                        :
    TimeCreated                  : 6/24/2021 12:19:56 PM
    OsType                       :
    HyperVGeneration             :
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationDat
                                   a
    DiskSizeGB                   : 30
    DiskSizeBytes                : 32212254720
    UniqueId                     : 53743801-cbf2-4d2f-acb4-971d037a9395
    EncryptionSettingsCollection :
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/.../r
                                   esourceGroups/myaseazrg/providers/Microsoft.Compute/d
                                   isks/myazmd
    Name                         : myazmd
    Type                         : Microsoft.Compute/disks
    Location                     : DBELocal
    Tags                         : {}
    
    PS C:\WINDOWS\system32>
    ```
 

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
다음은 몇 가지 출력 예입니다. 

```output
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd
```
 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

다음은 몇 가지 출력 예입니다. 이 cmdlet에 대한 자세한 내용은 [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true)를 참조하세요.

```output
Tags               : New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/.../resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```
---

## <a name="create-a-vm-image-from-the-managed-disk"></a>관리 디스크에서 VM 이미지 만들기

이제 관리 디스크에서 VM 이미지를 만듭니다.

### <a name="az"></a>[Az](#tab/az)

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $DiskSize = "<Size greater than or equal to size of source managed disk>"
    $OsType = "<linux or windows>" 
    $ImageName = "<Image name>"
    ```
1. VM 이미지를 만듭니다. 지원되는 OS 유형은 Linux 및 Windows입니다.

    ```powershell
    $imageConfig = New-AzImageConfig -Location DBELocal
    $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId 
    New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName  
    ```  
    예제 출력은 다음과 같습니다. 

    ```output
    PS C:\WINDOWS\system32> $OsType = "linux"
    PS C:\WINDOWS\system32> $ImageName = "myaseazlinuxvmimage"
    PS C:\WINDOWS\system32> $DiskSize = 35
    PS C:\WINDOWS\system32> $imageConfig = New-AzImageConfig -Location DBELocal
    PS C:\WINDOWS\system32> $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    HyperVGeneration     : V1
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
    PS C:\WINDOWS\system32> New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName
    
    ResourceGroupName    : myaseazrg
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    HyperVGeneration     : V1
    Id                   : /subscriptions/.../resourceG
                           roups/myaseazrg/providers/Microsoft.Compute/images/myaseazlin
                           uxvmimage
    Name                 : myaseazlinuxvmimage
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
    PS C:\WINDOWS\system32> 
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

다음 명령을 실행합니다. *\<Disk name>* , *\<OS type>* 및 *\<Disk size>* 을 실제 값으로 바꿉니다.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

지원되는 OS 유형은 Linux 및 Windows입니다.

다음은 몇 가지 출력 예입니다. 이 cmdlet에 대한 자세한 내용은 [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true)를 참조하세요.

```output
PS C:\Windows\system32> New-AzImage -Image $imageConfig -ImageName ig191113014333    -ResourceGroupName RG191113014333
ResourceGroupName    : RG191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
HyperVGeneration     : V1
Id                   : /subscriptions/.../resourceGroups/RG191113014333/providers/Microsoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```
---

## <a name="create-your-vm-with-previously-created-resources"></a>이전에 만든 리소스로 VM 만들기

VM을 만들고 배포하기 전에 하나의 가상 네트워크를 만들고 가상 네트워크 인터페이스를 연결해야 합니다.

> [!IMPORTANT]
> 다음 규칙이 적용됩니다.
> - 리소스 그룹 간에도 하나의 가상 네트워크만 만들 수 있습니다. 가상 네트워크에는 논리 네트워크와 정확히 동일한 주소 공간이 있어야 합니다.
> - 가상 네트워크에는 서브넷이 하나만 있을 수 있습니다. 서브넷에는 가상 네트워크와 정확히 동일한 주소 공간이 있어야 합니다.
> - 가상 네트워크 인터페이스 카드를 만들 때 정적 할당 방법만 사용할 수 있습니다. 사용자는 개인 IP 주소를 제공해야 합니다.<!--Confirm w/ Neeraj given we can have both static and DHCP-->

### <a name="query-the-automatically-created-virtual-network"></a>자동으로 만들어진 가상 네트워크 쿼리

디바이스의 로컬 UI에서 컴퓨팅을 사용하도록 설정하면 `ASERG` 리소스 그룹 아래에 `ASEVNET`이라는 가상 네트워크가 자동으로 만들어집니다. 

### <a name="az"></a>[Az](#tab/az)

다음 명령을 사용하여 기존 가상 네트워크를 쿼리합니다.

```powershell
$ArmVn = Get-AzVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

다음 명령을 사용하여 기존 가상 네트워크를 쿼리합니다.

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```
---

### <a name="create-a-virtual-network-interface-card"></a>가상 네트워크 인터페이스 카드 만들기

가상 네트워크 서브넷 ID를 사용하여 가상 네트워크 인터페이스 카드를 만듭니다.

### <a name="az"></a>[Az](#tab/az)

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $IpConfigName = "<IP config name>"
    $NicName = "<Network interface name>"
    ```

1. 가상 네트워크 인터페이스를 만듭니다.

    ```powershell
    $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig    
    ```

    기본적으로 IP는 컴퓨팅이 지원되는 네트워크에서 네트워크 인터페이스에 동적으로 할당됩니다. 네트워크 인터페이스에 고정 IP를 할당하는 경우 `-PrivateIpAddress parameter`를 사용합니다.         

    예제 출력은 다음과 같습니다.
    
    ```output
    PS C:\WINDOWS\system32> $IpConfigName = "myazipconfig1"
    PS C:\WINDOWS\system32> $NicName = "myaznic1"
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig
    PS C:\WINDOWS\system32> $Nic
        
    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettings                 : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
           
    PS C:\WINDOWS\system32>
    ```

선택적으로 VM에 대한 가상 네트워크 인터페이스 카드를 만드는 동안 공용 IP를 전달할 수 있습니다. 이 경우 공용 IP는 개인 IP를 반환합니다. 

```powershell
New-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

기본적으로 IP는 컴퓨팅이 지원되는 네트워크에서 네트워크 인터페이스에 동적으로 할당됩니다. 네트워크 인터페이스에 고정 IP를 할당하는 경우 `-PrivateIpAddress parameter`를 사용합니다. 

다음은 몇 가지 출력 예입니다.

```output
PS C:\windows\system32> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\windows\system32> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\windows\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\windows\system32> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\windows\system32> $Nic

PS C:\windows\system32> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/.../resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

선택적으로 VM에 대한 가상 네트워크 인터페이스 카드를 만드는 동안 공용 IP를 전달할 수 있습니다. 이 경우 공용 IP는 개인 IP를 반환합니다. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```
---

### <a name="create-a-vm"></a>VM 만들기

이제 VM 이미지를 사용하여 VM을 만들고 이전에 만든 가상 네트워크에 연결할 수 있습니다.

### <a name="az"></a>[Az](#tab/az)

1. 만들려는 VM에 로그인하려면 사용자 이름 및 암호를 설정합니다.

    ```powershell
    $pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
    $cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
    ```
    VM을 만들고 전원을 켠 후에는 다음 사용자 이름과 암호를 사용하여 로그인합니다.

1. 매개 변수를 설정합니다.

    ```powershell
    $VmName = "<VM name>"
    $ComputerName = "<VM display name>"
    $OsDiskName = "<OS disk name>"
    ```
1. VM을 만듭니다.

    ```powershell
    $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
 
    $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
     
    $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
     
    $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
     
    $VirtualMachine =  Add-AzVMNetworkInterface -Vm $VirtualMachine -Id $nicID
     
    $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
     
    $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
     
    New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    ```

    예제 출력은 다음과 같습니다.
    
    ```powershell
    PS C:\WINDOWS\system32> $pass = ConvertTo-SecureString "Password1" -AsPlainText -Force;
    PS C:\WINDOWS\system32> $cred = New-Object System.Management.Automation.PSCredential("myazuser", $pass)
    PS C:\WINDOWS\system32> $VmName = "myazvm"
    >> $ComputerName = "myazvmfriendlyname"
    >> $OsDiskName = "myazosdisk1"
    PS C:\WINDOWS\system32> $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
    PS C:\WINDOWS\system32> $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> $nicID/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.Network/networkInterfaces/myaznic1
    PS C:\WINDOWS\system32> $VirtualMachine =  Add-AzVMNetworkInterface -VM $VirtualMachine -Id $nicID
    PS C:\WINDOWS\system32> $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
    PS C:\WINDOWS\system32> New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing
    standard storage account, myaseazsa, is used for boot diagnostics.
    VERBOSE: Performing the operation "New" on target "myazvm".
    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```
1. 만든 VM에 할당된 IP를 파악하려면 만든 가상 네트워크 인터페이스를 쿼리합니다. `PrivateIPAddress`를 찾아 VM의 IP를 복사합니다. 예제 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> $Nic

    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettings                 : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
      
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

VM을 만들고 전원을 켠 후에는 다음 사용자 이름과 암호를 사용하여 로그인합니다.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```
---

## <a name="connect-to-the-vm"></a>VM에 연결

만들어진 Windows VM 또는 Linux VM에 따라 연결 지침이 다를 수 있습니다.

## <a name="connect-to-a-linux-vm"></a>Linux VM에 연결

Linux VM에 연결하려면 다음을 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

VM을 만들 때 공용 IP 주소를 사용한 경우 해당 IP를 사용하여 VM에 연결할 수 있습니다. 공용 IP를 가져오려면 다음 명령을 실행합니다. 

### <a name="az"></a>[Az](#tab/az)

```powershell
$publicIp = Get-AzPublicIpAddress -Name $PublicIp -ResourceGroupName $ResourceGroupName
```
이 인스턴스에서 공용 IP는 가상 네트워크 인터페이스를 만드는 동안 전달한 개인 IP와 동일합니다.
  
### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
이 인스턴스에서 공용 IP는 가상 네트워크 인터페이스를 만드는 동안 전달한 개인 IP와 동일합니다.

---

## <a name="connect-to-a-windows-vm"></a>Windows VM에 연결

Windows VM에 연결하려면 다음을 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


## <a name="manage-the-vm"></a>VM 관리

다음 섹션에서는 Azure Stack Edge Pro 디바이스에서 만들 수 있는 몇 가지 일반적인 작업에 대해 설명합니다.

### <a name="list-vms-that-are-running-on-the-device"></a>디바이스에서 실행 중인 VM 나열

Azure Stack Edge 디바이스에서 실행 중인 모든 VM 목록을 반환하려면 다음 명령을 실행합니다.

### <a name="az"></a>[Az](#tab/az)

```powershell
Get-AzVM -ResourceGroupName <String> -Name <String>
```

이 cmdlet에 대한 자세한 내용은 [Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-6.1.0&preserve-view=true)을 참조하세요.

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Get-AzureRmVM -ResourceGroupName <String> -Name <String>
```

---


### <a name="turn-on-the-vm"></a>VM 켜기

디바이스에서 실행 중인 가상 머신을 켜려면 다음 cmdlet을 실행합니다.

### <a name="az"></a>[Az](#tab/az)

```powershell
Start-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
이 cmdlet에 대한 자세한 내용은 [Start-AzVM](/powershell/module/az.compute/start-azvm?view=azps-5.9.0&preserve-view=true)을 참조하세요.

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

이 cmdlet에 대한 자세한 내용은 [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조하세요.

---

### <a name="suspend-or-shut-down-the-vm"></a>VM 일시 중지 또는 종료

디바이스에서 실행 중인 가상 머신을 중지하거나 종료하려면 다음 cmdlet을 실행합니다.

### <a name="az"></a>[Az](#tab/az)

```powershell
Stop-AzVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

이 cmdlet에 대한 자세한 내용은 [Stop-AzVM cmdlet](/powershell/module/az.compute/stop-azvm?view=azps-5.9.0&preserve-view=true)을 참조하세요.

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

이 cmdlet에 대한 자세한 내용은 [Stop-AzureRmVM cmdlet](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조하세요.

---

### <a name="add-a-data-disk"></a>데이터 디스크 추가

VM의 워크로드 요구 사항이 증가하면 데이터 디스크를 추가해야 할 수 있습니다. 이렇게 하려면 다음 명령을 실행합니다.

### <a name="az"></a>[Az](#tab/az)

```powershell
Add-AzRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```
---



### <a name="delete-the-vm"></a>VM 삭제

디바이스에서 가상 머신을 제거하려면 다음 cmdlet을 실행합니다.

### <a name="az"></a>[Az](#tab/az)

```powershell
Remove-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
이 cmdlet에 대한 자세한 내용은 [Remove-AzVm cmdlet](/powershell/module/az.compute/remove-azvm?view=azps-5.9.0&preserve-view=true)을 참조하세요.

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```
이 cmdlet에 대한 자세한 내용은 [Remove-AzureRmVm cmdlet](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조하세요.

---

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
