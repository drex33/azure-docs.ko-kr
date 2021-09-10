---
title: Resource Health 경고를 만들기 위한 템플릿
description: Azure 리소스를 사용할 수 없게 되면 알려주는 경고를 프로그래밍 방식으로 작성합니다.
ms.topic: conceptual
ms.date: 9/4/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6543e376f1cfa3ed2592972b997895c8bae68f01
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113643580"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 리소스 상태 경고 구성

이 문서에서는 Azure Resource Manager 템플릿과 Azure PowerShell을 사용하여 프로그래밍 방식으로 Resource Health 활동 로그 경고를 만드는 방법을 설명합니다.

Azure Resource Health는 Azure 리소스의 현재 및 과거 상태에 대한 정보를 알려줍니다. Azure Resource Health 경고는 이러한 리소스의 상태가 변경되면 거의 실시간으로 알려줍니다. Resource Health 경고를 프로그래밍 방식으로 만들면 사용자가 경고를 대량으로 생성하고 사용자 지정할 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 페이지의 지침을 따르려면 미리 몇 가지 사항을 설정해야 합니다.

1. [Azure PowerShell 모듈](/powershell/azure/install-az-ps)을 설치해야 합니다.
2. 내게 알려주도록 구성된 [작업 그룹을 만들거나 재사용](../azure-monitor/alerts/action-groups.md)해야 합니다.

## <a name="instructions"></a>지침
1. PowerShell을 사용하여 계정으로 Azure에 로그인하고 상호 작용하려는 구독을 선택합니다.

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > `Get-AzSubscription`을 사용하면 액세스 권한이 있는 구독을 나열할 수 있습니다.

