---
title: Azure Monitor Application Insights 스마트 검색을 경고로 업그레이드(미리 보기) | Microsoft Docs
description: Azure Monitor Application Insights 스마트 검색을 경고 규칙으로 업그레이드하는 데 필요한 단계에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/30/2021
ms.openlocfilehash: d0995c05af707c5a4fb94305ca1211e38ca59e3a
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587484"
---
# <a name="migrate-azure-monitor-application-insights-smart-detection-to-alerts-preview"></a>Azure Monitor Application Insights 스마트 검색을 경고로 마이그레이션(미리 보기)

이 문서에서는 Application Insights 스마트 검색을 경고로 마이그레이션하는 프로세스를 설명합니다. 마이그레이션은 다양한 스마트 검색 모듈에 대한 경고 규칙을 만듭니다. 다른 Azure Monitor 경고 규칙과 마찬가지로 이러한 규칙을 관리하고 구성할 수 있습니다. 이러한 규칙에 대한 작업 그룹을 구성하여 새 검색에 대한 여러 작업 또는 알림을 제공할 수도 있습니다.

## <a name="benefits-of-migration-to-alerts"></a>경고로 마이그레이션의 이점

마이그레이션을 사용하면 이제 스마트 검색을 통해 다음을 비롯한 Azure Monitor 경고의 전체 기능을 활용할 수 있습니다.

- **모든 검색기에 대한 풍부한 알림 옵션** - [작업 그룹](../alerts/action-groups.md)을 사용하면 경고가 실행될 때 트리거되는 여러 유형의 알림 및 작업을 구성할 수 있습니다. 이메일, SMS, 음성 통화 또는 푸시 알림 및 안전한 웹후크 호출, 논리 앱, 자동화 Runbook 등의 작업을 통해 알림을 구성할 수 있습니다. 작업을 한 번 구성하고 여러 경고 규칙에서 사용할 수 있도록 하여 작업 그룹을 대규모로 추가 관리합니다.
- Azure Monitor 경고 환경 및 API를 사용하여 스마트 검색 경고를 **대규모 관리** 합니다.
- **규칙 기반 알림 억제** - [작업 규칙](../alerts/alerts-action-rules.md)을 통해 모든 Azure Resource Manager 범위(Azure 구독, 리소스 그룹 또는 대상 리소스)에서 작업을 정의하거나 억제할 수 있습니다. 조치를 취하려는 경고 인스턴스의 특정 하위 집합의 범위를 좁히는 데 도움이 되는 다양한 필터가 있습니다. 

## <a name="migrated-smart-detection-capabilities"></a>마이그레이션된 스마트 검색 기능

Application Insights 리소스를 마이그레이션할 때 새 경고 규칙 집합이 만들어집니다.  마이그레이션된 각 스마트 검색 기능에 대해 하나의 규칙이 만들어집니다. 다음 표에서는 마이그레이션 전 스마트 검색 기능을 마이그레이션 후 경고 규칙에 매핑합니다.

| 스마트 검색 규칙 이름<sup>(1)</sup>          | 경고 규칙 이름<sup>(2)</sup> |
| -------------------------                          | ------------------------------ |
| 서버 응답 시간 저하                | 응답 대기 시간 저하 - *\<Application Insights resource name\>*  |
| 종속성 기간 저하                 | 종속성 대기 시간 저하 - *\<Application Insights resource name\>*|
| 추적 심각도 비율의 저하(미리 보기)      | 추적 심각도 저하 - *\<Application Insights resource name\>*|
| 예외 볼륨의 비정상적인 증가(미리 보기)        | 예외 변칙 - *\<Application Insights resource name\>*|
| 잠재적인 메모리 누수 검색됨(미리 보기)           | 잠재적인 메모리 누수 - *\<Application Insights resource name\>*|
| 느린 페이지 로드 시간                                | *중단됨* <sup>(3)</sup>  |
| 느린 서버 응답 시간                          | *중단됨* <sup>(3)</sup>  |
| 긴 종속성 기간                           | *중단됨* <sup>(3)</sup>  |
| 잠재적인 보안 문제 검색됨(미리 보기)        | *중단됨* <sup>(3)</sup>  |
| 일일 데이터 볼륨의 비정상적인 증가(미리 보기)       | *중단됨* <sup>(3)</sup>  |

