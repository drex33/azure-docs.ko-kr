---
title: Azure Firewall 프리미엄으로 마이그레이션
description: Azure Firewall 표준에서 Azure Firewall 프리미엄으로 마이그레이션하는 방법을 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 11/02/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 574c1f7f84ae34b1a1158b61206135de79ee73af
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548067"
---
# <a name="migrate-to-azure-firewall-premium"></a>Azure Firewall 프리미엄으로 마이그레이션

Azure Firewall 표준에서 Azure Firewall 프리미엄으로 마이그레이션하면 새 프리미엄 기능을 활용할 수 있습니다. Azure Firewall 프리미엄 기능에 대한 자세한 내용은 [Azure Firewall 프리미엄 기능](premium-features.md)을 참조하세요.

다음 두 예제에서는 다음을 수행하는 방법을 보여 줍니다.
- Azure PowerShell을 사용하여 기존 표준 정책을 마이그레이션합니다.
- 기존 표준 방화벽(클래식 규칙 포함)을 프리미엄 정책이 있는 Azure Firewall 프리미엄으로 마이그레이션합니다.

Terraform을 사용하여 Azure Firewall 배포하는 경우 Terraform을 사용하여 Azure Firewall Premium 마이그레이션할 수 있습니다. 자세한 내용은 [Terraform을 사용하여 Premium Azure Firewall Standard 마이그레이션을 참조하세요.](/azure/developer/terraform/firewall-upgrade-premium?toc=/azure/firewall/toc.json&bc=/azure/firewall/breadcrumb/toc.json)

## <a name="performance-considerations"></a>성능 고려 사항

성능은 표준 SKU에서 마이그레이션할 때 고려해야 할 사항입니다. IDPS 및 TLS 검사는 컴퓨팅 집약적 작업입니다. 프리미엄 SKU는 표준 SKU와 필적할만한 최대 처리량 30Gbps로 스케일링되는 보다 강력한 VM SKU를 사용합니다. 경고 모드에서 IDPS로 구성된 경우 30Gbps 처리량이 지원됩니다. 거부 모드의 IDPS와 TLS 검사를 사용하면 CPU 사용량이 증가합니다. 최대 처리량이 저하될 수 있습니다. 

하나 이상의 서명이 **경고 및 거부** 로 설정되어 있거나 **TLS 검사** 를 사용하도록 설정한 애플리케이션 규칙이 있는 경우 방화벽 처리량이 30Gbps보다 낮을 수 있습니다. 고객은 Azure 배포에서 전체 스케일 테스트를 수행하여 방화벽 서비스 성능이 사용자의 기대를 충족하는지 확인하는 것이 좋습니다.

## <a name="downtime"></a>가동 중지 시간

마이그레이션하는 동안 가동 중지 시간이 발생하기 때문에 계획된 유지 관리 시간 동안 방화벽을 마이그레이션합니다.

## <a name="migrate-classic-rules-to-standard-policy"></a>클래식 규칙을 표준 정책으로 마이그레이션

마이그레이션 프로세스 중에 클래식 방화벽 규칙을 표준 정책으로 마이그레이션해야 할 수 있습니다. Azure Portal 사용하여 이 작업을 수행할 수 있습니다.

1. Azure Portal에서 표준 방화벽을 선택합니다. **개요** 페이지에서 **방화벽 정책으로 마이그레이션** 을 선택합니다.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="방화벽 정책으로 마이그레이션":::

1. **방화벽 정책으로 마이그레이션** 페이지에서 **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

   배포를 완료하는 데 몇 분이 걸립니다.

Azure PowerShell 사용하여 기존 클래식 규칙을 Azure Firewall 마이그레이션하여 정책을 만들 수도 있습니다. 자세한 내용은 [PowerShell을 사용하여 Azure Firewall 구성을 Azure Firewall 정책 마이그레이션을 참조하세요.](../firewall-manager/migrate-to-policy.md)

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Azure PowerShell을 사용하여 기존 정책 마이그레이션

`Transform-Policy.ps1`은 기존 표준 정책에서 새 프리미엄 정책을 만드는 Azure PowerShell 스크립트입니다.

표준 방화벽 정책 ID가 있는 경우 이 스크립트는 이를 프리미엄 Azure Firewall 정책으로 변환합니다. 이 스크립트는 먼저 Azure 계정에 연결하고, 정책을 끌어오고, 다양한 매개 변수를 변환/추가하고, 새 프리미엄 정책을 업로드합니다. 새 프리미엄 정책의 이름은 `<previous_policy_name>_premium`입니다. 자식 정책 변환의 경우 부모 정책에 대한 링크가 유지됩니다.

사용법 예제:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> 이 스크립트는 위협 인텔리전스 설정을 마이그레이션하지 않습니다. 계속하기 전에 해당 설정을 적어 두고 수동으로 마이그레이션해야 합니다.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy.Id 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -MinimumVersion 4.5 -ErrorAction SilentlyContinue
    if ($null -eq $networkModule) {
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    $resourceModule = Get-InstalledModule -Name "Az.Resources" -MinimumVersion 4.2 -ErrorAction SilentlyContinue
    if ($null -eq $resourceModule) {
        Write-Host "Please install Az.Resources module version 4.2.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
    Import-Module Az.Resources -MinimumVersion 4.2.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy

```

## <a name="migrate-azure-firewall-using-stopstart"></a>중지/시작을 사용하여 Azure Firewall 마이그레이션

방화벽 정책과 함께 Azure Firewall 표준 SKU를 사용하는 경우 Allocate/Deallocate 메서드를 사용하여 방화벽 SKU를 Premium 마이그레이션할 수 있습니다. 이 마이그레이션 방법은 VNet Hub 및 보안 허브 방화벽 모두에서 지원됩니다. 보안 허브 배포를 마이그레이션하면 방화벽 공용 IP 주소가 유지됩니다.

최소 Azure PowerShell 버전 요구 사항은 6.5.0입니다. 자세한 내용은 [Az 6.5.0을 참조하세요.](https://www.powershellgallery.com/packages/Az/6.5.0)

 
### <a name="migrate-a-vnet-hub-firewall"></a>VNET Hub 방화벽 마이그레이션

- 표준 방화벽 할당을 해결합니다. 

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Deallocate()
   Set-AzFirewall -AzureFirewall $azfw
   ```


- 방화벽 Premium 할당

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Sku.Tier="Premium"
   $azfw.Allocate($vnet,$pip, $mgmtpip)
   Set-AzFirewall -AzureFirewall $azfw
   ```

### <a name="migrate-a-secure-hub-firewall"></a>보안 허브 방화벽 마이그레이션


- 표준 방화벽 할당을 해결합니다.

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Deallocate()
   Set-AzFirewall -AzureFirewall $azfw
   ```

- 방화벽 Premium 할당

   ```azurepowershell
   $azfw = Get-AzFirewall -Name -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Sku.Tier="Premium"
   $azfw.Allocate($hub.id)
   Set-AzFirewall -AzureFirewall $azfw
   ```

## <a name="next-steps"></a>다음 단계

- [Azure Firewall 프리미엄 기능](premium-features.md)에 대해 자세히 알아보기
