---
title: '빠른 시작: Azure PowerShell을 사용하는 Azure Virtual Network Manager를 통해 메시 네트워크 만들기'
description: 이 빠른 시작을 활용하여 Azure PowerShell을 사용하는 Azure Virtual Network Manager를 통해 메시 네트워크를 만드는 방법에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: a25f96fc9584c776c51798ea05d6221116b0b6a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029369"
---
# <a name="quickstart-create-a-mesh-network-with-azure-virtual-network-manager-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하는 Azure Virtual Network Manager를 통해 메시 네트워크 만들기

가상 네트워크 연결을 관리하기 위해 Azure PowerShell을 사용하여 Azure Virtual Network Manager를 시작합니다.

이 빠른 시작에서는 세 개의 가상 네트워크를 배포하고 Azure Virtual Network Manager를 사용하여 메시 네트워크 토폴로지 만듭니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 최신 PowerShell 모듈이 있는지 확인하거나 포털에서 Azure Cloud Shell을 사용할 수 있습니다.
* 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-virtual-network-manager"></a>Virtual Network Manager 만들기

1. 이 Azure Virtual Network Manager 인스턴스에 포함될 범위 및 액세스 유형을 정의합니다. 구독 그룹 또는 관리 그룹이나 둘의 조합을 사용하여 범위를 만들기 위해 선택할 수 있습니다. [New-AzNetworkManagerScope](/powershell/module/az.network/new-aznetworkmanagerscope)을 사용하여 범위를 만듭니다.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$subGroup = @()  
    $group.Add("/subscriptions/abcdef12-3456-7890-abcd-ef1234567890")
    [System.Collections.Generic.List[string]]$mgGroup = @()  
    $group.Add("/managementGroups/abcdef12-3456-7890-abcd-ef1234567890")
    
    [System.Collections.Generic.List[String]]$access = @()  
    $access.Add("Connectivity");  
    $access.Add("Security"); 
 
    $scope = New-AzNetworkManagerScope -Subscription $subGroup  -ManagementGroup $mgGroup
    ```

1. [New-AzNetworkManager](/powershell/module/az.network/new-aznetworkmanager)를 사용하여 Virtual Network Manager를 만듭니다. 이 예제에서는 미국 서부에 위치한 **myAVNM** 이라는 Azure Virtual Network Manager를 만듭니다.

    ```azurepowershell-interactive
    $avnm = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerScope = $scope.id
        NetworkManagerScopeAccess = $access
        Location = 'West US'
    }
    $networkmanager = New-AzNetworkManager @avnm
    ```

## <a name="create-resource-group-and-virtual-networks"></a>리소스 그룹 및 가상 네트워크 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure Virtual Network Manager를 만들려면 먼저 Network Manager를 호스트하는 리소스 그룹을 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 **미국 서부** 에 있는 **myAVNMResourceGroup** 이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$rg = @{
    Name = 'myAVNMResourceGroup'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-three-virtual-networks"></a>세 개의 가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 세 개의 가상 네트워크를 만듭니다. 이 예제에서는 **미국 서부** 에 있는 **VNetA**, **VNetB** 및 **VNetC** 이라는 가상 네트워크를 만듭니다. 메시 네트워크를 만들려는 가상 네트워크가 이미 있는 경우 다음 섹션으로 건너뛸 수 있습니다.

```azurepowershell-interactive
$vnetA = @{
    Name = 'VNetA'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetworkA = New-AzVirtualNetwork @vnetA

$vnetB = @{
    Name = 'VNetB'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.1.0.0/16'    
}
$virtualNetworkB = New-AzVirtualNetwork @vnetB

$vnetC = @{
    Name = 'VNetC'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.2.0.0/16'    
}
$virtualNetworkC = New-AzVirtualNetwork @vnetC
```

### <a name="add-a-subnet-to-each-virtual-network"></a>각 가상 네트워크에 서브넷을 추가

가상 네트워크의 구성을 완료하려면 각 네트워크에 /24 서브넷을 추가합니다. [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)를 사용하여 **기본값** 으로 명명된 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$subnetA = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkA
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigA = Add-AzVirtualNetworkSubnetConfig @subnetA
$virtualnetworkA | Set-AzVirtualNetwork

$subnetB = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkB
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetB
$virtualnetworkB | Set-AzVirtualNetwork

$subnetC = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkC
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetC
$virtualnetworkC | Set-AzVirtualNetwork
```

## <a name="create-a-network-group"></a>네트워크 그룹 만들기

### <a name="static-membership"></a>정적 멤버 자격

1. [New-AzNetworkManagerGroupMembersItem](/powershell/module/az.network/new-aznetworkmanagergroupmembersitem)을 사용하여 정적 가상 네트워크 멤버를 만듭니다.

    ```azurepowershell-interactive
    $member = New-AzNetworkManagerGroupMembersItem –ResourceId "/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA"
    ```

1. 다음 명령을 사용하여 정적 멤버 자격 그룹에 정적 멤버를 추가합니다.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerGroupMembersItem]]$groupMembers = @()  
    $groupMembers.Add($member)
    ```

### <a name="dynamic-membership"></a>동적 멤버 자격

1. 조건문을 정의하고 변수에 저장합니다.

    ```azurepowershell-interactive
    $conditionalMembership = '{ 
        "allof":[ 
            { 
            "field": "name", 
            "contains": "VNet" 
            } 
        ] 
    }' 
    ```

1. [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup)을 사용하여 마지막 단계에서 정의한 조건문을 통해 네트워크 그룹을 만듭니다.

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetwork'
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-configuration"></a>구성 만들기

1. [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem)을 사용하여 네트워크 그룹을 추가할 연결 그룹 항목을 만듭니다.

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = $networkgroup.Id
    }
    $groupItem = New-AzNetworkManagerConnectivityGroupItem @gi
    ```

