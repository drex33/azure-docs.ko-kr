---
title: 로그에 대한 Azure AD 인증
description: Azure Monitor의 Log Analytics에 Azure AD(Azure Active Directory) 인증을 사용하도록 설정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 526312df5c1bbf5e21a4017177752fb4f476ae5c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825304"
---
# <a name="azure-ad-authentication-for-logs"></a>로그에 대한 Azure AD 인증

Azure Monitor는 가상 머신의 에이전트, Application Insights, Azure 리소스 진단 설정, 데이터 수집기 API를 포함한 [여러 원본에서 로그의 데이터를 수집](data-platform-logs.md#data-collection)할 수 있습니다.

Log Analytics 에이전트는 작업 영역 키를 등록 키로 사용하여 초기 액세스를 확인하고 에이전트와 Azure Monitor 간의 보안 연결을 설정하는 데 사용되는 인증서를 추가로 프로비저닝합니다. 자세한 내용은 [에이전트에서 데이터 보내기](data-security.md#2-send-data-from-agents)를 참조하세요. 데이터 수집기 API는 동일한 작업 영역 키를 사용하여 [액세스 권한을 부여합니다](data-collector-api.md#authorization).

자격 증명, 특히 작업 영역 키를 대규모로 관리하기가 어렵기 때문에 이러한 옵션은 번거롭고 위험할 수 있습니다. 로컬 인증을 옵트아웃하고 관리 ID 및 Azure Active Directory를 사용하여 단독 인증된 원격 분석만 Azure Monitor에 수집되게 합니다. 이 기능은 중요한 운영 결정 및 비즈니스 의사 결정 시 사용되는 원격 분석의 보안 및 안정성을 개선합니다.

다음 단계를 통해 Azure Monitor 로그를 위한 Azure Active Directory 통합을 사용하도록 설정하고 이러한 공유 비밀에 대한 의존도를 제거합니다.

1. AMA(Azure Monitor 에이전트)에는 키가 필요하지 않지만 대신 [시스템 관리 ID가 필요](../agents/azure-monitor-agent-overview.md#security)합니다. Log Analytics 에이전트에서 [Azure Monitor 에이전트로 마이그레이션](../agents/azure-monitor-agent-migration.md)합니다.
2. [Log Analytics 작업 영역에 대해 로컬 인증을 사용하지 않도록 설정](#disable-local-authentication-for-log-analytics)합니다.
3. 인증된 원격 분석만 [Application Insights에 대한 Azure AD 인증(미리 보기)](../app/azure-ad-authentication.md)을 통해 Application Insights 리소스에서 수집되는지 확인합니다.

## <a name="disable-local-authentication-for-log-analytics"></a>Log Analytics에 대한 로컬 인증 사용 안 함

Log Analytics 에이전트에 대한 의존도를 제거한 후 Log Analytics 작업 영역을 위한 로컬 인증을 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 Azure AD에서만 인증된 원격 분석을 수집하고 쿼리할 수 있습니다.

로컬 인증을 사용하지 않도록 지정하면 사용 가능한 일부 기능이 제한될 수 있는데 구체적으로는 다음과 같습니다.

- 기존 Log Analytics 에이전트는 작동을 중지하고 AMA(Azure Monitor 에이전트)만 지원됩니다. Azure Monitor 에이전트에는 Log Analytics 에이전트(예: 사용자 지정 로그 수집, IIS 로그 수집)를 통해 사용할 수 있는 일부 기능이 없습니다.
- 데이터 수집기 API(미리 보기)는 Azure AD 인증을 지원하지 않으며 데이터를 수집할 수 없습니다.

Azure Policy를 사용하거나 Azure Resource Manager 템플릿, PowerShell 또는 CLI를 통해 프로그래밍 방식으로 로컬 인증을 사용하지 않도록 설정할 수 있습니다.

### <a name="azure-policy"></a>Azure Policy

‘DisableLocalAuth’에 대한 Azure Policy는 사용자가 속성을 ‘true’로 설정하지 않고 새 Log Analytics 작업 영역을 만드는 것을 허용하지 않습니다. 정책 이름은 `Log Analytics Workspaces should block non-Azure Active Directory based ingestion`입니다. 구독에 이 정책 정의를 적용하려면 [새 정책 할당을 만들고 정책을 할당](../../governance/policy/assign-policy-portal.md)합니다. 

다음은 정책 템플릿 정의입니다.

```json
{
  "properties": {
    "displayName": "Log Analytics Workspaces should block non-Azure Active Directory based ingestion.",
    "policyType": "BuiltIn",
    "mode": "Indexed",
    "description": "Enforcing log ingestion to require Azure Active Directory authentication prevents unauthenticated logs from an attacker which could lead to incorrect status, false alerts, and incorrect logs stored in the system.",
    "metadata": {
      "version": "1.0.0",
      "category": "Monitoring"
    },
    "parameters": {
      "effect": {
        "type": "String",
        "metadata": {
          "displayName": "Effect",
          "description": "Enable or disable the execution of the policy"
        },
        "allowedValues": [
          "Deny",
          "Audit",
          "Disabled"
        ],
        "defaultValue": "Audit"
      }
    },
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.OperationalInsights/workspaces"
          },
          {
            "field": "Microsoft.OperationalInsights/workspaces/features.disableLocalAuth",
            "notEquals": "true"
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
  },
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e15effd4-2278-4c65-a0da-4d6f6d1890e2",
  "type": "Microsoft.Authorization/policyDefinitions",
  "name": "e15effd4-2278-4c65-a0da-4d6f6d1890e2"
}
```

### <a name="azure-resource-manager"></a>Azure Resource Manager

`DisableLocalAuth` 속성은 Log Analytics 작업 영역에서 로컬 인증을 사용하지 않도록 설정하는 데 사용됩니다. 이 속성은 true로 설정하면 모든 액세스에 Azure AD 인증을 사용해야 합니다. 

다음은 로컬 인증을 사용하지 않도록 설정하는 데 사용할 수 있는 Azure Resource Manager 템플릿의 예입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaces_name": {
            "defaultValue": "workspace-name",
            "type": "string"
        },
        "workspace_location": {
          "defaultValue": "region-name",
          "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaces_name')]",
            "location": "[parameters('workspace_location')]",
            "properties": {
                 "sku": {
                    "name": "PerGB2018"
                },
                "retentionInDays": 30,
                "features": {
                    "disableLocalAuth": false,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
}

```


### <a name="cli"></a>CLI

`DisableLocalAuth` 속성은 Log Analytics 작업 영역에서 로컬 인증을 사용하지 않도록 설정하는 데 사용됩니다. 이 속성은 true로 설정하면 모든 액세스에 Azure AD 인증을 사용해야 합니다. 

다음은 로컬 인증을 사용하지 않도록 설정하는 데 사용할 수 있는 CLI 명령의 예입니다.

```azurecli
    az resource update --ids "/subscriptions/[Your subscription ID]/resourcegroups/[Your resource group]/providers/microsoft.operationalinsights/workspaces/[Your workspace name]--api-version "2021-06-01" --set properties.features.disableLocalAuth=True
```

### <a name="powershell"></a>PowerShell

`DisableLocalAuth` 속성은 Log Analytics 작업 영역에서 로컬 인증을 사용하지 않도록 설정하는 데 사용됩니다. 이 속성은 true로 설정하면 모든 액세스에 Azure AD 인증을 사용해야 합니다. 

다음은 로컬 인증을 사용하지 않도록 설정하는 데 사용할 수 있는 PowerShell 명령의 예입니다.

```powershell
    $workspaceSubscriptionId = "[You subscription ID]"
    $workspaceResourceGroup = "[You resource group]"
    $workspaceName = "[Your workspace name]"
    $disableLocalAuth = $false
    
    # login
    Connect-AzAccount
    
    # select subscription
    Select-AzSubscription -SubscriptionId $workspaceSubscriptionId
    
    # get private link workspace resource
    $workspace = Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ResourceGroupName $workspaceResourceGroup -ResourceName $workspaceName -ApiVersion "2021-06-01"
    
    # set DisableLocalAuth
    $workspace.Properties.Features | Add-Member -MemberType NoteProperty -Name DisableLocalAuth -Value $disableLocalAuth -Force
    $workspace | Set-AzResource -Force
```

## <a name="next-steps"></a>다음 단계
* [Application Insights에 대한 Azure AD 인증(미리 보기)](../app/azure-ad-authentication.md)