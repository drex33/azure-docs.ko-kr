---
title: Azure Monitor Application Insights 클래식 리소스를 작업 영역 기반 리소스로 마이그레이션 | Microsoft Docs
description: Azure Monitor Application Insights 클래식 리소스를 새 작업 영역 기반 모델로 업그레이드하는 데 필요한 단계에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3eea51b69bbb1138ac7c5418370759d1777b482c
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361840"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>작업 영역 기반 Application Insights 리소스로 마이그레이션

이 가이드에서는 클래식 Application Insights 리소스를 작업 영역 기반 리소스로 마이그레이션하는 과정을 안내합니다. 작업 영역 기반 리소스는 Application Insights와 Log Analytics 간의 완전한 통합을 지원합니다. 작업 영역 기반 리소스는 Application Insights 원격 분석을 공통 Log Analytics 작업 영역으로 보냅니다. 따라서 애플리케이션, 인프라 및 플랫폼 로그를 통합된 단일 위치에 유지하면서 [Azure Monitor의 최신 기능](#new-capabilities)에 액세스할 수 있습니다.

작업 영역 기반 리소스는 리소스 전반에 걸쳐 일반적인 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원하며 앱/작업 영역 간에 쿼리가 필요 없습니다.

**작업 영역 기반 리소스는 현재 모든 상업용 지역 및 Azure US Government에서 사용할 수 있습니다.**

## <a name="new-capabilities"></a>새로운 기능

작업 영역 기반 Application Insights를 사용하면 다음과 같은 Azure Monitor 및 Log Analytics의 최신 기능을 모두 활용할 수 있습니다.

* [CMK(고객 관리형 키)](../logs/customer-managed-keys.md)는 사용자만 액세스할 수 있는 암호화 키를 사용하여 미사용 데이터에 대한 암호화를 제공합니다.
* [Azure Private Link](../logs/private-link-security.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다.
* [프로파일러 및 스냅샷 디버거에 대한 BYOS(사용자 고유 스토리지)](./profiler-bring-your-own-storage.md)를 사용하면 Application Insights Profiler 및 스냅샷 디버거와 연결된 모든 데이터에 대한 미사용 암호화 정책, 수명 관리 정책 및 네트워크 액세스를 완벽하게 제어할 수 있습니다. 
* [약정 계층](../logs/manage-cost-storage.md#pricing-model)을 사용하면 종량제 가격에 비해 비용을 30%를 절약할 수 있습니다. 
* Log Analytics 스트리밍 수집을 통해 데이터를 더 빨리 수집합니다.

## <a name="migration-process"></a>마이그레이션 프로세스

작업 영역 기반 리소스로 마이그레이션하면 클래식 리소스의 스토리지에서 새 작업 영역 기반 스토리지로 데이터가 전송되지 않습니다. 마이그레이션을 선택하면 클래식 리소스 데이터에 대한 액세스가 유지되면서 새 데이터가 기록되는 위치가 Log Analytics 작업 영역으로 변경됩니다. 

클래식 리소스 데이터가 유지되고 클래식 Application Insights 리소스의 보존 설정이 적용됩니다. 마이그레이션 후 수집되는 모든 새 데이터에는 연결된 Log Analytics 작업 영역의 [보존 설정이](../logs/manage-cost-storage.md#change-the-data-retention-period) 적용됩니다. 이는 [데이터 형식에 따라 다른 보존 설정](../logs/manage-cost-storage.md#retention-by-data-type)도 지원합니다.
마이그레이션 프로세스는 **영구적이며 되돌릴 수 없습니다**. 리소스를 작업 영역 기반 Application Insights로 마이그레이션하면 항상 작업 영역 기반 리소스가 됩니다. 그러나 마이그레이션한 후에는 필요에 따라 자주 대상 작업 영역을 변경할 수 있습니다. 

> [!NOTE]
> 작업 영역 기반 Application Insights 리소스에 대한 데이터 수집 및 보존 비용은 데이터가 있는 [Log Analytics 작업 영역을 통해 청구](../logs/manage-cost-storage.md)됩니다. 마이그레이션 전에 클래식 Application Insights 리소스에 수집된 데이터에 90일보다 긴 데이터 보존 기간을 선택한 경우에는 해당 Application Insights 리소스를 통해 데이터 보존 비용이 계속 청구됩니다. 작업 영역 기반 Application Insights 리소스에 대한 요금 청구에 대해 [자세히 알아봅니다]( ./pricing.md#workspace-based-application-insights).

기존 리소스를 마이그레이션할 필요가 없으며 대신 새 작업 영역 기반 Application Insights 리소스를 만들려는 경우에는 [작업 영역 기반 리소스 생성 가이드](create-workspace-resource.md)를 사용합니다.

## <a name="pre-requisites"></a>필수 구성 요소

- 액세스 제어 모드가 **`use resource or workspace permissions`** 설정으로 지정 된 Log Analytics 작업 영역 

    - 작업 영역 기반 Application Insights 리소스는 전용 **`workspace based permissions`** 설정으로 지정된 작업 영역과 호환되지 않습니다. Log Analytics 작업 영역 액세스 제어에 대한 자세한 내용은 [Log Analytics 액세스 제어 모드 구성 참고 자료](../logs/manage-access.md#configure-access-control-mode)를 참조하세요.

    - 기존 Log Analytics 작업 영역이 아직 없는 경우 [Log Analytics 작업 영역 만들기 설명서를 참조하세요](../logs/quick-create-workspace.md).
    
- **연속 내보내기는 작업 영역 기반 리소스에 지원되지 않으므로** 사용하지 않도록 설정해야 합니다.
마이그레이션이 완료되면 [진단 설정](../essentials/diagnostic-settings.md)을 사용하여 스토리지 계정으로의 데이터 보관 또는 Azure 이벤트 허브로의 스트리밍을 구성할 수 있습니다.  

    > [!CAUTION]
    > 진단 설정은 연속 내보내기와 다른 내보내기 형식/스키마를 사용하므로 마이그레이션하면 Stream Analytics와의 기존 통합이 중단됩니다.

- Log Analytics 작업 영역의 **일반** > **사용량 및 예상 비용** > **데이터 보존** 에서 현재 보존 설정을 확인합니다. 이 설정은 Application Insights 리소스를 마이그레이션한 후 새로 수집된 데이터가 저장되는 기간에 영향을 줍니다. 현재 Application Insights 데이터를 기본 90일보다 오래 저장하고 있고, 이 긴 보존 기간을 유지하려는 경우에는 작업 영역 보존 설정을 조정해야 할 수 있습니다.

## <a name="migrate-your-resource"></a>리소스 마이그레이션

이 섹션에서는 클래식 Application Insights 리소스를 새 작업 영역 기반 리소스 종류로 마이그레이션하는 과정을 안내합니다.

1. Application Insights 리소스에서 왼쪽 메뉴 모음의 **구성** 제목 아래에 있는 **속성** 을 선택합니다.

    ![빨간색 상자에 강조 표시된 속성](./media/convert-classic-resource/properties.png)

2. **`Migrate to Workspace-based`** 을 선택합니다.
    
     ![리소스 마이그레이션 단추](./media/convert-classic-resource/migrate.png)

3. 향후에 수집되는 모든 Application Insights 원격 분석을 저장할 Log Analytics 작업 영역을 선택합니다.

     ![마이그레이션 마법사 UI에서 대상 작업 영역을 선택하는 옵션](./media/convert-classic-resource/migration.png)
    

리소스가 마이그레이션되면 **개요** 창에 해당 작업 영역 정보가 표시됩니다.

![작업 영역 이름](./media/create-workspace-resource/workspace-name.png)

파란색 링크 텍스트를 클릭하면 새 통합 작업 영역 쿼리 환경을 활용할 수 있는 관련 Log Analytics 작업 영역으로 이동합니다.

> [!NOTE]
> 작업 영역 기반 Application Insights 리소스로 마이그레이션한 후 Application Insights의 한도 대신 [작업 영역의 일일 한도](../logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume)를 사용하여 수집 및 비용을 제한하는 것이 좋습니다.

## <a name="understanding-log-queries"></a>로그 쿼리 이해

Application Insights 환경 내에서 Application Insights 클래식 리소스 쿼리, 통합 문서 및 로그 기반 경고에 대해 계속 이전 버전과 완전히 호환됩니다. 

[새 작업 영역 기반 테이블 구조/스키마](apm-tables.md)에 대한 쿼리를 작성하려면 먼저 Log Analytics 작업 영역으로 이동해야 합니다. 

작업 영역 내의 Log Analytics UI에서 직접 쿼리하면 마이그레이션 후 수집된 데이터만 표시됩니다. 클래식 Application Insights 데이터 + 마이그레이션 후 수집된 새 데이터를 모두 통합 쿼리 환경에서 보려면 마이그레이션된 Application Insights 리소스 내에서 로그(분석) 쿼리 뷰를 사용합니다.

## <a name="programmatic-resource-migration"></a>프로그래매틱 리소스 마이그레이션

### <a name="azure-cli"></a>Azure CLI

미리 보기 Application Insights Azure CLI 명령에 액세스하려면 먼저 다음을 실행해야 합니다.

```azurecli
 az extension add -n application-insights
```

`az extension add` 명령을 실행하지 않으면 다음 오류 메시지가 표시됩니다. `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

이제 다음을 실행하여 Application Insights 리소스를 만들 수 있습니다.

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>예제

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

이 명령에 대한 전체 Azure CLI 설명서는 [Azure CLI 설명서](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_update)를 참조하세요.

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights` PowerShell 명령은 현재 클래식 Application Insights 리소스를 작업 영역 기반 리소스로 마이그레이션하도록 지원하지 않습니다. PowerShell을 사용하여 작업 영역 기반 리소스를 만들려면 아래 Azure Resource Manager 템플릿을 사용하고 PowerShell을 사용하여 배포할 수 있습니다.

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

#### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>매개 변수 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>연결된 작업 영역 수정

작업 영역 기반 Application Insights 리소스를 만든 후에는 연결된 Log Analytics 작업 영역을 수정할 수 있습니다.

Application Insights 리소스 창 내에서 **속성** > **작업 영역 변경** > **Log Analytics 작업 영역** 을 선택합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="access-mode"></a>액세스 모드

**오류 메시지:** *선택한 작업 영역이 작업 영역 기반 액세스 모드로 구성되어 있습니다. 일부 APM 기능이 영향을 받을 수 있습니다. 다른 작업 영역을 선택하거나 작업 영역 설정에서 리소스 기반 액세스를 허용합니다. CLI를 사용하여 이 오류를 재정의할 수 있습니다.* 

작업 영역 기반 Application Insights 리소스가 제대로 작동하도록 하려면 대상 Log Analytics 작업 영역의 액세스 제어 모드를 **리소스 또는 작업 영역 권한** 설정으로 변경해야 합니다. 이 설정은 Log Analytics 작업 영역 UI의 **속성** > **액세스 제어 모드** 에 있습니다. 자세한 지침은 [Log Analytics 액세스 제어 모드 구성 참고 자료](../logs/manage-access.md#configure-access-control-mode)를 참조하세요. 액세스 제어 모드가 전용 **작업 영역 권한 필요** 설정으로 지정된 경우 포털 마이그레이션 환경을 통한 마이그레이션은 차단된 상태로 유지됩니다.

보안상의 이유로 현재 대상 작업 영역의 액세스 제어 모드를 변경할 수 없는 경우, 마이그레이션에 사용할 새 Log Analytics 작업 영역을 만드는 것이 좋습니다. 

### <a name="continuous-export"></a>연속 내보내기

**오류 메시지:** *계속하려면 먼저 연속 내보내기를 사용하지 않도록 설정해야 합니다. 마이그레이션 후 내보내기에 진단 설정을 사용하세요.* 

작업 영역 기반 리소스에는 레거시 연속 내보내기 기능이 지원되지 않습니다. 마이그레이션하기 전에 연속 내보내기를 사용하지 않도록 설정해야 합니다.

1. Application Insights 리소스 보기의 **구성** 제목에서 **연속 내보내기** 를 선택합니다.

    ![연속 내보내기 메뉴 항목](./media/convert-classic-resource/continuous-export.png)

2. **사용 안 함** 을 선택합니다.

    ![연속 내보내기 사용 안 함 단추](./media/convert-classic-resource/disable.png)

- 사용 안 함을 선택하면 마이그레이션 UI로 다시 이동할 수 있습니다. 연속 내보내기 편집 페이지에 설정이 저장되지 않는다는 메시지가 표시되는 경우에는 확인을 선택해도 됩니다. 연속 내보내기를 사용하도록 설정하거나 사용하지 않도록 설정하는 것과는 관련이 없기 때문입니다.

- Application Insights 리소스를 작업 영역 기반으로 성공적으로 마이그레이션한 후에는 진단 설정을 사용하여 연속 내보내기에서 제공했던 기능을 대체할 수 있습니다. Application Insights 리소스 내에서 **진단 설정** > **진단 설정 추가** 를 선택합니다. 모든 테이블 또는 테이블 하위 집합을 선택하여 스토리지 계정에 보관하거나 Azure Event Hub로 스트리밍할 수 있습니다. 진단 설정에 대한 자세한 참고 자료는 [Azure Monitor 진단 설정 참고 자료](../essentials/diagnostic-settings.md)를 참조하세요.

### <a name="retention-settings"></a>보존 설정

**경고 메시지:** *사용자 지정된 Application Insights 보존 설정은 작업 영역으로 전송된 데이터에 적용되지 않습니다. 개별적으로 다시 구성해야 합니다.*

마이그레이션하기 전에 변경 작업을 수행할 필요는 없지만, 이 메시지는 현재 Application Insights 보존 설정이 기본 90일 보존 기간으로 지정되어 있지 않다고 경고하기 위한 것입니다. 이 경고 메시지는 마이그레이션하고 새 데이터 수집을 시작하기 전에 Log Analytics 작업 영역에 대한 보존 설정을 수정하는 것이 좋음을 의미합니다. 

Log Analytics UI 내 **일반** > **사용량 및 예상 비용** > **데이터 보존** 에서 Log Analytics의 현재 보존 설정을 확인할 수 있습니다. 이 설정은 Application Insights 리소스를 마이그레이션한 후 새로 수집된 데이터가 저장되는 기간에 영향을 줍니다.

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색](../essentials/metrics-charts.md)
* [분석 쿼리 작성](../logs/log-query-overview.md)
