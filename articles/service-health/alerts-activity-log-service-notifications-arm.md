---
title: Resource Manager 템플릿을 사용하여 Azure 서비스 알림에 대한 활동 로그 경고 수신
description: Azure 서비스가 발생할 때 SMS, 전자 메일 또는 웹후크를 통해 알림을 받습니다.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: d9f869567abe9fbdec0cfcac5dbf8b9acd59e105
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019422"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 서비스 알림에 대한 활동 로그 경고 만들기

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 서비스 상태 알림에 대한 활동 로그 경고를 설정하는 방법을 보여줍니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

서비스 상태 알림은 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에 저장됩니다. 활동 로그에 저장된 정보의 양이 많을 수 있으므로, 서비스 상태 알림에 대한 경고를 보다 쉽게 확인하고 설정할 수 있도록 별도의 사용자 인터페이스가 있습니다.

Azure에서 Azure 구독에 서비스 상태 알림을 전송할 때 경고를 받을 수 있습니다. 다음 항목에 따라 경고를 구성할 수 있습니다.

- 서비스 상태 알림 클래스(서비스 문제, 계획된 유지 보수, 상태 자문)
- 영향을 받는 구독
- 영향을 받는 서비스
- 영향을 받는 하위 지역

> [!NOTE]
> 서비스 상태 알림은 리소스 상태 이벤트에 대한 경고를 전송하지 않습니다.

다음과 같이 경고를 받는 사람도 구성할 수 있습니다.

- 기존 작업 그룹을 선택합니다.
- 새 작업 그룹을 만듭니다(향후 경고에 사용할 수 있음).

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 로컬 컴퓨터에서 명령을 실행하려면 Azure CLI 또는 Azure PowerShell 모듈을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

## <a name="review-the-template"></a>템플릿 검토

이메일 대상이 포함된 작업 그룹을 만들고 대상 구독에 모든 서비스 상태 알림을 사용하도록 설정하는 템플릿은 다음과 같습니다. 이 템플릿을 *CreateServiceHealthAlert.json* 으로 저장합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

템플릿은 다음 두 가지 리소스를 정의합니다.

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>템플릿 배포

CLI 및 PowerShell을 사용하는 다음 예제처럼 [ARM 템플릿 배포](../azure-resource-manager/templates/deploy-portal.md)를 위한 표준 방법을 사용하여 템플릿을 배포합니다. **리소스 그룹** 및 **emailAddress** 에 대한 샘플 값을 사용자 환경에 적합한 값으로 바꿉니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>배포 유효성 검사

다음 명령 중 하나를 사용하여 작업 영역이 생성되었는지 확인합니다. **리소스 그룹** 에 대한 샘플 값을 위에서 사용한 값으로 바꿉니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- [Azure Service Health 경고 설정 모범 사례](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)에 대해 알아봅니다.
- [Azure Service Health에 대한 모바일 푸시 알림을 설정](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)하는 방법을 알아봅니다.
- [기존 문제 관리 시스템에 대한 웹후크 알림 구성](service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.
- [서비스 상태 알림](service-notifications.md)에 대해 자세히 알아보세요.
- [알림 속도 제한](../azure-monitor/alerts/alerts-rate-limiting.md)에 대해 자세히 알아보세요.
- [활동 로그 경고 웹후크 스키마](../azure-monitor/alerts/activity-log-alerts-webhook.md)를 검토하세요.
- [활동 로그 경고의 개요](../azure-monitor/alerts/alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.
- [작업 그룹](../azure-monitor/alerts/action-groups.md)에 대해 자세히 알아보세요.