<sup>(1)</sup> 스마트 검색 설정 블레이드에 표시되는 규칙 이름  
<sup>(2)</sup> 마이그레이션 후 새 경고 규칙의 이름  
<sup>(3)</sup> 이러한 스마트 검색 기능은 사용량이 적고 검색 효율성을 재평가하기 때문에 경고로 변환되지 않습니다. 이러한 검색기는 마이그레이션이 완료되면 이 리소스에 대해 더 이상 지원되지 않습니다. 

마이그레이션은 스마트 검색의 알고리즘 디자인 및 동작을 변경하지 않습니다. 변경 전후에도 동일한 검색 성능이 예상됩니다.

각 Application Insights 리소스에 별도로 마이그레이션을 적용해야 합니다. 명시적으로 마이그레이션되지 않은 리소스의 경우 스마트 검색은 이전처럼 계속 작동합니다.

### <a name="action-group-configuration-for-the-new-smart-detection-alert-rules"></a>새 스마트 검색 경고 규칙에 대한 작업 그룹 구성

마이그레이션의 일부로 각각의 새 경고 규칙은 작업 그룹으로 자동으로 구성됩니다. 마이그레이션은 각 규칙에 대한 기본 작업 그룹을 할당할 수 있습니다. 기본 작업 그룹은 마이그레이션 전에 규칙 알림에 따라 구성됩니다.

- **스마트 검색 규칙에 기본 이메일이 있거나 알림이 구성되지 않은 경우** 새 경고 규칙은 "Application Insights Smart Detection"이라는 작업 그룹으로 구성됩니다.
    - 마이그레이션 도구가 해당 이름의 기존 작업 그룹을 찾은 경우 새 경고 규칙을 해당 작업 그룹에 연결합니다.  
    - 찾지 못한 경우 해당 이름을 가진 새 작업 그룹을 만듭니다. 새 그룹이 "Azure Resource Manager 역할에 이메일 보내기" 작업에 대해 구성되고 Azure Resource Manager 모니터링 기여자 및 모니터링 읽기 권한자에게 알림을 보냅니다.

- 마이그레이션 전에 **기본 이메일 알림이 변경** 된 경우 이전에 구성된 이메일 주소로 알림을 보내는 이메일 작업과 함께 "Application Insights Smart Detection\<n\>"이라는 작업 그룹이 생성됩니다.

기본 작업 그룹을 사용하는 대신 모든 새 경고 규칙에 대해 구성할 기존 작업 그룹을 선택합니다.

## <a name="executing-smart-detection-migration-process"></a>스마트 검색 마이그레이션 프로세스 실행

### <a name="migrate-your-smart-detection-using-the-azure-portal"></a>Azure Portal을 사용하여 스마트 검색 마이그레이션

한 번에 하나의 특정 Application Insights 리소스에 마이그레이션을 적용합니다.

리소스에서 스마트 검색을 마이그레이션하려면 다음 단계를 수행합니다.

1. Application Insights 리소스 왼쪽 메뉴의 **조사** 제목 아래에서 **스마트 검색** 을 선택합니다.

2. **경고로 스마트 검색 마이그레이션(미리 보기)** 이라는 배너를 클릭합니다. 마이그레이션 대화 상자가 열립니다.

   ![스마트 검색 피드 배너](media/alerts-smart-detections-migration/smart-detection-feed-banner.png)

3. 새 경고 규칙에 대해 구성할 작업 그룹을 선택합니다. 위에서 설명한 대로 기본 작업 그룹을 사용하거나 기존 작업 그룹 중 하나를 사용할 수 있습니다.

4. 마이그레이션 프로세스를 시작하려면 **이전** 을 선택합니다.

   ![스마트 검색 마이그레이션 대화 상자](media/alerts-smart-detections-migration/smart-detection-migration-dialog.png)

마이그레이션 후 위에서 설명한 대로 Application Insight 리소스에 대한 새 경고 규칙이 만들어집니다.

### <a name="migrate-your-smart-detection-using-azure-cli"></a>Azure CLI를 사용하여 스마트 검색 마이그레이션

다음 Azure CLI 명령을 사용하여 스마트 검색 마이그레이션을 시작할 수 있습니다. 이 명령은 앞서 설명한 대로 미리 구성된 마이그레이션 프로세스를 트리거합니다.

```azurecli
az rest --method POST --uri /subscriptions/{subscriptionId}/providers/Microsoft.AlertsManagement/migrateFromSmartDetection?api-version=2021-01-01-preview --body @body.txt
```

