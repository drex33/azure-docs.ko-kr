---
title: Azure Monitor에서 활동 로그 경고 만들기, 보기 및 관리
description: Azure Portal, Azure Resource Manager 템플릿 및 Azure PowerShell을 사용하여 활동 로그 경고를 만듭니다.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 08/12/2021
ms.openlocfilehash: 8f26806a08ca463350e54d5ce1319fe0323715e7
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220040"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기  

## <a name="overview"></a>개요

활동 로그 경고는 경고에 지정된 조건과 일치하는 새 활동 로그 이벤트가 발생할 때 활성화되는 경고입니다.

이 경고는 Azure 리소스에 대한 것이며, Azure Resource Manager 템플릿을 사용하여 만들 수 있습니다. 또한 Azure Portal에서 생성, 업데이트 또는 삭제할 수 있습니다. 일반적으로 Azure 구독의 리소스에서 특정 변경이 발생할 때 알림을 받기 위해 활동 로그 경고를 만듭니다. 특정 리소스 그룹 또는 리소스로 범위를 지정하는 경우가 많습니다. 예를 들어 샘플 리소스 그룹 **myProductionResourceGroup** 에서 가상 머신이 삭제되는 경우 알림을 받도록 할 수 있습니다. 또는 새 규칙이 사용자 구독의 사용자에 할당되는 경우 알림을 받도록 할 수도 있습니다.

> [!IMPORTANT]
> 서비스 상태 알림에 대한 경고는 활동 로그 경고 작성을 위한 인터페이스를 통해 만들 수 없습니다. 서비스 상태 알림 생성 및 사용에 대해 자세히 알아보려면 [서비스 상태 알림에서 활동 로그 경고 수신](../../service-health/alerts-activity-log-service-notifications-portal.md)을 참조하세요.

경고 규칙을 만들 때 다음 사항을 확인합니다.

- 범위의 구독이 경고가 생성된 구독과 다르지 않습니다.
- 조건은 경고가 구성된 수준/상태/호출자/리소스 그룹/리소스 ID/리소스 종류/이벤트 범주여야 합니다.
- 경고 구성 JSON에는 "anyOf" 조건 또는 중첩 조건이 없습니다. 기본적으로 "allOf" 또는 "anyOf" 조건이 추가로 포함되지 않은 하나의 "allOf" 조건만 허용됩니다.
- 범주가 "관리"인 경우, 앞에 나온 조건 중 하나 이상을 경고에 지정해야 합니다. 활동 로그에서 이벤트가 생성될 때마다 활성화되는 경고를 만들 필요는 없습니다.
- 활동 로그의 경고 범주에서는 이벤트에 대한 경고를 만들 수 없습니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal를 사용하여 활동 로그 경고 규칙을 만들고 수정할 수 있습니다. 이 환경은 관심이 있는 특정 이벤트에 대한 원활한 경고 생성을 보장하기 위해 Azure 활동 로그와 통합됩니다.
Azure Portal에서 Azure Monitor 경고 블레이드 또는 Azure Monitor 활동 로그 블레이드에서 새 활동 로그 경고 규칙을 만들 수 있습니다. 


### <a name="create-an-alert-rule-from-the-azure-monitor-alerts-blade"></a>Azure Monitor 경고 블레이드에서 경고 규칙 만들기

다음은 Azure Portal에서 메트릭 경고 규칙을 만드는 절차입니다.

