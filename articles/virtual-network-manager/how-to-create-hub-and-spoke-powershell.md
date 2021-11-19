---
title: Azure Virtual Network Manager(미리 보기)를 사용하여 허브 및 스포크 토폴로지 만들기 - Azure PowerShell
description: azure Virtual Network Manager에서 Azure PowerShell 사용하여 허브 및 스포크 네트워크 토폴로지를 만드는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 4f3d9cf8a16de506571e5c79a9d8741dc81e5639
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808283"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Azure Virtual Network Manager(미리 보기)를 사용하여 허브 및 스포크 토폴로지 만들기 - Azure PowerShell

이 문서에서는 Azure Virtual Network Manager를 사용하여 허브 및 스포크 네트워크 토폴로지를 만드는 방법을 알아봅니다. 이 구성을 사용하면 허브 역할을 할 가상 네트워크를 선택하고 모든 스포크 가상 네트워크에는 기본적으로 허브만 있는 양방향 피어링이 있습니다. 또한 스포크 가상 네트워크 간의 직접 연결을 사용하도록 설정하고 스포크 가상 네트워크가 허브의 가상 네트워크 게이트웨이를 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> *Azure Virtual Network Manager는* 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 허브 [및 스포크](concept-connectivity-configuration.md#hub-and-spoke-topology) 네트워크 토폴로지 알아보기
* Azure [Virtual Network Manager 인스턴스를](create-virtual-network-manager-powershell.md#create-virtual-network-manager)만들었습니다.
* 허브 및 스포크 구성에서 사용하려는 가상 네트워크를 식별하거나 새 [가상 네트워크를](../virtual-network/quick-create-powershell.md)만듭니다. 

## <a name="create-a-network-group"></a>네트워크 그룹 만들기

이 섹션에서는 허브 및 스포크 네트워크 토폴로지에서 사용할 가상 네트워크가 포함된 네트워크 그룹을 만드는 데 도움이 됩니다.

### <a name="static-membership"></a>정적 멤버 자격

1. New-AzNetworkManagerGroupMembersItem을 사용하여 정적 가상 네트워크 멤버를 만듭니다.

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

1. 이전에 New-AzNetworkManagerGroup을 사용하여 정의한 정적 멤버 자격 그룹(GroupMember) 또는 동적 멤버 자격 그룹(ConditionalMembership)을 사용하여 네트워크 그룹을 만듭니다.

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

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>허브 및 스포크 연결 구성 만들기

이 섹션에서는 이전 섹션에서 만든 네트워크 그룹을 사용하여 허브 및 스포크 구성을 만드는 방법을 안내합니다.

1. New-AzNetworkManagerConnectivityGroupItem을 사용하여 네트워크 그룹을 추가하는 스포크 연결 그룹 항목을 만듭니다. 플래그, 플래그가 있는 전역 메시 또는 플래그를 사용하여 직접 연결을 사용하도록 `-GroupConnectivity` `-IsGlobal` `-UseHubGateway` 설정하여 허브 가상 네트워크에서 게이트웨이를 사용할 수 있습니다.

    ```azurepowershell-interactive
    $spokes = @{
        NetworkGroupId = $networkgroup.Id
    }
    $spokesGroup = New-AzNetworkManagerConnectivityGroupItem @gi -UseHubGateway -GroupConnectivity 'None' -IsGlobal
    ```

1. 스포크 연결 그룹을 만들고 이전 단계의 그룹 항목을 추가합니다.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($spokesGroup) 
    ```

1. 허브 연결 그룹 항목을 만들고 New-AzNetworkManagerHub를 사용하여 허브로 사용할 가상 네트워크를 정의합니다.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerHub]]$hubList = @()
    
    $hub = @{
        ResourceId = '/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA'
        ResourceType = 'Microsoft.Network/virtualNetworks'
    } 
    $hubvnet = New-AzNetworkManagerHub @hub

    $hubList.Add($hubvnet)
    ```

1. New-AzNetworkManagerConnectivityConfiguration을 사용하여 연결 구성을 만듭니다.

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'HubAndSpoke'
        Hub = $hubList
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config -DeleteExistingPeering -IsGlobal
     ```

## <a name="deploy-the-hub-and-spoke-configuration"></a>허브 및 스포크 구성 배포

Deploy-AzNetworkManagerCommit을 사용하여 대상 지역에 구성을 커밋합니다.

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>배포 확인

1. 포털의 가상 네트워크 중 하나로 이동하여 *설정* 아래에서 **피어링을** 선택합니다. 이름에 *AVNM을* 사용하여 허브와 스포크 가상 네트워크 간에 새 피어링 연결 만들기가 표시됩니다.

1. 스포크 간의 *직접 연결을* 테스트하려면 각 스포크 가상 네트워크에 가상 머신을 배포합니다. 그런 다음, 한 가상 머신에서 다른 가상 머신으로 ICMP 요청을 시작합니다.

## <a name="next-steps"></a>다음 단계

- 보안 [관리자 규칙에](concept-security-admins.md) 대해 알아보기
- [SecurityAdmin 구성을](how-to-block-network-traffic-powershell.md)사용하여 네트워크 트래픽을 차단하는 방법을 알아봅니다.
