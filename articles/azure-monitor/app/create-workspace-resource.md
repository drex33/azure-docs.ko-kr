---
title: 새 Azure Monitor Application Insights 작업 영역 기반 리소스 만들기
description: 새 Azure Monitor Application Insights 작업 영역 기반 리소스를 사용하도록 설정하는 데 필요한 단계에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 10/06/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 42025ec3e2c509e2fcf7aa4874d9d5363f134a97
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604496"
---
# <a name="workspace-based-application-insights-resources"></a>작업 영역 기반 Application Insights 리소스

작업 영역 기반 리소스는 Application Insights와 Log Analytics 간의 완전한 통합을 지원합니다. 이제 Application Insights 원격 분석을 공통 Log Analytics 작업 영역으로 보낼 수 있습니다. 이를 통해 애플리케이션, 인프라 및 플랫폼 로그를 통합된 단일 위치에 유지하면서 Log Analytics의 모든 기능에 액세스할 수 있습니다.

이는 또한 리소스 전반에 걸쳐 일반적인 Azure RBAC(Azure 역할 기반 액세스 제어)를 허용하며 앱/작업 영역 간의 쿼리를 필요로 하지 않습니다.

> [!NOTE]
> 작업 영역 기반 Application Insights 리소스에 대한 데이터 수집 및 보존 비용은 데이터가 있는 Log Analytics 작업 영역을 통해 청구됩니다. 작업 영역 기반 Application Insights 리소스에 대한 요금 청구에 대해 [자세히 알아봅니다]( ./pricing.md#workspace-based-application-insights).

## <a name="new-capabilities"></a>새로운 기능

작업 영역 기반 Application Insights를 사용하면 다음과 같은 Azure Monitor 및 Log Analytics의 최신 기능을 활용할 수 있습니다.

* [CMK(고객 관리형 키)](../logs/customer-managed-keys.md)는 사용자만 액세스할 수 있는 암호화 키를 사용하여 미사용 데이터에 대한 암호화를 제공합니다.
* [Azure Private Link](../logs/private-link-security.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다.
* [프로파일러 및 스냅샷 디버거에 대한 BYOS(사용자 고유 스토리지)](./profiler-bring-your-own-storage.md)를 사용하면 Application Insights Profiler 및 스냅샷 디버거와 연결된 모든 데이터에 대한 미사용 암호화 정책, 수명 관리 정책 및 네트워크 액세스를 완벽하게 제어할 수 있습니다. 
* [약정 계층](../logs/manage-cost-storage.md#pricing-model)을 사용하면 종량제 가격에 비해 비용을 30%를 절약할 수 있습니다. 
* Log Analytics 스트리밍 수집을 통해 데이터를 더 빨리 수집합니다.

## <a name="create-workspace-based-resource"></a>작업 영역 기반 리소스 만들기

[Azure Portal](https://portal.azure.com)에 로그인하여 다음과 같이 Application Insights 리소스를 만듭니다.

> [!div class="mx-imgBorder"]
> ![작업 영역 기반 Application Insights 리소스](./media/create-workspace-resource/create-workspace-based.png)

기존 Log Analytics 작업 영역이 아직 없는 경우 [Log Analytics 작업 영역 만들기 설명서를 참조하세요](../logs/quick-create-workspace.md).

**작업 영역 기반 리소스는 현재 모든 상업용 지역 및 Azure Government에서 사용할 수 있습니다.**

리소스가 만들어지면 **개요** 창에 해당 작업 영역 정보가 표시됩니다.

![작업 영역 이름](./media/create-workspace-resource/workspace-name.png)

파란색 링크 텍스트를 클릭하면 새 통합 작업 영역 쿼리 환경을 활용할 수 있는 관련 Log Analytics 작업 영역으로 이동합니다.

> [!NOTE]
> Application Insights 환경 내에서 Application Insights 클래식 리소스 쿼리, 통합 문서 및 로그 기반 경고에 대해 계속 이전 버전과 완전히 호환됩니다. [새 작업 영역 기반 테이블 구조/스키마](apm-tables.md)를 쿼리/확인하려면 먼저 Log Analytics 작업 영역으로 이동해야 합니다. Application Insights 창 내에서 **로그(분석)** 를 선택하면 클래식 Application Insights 쿼리 환경에 액세스할 수 있습니다.

## <a name="copy-the-connection-string"></a>연결 문자열 복사

[연결 문자열](./sdk-connection-string.md?tabs=net)은 원격 분석 데이터를 연결하려는 리소스를 식별합니다. 또한 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.

## <a name="monitoring-configuration"></a>모니터링 구성

작업 영역 기반 Application Insights 리소스를 만든 후에는 모니터링을 비교적 간단하게 구성할 수 있습니다.

### <a name="code-based-application-monitoring"></a>코드 기반 애플리케이션 모니터링

코드 기반 애플리케이션 모니터링의 경우 적절한 Application Insights SDK를 설치하고, 새로 만든 리소스에 대한 계측 키 또는 연결 문자열을 가리키도록 합니다.  

코드 기반 모니터링을 위해 Application Insights SDK를 설정하는 방법에 대한 자세한 내용은 언어/프레임워크 관련 설명서를 참조하세요.

- [ASP.NET](./asp-net.md)
- [ASP.NET Core](./asp-net-core.md)
- [백그라운드 작업 및 최신 콘솔 애플리케이션(.NET/.NET Core)](./worker-service.md)
- [클래식 콘솔 애플리케이션(.NET)](./console.md) 
- [Java](./java-in-process-agent.md)
- [JavaScript](./javascript.md)
- [Node.JS](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>코드 없는 모니터링 및 Visual Studio 리소스 만들기

Azure Functions 및 Azure App Service와 같은 서비스의 코드 없는 모니터링을 위해 먼저 작업 영역 기반 Application Insights 리소스를 만든 다음 모니터링 구성 단계에서 해당 리소스를 가리켜야 합니다.

이러한 서비스는 자체 리소스 생성 프로세스 내에 새 Application Insights 리소스를 만드는 옵션을 제공하지만 이러한 UI 옵션을 통해 만든 리소스는 현재 클래식 Application Insights 환경으로 제한됩니다.

ASP.NET 및 ASP.NET Core용 Visual Studio의 Application Insights 리소스 생성 환경에도 동일하게 적용됩니다. Visual Studio 모니터링 사용 UI에서 기존 작업 영역 기반 리소스를 선택해야 합니다. Visual Studio 내에서 새 리소스 만들기를 선택하면 클래식 Application Insights 리소스만 만들 수 있습니다.

## <a name="creating-a-resource-automatically"></a>자동으로 리소스 만들기

### <a name="azure-cli"></a>Azure CLI

미리 보기 Application Insights Azure CLI 명령에 액세스하려면 먼저 다음을 실행해야 합니다.

```azurecli
 az extension add -n application-insights
```

`az extension add` 명령을 실행하지 않으면 다음 오류 메시지가 표시됩니다. `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

이제 다음을 실행하여 Application Insights 리소스를 만들 수 있습니다.

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>예제

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

이 명령에 대한 전체 Azure CLI 설명서는 [Azure CLI 설명서](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)를 참조하세요.

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights` PowerShell 명령은 현재 작업 영역 기반 Application Insights 리소스 만들기를 지원하지 않습니다. PowerShell을 사용하여 작업 영역 기반 리소스를 만들려면 아래 Azure Resource Manager 템플릿을 사용하고 PowerShell을 사용하여 배포할 수 있습니다.

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

## <a name="export-telemetry"></a>원격 분석 내보내기

작업 영역 기반 리소스에는 레거시 연속 내보내기 기능이 지원되지 않습니다. 대신 Application Insights 리소스 내에서 **진단 설정** > **진단 설정 추가** 를 선택합니다. 모든 테이블 또는 테이블 하위 집합을 선택하여 스토리지 계정에 보관하거나 Azure Event Hub로 스트리밍할 수 있습니다.

> [!NOTE]
> 현재 원격 분석 내보내기에 대한 추가 요금은 없습니다. 이 기능에 대한 가격 책정 정보는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에서 제공됩니다.  청구가 시작되기 전에 알림이 전송됩니다. 알림 기간이 끝난 후에도 원격 분석 내보내기를 계속 사용 하도록 선택 하는 경우 해당 요금에 대 한 요금이 청구 됩니다. 

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색](../essentials/metrics-charts.md)
* [분석 쿼리 작성](../logs/log-query-overview.md)
