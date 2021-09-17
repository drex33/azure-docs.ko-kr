---
title: Azure Portal 사용하여 Log Analytics 작업 영역을 다른 Azure 지역으로 이동
description: Azure Portal 사용하여 Azure Resource Manager 템플릿을 사용하여 한 Azure 지역에서 다른 Azure 지역으로 Log Analytics 작업 영역을 이동합니다.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: 8d8a12fe1fcc53b5e268ca412f3e0320cf8d95d9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427249"
---
# <a name="move-a-log-analytics-workspace-to-another-region-by-using-the-azure-portal"></a>Azure Portal 사용하여 Log Analytics 작업 영역을 다른 지역으로 이동

기존 Log Analytics 작업 영역을 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 Log Analytics는 최근에 대부분의 리소스를 호스팅하는 지역에서 사용할 수 있게 되었으며 작업 영역을 더 가깝게 만들고 송신 요금을 절약하려고 합니다. 데이터 주권 요구 사항을 위해 작업 영역을 새로 추가된 지역으로 이동할 수도 있습니다.

Log Analytics 작업 영역은 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용하여 작업 영역 리소스 및 관련 리소스를 내보낼 수 있습니다. 그런 다음 작업 영역을 템플릿으로 내보내고, 대상 지역에 맞게 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 다른 지역의 리소스를 준비할 수 있습니다. Resource Manager 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요. 

