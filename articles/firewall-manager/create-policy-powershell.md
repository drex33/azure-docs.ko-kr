---
title: '빠른 시작: Azure PowerShell 사용하여 Azure Firewall 정책 만들기 및 업데이트'
description: 이 빠른 시작에서는 Azure PowerShell 사용하여 Azure Firewall 정책을 만들고 업데이트하는 방법을 알아봅니다.
services: firewall-manager
author: vaboya
ms.author: victorh
ms.date: 08/16/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.openlocfilehash: 3e7eef6eed76f2b6e3e122c1a373580e05556f03
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254381"
---
# <a name="quickstart-create-and-update-an-azure-firewall-policy-using-azure-powershell"></a>빠른 시작: Azure PowerShell 사용하여 Azure Firewall 정책 만들기 및 업데이트

이 빠른 시작에서는 Azure PowerShell을 사용하여 네트워크 및 애플리케이션 규칙으로 Azure Firewall 정책을 만듭니다. 또한 네트워크 및 애플리케이션 규칙을 추가하여 기존 정책을 업데이트합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="set-up-the-network-and-policy"></a>네트워크 및 정책 설정

먼저 리소스 그룹과 가상 네트워크를 만듭니다. 그런 다음, Azure Firewall 정책을 만듭니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹에는 이 절차에 사용된 모든 리소스가 포함되어 있습니다.

```azurepowershell
New-AzResourceGroup -Name Test-FWpolicy-RG -Location "East US"
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

```azurepowershell
$ServerSubnet = New-AzVirtualNetworkSubnetConfig -Name subnet-1 -AddressPrefix 10.0.0.0/24
$testVnet = New-AzVirtualNetwork -Name Test-FWPolicy-VNET -ResourceGroupName Test-FWPolicy-RG -Location "East US" -AddressPrefix 10.0.0.0/8 -Subnet $ServerSubnet
```
### <a name="create-a-firewall-policy"></a>방화벽 정책 만들기

```azurepowershell
New-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG -Location "EAST US"
```

## <a name="create-a-network-rule-collection-group-and-add-new-rules"></a>네트워크 규칙 컬렉션 그룹 만들기 및 새 규칙 추가

먼저 규칙 컬렉션 그룹을 만든 다음, 규칙을 통해 규칙 컬렉션을 추가합니다.

### <a name="create-the-network-rule-collection-group"></a>네트워크 규칙 컬렉션 그룹 만들기

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newnetworkrulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "NetworkRuleCollectionGroup" -Priority 200 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
```
### <a name="create-network-rules"></a>네트워크 규칙 만들기

```azurepowershell
$networkrule1= New-AzFirewallPolicyNetworkRule -Name NwRule1 -Description testRule1  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.1/32 -DestinationPort 22 
$networkrule2= New-AzFirewallPolicyNetworkRule -Name NWRule2 -Description TestRule2  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.10/32 -DestinationPort 1434
```
### <a name="create-a-network-rule-collection-and-add-new-rules"></a>네트워크 규칙 컬렉션 만들기 및 새 규칙 추가

```azurepowershell
$newrulecollectionconfig=New-AzFirewallPolicyFilterRuleCollection -Name myfirstrulecollection -Priority 1000 -Rule $networkrule1,$networkrule2 -ActionType Allow
$newrulecollection = $networkrulecollectiongroup.Properties.RuleCollection.Add($newrulecollectionconfig)
```

### <a name="update-the-network-rule-collection-group"></a>네트워크 규칙 컬렉션 탭 업데이트

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -Priority "200" -FirewallPolicyObject $firewallpolicy -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```

### <a name="output"></a>출력

새 규칙 컬렉션 및 해당 규칙 보기:

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-Output  $output
```

## <a name="add-network-rules-to-an-existing-rule-collection"></a>기존 규칙 컬렉션에 네트워크 규칙 추가

기존 규칙 컬렉션이 생겼으므로 규칙을 더 추가할 수 있습니다.

