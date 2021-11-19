---
title: Azure Virtual Network Manager (미리 보기)를 사용 하 여 네트워크 트래픽을 차단 하는 방법-Azure PowerShell
description: Azure PowerShell에서 Azure Virtual Network Manager의 보안 규칙을 사용 하 여 네트워크 트래픽을 차단 하는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ccd3df8ef4e13822bd7fe7068634f9acdaa86122
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808302"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-powershell"></a>Azure Virtual Network Manager (미리 보기)를 사용 하 여 네트워크 트래픽을 차단 하는 방법-Azure PowerShell

이 문서에서는 규칙 컬렉션에 추가할 수 있는 포트 80 및 443에 대 한 아웃 바운드 네트워크 트래픽을 차단 하는 보안 규칙을 만드는 방법을 보여 줍니다. 자세한 내용은 [보안 관리 규칙](concept-security-admins.md)을 참조 하세요.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

보안 규칙 구성을 시작 하기 전에 다음 단계를 확인 합니다.

* [보안 관리 규칙](concept-security-admins.md)의 각 요소를 이해 합니다.
* [Azure Virtual Network Manager 인스턴스](create-virtual-network-manager-powershell.md)를 만들었습니다.

## <a name="create-a-securityadmin-configuration"></a>SecurityAdmin 구성 만들기

1. AzNetworkManagerSecurityAdminConfiguration를 사용 하 여 새 SecurityAdmin 구성을 만듭니다.

    ```azurepowershell-interactive
    $config = @{
        Name = 'SecurityConfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $securityconfig = New-AzNetworkManagerSecurityAdminConfiguration @config 
    ```

1. AzNetworkManagerGroup를 사용 하 여 변수에 네트워크 그룹을 저장 합니다.

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResoureceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $networkgroup = Get-AzNetworkManagerGroup @ng   
    ```

1. AzNetworkManagerSecurityGroupItem를 사용 하 여에 네트워크 그룹을 추가할 연결 그룹 항목을 만듭니다.

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

1. AzNetworkManagerSecurityAdminRuleCollection를 사용 하 여 보안 관리 규칙 컬렉션을 만듭니다.

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

1. AzNetworkManagerAddressPrefixItem를 사용 하 여 원본 및 대상 주소 접두사 및 포트에 대 한 변수를 정의 합니다.

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

1. AzNetworkManagerSecurityAdminRule를 사용 하 여 보안 규칙을 만듭니다.

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

AzNetworkManagerCommit를 사용 하 여 대상 지역에 보안 구성을 커밋합니다.

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

보안 구성이 더 이상 필요 하지 않은 경우 보안 구성 자체를 삭제 하기 전에 다음 조건이 충족 되는지 확인 해야 합니다.

* 어떤 지역에도 배포된 구성이 없습니다.
* 보안 구성에 연결 된 규칙 컬렉션의 모든 보안 규칙을 삭제 합니다.

### <a name="remove-security-configuration-deployment"></a>보안 구성 배포 제거

AzNetworkManagerCommit를 사용 하 여 구성을 배포 하 여 보안 배포를 제거 합니다.

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

AzNetworkManagerSecurityAdminRule를 사용 하 여 보안 규칙을 제거 합니다.

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

AzNetworkManagerSecurityAdminConfiguration를 사용 하 여 보안 구성을 삭제 합니다.

```azurepowershell-interactive
$removeconfig = @{
    Name = 'SecurityConfig'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
}
Remove-AzNetworkManagerSecurityAdminConfiguration @removeconfig
```

## <a name="next-steps"></a>다음 단계

[보안 관리 규칙](concept-security-admins.md)에 대해 자세히 알아보세요.
