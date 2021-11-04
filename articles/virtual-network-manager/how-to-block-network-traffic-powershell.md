---
title: Azure Virtual Network Manager(미리 보기)를 사용하여 네트워크 트래픽을 차단하는 방법 - Azure PowerShell
description: Azure PowerShell Azure Virtual Network Manager에서 보안 규칙을 사용하여 네트워크 트래픽을 차단하는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d2e1a79891c0061dd49749fa2e27a2725a0b922f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103620"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-powershell"></a>Azure Virtual Network Manager(미리 보기)를 사용하여 네트워크 트래픽을 차단하는 방법 - Azure PowerShell

이 문서에서는 규칙 컬렉션에 추가할 수 있는 포트 80 및 443에 대한 아웃바운드 네트워크 트래픽을 차단하는 보안 규칙을 만드는 방법을 보여 줍니다. 자세한 내용은 [보안 관리자 규칙을 참조하세요.](concept-security-admins.md)

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

보안 규칙 구성을 시작하기 전에 다음 단계를 확인합니다.

* [보안 관리자 규칙](concept-security-admins.md)의 각 요소를 이해합니다.
* [Azure Virtual Network Manager 인스턴스를](create-virtual-network-manager-powershell.md)만들었습니다.

## <a name="create-a-securityadmin-configuration"></a>SecurityAdmin 구성 만들기

1. [New-AzNetworkManagerSecurityAdminConfiguration 을](/powershell/module/az.network/new-aznetworkmanagersecurityadminconfiguration)사용하여 새 SecurityAdmin 구성을 만듭니다.

    ```azurepowershell-interactive
    $config = @{
        Name = 'SecurityConfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $securityconfig = New-AzNetworkManagerSecurityAdminConfiguration @config 
    ```

1. [Get-AzNetworkManagerGroup](/powershell/module/az.network/get-aznetworkmanagergroup)을 사용하여 네트워크 그룹을 변수에 저장합니다.

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResoureceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $networkgroup = Get-AzNetworkManagerGroup @ng   
    ```

1. [New-AzNetworkManagerSecurityGroupItem을](/powershell/module/az.network/new-aznetworkmanagersecuritygroupitem)사용하여 에 네트워크 그룹을 추가할 연결 그룹 항목을 만듭니다.

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = '$networkgroup.Id'
    }
    $groupItem = New-AzNetworkManagerSecurityGroupItem @gi
    ```

1. 구성 그룹을 만들고 이전 단계의 그룹 항목을 추가합니다.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerSecurityGroupItem]]$configGroup = @()  
    $configGroup.Add($groupItem) 
    ```

1. [New-AzNetworkManagerSecurityAdminRuleCollection 을](/powershell/module/az.network/new-aznetworkmanagersecurityadminrulecollection)사용하여 보안 관리자 규칙 컬렉션을 만듭니다.

    ```azurepowershell-interactive
    $collection = @{
        Name = 'myRuleCollection'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManager = 'myAVNM'
        ConfigName = 'SecurityConfig'
        AppliesToGroup = $configGroup
    }
    $rulecollection = New-AzNetworkManagerSecurityAdminRuleCollection @collection
    ```

1. [New-AzNetworkManagerAddressPrefixItem](/powershell/module/az.network/new-aznetworkmanageraddressprefixitem)을 사용하여 원본 및 대상 주소 접두사 및 포트에 대한 변수를 정의합니다.

    ```azurepowershell-interactive
    $sourceip = @{
        AddressPrefix = 'Internet'
        AddressPrefixType = 'ServiceTag'
    }
    $sourceprefix = New-AzNetworkManagerAddressPrefixItem @sourceip

    $destinationip = @{
        AddressPrefix = '10.0.0.0/24'
        AddressPrefixType = 'IPPrefix'
    }
    $destinationprefix = New-AzNetworkManagerAddressPrefixItem @destinationip

    [System.Collections.Generic.List[string]]$sourcePortList = @() 
    $sourcePortList.Add("65500”) 

    [System.Collections.Generic.List[string]]$destinationPortList = @() 
    $destinationPortList.Add("80”)
    $destinationPortList.Add("443”)
    ```

1. [New-AzNetworkManagerSecurityAdminRule 을](/powershell/module/az.network/new-aznetworkmanagersecurityadminrule)사용하여 보안 규칙을 만듭니다.

    ```azurepowershell-interactive
    $rule = @{
        Name = 'Block_HTTP_HTTPS'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        SecurityAdminConfigurationName = 'SecurityConfig'
        RuleCollectionName = 'myRuleCollection'
        Protocol = 'TCP'
        Access = 'Deny'
        Priority = '100'
        Direction = 'Outbound'
        Source = $sourceprefix
        SourcePortRange = $sourcePortList
        Destination = $destinationprefix
        DestinationPortRange = $destinationPortList
    }
    $securityrule = New-AzNetworkManagerSecurityAdminRule @rule
    ```

## <a name="commit-deployment"></a>배포 커밋

[Deploy-AzNetworkManagerCommit을](/powershell/module/az.network/deploy-aznetworkmanagercommit)사용하여 보안 구성을 대상 지역에 커밋합니다.

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($securityconfig.id) 
[System.Collections.Generic.List[string]]regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @deployment 
```

## <a name="delete-security-configuration"></a>보안 구성 삭제

보안 구성이 더 이상 필요하지 않은 경우 보안 구성 자체를 삭제하려면 다음 조건이 충족되는지 확인해야 합니다.

* 모든 지역에 구성이 배포되지 않습니다.
* 보안 구성에 연결된 규칙 컬렉션의 모든 보안 규칙을 삭제합니다.

### <a name="remove-security-configuration-deployment"></a>보안 구성 배포 제거

[Deploy-AzNetworkManagerCommit을](/powershell/module/az.network/deploy-aznetworkmanagercommit)통해 구성을 배포하여 보안 배포를 제거합니다.

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     
$removedeployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @removedeployment
```

### <a name="remove-security-rules"></a>보안 규칙 제거

[Remove-AzNetworkManagerSecurityAdminRule 을](/powershell/module/az.network/remove-aznetworkmanagersecurityadminrule)통해 보안 규칙을 제거합니다.

```azurepowershell-interactive
$removerule = @{
    Name = 'Block80'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecurityConfig'
}
Remove-AzNetworkManagerSecurityAdminRule @removerule
```

### <a name="remove-security-rule-collections"></a>보안 규칙 컬렉션 제거

```azurepowershell-interactive
$removecollection = @{
    Name = 'myRuleCollection'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecuritConfig'
}
Remove-AzNetworkManagerSecurityAdminRuleCollection @removecollection
```

### <a name="delete-configuration"></a>구성 삭제

[Remove-AzNetworkManagerSecurityAdminConfiguration을](/powershell/module/az.network/remove-aznetworkmanagersecurityadminconfiguration)통해 보안 구성을 삭제합니다.

```azurepowershell-interactive
$removeconfig = @{
    Name = 'SecurityConfig'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
}
Remove-AzNetworkManagerSecurityAdminConfiguration @removeconfig
```

## <a name="next-steps"></a>다음 단계

[보안 관리자 규칙에](concept-security-admins.md)대해 자세히 알아보세요.
