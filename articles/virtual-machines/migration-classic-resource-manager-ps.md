---
title: PowerShell을 사용하여 리소스 관리자에 마이그레이션하기
description: 본 문서에서는 Azure PowerShell 명령을 사용하여 VM(가상 머신), 가상 네트워크, 스토리지 계정 등의 IaaS 리소스를 클래식에서 Azure Resource Manager로 플랫폼 지원 마이그레이션하는 과정을 살펴봅니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 97754cde03e6660ee13444dca03e9c77d81e2a67
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690282"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

> [!IMPORTANT]
> 현재 IaaS VM의 90%가 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)를 사용하고 있습니다. 2020년 2월 28일부터 클래식 VM은 더 이상 사용되지 않으며 2023년 3월 1일에 완전히 사용 중지될 예정입니다. 사용 중단 및 [그 영향](classic-vm-deprecation.md#how-does-this-affect-me)에 대해 [자세히 알아보세요]( https://aka.ms/classicvmretirement).

이러한 단계에서는 Azure PowerShell 명령을 사용하여 클래식 배포 모델의 laaS(Infrastructure as a Service) 리소스를 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 보여 줍니다.

원한다면, [Azure CLI](migration-classic-resource-manager-cli.md)를 사용하여도 리소스를 마이그레이션할 수 있습니다.

* 지원되는 마이그레이션 시나리오에 대한 배경 정보를 보려면 [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](migration-classic-resource-manager-overview.md)을 참조하세요.
* 자세한 지침 및 마이그레이션 연습에 대해서는 [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)를 참조하세요.
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md)

<br>
다음은 마이그레이션 프로세스 중 실행하여야 하는 단계의 순서를 나타내는 순서도입니다.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>1단계: 마이그레이션 계획
다음은 클래식에서 리소스 관리자로 IaaS 리소스를 마이그레이션할지 판단할 때 참고할 만한 몇 가지 모범 사례입니다.

* [지원/지원되지 않는 기능 및 구성](migration-classic-resource-manager-overview.md)을 읽어봅니다. 지원하지 않는 구성이나 기능을 사용하는 가상 머신이 있다면, 해당 구성이나 기능에 대한 지원이 발표될 때까지 기다립니다. 필요에 따라서는 마이그레이션이 가능하도록 해당 기능을 제거하거나 구성 외부로 이동할 수도 있습니다.
* 현재 인프라 및 애플리케이션을 배포하는 스크립트를 자동화한 경우 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설정을 만들어봅니다. 또는 Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.

> [!IMPORTANT]
> 애플리케이션 게이트웨이는 현재 클래식에서 리소스 관리자로 마이그레이션하는 것이 지원되지 않습니다. 애플리케이션 게이트웨이가 있는 가상 네트워크를 마이그레이션하려면 네트워크 이전을 위한 준비 작업을 실행하기 전에 해당 게이트웨이를 제거하세요. 마이그레이션을 완료한 후 Azure Resource Manager에서 게이트웨이를 다시 연결합니다.
>
> 다른 구독의 ExpressRoute 회로에 연결하는 Azure ExpressRoute 게이트웨이는 자동으로 마이그레이션할 수 없습니다. 이런 경우, ExpressRoute 게이트웨이를 제거하고 가상 네트워크를 마이그레이션한 다음에 해당 게이트웨이를 재생성하세요. 자세한 내용은 [Migrate ExpressRoute 회로와 관련 가상 네트워크를 클래식에서 리소스 관리자 배포 모델로 마이그레이션하기](../expressroute/expressroute-migration-classic-resource-manager.md)를 참조하세요.