1. 구성 그룹을 만들고 이전 단계의 그룹 항목을 추가합니다.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($groupItem)
    ```

1. [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration)을 사용하여 연결 구성을 만듭니다.

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'Mesh'
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config
     ```

## <a name="commit-deployment"></a>배포 커밋

[Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit)을 사용하여 대상 지역에 구성을 커밋합니다.

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$target = @()   
$target.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $target
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment 
```

## <a name="clean-up-resources"></a>리소스 정리

Azure Virtual Network Manager가 더 이상 필요하지 않은 경우 리소스를 삭제하기 전에 다음 사항을 모두 충족해야 합니다.

* 모든 지역에 구성이 배포되지 않습니다.
* 모든 구성이 삭제되었습니다.
* 모든 네트워크 그룹이 삭제되었습니다.

1. [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit)로 빈 구성을 배포하여 연결 배포를 제거합니다.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$configIds = @()
    [System.Collections.Generic.List[string]]$target = @()   
    $target.Add("westus")     
    $removedeployment = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        ConfigurationId = $configIds
        Target = $target
        CommitType = 'Connectivity'
    }
    Deploy-AzNetworkManagerCommit @removedeployment
    ```

1. [Remove-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/remove-aznetworkmanagerconnectivityconfiguration)을 사용하여 연결 구성을 제거합니다

    ```azurepowershell-interactive
    $removeconfig = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerConnectivityConfiguration @removeconfig   
    ```

1. [Remove-AzNetworkManagerGroup](/powershell/module/az.network/remove-aznetworkmanagergroup)을 사용하여 네트워크 그룹을 제거합니다.

    ```azurepowershell-interactive
    $removegroup = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerGroup @removegroup
    ```

1. [Remove-AzNetworkManager](/powershell/module/az.network/remove-aznetworkmanager)를 사용하여 네트워크 관리자 인스턴스를 삭제합니다.

    ```azurepowershell-interactive
    $removenetworkmanager = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
    }
    Remove-AzNetworkManager @removenetworkmanager
    ```

1. 만든 리소스가 더 이상 필요하지 않으면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹을 삭제합니다.

    ```azurepowershell-interactive
    Remove-AzResourceGroup -Name 'myAVNMResourceGroup'
    ```

## <a name="next-steps"></a>다음 단계

Azure Virtual Network Manager를 만들었다면 이제 보안 관리 구성을 사용하여 네트워크 트래픽을 차단하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [보안 관리 규칙을 사용하여 네트워크 트래픽 차단](how-to-block-network-traffic-powershell.md)