### <a name="get-existing-network-rule-group-collection"></a>기존 네트워크 규칙 그룹 컬렉션 얻기

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy 
```

### <a name="create-new-network-rules"></a>새 네트워크 규칙 만들기

```azurepowershell
$newnetworkrule1 = New-AzFirewallPolicyNetworkRule -Name newNwRule01 -Description testRule01  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.5/32 -DestinationPort 3389
$newnetworkrule2 = New-AzFirewallPolicyNetworkRule -Name newNWRule02 -Description TestRule02  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.15/32 -DestinationPort 1434
```
### <a name="update-the-network-rule-collection-and-add-new-rules"></a>네트워크 규칙 컬렉션 업데이트 및 새 규칙 추가

```azurepowershell
$getexistingrullecollection = $networkrulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myfirstrulecollection"}
$getexistingrullecollection.RuleS.Add($newnetworkrule1)
$getexistingrullecollection.RuleS.Add($newnetworkrule2)
```
### <a name="update-network-rule-collection-group"></a>네트워크 규칙 컬렉션 그룹 업데이트

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 200 -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>출력

방금 추가한 규칙 확인:

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-output $output
```
## <a name="create-an-application-rule-collection-and-add-new-rules"></a>애플리케이션 규칙 컬렉션 만들기 및 새 규칙 추가

먼저 규칙 컬렉션 그룹을 만든 다음, 규칙을 통해 규칙 컬렉션을 추가합니다.

### <a name="create-the-application-rule-collection-group"></a>애플리케이션 규칙 컬렉션 만들기

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newapprulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "ApplicationRuleCollectionGroup" -Priority 300 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
```
### <a name="create-new-application-rules"></a>새 애플리케이션 규칙 만들기

```azurepowershell
$apprule1 = New-AzFirewallPolicyApplicationRule -Name apprule1 -Description testapprule1 -SourceAddress 192.168.0.1/32 -TargetFqdn "*.contoso.com" -Protocol HTTPS
$apprule2 = New-AzFirewallPolicyApplicationRule -Name apprule2 -Description testapprule2  -SourceAddress 192.168.0.10/32 -TargetFqdn "www.contosoweb.com" -Protocol HTTPS
```
### <a name="create-a-new-application-rule-collection-with-rules"></a>규칙으로 새 애플리케이션 규칙 컬렉션 만들기

```azurepowershell
$apprulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
$apprulecollection = New-AzFirewallPolicyFilterRuleCollection -Name myapprulecollection -Priority 1000 -Rule $apprule1,$apprule2 -ActionType Allow 
$newapprulecollection = $apprulecollectiongroup.Properties.RuleCollection.Add($apprulecollection) 
```

### <a name="update-the-application-rule-collection-group"></a>애플리케이션 규칙 컬렉션 업데이트

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection 
```

### <a name="output"></a>출력

새 규칙 컬렉션 그룹 및 해당 새 규칙 검사:

```azurepowershell
$output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="add-application-rules-to-an-existing-rule-collection"></a>기존 규칙 컬렉션에 애플리케이션 규칙 추가

기존 규칙 컬렉션이 생겼으므로 규칙을 더 추가할 수 있습니다.

```azurepowershell 
#Create new Application Rules for exist Rule collection
$newapprule1 = New-AzFirewallPolicyApplicationRule -Name newapprule01 -Description testapprule01 -SourceAddress 192.168.0.5/32 -TargetFqdn "*.contosoabc.com" -Protocol HTTPS
$newapprule2 = New-AzFirewallPolicyApplicationRule -Name newapprule02 -Description testapprule02  -SourceAddress 192.168.0.15/32 -TargetFqdn "www.contosowebabcd.com" -Protocol HTTPS
```

### <a name="update-the-application-rule-collection"></a>애플리케이션 규칙 컬렉션 업데이트

```azurepowershell
$apprulecollection = $apprulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myapprulecollection"}
$apprulecollection.Rules.Add($newapprule1)
$apprulecollection.Rules.Add($newapprule2)

# Update Application Rule collection Group  
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>출력

새 규칙 보기:

```azurepowershell
$Output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="clean-up-resources"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않은 경우 해당 리소스 그룹을 삭제합니다. 그러면 만든 모든 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 사용합니다.

```azurepowershell
Remove-AzResourceGroup -Name Test-FWpolicy-RG
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Firewall Manager 배포에 대한 자세한 정보](deployment-overview.md)