---
title: Azure Virtual Network Manager(미리 보기)를 사용하여 메시 네트워크 토폴로지 만들기 - Azure PowerShell
description: Azure PowerShell 사용하여 Azure Virtual Network Manager에서 메시 네트워크 토폴로지를 만드는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d763a0914a624d21d3845a05c1f2c0e6ef7e1ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053252"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Azure Virtual Network Manager(미리 보기)를 사용하여 메시 네트워크 토폴로지 만들기 - Azure PowerShell

이 문서에서는 Azure PowerShell 사용하여 Azure Virtual Network Manager에서 메시 네트워크 토폴로지를 만드는 방법을 알아봅니다. 이 구성을 사용하면 동일한 네트워크 그룹에 있는 동일한 지역의 모든 가상 네트워크가 서로 통신할 수 있습니다. 연결 구성에서 전역 메시 설정을 사용하도록 설정하여 지역 간 연결을 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

* [메시](concept-connectivity-configuration.md#mesh-network-topology) 네트워크 토폴로지 알아보기
* Azure [Virtual Network Manager 인스턴스를](create-virtual-network-manager-powershell.md#create-virtual-network-manager)만들었습니다.
* 메시 구성에서 사용하려는 가상 네트워크를 식별하거나 새 [가상 네트워크를](../virtual-network/quick-create-powershell.md)만듭니다.

## <a name="create-a-network-group"></a>네트워크 그룹 만들기

이 섹션에서는 허브 및 스포크 네트워크 토폴로지에서 사용할 가상 네트워크가 포함된 네트워크 그룹을 만드는 데 도움이 됩니다.

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

1. 이전에 [New-AzNetworkManagerGroup을](/powershell/module/az.network/new-aznetworkmanagergroup)사용하여 정적 멤버 자격 그룹(GroupMember) 또는 동적 멤버 자격 그룹(ConditionalMembership)을 정의하여 네트워크 그룹을 만듭니다.

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetworks
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-mesh-connectivity-configuration"></a>메시 연결 구성 만들기

이 섹션에서는 이전 섹션에서 만든 네트워크 그룹을 사용하여 메시 구성을 만드는 방법을 안내합니다.

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

## <a name="deploy-the-mesh-configuration"></a>메시 구성 배포

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

## <a name="confirm-deployment"></a>배포 확인

1. 포털의 가상 네트워크 중 하나로 이동하고 *설정* 아래에서 **네트워크 관리자를** 선택합니다. 해당 페이지에 나열된 구성이 표시됩니다.

1. 가상 네트워크 간의 연결을 테스트하려면 각 가상 네트워크에 테스트 가상 머신을 배포하고 가상 네트워크 간에 ICMP 요청을 시작합니다.

## <a name="next-steps"></a>다음 단계

- 보안 [관리자 규칙에](concept-security-admins.md) 대해 알아보기
- [SecurityAdmin 구성을](how-to-block-network-traffic-powershell.md)사용하여 네트워크 트래픽을 차단하는 방법을 알아봅니다.
