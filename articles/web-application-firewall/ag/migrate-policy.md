---
title: Azure Application Gateway에 대한 WAF 정책 마이그레이션
description: Azure PowerShell을 사용하여 Azure Web Application Firewall 정책을 마이그레이션하는 방법을 알아봅니다.
services: web-application-firewall
ms.topic: conceptual
author: KumudD
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 162a6dbaa7430ddec38531d92590d22ea4e8d2fa
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549739"
---
# <a name="migrate-web-application-firewall-policies-using-azure-powershell"></a>Azure PowerShell을 사용하여 Web Application Firewall 정책 마이그레이션

이 스크립트를 사용하면 WAF 구성 또는 사용자 지정 규칙 전용 WAF 정책에서 전체 WAF 정책으로 쉽게 전환할 수 있습니다. 포털에 *WAF 정책으로 마이그레이션* 이라는 경고가 표시되거나, 또는 Geomatch 사용자 지정 규칙, 사이트별 WAF 정책, URI별 WAF 정책(미리 보기) 또는 봇 완화 규칙 집합과 같은 새로운 WAF 기능이 필요할 수 있습니다. 이러한 기능을 사용하려면 애플리케이션 게이트웨이에 연결된 전체 WAF 정책이 필요합니다. 

새 WAF 정책을 만드는 방법에 대한 자세한 내용은 [Application Gateway에 대한 Web Application Firewall 정책 만들기](create-waf-policy-ag.md)를 참조하세요. 마이그레이션에 대한 자세한 내용은 [WAF 정책으로 마이그레이션](create-waf-policy-ag.md#migrate-to-waf-policy)을 참조하세요.

## <a name="to-migrate-to-waf-policy-using-the-migration-script"></a>마이그레이션 스크립트를 사용하여 WAF 정책으로 마이그레이션하려면

다음 단계를 사용하여 마이그레이션 스크립트를 실행합니다. 

1. 다음 Cloud Shell 창을 열거나 포털 내에서 하나를 엽니다.
2. Cloud Shell 창에 스크립트를 복사하여 실행합니다.
3. 이 스크립트는 구독 ID, 리소스 그룹 이름, WAF 구성이 연결된 Application Gateway의 이름 및 만들 새 WAF 정책의 이름을 묻는 메시지를 표시합니다. 이러한 항목을 입력하면 스크립트가 실행되고 새 WAF 정책을 만듭니다.
4. 새 WAF 정책을 애플리케이션 게이트웨이와 연결합니다. 포털의 WAF 정책으로 이동하고 **연결된 Application Gateway** 탭을 선택합니다. **Application Gateway를 연결** 을 선택한 다음 WAF 정책을 연결할 Application Gateway를 선택합니다.

> [!NOTE]
> 다음 조건에 해당하는 경우 스크립트는 마이그레이션을 완료하지 않습니다.
> - 전체 규칙을 사용할 수 없습니다. 마이그레이션을 완료하려면 전체 rulegroup이 비활성화되지 않았음을 확인합니다.
> - *Equals any* 연산자가 있는 제외 항목. 마이그레이션을 완료하려면 *Equals Any* 연산자가 있는 제외 항목이 없는지 확인합니다.
>
> 자세한 내용은 스크립트의 *Validateinput* 함수를 참조하세요.

```azurepowershell-interactive
<#PSScriptInfo
.DESCRIPTION
Will be used to migrate to the application-gateway to a top level waf policy experience.

.VERSION 1.0

.GUID b6fedd43-ebd0-41ed-9847-4f1c1c43be22

.AUTHOR Venkat.Krishnan

.PARAMETER subscriptionId 
Subscription Id of where the resources are present.
.PARAMETER resourceGroupName
Resource-group where the resources are present.
.PARAMETER applicationGatewayName
Application-Gateway name
.PARAMETER wafPolicyName
Name of the web application firewall policy

.EXAMPLE
./migrateToWafPolicy.ps1 -subscriptionId  <your-subscription-id> -applicationGatewayName <your-appgw-name> -resourceGroupName <your-resource-group-name> -wafPolicyName <new-waf-policy-name>
#>

param(
    [Parameter(Mandatory=$true)]
    [string] $subscriptionId,
    [Parameter(Mandatory=$true)]
    [string] $resourceGroupName,
    [Parameter(Mandatory=$true)]
    [string] $applicationGatewayName,
    [Parameter(Mandatory=$true)]
    [string] $wafPolicyName
)

function ValidateInput ($appgwName, $resourceGroupName) {
    # Obtain the application-gateway
    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName
    if (-not $appgw) {
        Write-Error "ApplicationGateway: $applicationGatewayName is not present in ResourceGroup: $resourceGroupName"
        return $false
    }

    # Check if already have a global firewall policy
    if ($appgw.FirewallPolicy) {
        $fp = Get-AzResource -ResourceId $appgw.FirewallPolicy.Id
        if ($fp.PolicySettings) {
            Write-Error "ApplicationGateway: $applicationGatewayName already has a global firewall policy: $fp.Name. Please use portal for changing the policy."
            return $false
        }
    }

    if ($appgw.WebApplicationFirewallConfiguration) {
        # Throw an error, since ruleGroup disabled case can't be migrated now.
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                if ($disabled.Rules.Count -eq 0) {
                    $ruleGroupName = $disabled.RuleGroupName
                    Write-Error "The ruleGroup '$ruleGroupName' is disabled. Currently we can't migrate to a firewall policy when an entire ruleGroup is disabled. This feature will be delivered shortly. To continue, kindly ensure the entire rulegroups are not disabled. "
                    return $false
                }
            }
        }

        # Throw an error when exclusion entry with 'EqualsAny' operator is present
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($null -ne $excl.MatchVariable -and $null -eq $excl.SelectorMatchOperator -and $null -eq $excl.Selector) {
                    Write-Error " You have an exclusion entry(s) with the 'Equals any' operator. Currently we can't migrate to a firewall policy with 'Equals Any' operator. This feature will be delivered shortly. To continue, kindly ensure exclusion entries with 'Equals Any' operator is not present. "
                    return $false
                }
            }
        }
    }

    if ($appgw.Sku.Name -ne "WAF_v2" -or $appgw.Sku.Tier -ne "WAF_v2") {
        Write-Error " Cannot associate a firewall policy to application gateway :$applicationGatewayName since the Sku is not on WAF_v2"
        return $false
    }

    return $true
}

function Login() {
    $context = Get-AzContext
    if ($null -eq $context -or $null -eq $context.Account) {
        Login-AzAccount
    }
}

function createNewTopLevelWafPolicy ($subscriptionId, $resourceGroupName, $applicationGatewayName, $wafPolicyName) {
    Select-AzSubscription -Subscription $subscriptionId
    $retVal = ValidateInput -appgwName $applicationGatewayName -resourceGroupName $resourceGroupName
    if (!$retVal) {
        return
    }

    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName

    # Get the managedRule and PolicySettings
    $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule
    $policySetting = New-AzApplicationGatewayFirewallPolicySetting
    if ($appgw.WebApplicationFirewallConfiguration) {
        $ruleGroupOverrides = [System.Collections.ArrayList]@()
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                $rules = [System.Collections.ArrayList]@()
                if ($disabled.Rules.Count -gt 0) {
                    foreach ($rule in $disabled.Rules) {
                        $ruleOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleOverride -RuleId $rule
                        $_ = $rules.Add($ruleOverride)
                    }
                }
                
                $ruleGroupOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleGroupOverride -RuleGroupName $disabled.RuleGroupName -Rule $rules
                $_ = $ruleGroupOverrides.Add($ruleGroupOverride)
            }
        }

        $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion 
        if ($ruleGroupOverrides.Count -ne 0) {
            $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion -RuleGroupOverride $ruleGroupOverrides
        }
    
        $exclusions = [System.Collections.ArrayList]@()  
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($excl.MatchVariable -and $excl.SelectorMatchOperator -and $excl.Selector) {
                    $exclusionEntry = New-AzApplicationGatewayFirewallPolicyExclusion -MatchVariable  $excl.MatchVariable -SelectorMatchOperator $excl.SelectorMatchOperator -Selector $excl.Selector
                    $_ = $exclusions.Add($exclusionEntry)
                }
            }
        }
    
        $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet
        $exclCount = $exclusions.Count
        if ($exclCount -ne 0) {
            $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet -Exclusion $exclusions
        }

        
        $policySetting = New-AzApplicationGatewayFirewallPolicySetting -MaxFileUploadInMb $appgw.WebApplicationFirewallConfiguration.FileUploadLimitInMb -MaxRequestBodySizeInKb $appgw.WebApplicationFirewallConfiguration.MaxRequestBodySizeInKb -Mode Detection -State Disabled
        if ($appgw.WebApplicationFirewallConfiguration.FirewallMode -eq "Prevention") {
            $policySetting.Mode = "Prevention"
        }

        if ($appgw.WebApplicationFirewallConfiguration.Enabled) {
            $policySetting.State = "Enabled"
        }

        $policySetting.RequestBodyCheck = $appgw.WebApplicationFirewallConfiguration.RequestBodyCheck;
    }

    if ($appgw.FirewallPolicy) {
        $customRulePolicyId = $appgw.FirewallPolicy.Id
        $rg = Get-AzResourceGroup -Id $customRulePolicyId
        $crPolicyName = $customRulePolicyId.Substring($customRulePolicyId.LastIndexOf("/") + 1)
        $customRulePolicy = Get-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $crPolicyName
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $wafPolicyName -CustomRule $customRulePolicy.CustomRules -ManagedRule $managedRule -PolicySetting $policySetting -Location $appgw.Location
    } else { 
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name $wafPolicyName -ResourceGroupName $resourceGroupName -PolicySetting $policySetting -ManagedRule $managedRule -Location $appgw.Location
    }

    if (!$wafPolicy) {
        return
    }

    $appgw.FirewallPolicy = $wafPolicy
    $appgw = Set-AzApplicationGateway -ApplicationGateway $appgw
    Write-Host " firewallPolicy: $wafPolicyName has been created/updated successfully and applied to applicationGateway: $applicationGatewayName!"
    return $wafPolicy
}

function Main() {
    Login
    $policy = createNewTopLevelWafPolicy -subscriptionId $subscriptionId -resourceGroupName $resourceGroupName -applicationGatewayName $applicationGatewayName -wafPolicyName $wafPolicyName
    return $policy
}

Main
```
## <a name="next-steps"></a>다음 단계

[Web Application Firewall CRS 규칙 그룹 및 규칙](application-gateway-crs-rulegroups-rules.md)에 대해 자세히 알아보세요.
