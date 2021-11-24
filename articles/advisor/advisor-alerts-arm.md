---
title: Resource Manager 템플릿을 사용하여 새 추천에 대한 Azure Advisor 경고 만들기
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Advisor의 새 권장 사항에 대한 경고를 설정하는 방법을 알아봅니다.
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell, mode-arm
ms.date: 06/29/2020
ms.openlocfilehash: 3251fada0281f3aff72db42fa818cdbce5010af4
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076546"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 새 추천에 대한 Azure Advisor 경고 만들기

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Advisor의 새 추천에 대한 경고를 설정하는 방법을 보여 줍니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Advisor에서 리소스 중 하나에 대한 새 추천을 검색할 때마다 이벤트가 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에 저장됩니다. 추천별 경고 만들기 환경을 사용하여 Azure Advisor에서 이러한 이벤트에 대한 경고를 설정할 수 있습니다. 구독을 선택하고 필요에 따라 리소스 그룹을 선택하여 경고를 받도록 하려는 리소스를 지정할 수 있습니다.

다음 속성을 사용하여 추천 유형을 확인할 수도 있습니다.

- 범주
- 영향 수준
- 추천 유형

다음과 같은 방법으로 경고가 트리거될 때 수행되는 작업을 구성할 수도 있습니다.

- 기존 작업 그룹 선택
- 새 작업 그룹 만들기

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md)를 참조하세요.

> [!NOTE]
> Advisor 경고는 현재 고가용성, 성능 및 비용 추천에만 사용할 수 있습니다. 보안 추천은 지원되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 로컬 컴퓨터에서 명령을 실행하려면 Azure CLI 또는 Azure PowerShell 모듈을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

## <a name="review-the-template"></a>템플릿 검토

이메일 대상이 포함된 작업 그룹을 만들고 대상 구독에 모든 서비스 상태 알림을 사용하도록 설정하는 템플릿은 다음과 같습니다. 이 템플릿을 *CreateAdvisorAlert.json* 으로 저장합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

템플릿은 다음 두 가지 리소스를 정의합니다.

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>템플릿 배포

CLI 및 PowerShell을 사용하는 다음 예제처럼 [ARM 템플릿 배포](../azure-resource-manager/templates/deploy-portal.md)를 위한 표준 방법을 사용하여 템플릿을 배포합니다. **리소스 그룹** 및 **emailAddress** 에 대한 샘플 값을 사용자 환경에 적합한 값으로 바꿉니다. 작업 영역 이름은 모든 Azure 구독에서 고유해야 합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>배포 유효성 검사

다음 명령 중 하나를 사용하여 작업 영역이 생성되었는지 확인합니다. **리소스 그룹** 에 대한 샘플 값을 위에서 사용한 값으로 바꿉니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다. 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 경고 규칙과 관련 리소스가 삭제됩니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>다음 단계

- [활동 로그 경고의 개요](../azure-monitor/alerts/alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.
- [작업 그룹](../azure-monitor/alerts/action-groups.md)에 대해 자세히 알아보세요.
