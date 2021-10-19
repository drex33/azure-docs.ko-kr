---
title: Azure PowerShell을 사용하여 유연한 확장 집합에서 가상 머신 만들기
description: PowerShell을 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 45f998dbba1ffac99fc8d491cb90694a76c11f98
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165740"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-powershell"></a>PowerShell을 사용하여 유연한 확장 집합으로 가상 머신 만들기

**적용 대상:** :heavy_check_mark: 유연한 확장 집합


이 문서에서는 PowerShell을 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합을 만드는 단계를 안내합니다. 유연한 확장 집합에 대한 자세한 내용은 [가상 머신 확장 집합에 대한 유연한 오케스트레이션 모드](flexible-virtual-machine-scale-sets.md)를 참조하세요. 

> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.


## <a name="get-started-with-flexible-scale-sets"></a>유연한 확장 집합 시작

Azure PowerShell을 사용하여 유연한 가상 머신 확장 집합 만듭니다.

### <a name="add-multiple-vms-to-a-scale-set"></a>확장 집합에 여러 VM 추가 

다음 예제에서는 가상 머신 프로필(VM 유형, 네트워킹 구성, 스토리지 유형 등) 및 만들 인스턴스 수(SKU 용량 = 2)를 지정합니다. 

1. IP 주소 구성 만들기:

    ```azurepowershell-interactive
    $ipConfig = New-AzVmssIpConfig -Name "myIPConfig"
    -SubnetId "${vnetid}/subnets/default" `
    -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id
    ```

1. 구성 개체 만들기:

    구성 개체는 확장 집합을 만들기 위한 핵심 정보를 저장합니다.

    ```azurepowershell-interactive
    $vmssConfig = New-AzVmssConfig -Location $loc
    -SkuCapacity 2 -SkuName "Standard_DS1_v2"
    -OrchestrationMode 'Flexible' `
    -PlatformFaultDomainCount 1
    ```

1. 갤러리에서 가상 머신 이미지 참조:

    ```azurepowershell-interactive
    Set-AzVmssStorageProfile $vmssConfig -OsDiskCreateOption "FromImage"
    -ImageReferencePublisher "Canonical" -ImageReferenceOffer "UbuntuServer"
    -ImageReferenceSku "18.04-LTS" `
    -ImageReferenceVersion "latest"
    ```

1. 가상 머신을 인증하는 데 필요한 정보 설정:

    ```azurepowershell-interactive
    Set-AzVmssOsProfile $vmssConfig -AdminUsername $cred.UserName
    -AdminPassword $cred.Password -ComputerNamePrefix $vmname
    ```

1. 가상 네트워크를 구성 개체에 연결합니다.

    ```azurepowershell-interactive
    Add-AzVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig
    -Name "network-config" -Primary $true
    -IPConfiguration $ipConfig `
    -NetworkApiVersion '2020-11-01'
    ```

1. 구성 개체를 사용하여 확장 집합을 만듭니다.

    이 단계를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 

    ```azurepowershell-interactive
    New-AzVmss -ResourceGroupName $rgname
    -Name $vmssName `
    -VirtualMachineScaleSet $vmssConfig
    ```

### <a name="add-a-single-vm-to-a-scale-set"></a>확장 집합에 단일 VM 추가

다음 예제에서는 장애 도메인 수가 1로 설정된 VM 프로필 없이 유연한 확장 집합을 만드는 모습을 보여 줍니다. 가상 머신이 만들어지고 유연한 확장 집합에 추가됩니다.

1. Azure PowerShell에 로그인하고 배포에 대한 구독 및 변수를 지정합니다. 

    ```azurepowershell-interactive
    Connect-AzAccount
    Set-AzContext `
        -Subscription "00000000-0000-0000-0000-000000000" 
    
    $loc = "eastus" 
    $rgname = "myResourceGroupFlexible" 
    $vmssName = "myFlexibleVMSS" 
    $vmname = "myFlexibleVM"
    ```

1. 네트워킹 또는 VM SKU와 같은 VM 프로필 매개 변수를 지정하지 마세요.

    ```azurepowershell-interactive
    $VmssConfigWithoutVmProfile = new-azvmssconfig -location $loc -platformfaultdomain 1 `
    $VmssFlex = new-azvmss -resourcegroupname $rgname -vmscalesetname $vmssName -virtualmachinescaleset $VmssConfigWithoutVmProfile 
    ```
 
1. 유연한 확장 집합에 VM을 추가합니다.

    ```azurepowershell-interactive
    $vm = new-azvm -resourcegroupname $rgname -location $loc -name $vmname -credential $cred -domainnamelabel $domainName -vmssid $VmssFlex.id 
    ```


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Portal에서 유연한 확장 집합을 만드는 방법을 알아봅니다.](flexible-virtual-machine-scale-sets-portal.md)