## <a name="step-2-install-the-latest-version-of-powershell"></a>2단계: PowerShell을 최신 버전으로 설치
Azure PowerShell을 설치하는 두 가지 주요 옵션으로 [PowerShell 갤러리](https://www.powershellgallery.com/profiles/azure-sdk/) 또는 [WebPI(웹 플랫폼 설치 관리자)](https://aka.ms/webpi-azps)가 있습니다. WebPI는 매월 업데이트를 수신합니다. PowerShell 갤러리는 지속적으로 업데이트를 수신합니다. 이 문서는 Azure PowerShell 버전 2.1.0을 기반으로 합니다.

설치 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>3단계: 본인이 해당 구독의 관리자인지 확인
이 마이그레이션을 수행하려면, 해당 구독에 대하여 [Azure Portal](https://portal.azure.com)에 공동 관리자로 추가되어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **허브** 메뉴에서 **구독** 을 선택합니다. 표시되지 않으면 **모든 서비스** 를 선택합니다.
3. 적절한 구독 항목을 찾아 **내 역할** 필드를 확인합니다. 공동 관리자인 경우 해당 값은 _계정 관리자_ 여야 합니다.

공동 관리자를 추가할 수 없는 상태라면 서비스 관리자나 공동 관리자에게 자신을 해당 구독에 추가하여 달라고 요청합니다.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4단계: 구독 설정 및 마이그레이션 등록
먼저, PowerShell 프롬프트를 시작합니다. 마이그레이션의 경우, 클래식과 Resource Manager 양쪽 모두에 대한 환경을 설정합니다.

Resource Manager 모델에 대한 계정으로 로그인합니다.

```powershell
    Connect-AzAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독 이름을 **내 Azure 구독** 으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> 등록은 일회성 단계이지만 마이그레이션 시도 전에 반드시 한 번은 하여야 합니다. 등록하지 않으면 다음과 같은 오류 메시지가 표시됩니다.
>
> *BadRequest : 구독이 마이그레이션에 대해 등록되지 않았습니다.*

다음 명령을 사용하여 마이그레이션 리소스 공급자에 등록합니다.

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

등록이 완료될 때까지 5분간 기다리세요. 다음 명령을 사용하여 승인 상태를 확인하세요.

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

계속 진행하기 전에 RegistrationState가 `Registered` 인지 확인합니다.

클래식 배포 모델로 전환하기 전에는 반드시 현재 배포나 가상 네트워크의 Azure 지역 내에 Azure Resource Manager 가상 머신 vCPU가 충분한지 확인합니다. 다음 PowerShell 명령을 사용하여 Azure Resource Manager에 있는 현재 vCPU 수를 확인할 수 있습니다. vCPU 할당량에 대한 자세한 내용은 [제한 및 Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)를 참조하세요.

이 예제에서는 **미국 서부** 지역의 사용 가능 여부를 확인합니다. 예제 지역 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Get-AzVMUsage -Location "West US"
```

이제 클래식 배포 모델 계정에 로그인합니다.

```powershell
    Add-AzureAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독을 **내 Azure 구독** 으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>5단계: IaaS 리소스를 마이그레이션하는 명령 실행
* [가상 네트워크가 아닌 클라우드 서비스의 VM 마이그레이션](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [가상 네트워크에서 VM 마이그레이션](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [스토리지 계정 마이그레이션](#step-52-migrate-a-storage-account)

> [!NOTE]
> 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼이 해당 작업을 다시 시도합니다.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>5\.1단계: 옵션 1 - 가상 네트워크가 아닌 클라우드 서비스의 가상 머신 마이그레이션
다음 명령을 사용하여 해당 클라우드 서비스 목록을 가져옵니다. 그런 다음, 마이그레이션하려는 클라우드 서비스를 선택합니다. 클라우드 서비스의 VM이 가상 네트워크에 있거나 웹 또는 작업자 역할을 포함하는 경우 명령을 오류 메시지를 반환합니다.

```powershell
    Get-AzureService | ft Servicename
```

클라우드 서비스에 대한 배포 이름을 가져옵니다. 이 예제에서는 서비스 이름이 **내 서비스** 입니다. 예제 서비스 이름을 사용자 고유의 서비스 이름으로 바꿉니다.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

마이그레이션을 위해 클라우드 서비스의 가상 머신을 준비합니다. 두 가지 옵션 중 선택할 수 있습니다.

* **옵션 1: 플랫폼 생성 가상 네트워크에 VM 마이그레이션하기**

    먼저, 다음 명령을 사용하여 해당 클라우드 서비스를 마이그레이션할 수 있는지 유효성을 검사합니다.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사 메시지에 유형 오류 메시지가 없다면 준비 단계로 이행할 수 있습니다.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **옵션 2: 리소스 관리자 배포 모델 내의 기존 가상 네트워크로 마이그레이션하기**

    해당 예제에서는 관련 리소스 그룹 이름을 **myResourceGroup** 으로 설정하고, 그 가상 네트워크 이름은 **myVirtualNetwork** 로, 서브넷 이름은 **mySubNet** 으로 설정합니다. 예제의 이름을 사용자 고유의 리소스 이름으로 바꿉니다.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    먼저, 다음 명령을 사용하여 해당 가상 네트워크를 마이그레이션할 수 있는지 유효성을 검사합니다.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사 메시지에 오류가 없다면 다음 준비 단계로 이행할 수 있습니다.

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

위의 옵션 중 하나를 사용할 경우 준비 작업에 성공적으로 수행되면 VM의 마이그레이션 상태를 쿼리합니다. 상태가 `Prepared`인지 확인합니다.

이 예제에서는 VM 이름을 **myVM** 으로 설정합니다. 예제 이름을 사용자 고유의 VM 이름으로 바꿉니다.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

PowerShell 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. 마이그레이션 준비가 되어 있지 않아 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>5\.1단계: 옵션 2 - 가상 네트워크의 가상 머신 마이그레이션하기

가상 네트워크에서 가상 머신을 마이그레이션하려면 가상 네트워크를 마이그레이션합니다. 가상 머신은 가상 네트워크와 함께 자동으로 마이그레이션됩니다. 마이그레이션할 가상 네트워크를 선택합니다.
> [!NOTE]
> 해당 가상 머신의 VHD(OS 및 데이터) 파일을 사용하여 관리 디스크가 있는 새 리소스 관리자 가상 머신을 만들어 클래식 배포 모델을 사용하여 만들어진 [단일 가상 머신을 마이그레이션합니다](./windows/create-vm-specialized-portal.md).
<br>

> [!NOTE]
> 가상 네트워크 이름은 새 포털에 표시된 것과 다를 수 있습니다. 새로운 Azure Portal에는 이름이 `[vnet-name]`처럼 표시되지만 실제 가상 네트워크 이름은 `Group [resource-group-name] [vnet-name]` 형식입니다. 마이그레이션을 시작하기 전에 `Get-AzureVnetSite | Select -Property Name` 명령을 사용하여 실제 가상 네트워크 이름을 조회하거나 이전 Azure Portal에서 그 이름을 확인합니다. 

이 예제에서는 가상 네트워크 이름을 **myVnet** 으로 설정합니다. 예제 가상 네트워크 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 가상 네트워크에 웹 또는 작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면 유효성 검사 오류 메시지가 표시됩니다.

먼저, 다음 명령을 사용하여 해당 가상 네트워크를 마이그레이션할 수 있는지 유효성을 검사합니다.

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사가 성공하면 다음 준비 단계를 이행할 수 있습니다.

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell 또는 Azure 포털을 사용하여 준비된 가상 머신에 대한 구성을 확인합니다. 마이그레이션 준비가 되어 있지 않아 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>5\.2단계: 스토리지 계정 마이그레이션하기
가상 머신을 완전히 마이그레이션 하였으면 스토리지 계정을 마이그레이션하기 전에 다음 필수 구성 요소 검사를 수행합니다.

> [!NOTE]
> 스토리지 계정에 관련 디스크나 VM 데이터가 없다면 “스토리지 계정 유효성 검사 및 마이그레이션 시작” 섹션으로 건너뛸 수 있습니다. 클래식 디스크나 VM 이미지 또는 OS 이미지를 삭제하여도 해당 스토리지 계정의 원본 VHD 파일을 제거하지는 않습니다. 그러나 이러한 동작은 해당 VHD 파일에 대한 임대를 중단시켜 마이그레이션 이후에 ARM 디스크나 이미지를 만들 때 그 파일을 다시 사용할 수 있게 됩니다.

* VM을 마이그레이션하였거나 스토리지 계정에 디스크 리소스가 있는 경우의 필수 구성 요소 검사:
    * 스토리지 계정에 디스크를 저장한 가상 머신을 마이그레이션합니다.

        다음 명령은 스토리지 계정에서 모든 VM 디스크의 RoleName 및 DiskName 속성을 반환합니다. RoleName은 디스크가 연결된 가상 머신의 이름입니다. 해당 명령이 디스크를 반환하면, 스토리지 계정을 마이그레이션하기 전에 이들 디스크가 연결된 가상 머신을 마이그레이션하여야 합니다.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * 스토리지 계정에 저장된 VM 디스크 중 연결되지 않은 것을 삭제

        다음 명령을 사용하여 스토리지 계정 내의 VM 디스크 중 연결되지 않은 것을 찾습니다.

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        이전 명령이 디스크를 반환한다면 다음 명령을 사용하여 해당 디스크를 삭제합니다.

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * 스토리지 계정에 저장된 VM 이미지 삭제

        다음 명령은 OS 디스크가 스토리지 계정에 저장되어 있는 모든 VM 이미지를 반환합니다.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         다음 명령은 스토리지 계정에 저장된 이미지 중 데이터가 포함된 모든 VM 이미지를 반환합니다.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        다음 명령을 사용하여 이전 명령에서 반환시킨 VM 이미지를 모두 삭제합니다.
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* 스토리지 계정의 유효성을 검사하고 마이그레이션을 시작합니다.

    다음 명령을 사용하여 마이그레이션을 위한 각 스토리지 계정의 유효성을 검사합니다. 이 예제에서 스토리지 계정 이름은 **myStorageAccount** 입니다. 예제 이름을 사용자 고유의 스토리지 계정 이름으로 바꿉니다.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    다음 단계는 마이그레이션을 위하여 스토리지 계정을 준비하는 것입니다.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Azure PowerShell 또는 Azure 포털을 사용하여 준비된 스토리지 계정에 대한 구성을 확인합니다. 마이그레이션 준비가 되어 있지 않아 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>다음 단계
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-cli.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](migration-classic-resource-manager-community-tools.md)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.yml)