body.txt에는 다음이 포함되어야 합니다.

```json
{
      "scope": [
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName} /providers/microsoft.insights/components/{resourceName} "
      ],
      "actionGroupCreationPolicy" : "{Auto/Custom}",
      "customActionGroupName" : "{actionGroupName}"           
}
```

**ActionGroupCreationPolicy** 는 스마트 검색 규칙의 이메일 설정을 작업 그룹으로 마이그레이션하기 위한 정책을 선택합니다. 허용된 값은

- **'자동'** : 이 문서에 설명된 대로 기본 작업 그룹을 사용합니다.
- **'사용자 지정'** : **'customActionGroupName'** 에 지정된 작업 그룹으로 모든 경고 규칙을 만듭니다.
- *\<blank\>* - **ActionGroupCreationPolicy** 를 지정하지 않으면 '자동' 정책이 사용됩니다.

### <a name="migrate-your-smart-detection-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 스마트 검색 마이그레이션

Azure Resource Manager 템플릿을 사용하여 특정 Application Insights 리소스에 대한 경고로 스마트 검색 마이그레이션을 트리거할 수 있습니다. 이 방법을 사용하면 다음을 수행해야 합니다.

- 지원되는 각각의 검색기에 대해 스마트 검색 경고 규칙 만들기
- 마이그레이션이 완료되었음을 나타내도록 Application Insight 속성을 수정합니다.

이 방법을 사용하면 만들 경고 규칙을 제어하고, 고유한 경고 규칙 이름 및 설명을 정의하고, 각 규칙에 대해 원하는 작업 그룹을 선택할 수 있습니다.