1. [Azure Portal](https://portal.azure.com)에서 **모니터** 를 클릭합니다. [모니터] 블레이드는 모든 모니터링 설정 및 데이터를 하나의 뷰에 통합합니다.

2. **경고** 를 클릭한 다음, **+ 새로운 경고 규칙** 을 클릭합니다.

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-button-new.png" alt-text="새 경고 규칙 단추의 스크린샷.":::
    > [!TIP]
    > 대부분의 리소스 블레이드도 **모니터링** 아래의 리소스 메뉴에 **경고** 가 있으며, 여기서 경고를 만들 수 있습니다.

3. **대상 선택** 을 클릭하고, 로드되는 컨텍스트 창에서 경고를 사용할 대상 리소스를 선택합니다. **구독** 및 **리소스 종류** 드롭다운을 사용하여 모니터링하려는 리소스를 찾습니다. 검색 창을 사용하여 리소스를 검색할 수도 있습니다.
    
    > [!NOTE]
    > 대상으로 전체 구독, 리소스 그룹 또는 특정 리소스를 선택할 수 있습니다. 구독 또는 리소스 그룹을 대상으로 선택하고 리소스 유형도 선택한 경우 규칙은 선택한 구독 또는 리소스 그룹 내에서 해당 유형의 모든 리소스에 적용됩니다. 특정 대상 리소스를 선택하면 규칙이 해당 리소스에만 적용됩니다. 대상 선택기를 사용하여 여러 구독, 리소스 그룹 또는 리소스를 명시적으로 선택할 수 없습니다. 

4. 선택한 리소스에 경고를 만들 수 있는 활동 로그 작업이 있는 경우 오른쪽 하단의 **사용 가능한 신호** 에 활동 로그가 포함됩니다. 이 [문서](../../role-based-access-control/resource-provider-operations.md)의 활동 로그 경고에 지원되는 리소스 종류의 전체 목록을 볼 수 있습니다.

    :::image type="content" source="media/alerts-activity-log/select-target-new.png" alt-text="대상 선택 블레이드의 스크린샷." lightbox="media/alerts-activity-log/select-target-new.png":::

5. 대상 리소스를 선택한 후에는 **조건 추가** 를 클릭합니다.

6. 다양한 **활동 로그** 범주의 신호를 포함하여 리소스에 대해 지원되는 신호 목록이 표시됩니다. 경고를 만들 활동 로그 신호/작업을 선택합니다.

7. 지난 6시간 동안의 활동 로그 작업에 대한 차트가 표시됩니다. **차트 기간** 드롭다운을 사용하여 더 긴 작업 기록을 표시하도록 선택합니다.

8. **경고 논리** 에서 선택적으로 더 많은 필터링 기준을 정의할 수 있습니다.

    - **이벤트 수준**: 이벤트의 심각도 수준: _자세한 정보_, _정보_, _경고_, _오류_ 또는 _위험_.
    - **상태**: 이벤트의 상태: _시작_, _실패_ 또는 _성공_.
    - **이벤트를 시작한 사람**: 호출자라고도 합니다. 작업을 수행한 사용자의 이메일 주소 또는 Active Directory 식별자입니다.

    > [!NOTE]
    >   높은 품질의 효과적인 규칙을 갖기 위해 경고 범위가 전체 구독이고 선택한 신호가 "모든 관리 작업"인 경우 조건 정의의 일부로 경고 논리 드롭다운 중 하나를 채워야 합니다. "이벤트 수준", "상태" 또는 "초기자"를 선택하면 규칙이 더욱 구체적이게 됩니다.
        
9. **Done** 을 클릭합니다.

    :::image type="content" source="media/alerts-activity-log/condition-selected-new.png" alt-text="조건 선택 블레이드의 스크린샷." lightbox="media/alerts-activity-log/condition-selected-new.png":::

10. **경고 규칙 이름**, **설명** 및 **심각도** 와 같은 **경고 세부 정보** 를 입력합니다.

    > [!NOTE]
    >   활동 로그 경고에 대한 경고 심각도는 현재 사용자가 구성할 수 없으며 항상 기본적으로 Sev4입니다.

11. 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어서 경고에 작업 그룹을 추가합니다.

12. **완료** 를 클릭하여 활동 로그 경고 규칙을 저장합니다.
     
     
### <a name="create-an-alert-rule-from-the-azure-monitor-activity-log-blade"></a>Azure Monitor 활동 로그 블레이드에서 경고 규칙 만들기

활동 로그 경고를 만드는 다른 방법은 [Azure Portal의 활동 로그](../essentials/activity-log.md#view-the-activity-log)를 통해 이미 발생한 활동 로그 이벤트로 시작하는 것입니다. 

1. **Azure Monitor - 활동 로그** 블레이드에서 원하는 이벤트를 필터링하거나 찾은 다음 **활동 로그 경고 추가** 단추를 사용하여 향후 유사한 이벤트에 대한 경고를 만들 수 있습니다. 

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png" alt-text="활동 로그 이벤트에서 경고 규칙 만들기 스크린샷." lightbox="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png":::

2. 경고 규칙 만들기 블레이드는 이전에 선택한 활동 로그 이벤트에 따라 이미 채워진 경고 규칙 범위 및 조건으로 열립니다. 필요한 경우 이 단계에서 범위와 조건을 편집하고 수정할 수 있습니다. 기본적으로 새 규칙의 정확한 범위와 조건은 원래 이벤트 속성에서 '있는 그대로' 복사됩니다. 예를 들어, 이벤트가 발생한 정확한 리소스와 이벤트를 시작한 특정 사용자/서비스 이름은 기본적으로 새 경고 규칙에 포함됩니다. 경고 규칙을 보다 일반적으로 만들려면 위의 3~9단계에서 설명한 대로 범위와 조건을 적절하게 수정해야 합니다. 

3. 이후 앞에 있는 10~12단계를 수행합니다.
    
### <a name="view-and-manage-in-the-azure-portal"></a>Azure Portal에서 보기 및 관리

1. Azure Portal에서 **모니터** > **경고** 를 선택합니다. 창의 왼쪽 상단에서 **경고 규칙 관리** 를 선택합니다.

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-button-new.png" alt-text="경고 규칙 관리 단추 스크린샷.":::
    
    사용 가능한 규칙 목록이 나타납니다.

2. 수정할 활동 로그 규칙을 필터링하거나 검색합니다.

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-new.png" alt-text="경고 규칙 관리 블레이드의 스크린샷." lightbox="media/alerts-activity-log/manage-alert-rules-new.png":::

    사용 가능한 필터(구독, 리소스 그룹, 리소스, 신호 유형 또는 상태)를 사용하여 편집할 활동 규칙을 찾을 수 있습니다.
 
3. 규칙을 선택하고 두 번 클릭하여 규칙 옵션을 편집합니다. 필요한 변경 작업을 수행하고 **저장** 을 선택합니다. 

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
Azure Resource Manager 템플릿을 사용하여 활동 로그 경고 규칙을 만들려면 `microsoft.insights/activityLogAlerts` 종류의 리소스를 만듭니다. 그런 다음 모든 관련된 속성을 입력합니다. 다음은 활동 로그 경고 규칙을 만드는 템플릿입니다.

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
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
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
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
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
이전 샘플 JSON은 예를 들어 sampleActivityLogAlert.json으로 저장될 수 있으며 [Azure Portal에서 Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md)를 사용하여 배포될 수 있습니다.

> [!NOTE]
> 
> 가장 높은 수준의 활동 로그 경고를 구독으로 정의할 수 있습니다.
> 즉, 두 구독에 대해 경고를 정의하는 옵션은 없으므로 구독당 하나의 경고를 정의해야 합니다.

다음 필드는 조건 필드의 Azure Resource Manager 템플릿에서 사용할 수 있는 옵션입니다. “Resource Health”, “Advisor” 및 “Service Health”에는 특수 필드에 대한 추가 속성 필드가 있습니다. 
1. resourceId:  경고가 생성되어야 하는 활동 로그 이벤트에서 영향을 받는 리소스의 리소스 ID입니다.
2. category: 활동 로그 이벤트의 범주입니다. 다음은 그 예입니다.  Administrative, ServiceHealth, ResourceHealth, Autoscale, Security, Recommendation, Policy.
3. caller: 활동 로그 이벤트의 작업을 수행한 사용자의 메일 주소 또는 Azure Active Directory 식별자입니다.
4. level: 경고가 생성되어야 하는 활동 로그 이벤트의 활동 수준입니다. 다음은 그 예입니다.  Critical, Error, Warning, Informational, Verbose.
5. operationName: 활동 로그 이벤트의 작업 이름입니다. 다음은 그 예입니다.  Microsoft.Resources/deployments/write
6. resourceGroup: 활동 로그 이벤트에서 영향을 받는 리소스의 리소스 그룹 이름입니다.
7. resourceProvider: [Azure 리소스 공급자 및 종류 설명](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). 리소스 공급자를 Azure 서비스로 매핑하는 목록은 [Azure 서비스용 리소스 공급자](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)를 참조하세요.
8. status: 활동 이벤트의 작업 상태를 설명하는 문자열입니다. 다음은 그 예입니다.  Started, In Progress, Succeeded, Failed, Active, Resolved
9. subStatus: 하지만 일반적으로 해당 REST 호출의 HTTP 상태 코드는 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다.   다음은 그 예입니다.  확인(HTTP 상태 코드: 200), 만들어짐(HTTP 상태 코드: 201), 수락됨(HTTP 상태 코드: 202), 콘텐츠 없음(HTTP 상태 코드: 204), 잘못된 요청(HTTP 상태 코드: 400), 찾을 수 없음(HTTP 상태 코드: 404), 충돌(HTTP 상태 코드: 409), 내부 서버 오류(HTTP 상태 코드: 500), 서비스를 사용할 수 없음(HTTP 상태 코드: 503), 게이트웨이 시간 초과(HTTP 상태 코드: 504) 등이 있습니다.
10. resourceType: 이벤트의 영향을 받은 리소스 종류입니다. 다음은 그 예입니다.  Microsoft.Resources/deployments

다음은 그 예입니다.

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
[여기](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)에서 활동 로그 필드에 대한 자세한 내용을 확인할 수 있습니다.



> [!NOTE]
> 새로운 활동 로그 경고 규칙이 활성화되는 데에는 최대 5분이 걸릴 수 있습니다.

## <a name="rest-api"></a>REST API 
[Azure Monitor 활동 로그 경고 API](/rest/api/monitor/activitylogalerts)는 REST API입니다. Azure Resource Manager REST API와 완전히 호환됩니다. Resource Manager cmdlet 또는 Azure CLI를 사용하여 PowerShell을 통해 사용할 수 있습니다.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>PowerShell을 사용하여 Resource Manager 템플릿 배포
PowerShell을 사용하여 이전 [Azure Resource Manager 템플릿](#azure-resource-manager-template) 섹션에 표시된 샘플 Resource Manager 템플릿을 배포하려면 다음 명령을 사용합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

여기에서 sampleActivityLogAlert.parameters.json에는 경고 규칙 생성에 필요한 매개 변수에 대해 제공된 값이 있습니다.

### <a name="use-activity-log-powershell-cmdlets"></a>활동 로그 PowerShell cmdlet 사용

활동 로그 경고에서는 다음과 같은 전용 PowerShell cmdlet을 사용할 수 있습니다.

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): 새 활동 로그 경고를 만들거나 기존 활동 로그 경고를 업데이트합니다.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): 하나 이상의 활동 로그 경고 리소스를 가져옵니다.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): 기존 활동 로그 경고를 사용 설정하고 태그를 설정합니다.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): 기존 활동 로그 경고를 사용하지 않도록 설정하고 태그를 설정합니다.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): 활동 로그 경고를 제거합니다.