작업 영역 환경은 복잡할 수 있으며 연결된 원본, 관리형 솔루션, 연결된 서비스, 경고 및 쿼리 팩을 포함할 수 있습니다. 일부 리소스는 Resource Manager 템플릿에서 내보낼 수 없으며 작업 영역을 이동할 때 별도의 구성이 필요한 리소스도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 작업 영역 구성을 다른 지역에 배포할 수 있는 템플릿으로 내보내려면 [Log Analytics 기여자](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) 또는 [모니터링 기여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할 이상이어야 합니다.

- 다음을 포함하여 현재 작업 영역과 연결된 모든 리소스를 식별합니다.
  - *연결된 에이전트:* 작업 영역에 **로그를** 입력하고 [하트비트](../insights/solution-agenthealth.md#heartbeat-records) 테이블을 쿼리하여 연결된 에이전트를 나열합니다.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - *진단 설정:* 리소스는 작업 영역의 Azure Diagnostics 또는 전용 테이블에 로그를 보낼 수 있습니다. 작업 영역에 **로그를** 입력하고 테이블에 데이터를 보내는 리소스에 대해 다음 쿼리를 실행합니다. `AzureDiagnostics`

    ```kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | summarize by  ResourceProvider , ResourceType, Resource
    | sort by ResourceProvider, ResourceType
    ```

    전용 테이블에 데이터를 보내는 리소스에 대해 다음 쿼리를 실행합니다.

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | where isnotnull(_ResourceId)
    | extend ResourceProvider = split(_ResourceId, '/')[6]
    | where ResourceProvider !in ('microsoft.compute', 'microsoft.security')
    | extend ResourceType = split(_ResourceId, '/')[7]
    | extend Resource = split(_ResourceId, '/')[8]
    | summarize by tostring(ResourceProvider) , tostring(ResourceType), tostring(Resource)
    | sort by ResourceProvider, ResourceType
    ```

  - *설치된 솔루션:* 작업 영역 탐색 창에서 **솔루션을** 선택하여 설치된 솔루션 목록을 확인합니다.
  - *데이터 수집기 API:* [데이터 수집기 API를](../logs/data-collector-api.md) 통해 도착하는 데이터는 사용자 지정 로그 테이블에 저장됩니다. 사용자 지정 로그 테이블 목록을 확인하려면 작업 영역 탐색 창에서 **로그를** 선택한 다음 스키마 창에서 **사용자 지정 로그를** 선택합니다.
  - *연결된 서비스:* 작업 영역에는 Azure Automation 계정, 스토리지 계정 또는 전용 클러스터와 같은 종속 리소스에 연결된 서비스가 있을 수 있습니다. 작업 영역에서 연결된 서비스를 제거합니다. 대상 작업 영역에서 수동으로 다시 구성합니다.
  - *경고:* 경고를 나열하려면 작업 영역 탐색 창에서 **경고를** 선택한 다음, 도구 모음에서 **경고 규칙 관리를** 선택합니다. Log Analytics 경고 [API에서 scheduledQueryRules API로 업그레이드된](../alerts/alerts-log-api-switch.md) 작업 영역 또는 2019년 6월 1일 이후에 생성된 작업 영역의 경고는 템플릿에 포함될 수 있습니다. 
  
     [scheduledQueryRules API가 작업 영역 의 경고에 사용되는지 확인할 수 있습니다.](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace) 또는 대상 작업 영역에서 수동으로 경고를 구성할 수 있습니다.
  - *쿼리 팩:* 작업 영역은 여러 쿼리 팩과 연결될 수 있습니다. 작업 영역에서 쿼리 팩을 식별하려면 작업 영역 탐색 창에서 **로그를** 선택하고 왼쪽 창에서 **쿼리를** 선택한 다음 검색 상자의 오른쪽에 있는 말줄임표를 선택합니다. 선택한 쿼리 팩이 있는 대화 상자가 오른쪽에 열립니다. 쿼리 팩이 이동하는 작업 영역과 동일한 리소스 그룹에 있는 경우 이 마이그레이션에 포함할 수 있습니다.
- Azure 구독을 통해 대상 지역에 Log Analytics 작업 영역을 만들 수 있는지 확인합니다.

## <a name="prepare-and-move"></a>준비 및 이동
다음 절차에서는 Resource Manager 템플릿을 사용하여 이동할 작업 영역 및 리소스를 준비한 다음 포털을 사용하여 대상 지역으로 이동하는 방법을 보여줍니다. 절차를 순서대로 수행합니다.

> [!NOTE]
> 템플릿을 통해 모든 리소스를 내보낼 수 있는 것은 아닙니다. 작업 영역을 대상 지역에 만든 후에는 별도로 구성해야 합니다.

### <a name="select-resource-groups-and-edit-parameters"></a>리소스 그룹 선택 및 매개 변수 편집

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, **리소스 그룹** 을 선택합니다.
1. 작업 영역이 포함된 리소스 그룹을 찾아 선택합니다.
1. 경고 리소스를 보려면 **숨겨진 형식 표시** 확인란을 선택합니다.
1. **형식** 필터를 선택합니다. **Log Analytics 작업 영역,** **솔루션**, **SavedSearches,** **microsoft.insights/scheduledqueryrules,** **defaultQueryPack** 및 기타 작업 영역 관련 리소스(예: Automation 계정)를 선택합니다. 그런 다음, **적용** 을 선택합니다.
1. 작업 영역, 솔루션, 저장된 검색, 경고, 쿼리 팩 및 기타 작업 영역 관련 리소스(예: Automation 계정)를 선택합니다. 그런 다음, 도구 모음에서 **템플릿 내보내기** 를 선택합니다.
    
    > [!NOTE]
    > Azure Sentinel 템플릿을 통해 내보낼 수 없습니다. Sentinel을 대상 작업 영역에 [온보딩해야](../../sentinel/quickstart-onboard.md) 합니다.
   
1. 도구 모음에서 **배포를** 선택하여 배포할 템플릿을 편집하고 준비합니다.
1. 도구 모음에서 **매개 변수 편집을** 선택하여 온라인 편집기에서 *parameters.json* 파일을 엽니다.
1. 매개 변수를 편집하려면 `value` 에서 속성을 `parameters` 변경합니다. 예를 들면 다음과 같습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "value": "my-workspace-name"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/resource-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/workspaces/workspace-name"
        },
        "alertName": {
          "value": "my-alert-name"
        },
        "querypacks_name": {
          "value": "my-default-query-pack-name"
        }
      }
    }
    ```

1. 편집기에서 **저장** 을 선택합니다.

### <a name="edit-the-template"></a>템플릿 편집

1. 도구 모음에서 **템플릿 편집을** 선택하여 온라인 편집기에서 *template.json* 파일을 엽니다.
1. Log Analytics 작업 영역을 배포할 대상 지역을 편집하려면 `location` 온라인 편집기에서 아래의 속성을 `resources` 변경합니다. 

   지역 위치 코드를 얻으려면 [Azure의 데이터 상주 를](https://azure.microsoft.com/global-infrastructure/locations/)참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 **미국 중부는** `centralus` 여야 합니다.
1. 연결된 서비스 `microsoft.operationalinsights/workspaces/linkedservices` 리소스()가 템플릿에 있는 경우 제거합니다. 대상 작업 영역에서 이러한 리소스를 수동으로 다시 구성해야 합니다.

   다음 예제 템플릿에는 작업 영역, 저장된 검색, 솔루션, 경고 및 쿼리 팩이 포함됩니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "type": "String"
        },
        "workspaceResourceId": {
          "type": "String"
        },
        "alertName": {
          "type": "String"
        },
        "querypacks_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "type": "microsoft.operationalinsights/workspaces",
          "apiVersion": "2020-08-01",
          "name": "[parameters('workspaces_name')]",
          "location": "france central",
          "properties": {
            "sku": {
              "name": "pergb2018"
            },
            "retentionInDays": 30,
            "features": {
              "enableLogAccessUsingOnlyResourcePermissions": true
            },
            "workspaceCapping": {
              "dailyQuotaGb": -1
            },
            "publicNetworkAccessForIngestion": "Enabled",
            "publicNetworkAccessForQuery": "Enabled"
          }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
          "apiVersion": "2020-08-01",
          "name": "[concat(parameters('workspaces_name'), '/2b5112ec-5ad0-5eda-80e9-ad98b51d4aba')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaces_name'))]"
          ],
          "properties": {
            "category": "VM Monitoring",
            "displayName": "List all versions of curl in use",
            "query": "VMProcess\n| where ExecutableName == \"curl\"\n| distinct ProductVersion",
            "tags": [],
            "version": 2
          }
        },
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('Updates(', parameters('workspaces_name'))]",
          "location": "france central",
          "dependsOn": [
            "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]"
          ],
          "plan": {
            "name": "[concat('Updates(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/Updates",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/Updates(', parameters('workspaces_name'), ')')]"
            ]
          }
        }
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
          "location": "france central",
          "plan": {
            "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/VMInsights",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/VMInsights(', parameters('workspaces_name'), ')')]"
            ]
          }
        },
        {
          "type": "microsoft.insights/scheduledqueryrules",
          "apiVersion": "2021-02-01-preview",
          "name": "[parameters('alertName')]",
          "location": "france central",
          "properties": {
            "displayName": "[parameters('alertName')]",
            "severity": 3,
            "enabled": true,
            "evaluationFrequency": "PT5M",
            "scopes": [
              "[parameters('workspaceResourceId')]"
            ],
            "windowSize": "PT15M",
            "criteria": {
              "allOf": [
                {
                  "query": "Heartbeat | where computer == 'my computer name'",
                  "timeAggregation": "Count",
                  "operator": "LessThan",
                  "threshold": 14,
                  "failingPeriods": {
                    "numberOfEvaluationPeriods": 1,
                    "minFailingPeriodsToAlert": 1
                  }
                }
              ]
            },
            "autoMitigate": true,
            "actions": {}
          }
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks",
          "apiVersion": "2019-09-01-preview",
          "name": "[parameters('querypacks_name')]",
          "location": "francecentral",
          "properties": {}
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks/queries",
          "apiVersion": "2019-09-01-preview",
          "name": "[concat(parameters('querypacks_name'), '/00000000-0000-0000-0000-000000000000')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/querypacks', parameters('querypacks_name'))]"
          ],
          "properties": {
            "displayName": "my-query-name",
            "body": "my-query-text",
            "related": {
              "categories": [],
              "resourceTypes": [
                  "microsoft.operationalinsights/workspaces"
              ]
            },
            "tags": {
              "labels": []
            }
          }
        }
      ]
    }
    ```

1. 온라인 편집기에서 **저장** 을 선택합니다.

### <a name="deploy-the-workspace"></a>작업 영역 배포

1. **구독을** 선택하여 대상 작업 영역을 배포할 구독을 선택합니다.
1. **리소스 그룹을** 선택하여 대상 작업 영역을 배포할 리소스 그룹을 선택합니다. **새로 만들기를** 선택하여 대상 작업 영역에 대한 새 리소스 그룹을 만들 수 있습니다.
1. **지역이** 네트워크 보안 그룹을 배포할 대상 위치로 설정되어 있는지 확인합니다.
1. 검토 **+ 만들기** 단추를 선택하여 템플릿을 확인합니다.
1. **만들기를** 선택하여 작업 영역 및 선택한 리소스를 대상 지역에 배포합니다.
1. 선택한 리소스를 포함한 작업 영역이 이제 대상 지역에 배포됩니다. 원래 작업 영역으로 기능을 구문 분석하기 위해 작업 영역에서 나머지 구성을 완료할 수 있습니다.
   - *커넥트 에이전트:* 데이터 수집 규칙을 비롯한 사용 가능한 옵션을 사용하여 가상 머신 및 가상 머신 확장 집합에서 필요한 에이전트를 구성하고 새 대상 작업 영역을 대상으로 지정합니다.
   - *진단 설정:* 대상 작업 영역을 대상으로 하여 식별된 리소스의 진단 설정을 업데이트합니다.
   - *솔루션 설치:* [Azure Sentinel](../../sentinel/quickstart-onboard.md)같은 일부 솔루션에는 특정 온보딩 절차가 필요하며 템플릿에 포함되지 않았습니다. 새 작업 영역에 별도로 온보딩해야 합니다.
   - *데이터 수집기 API 구성:* 대상 작업 영역으로 데이터를 보내도록 데이터 수집기 API 인스턴스를 구성합니다.
   - *경고 규칙 구성:* 템플릿에서 경고를 내보내지 않는 경우 대상 작업 영역에서 수동으로 구성해야 합니다.
1. 새 데이터가 원래 작업 영역에 수집되지 않는지 확인합니다. 원래 작업 영역에서 다음 쿼리를 실행하고 마이그레이션 후에는 검색이 없는지 관찰합니다.

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | summarize max(TimeGenerated) by Type
    ```