2. 작업 그룹에 대한 전체 Azure Resource Manager ID 찾기 및 저장

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Resource Health 경고를 위한 Resource Manager 템플릿을 만들고 `resourcehealthalert.json`으로 저장([아래 세부 정보 참조](#resource-manager-template-options-for-resource-health-alerts))

4. 이 템플릿을 사용하여 Azure Resource Manager 배포 새로 만들기

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. 앞에서 복사한 경고 이름 및 작업 그룹 리소스 ID를 입력하라는 메시지가 나타납니다.

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. 모두 제대로 작동하면 PowerShell에 확인 메시지가 표시됩니다.

    ```output
    DeploymentName          : ExampleDeployment
    ResourceGroupName       : <resourceGroup>
    ProvisioningState       : Succeeded
    Timestamp               : 11/8/2017 2:32:00 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                            Name                     Type       Value
                            ===============          =========  ==========
                            activityLogAlertName     String     <Alert Name>
                            activityLogAlertEnabled  Bool       True
                            actionGroupResourceId    String     /...

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

이 프로세스를 완전히 자동화하려면 5단계에서 값을 묻지 않도록 Resource Manager 템플릿을 편집하기만 하면 됩니다.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Resource Health 경고를 위한 Resource Manager 템플릿 옵션

이러한 기본 템플릿을 Resource Health 경고를 작성하는 시작점으로 사용할 수 있습니다. 이 템플릿은 작성한대로 작동하며 구독의 모든 리소스에서 새로 활성화 된 모든 리소스 상태 이벤트에 대한 경고를 수신하도록 등록을 수행합니다.

> 이 문서의 맨 아래에는 이 템플릿에 비해 Resource Health 경고의 신호 대 노이즈 비율을 높여야 하는 더 복잡한 경고 템플릿도 포함되어 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

하지만 이와 같이 광범위한 경고는 일반적으로 권장되지 않습니다. 관심을 갖는 이벤트에 중점을 둘 수 있도록 경고의 범위를 좁히는 방법에 대해 알아봅니다.

### <a name="adjusting-the-alert-scope"></a>경고 범위 조정

Resource Health 경고는 세 가지 다른 범위에서 이벤트를 모니터링하도록 구성할 수 있습니다.

 * 구독 수준
 * 리소스 그룹 수준
 * 리소스 수준

경고 템플릿은 구독 수준에서 구성되지만 특정 리소스 또는 특정 리소스 그룹 내의 리소스에 대해서만 알려주도록 경고를 구성하려면 위의 템플릿에서 `scopes` 섹션을 수정하기만 하면 됩니다.

리소스 그룹 수준 범위에 대한 범위 섹션은 다음과 같아야 합니다.
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

리소스 수준 범위에 대한 범위 섹션은 다음과 같아야 합니다.

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

예: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Azure Portal로 이동하여 Azure 리소스를 볼 때 URL을 살펴보면 이 문자열을 얻을 수 있습니다.

### <a name="adjusting-the-resource-types-which-alert-you"></a>경고를 표시하는 리소스 유형 조정

구독 또는 리소스 그룹 수준의 경고에는 여러 종류의 리소스가 있을 수 있습니다. 리소스 유형의 특정 하위 집합에서만 경고를 보내도록 제한하려는 경우, 템플릿의 `condition` 섹션에서 다음과 같이 정의할 수 있습니다.

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

여기서 우리는 `anyOf` 래퍼를 사용하여 리소스 상태 경고가 지정된 조건 중 하나와 일치하도록 허용하여 특정 리소스 유형을 대상으로 하는 경고를 허용합니다.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>경고를 표시하는 Resource Health 이벤트 조정
리소스에 상태 이벤트가 발생하는 경우 상태 이벤트의 상태를 나타내는 일련의 단계(`Active`, `In Progress`, `Updated` 및 `Resolved`)를 거칠 수 있습니다.

리소스 상태가 비정상이 되는 경우에만 알림을 받으려는 경우에는 `status`가 `Active`인 경우에만 알리도록 경고를 구성할 수 있습니다. 하지만 다른 단계에서도 알림을 받으려면 다음과 같이 세부 정보를 추가할 수 있습니다.

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

상태 이벤트의 네 가지 단계 모두에 대해 알림을 받으려면 이 조건을 모두 제거합니다. 그러면 `status` 속성에 관계없이 경고가 표시됩니다.

> [!NOTE]
> 각 ‘anyOf’ 섹션에는 하나의 필드 형식 값만 포함되어야 합니다.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>"알 수 없음" 이벤트를 방지하기 위해 Resource Health 경고 조정

Azure Resource Health는 Test Runner를 사용하여 리소스를 지속적으로 모니터링하여 리소스의 최신 상태를 보고할 수 있습니다. 관련된 보고 상태는 "사용할 수 있음", "사용할 수 없음" 및 "저하됨"입니다. 하지만 Runner와 Azure 리소스가 통신할 수 없는 상황에서는 리소스에 대해 "알 수 없음" 상태가 보고되며 이것은 "활성" 상태 이벤트로 간주됩니다.

하지만 리소스가 "알 수 없음"으로 보고되면 가장 최근의 정확한 보고서 이후로 상태가 변경되지 않았을 가능성이 큽니다. "알 수 없음" 이벤트에 대한 경고를 제거하려면 템플릿에서 해당 논리를 지정하면 됩니다.

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

이 예제에서는 현재 및 이전 상태가 "알 수 없음"이 아닌 이벤트에 대해서만 알림이 표시됩니다. 이 변경 사항은 경고가 휴대폰이나 이메일로 직접 전송되는 경우 유용할 수 있습니다. 

일부 이벤트에서는 currentHealthStatus 및 previousHealthStatus 속성이 null일 수 있습니다. 예를 들어 Updated 이벤트가 발생하는 경우 마지막 보고서 이후에 리소스의 상태가 변경 되지 않았고 추가 이벤트 정보를 사용할 수 있는 것일 수 있습니다(예: 원인). 따라서 위의 절을 사용하면 properties.currentHealthStatus 값과 properties.previousHealthStatus 값이 null로 설정되기 때문에 일부 경고가 트리거되지 않을 수 있습니다.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>사용자가 시작한 이벤트를 방지하기 위한 경고 조정

Resource Health 이벤트는 플랫폼이 시작한 이벤트와 사용자가 시작한 이벤트에 의해 트리거될 수 있습니다. 상태 이벤트가 Azure 플랫폼에서 발생한 경우에만 알림을 보내는 것이 합리적일 수 있습니다.

이러한 종류의 이벤트만 필터링하도록 경고를 구성하기는 쉽습니다.

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
일부 이벤트에서 원인 필드가 null일 수 있습니다. 즉, 상태 전환이 발생하고(예: 사용할 수 있음에서 사용할 수 없음으로) 이벤트가 즉시 로그되어 알림 지연을 방지합니다. 따라서 위의 절을 사용하면 properties.clause 속성 값이 null로 설정되기 때문에 경고가 트리거되지 않을 수 있습니다.

## <a name="complete-resource-health-alert-template"></a>완전한 Resource Health 경고 템플릿

다음은 이전 섹션에서 설명한 다양한 조정을 사용하여 신호 대 잡음비를 최대화하도록 구성된 샘플 템플릿입니다. 위에서 설명한 currentHealthStatus, previousHealthStatus 및 원인 속성 값이 일부 이벤트에서 null일 수 있다는 주의 사항을 명심하세요.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

하지만 어떤 구성이 내게 가장 효과적인지 알기 때문에 설명서에 제공된 도구를 사용하여 직접 사용자 정의를 수행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Resource Health에 대해 알아봅니다.
-  [Azure Resource Health 개요](Resource-health-overview.md)
-  [Azure Resource Health를 통해 사용할 수 있는 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)


Service Health 경고 만들기:
-  [Service Health에 대한 경고 구성](./alerts-activity-log-service-notifications-portal.md) 
-  [Azure 활동 로그 이벤트 스키마](../azure-monitor/essentials/activity-log-schema.md)
