---
title: Azure Portal을 사용하여 Log Analytics 작업 영역을 다른 Azure 지역으로 이동
description: Azure Portal을 사용하여 Log Analytics 작업 영역을 한 Azure 지역에서 다른 Azure 지역으로 이동하려면 Azure Resource Manager 템플릿을 사용합니다.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: f3aeb0614907d1f10e3f080a2399029711192bb8
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662221"
---
# <a name="move-log-analytics-workspace-to-another-region-using-the-azure-portal"></a>Azure Portal을 사용하여 Log Analytics 작업 영역을 다른 지역으로 이동

기존 Log Analytics 작업 영역을 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 대부분의 리소스를 호스팅하는 지역에서 최근에 Log Analytics를 사용할 수 있게 되었으며 작업 영역을 더 가까이 이동하여 송신 요금을 절감하려고 합니다. 데이터 주권 요구 사항을 준수하기 위해 작업 영역을 새로 추가된 지역으로 이동하려 할 수도 있습니다.

Log Analytics 작업 영역은 한 지역에서 다른 지역으로 이동할 수 없습니다. 하지만 Azure Resource Manager 템플릿을 사용하여 작업 영역 리소스 및 관련 리소스를 내보낼 수 있습니다. 그런 다음, 작업 영역을 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 스테이징할 수 있습니다. Resource Manager 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요. 작업 영역 환경은 복잡할 수 있으며 연결된 원본, 관리형 솔루션, 연결된 서비스, 경고 및 쿼리 팩을 포함할 수 있습니다. 일부 리소스는 Resource Manager 템플릿에서 내보낼 수 없으며 작업 영역을 이동할 때 별도의 구성이 필요한 리소스도 있습니다.

## <a name="prerequisites"></a>전제 조건