데이터 원본이 대상 작업 영역에 연결되면 수집된 데이터가 대상 작업 영역에 저장됩니다. 이전 데이터는 원래 작업 영역에 유지되며 보존 정책이 적용됩니다. 작업 영역 [간 쿼리를](./cross-workspace-query.md#performing-a-query-across-multiple-resources)수행할 수 있습니다. 두 작업 영역에 동일한 이름이 할당된 경우 작업 영역 참조에서 정규화된 이름(*subscriptionName/resourceGroup/componentName*)을 사용합니다.

이름이 같은 두 작업 영역의 쿼리 예제는 다음과 같습니다.

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>취소

원본 작업 영역을 삭제하려면 내보낸 리소스 또는 이러한 리소스가 포함된 리소스 그룹을 삭제합니다.

1. Azure Portal 대상 리소스 그룹을 선택합니다.
1. **개요** 페이지에서 다음을 수행합니다.
   
   - 이 배포에 대한 새 리소스 그룹을 만든 경우 도구 모음에서 **리소스 그룹 삭제를** 선택하여 리소스 그룹을 삭제합니다.
   - 템플릿이 기존 리소스 그룹에 배포된 경우 템플릿과 함께 배포된 리소스를 선택한 다음, 도구 모음에서 **삭제를** 선택하여 선택한 리소스를 삭제합니다.

## <a name="clean-up"></a>정리

새 데이터가 새 작업 영역에 수집되는 동안 원래 작업 영역의 이전 데이터는 쿼리에 계속 사용할 수 있으며 작업 영역에 정의된 보존 정책이 적용됩니다. 작업 영역에서 [쿼리하는](./cross-workspace-query.md#performing-a-query-across-multiple-resources) 데 이전 데이터가 필요한 한 원래 작업 영역을 유지하는 것이 좋습니다. 

원래 작업 영역의 이전 데이터에 더 이상 액세스할 필요가 없는 경우:

1. Azure Portal 원래 리소스 그룹을 선택합니다. 
1. 제거하려는 리소스를 선택한 다음, 도구 모음에서 **삭제를** 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Log Analytics 작업 영역 및 관련 리소스를 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다. Azure에서 지역 간 리소스 이동 및 재해 복구에 대한 자세한 내용은 다음을 참조하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../../site-recovery/azure-to-azure-tutorial-migrate.md)