## <a name="azure-cli"></a>Azure CLI

설정된 [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert)의 전용 Azure CLI 명령을 활동 로그 경고 규칙을 관리하는 데 사용할 수 있습니다.

새로운 활동 로그 경고 규칙을 만들려면 다음 명령을 순서대로 사용합니다.

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): 새로운 활동 로그 경고 규칙 리소스를 만듭니다.
2. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope): 생성된 활동 로그 경고 규칙에 대한 범위를 추가합니다.
3. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group): 활동 로그 경고 규칙에 작업 그룹을 추가합니다.

하나의 활동 로그 경고 규칙 리소스를 검색하려면 Azure CLI 명령 [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)를 사용합니다. 리소스 그룹의 모든 활동 로그 경고 규칙 리소스를 보려면 [az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)를 사용합니다.
Azure CLI 명령 [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)를 사용하여 활동 로그 경고 규칙 리소스를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [활동 로그에 대한 웹후크 스키마](./activity-log-alerts-webhook.md)를 알아봅니다.
- [활동 로그 개요](./activity-log-alerts.md)를 읽습니다.
- [작업 그룹](../platform/action-groups.md)에 대해 자세히 알아보세요.  
- [서비스 상태 알림](../../service-health/service-notifications.md)에 대해 자세히 알아보세요.