- 작업 영역 구성을 다른 지역에 배포할 수 있는 템플릿으로 내보내려면 [Log Analytics 기여자](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) 또는 [모니터링 기여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할 이상이 필요합니다.

- 다음을 포함하여 현재 작업 영역에 연결된 모든 리소스를 식별합니다.
  - 연결된 에이전트 - 작업 영역에서 *로그* 를 입력하고 [Heartbeat](../insights/solution-agenthealth.md#heartbeat-records) 테이블을 쿼리하여 연결된 에이전트를 나열합니다.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - 설치된 솔루션 -- 작업 영역 탐색 창에서 **솔루션** 을 클릭하여 설치된 솔루션 목록을 표시합니다.
  - 데이터 수집기 API - [데이터 수집기 API](../logs/data-collector-api.md)를 통해 도착하는 데이터는 사용자 지정 로그 테이블에 저장됩니다. 작업 영역 탐색 창에서 ***로그** _를 클릭한 다음, 스키마 창에서 _ *사용자 지정 로그**를 클릭하여 사용자 지정 로그 테이블 목록을 표시합니다.
  - 연결된 서비스 - 작업 영역에는 Automation 계정, 스토리지 계정, 전용 클러스터와 같은 종속 리소스에 연결된 서비스가 있을 수 있습니다. 작업 영역에서 연결된 서비스를 제거합니다. 연결된 서비스는 대상 작업 영역에서 수동으로 다시 구성해야 합니다.
  - 경고 - 작업 영역 탐색 창에서 **경고** 를 클릭한 다음, 도구 모음에서 **경고 규칙 관리** 를 클릭하여 경고를 나열합니다. 2019년 6월 1일 이후에 만들어진 작업 영역 또는 [레거시 Log Analytics 경고 API에서 scheduledQueryRules API로 업그레이드된](../alerts/alerts-log-api-switch.md) 작업 영역의 경고는 템플릿에 포함할 수 있습니다. [작업 영역에서 경고 시 scheduledQueryRules API가 사용되는지 확인](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace)할 수 있습니다. 또는 대상 작업 영역에서 경고를 수동으로 구성할 수 있습니다.
  - 쿼리 팩 - 작업 영역을 여러 쿼리 팩과 연결할 수 있습니다. 작업 영역에서 쿼리 팩을 확인하려면 작업 영역 탐색 창에서 **로그** 를 클릭하고 왼쪽 창에서 **쿼리** 를 클릭한 다음, 검색 상자의 오른쪽에서 줄임표를 클릭하여 추가 설정을 표시합니다. 그러면 선택한 쿼리 팩이 있는 대화 상자가 오른쪽에 열립니다. 쿼리 팩이 이동하려는 작업 영역과 동일한 리소스 그룹에 있는 경우 이 마이그레이션에 포함할 수 있습니다.
- 대상 지역에 Log Analytics 작업 영역을 만드는 것을 Azure 구독에서 허용하는지 확인합니다.

## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 Resource Manager 템플릿을 사용하여 이동할 작업 영역 및 리소스를 준비하고, 포털을 사용하여 대상 지역으로 이동하는 방법을 보여줍니다. 일부 리소스는 템플릿을 통해 내보낼 수 없으며, 대상 지역에 작업 영역이 만들어지면 별도로 구성해야 합니다.

### <a name="export-the-template-and-deploy-from-the-portal"></a>템플릿 내보내기 및 포털에서 배포

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, **리소스 그룹** 으로 이동합니다.
2. 작업 영역이 포함된 리소스 그룹을 찾아 클릭합니다.
3. 경고 리소스를 보려면 **숨겨진 형식 표시** 확인란을 선택합니다.
4. '형식' 필터를 클릭하고 **Log Analytics 작업 영역**, **솔루션**, **SavedSearches**, **microsoft.insights/scheduledqueryrules** 및 **defaultQueryPack** 을 적용 가능으로 선택한 다음, [적용]을 클릭합니다.
5. 이동하려는 작업 영역, 솔루션, 경고, 저장된 검색 및 쿼리 팩을 선택한 다음, 도구 모음에서 **템플릿 내보내기** 를 클릭합니다.
    
    > [!NOTE]
    > Sentinel은 템플릿을 통해 내보낼 수 없으며 Sentinel을 대상 작업 영역에 [온보딩](../../sentinel/quickstart-onboard.md)해야 합니다.
   
6. 도구 모음에서 **배포** 를 클릭하여 배포용 템플릿을 편집하고 준비합니다.
7. 도구 모음에서 **매개 변수 편집** 을 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.
8. 매개 변수를 편집하려면 **parameters** 아래에서 **value** 속성을 변경합니다.

    다음은 매개 변수 파일 예제입니다.

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

9. 편집기에서 **저장** 을 클릭합니다.
10. 도구 모음에서 **템플릿 편집** 을 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.
11. Log Analytics 작업 영역이 배포될 대상 지역을 편집하려면 온라인 편집기에서 **resources** 아래에서 **location** 속성을 변경합니다. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름인 **미국 중부** = **centralus** 입니다.
12. 템플릿에 연결된 서비스 `microsoft.operationalinsights/workspaces/linkedservices`가 있는 경우 연결된 서비스를 제거합니다. 연결된 서비스는 대상 작업 영역에서 수동으로 다시 구성해야 합니다.

    작업 영역, 저장된 검색, 솔루션, 경고 및 쿼리 팩이 포함된 예제 템플릿입니다.

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

13. 온라인 편집기에서 **저장** 을 클릭합니다.
14. **구독** 을 클릭하여 대상 작업 영역을 배포할 구독을 선택합니다.
16. **리소스 그룹** 을 클릭하여 대상 작업 영역을 배포할 리소스 그룹을 선택합니다. **새로 만들기** 를 클릭하여 대상 작업 영역에 대한 새 리소스 그룹을 만들 수 있습니다.
17. **지역** 이 NSG를 배포할 대상 위치로 설정되었는지 확인합니다.
18. **검토 + 만들기** 단추를 클릭하여 템플릿을 확인합니다.
19. **만들기** 를 클릭하여 작업 영역 및 선택한 리소스를 대상 지역에 배포합니다.
20. 선택한 리소스를 포함하고 있는 작업 영역이 이제 대상 지역에 배포되었으며, 작업 영역에서 원래 작업 영역의 기능을 축소하는 나머지 구성을 완료할 수 있습니다.
    - 에이전트 연결 - DCR을 비롯한 사용 가능한 옵션을 사용하여 가상 머신 및 가상 머신 확장 집합에서 필요한 에이전트를 구성하고 새 대상 작업 영역을 대상으로 지정합니다.
    - 솔루션 설치 - [Azure Sentinel](../../sentinel/quickstart-onboard.md) 같은 일부 솔루션은 특정 온보딩 절차가 필요한데 이 템플릿에는 이러한 절차가 포함되어 있지 않습니다. 따라서 새 작업 영역에 별도로 온보딩해야 합니다.
    - 데이터 수집기 API - 대상 작업 영역으로 데이터를 보내도록 데이터 수집기 API 인스턴스를 구성합니다.
    - 경고 규칙 - 템플릿에서 경고를 내보내지 않는 경우 대상 작업 영역에서 수동으로 구성해야 합니다.
21. 새 데이터가 원래 작업 영역에 수집되지 않는지 확인합니다. 원래 작업 영역에서 이 쿼리를 실행하고 마이그레이션 시간 후에 수집되는 데이터가 없는지 관찰합니다.

    ```kusto
    search *
    | summarize max(TimeGenerated) by Type
    ```

데이터 원본을 대상 작업 영역에 연결한 후에 수집된 데이터는 대상 작업 영역에 저장되고, 이전 데이터는 원래 작업 영역에 남아 있습니다. [작업 영역 간 쿼리](./cross-workspace-query.md#performing-a-query-across-multiple-resources)를 수행할 수 있으며, 두 작업 영역에 동일한 이름이 할당된 경우 작업 영역 참조에서 정규화된 이름(*subscriptionName/resourceGroup/componentName*)을 사용합니다.

다음은 이름이 같은 두 작업 영역의 쿼리 예제입니다.

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>취소

원본 작업 영역을 삭제하려면 이러한 항목을 포함하고 있는 내보낸 리소스 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 Azure Portal에서 대상 리소스 그룹을 선택합니다. 배포를 위해 새 리소스 그룹을 만든 경우에는 [개요] 페이지의 도구 모음에서 **리소스 그룹 삭제** 를 클릭합니다. 템플릿이 기존 리소스 그룹에 배포된 경우 템플릿을 사용하여 배포된 리소스를 선택하고, 도구 모음에서 **삭제** 를 클릭합니다.

## <a name="clean-up"></a>정리

새 데이터가 새 작업 영역에 수집되는 동안, 원래 작업 영역의 이전 데이터는 쿼리에 계속 사용할 수 있으며 작업 영역에 정의된 보존 정책이 적용됩니다. 작업 영역에서 [쿼리](./cross-workspace-query.md#performing-a-query-across-multiple-resources)할 수 있도록 이전 데이터가 필요한 기간에는 원래 작업 영역을 유지하는 것이 좋습니다. 원래 작업 영역의 이전 데이터에 더 이상 액세스할 필요가 없는 경우 Azure Portal에서 원래 리소스 그룹을 선택한 다음, 제거할 리소스를 선택하고 도구 모음에서 **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Log Analytics 작업 영역을 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다. Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../../site-recovery/azure-to-azure-tutorial-migrate.md)