이를 위해 다음 템플릿을 사용해야 합니다(필요에 따라 편집하여 구독 ID 및 Application Insights 리소스 이름 제공).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "applicationInsightsResourceName": {
            "type": "string"
        },
        "actionGroupName": {
            "type": "string",
            "defaultValue": "Application Insights Smart Detection"
        },
        "actionGroupResourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().Name]"
        }
    },
    "variables": {
        "applicationInsightsResourceId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().Name,'/providers/microsoft.insights/components/',parameters('applicationInsightsResourceName'))]",
        "actionGroupId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',parameters('actionGroupResourceGroup'),'/providers/microsoft.insights/ActionGroups/',parameters('actionGroupName'))]",
        "requestPerformanceDegradationDetectorRuleName": "[concat('Response Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "dependencyPerformanceDegradationDetectorRuleName": "[concat('Dependency Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "traceSeverityDetectorRuleName": "[concat('Trace Severity Degradation - ', parameters('applicationInsightsResourceName'))]",
        "exceptionVolumeChangedDetectorRuleName": "[concat('Exception Anomalies - ', parameters('applicationInsightsResourceName'))]",
        "memoryLeakRuleName": "[concat('Potential Memory Leak - ', parameters('applicationInsightsResourceName'))]"
    },
    "resources": [
        {
            "name": "[variables('requestPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "RequestPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {   
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('dependencyPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Dependency Latency Degradation notifies you of an unusual increase in response by a dependency your app is calling (e.g. REST API or database)",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "DependencyPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('traceSeverityDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Trace Severity Degradation notifies you of an unusual increase in the severity of the traces generated by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "TraceSeverityDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('exceptionVolumeChangedDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Exception Anomalies notifies you of an unusual rise in the rate of exceptions thrown by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "ExceptionVolumeChangedDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('memoryLeakRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Potential Memory Leak notifies you of increased memory consumption pattern by your app which may indicate a potential memory leak.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "MemoryLeakDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[concat(parameters('applicationInsightsResourceName'),'/migrationToAlertRulesCompleted')]",
            "type": "Microsoft.Insights/components/ProactiveDetectionConfigs",
            "location": "[resourceGroup().location]",
            "apiVersion": "2018-05-01-preview",
            "properties": {
                "name": "migrationToAlertRulesCompleted",
                "sendEmailsToSubscriptionOwners": false,
                "customEmails": [],
                "enabled": true
            },
            "dependsOn": [
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('requestPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('dependencyPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('traceSeverityDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('exceptionVolumeChangedDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('memoryLeakRuleName'))]"
            ]
        }
    ]
}
```

## <a name="viewing-your-alerts-after-the-migration"></a>마이그레이션 후 경고 보기

마이그레이션 프로세스에 따라 Application Insights 리소스 왼쪽 메뉴에서 경고 항목을 선택하여 스마트 검색 경고를 볼 수 있습니다. **신호 유형** 을 **스마트 검색기** 로 선택하여 스마트 검색 경고만 필터링하여 표시합니다. 경고를 선택하여 해당 검색 세부 정보를 볼 수 있습니다.

![스마트 감지 경고](media/alerts-smart-detections-migration/smart-detector-alerts.png)

Application Insights 리소스의 스마트 검색 피드에서도 사용 가능한 검색을 볼 수도 있습니다.

![스마트 검색 피드](media/alerts-smart-detections-migration/smart-detection-feed.png)

## <a name="managing-smart-detection-alert-rules-settings-after-the-migration"></a>마이그레이션 후 스마트 검색 경고 규칙 설정 관리

### <a name="managing-alert-rules-settings-using-the-azure-portal"></a>Azure Portal을 사용하여 경고 규칙 설정 관리

마이그레이션이 완료되면 리소스에 대해 정의된 다른 경고 규칙과 비슷한 방식으로 새 스마트 검색 경고 규칙에 액세스합니다.

1. Application Insights 리소스 왼쪽 메뉴의 **모니터링** 제목 아래에서 **경고** 를 선택합니다.

   ![경고 메뉴](media/alerts-smart-detections-migration/application-insights-alerts.png)

2. **경고 규칙 관리** 를 선택합니다.

   ![경고 규칙 관리](media/alerts-smart-detections-migration/manage-alert-rules.png)

3. **신호 유형** 을 **스마트 검색기** 로 선택하여 스마트 검색 경고 규칙을 필터링하여 표시합니다.

   ![스마트 검색기 규칙](media/alerts-smart-detections-migration/smart-detector-rules.png)

### <a name="enabling--disabling-smart-detection-alert-rules"></a>스마트 검색 경고 규칙 사용/사용 안 함 

다른 경고 규칙과 마찬가지로 포털 UI를 통해 또는 프로그래밍 방식으로 스마트 검색 경고 규칙을 사용하거나 사용하지 않도록 설정할 수 있습니다.

마이그레이션 전에 특정 스마트 검색 규칙을 사용하지 않도록 설정하면 새 경고 규칙도 사용하지 않도록 설정됩니다.

### <a name="configuring-action-group-for-your-alert-rules"></a>경고 규칙에 대한 작업 그룹 구성

다른 Azure Monitor 경고 규칙과 마찬가지로 새 스마트 검색 경고 규칙에 대한 작업 그룹을 만들고 관리할 수 있습니다. 

### <a name="managing-alert-rule-settings-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 경고 규칙 설정 관리

마이그레이션을 완료한 후 Azure Resource Manager 템플릿을 사용하여 스마트 검색 경고 규칙 설정에 대한 설정을 구성할 수 있습니다.

> [!NOTE]
> 마이그레이션이 완료되면 스마트 검색 경고 규칙 템플릿을 사용하여 스마트 검색 설정을 구성해야 하며 더 이상 [Application Insights Resource Manager 템플릿](../app/proactive-arm-config.md#smart-detection-rule-configuration)을 사용하여 구성할 수 없습니다.

이 Azure Resource Manager 템플릿 예에서는 심각도가 2인 **사용** 상태에서 **응답 대기 시간 저하** 경고 규칙을 구성하는 방법을 보여 줍니다.
* 스마트 검색은 글로벌 서비스이므로 규칙 위치가 **글로벌** 위치에 생성됩니다.
* "id" 속성은 구성된 특정 검색기에 따라 변경되어야 합니다. 값은 다음 중 하나여야 합니다.

  - **FailureAnomaliesDetector**
  - **RequestPerformanceDegradationDetector**
  - **DependencyPerformanceDegradationDetector**
  - **ExceptionVolumeChangedDetector**
  - **TraceSeverityDetector**  
  - **MemoryLeakDetector**
 
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Response Latency Degradation - my-app",
            "location": "global", 
            "properties": {
                  "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT24H",
                  "detector": {
                  "id": "RequestPerformanceDegradationDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```



## <a name="next-steps"></a>다음 단계

- [Azure의 경고에 대해 자세히 알아보기](./alerts-overview.md)
- [Application Insights의 스마트 검색에 대해 자세히 알아보기](../app/proactive-diagnostics.md)
