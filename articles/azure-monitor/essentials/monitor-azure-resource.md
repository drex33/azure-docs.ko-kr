---
title: Azure Monitor로 Azure 리소스 모니터링 | Microsoft Docs
description: Azure에서 Azure Monitor를 사용하여 리소스의 모니터링 데이터를 수집하고 분석하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2021
ms.openlocfilehash: 32c1b2d6cd2d5574e03cdb6ad65900d8df598b2b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252766"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure Monitor를 사용하여 Azure 리소스 모니터링
Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure 리소스에서 생성되는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터에 대해 분석하고 경고하는 방법을 설명합니다.

> [!IMPORTANT]
> 이 문서는 Azure Monitor를 사용하는 Azure의 모든 서비스에 적용됩니다. VM 및 App Service를 포함한 컴퓨팅 리소스는 여기에 설명된 것과 동일한 모니터링 데이터를 생성하지만, 로그 및 메트릭을 생성할 수 있는 게스트 운영 체제도 있습니다. 이 데이터를 수집하고 분석하는 방법에 대한 자세한 내용은 이와 같은 서비스에 대한 모니터링 설명서를 참조하세요.

## <a name="what-is-azure-monitor"></a>Azure Monitor란 무엇인가요?
Azure의 전체 스택 모니터링 서비스인 Azure Monitor는 다른 클라우드 및 온-프레미스의 리소스 외에도 Azure 리소스를 모니터링하는 전체 기능 세트를 제공합니다. [Azure Monitor 데이터 플랫폼](../data-platform.md)은 데이터를 [로그](../logs/data-platform-logs.md)와 [메트릭](../essentials/data-platform-metrics.md)에 수집하여 전체 모니터링 도구 세트로 데이터를 함께 분석할 수 있습니다. [Azure Monitor에서 모니터링하는 항목](../monitor-reference.md)에서 Azure Monitor가 모니터링할 수 있는 애플리케이션과 서비스의 전체 목록을 참조하세요.

Azure 리소스를 만들자마자 Azure Monitor가 사용하도록 설정되며, [Azure Portal에서 보고 분석](#monitoring-in-the-azure-portal)할 수 있는 메트릭 및 활동 로그를 수집하기 시작합니다. 일부 구성에서는 추가 모니터링 데이터를 수집하고 추가 기능을 사용하도록 설정할 수 있습니다. 구성 요구 사항에 대한 자세한 내용은 아래의 [모니터링 데이터](#monitoring-data)를 참조하세요.


## <a name="costs-associated-with-monitoring"></a>모니터링과 관련된 비용
기본적으로 수집되는 모니터링 데이터의 분석 비용은 없습니다. 여기에는 다음이 포함됩니다.

- 플랫폼 메트릭을 수집하고 메트릭 탐색기를 사용하여 분석합니다.
- 활동 로그를 수집하고 Azure Portal에서 분석합니다.
- 활동 로그 경고 규칙을 만듭니다.

메트릭 수집 및 내보내기에 대 한 Azure Monitor 비용은 없습니다. 리소스 [로그 지원 범주](./resource-logs-categories.md)에 나열 된 리소스 로그의 특정 범주를 내보내는 데 요금이 부과 될 수 있습니다. 메트릭 및 로그에 대해 선택한 대상과 관련 된 비용이 있을 수도 있습니다.

- Log Analytics 작업 영역에서 메트릭과 로그를 수집할 때 데이터 수집 및 보존과 관련 된 비용입니다. [Log Analytics에 대한 Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.
- Azure 저장소 계정에 대 한 메트릭 및 로그를 수집 하는 경우 데이터 저장소와 관련 된 비용입니다. [Blob Storage에 대한 Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.
- Azure Event Hubs에 메트릭 및 로그를 전달할 때 이벤트 허브 스트림과 관련 된 비용입니다. [Azure Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요.

다음과 관련 된 Azure Monitor 비용이 있을 수 있습니다. [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

- 로그 쿼리를 실행합니다.
- 메트릭 또는 로그 쿼리 경고 규칙을 만듭니다.
- 경고 규칙에서 알림을 보냅니다.
- API를 통해 메트릭에 액세스합니다.

## <a name="monitoring-data"></a>데이터 모니터링
Azure의 리소스는 다음 다이어그램에 표시된 [로그](../logs/data-platform-logs.md) 및 [메트릭](../essentials/data-platform-metrics.md)을 생성합니다. 각 Azure 서비스에 대한 설명서를 참조하여 이와 같은 서비스에서 생성하는 특정 데이터와 제공하는 모든 추가 솔루션 또는 인사이트를 확인하세요.

![개요](media/monitor-azure-resource/logs-metrics.png)



- [플랫폼 메트릭](../essentials/data-platform-metrics.md) - 정기적으로 자동 수집되며 특정 시간에 리소스의 일부 측면을 설명하는 수치 값입니다. 
- [리소스 로그](./platform-logs-overview.md) - Azure 리소스(데이터 평면) 내에서 수행된 작업과 관련된 인사이트를 제공합니다. 예를 들어 키 자격 증명 모음에서 비밀을 가져오거나 데이터베이스에 요청할 수 있습니다. 리소스 로그의 내용과 구조는 Azure 서비스 및 리소스 종류에 따라 달라집니다.
- [활동 로그](./platform-logs-overview.md) - 외부(관리 평면)에서 구독의 각 Azure 리소스에 대한 작업과 관련된 인사이트를 제공합니다. 예를 들어 새 리소스를 만들거나 가상 머신을 시작할 수 있습니다. 구독의 리소스에 대해 수행된 모든 쓰기 작업(PUT, POST, DELETE)과 관련하여 무엇을 누가 언제 했는지에 대한 정보입니다.


## <a name="configuration-requirements"></a>구성 요구 사항

### <a name="configure-monitoring"></a>모니터링 구성
일부 모니터링 데이터는 자동으로 수집되지만 요구 사항에 따라 일부 구성을 수행해야 할 수도 있습니다. 각 모니터링 데이터 유형에 대한 구체적인 정보는 아래 정보를 참조하세요.

- [플랫폼 메트릭](../essentials/data-platform-metrics.md) - 플랫폼 메트릭은 구성할 필요 없이 [Azure Monitor 메트릭](../essentials/data-platform-metrics.md)에 자동으로 수집됩니다. 항목을 Azure Monitor 로그에 보내거나 Azure 외부에 전달하려면 진단 설정을 만듭니다.
- [리소스 로그](./platform-logs-overview.md) - 리소스 로그는 Azure 리소스에 의해 자동으로 생성되지만 진단 설정 없이는 수집되지 않습니다.  항목을 Azure Monitor 로그에 보내거나 Azure 외부에 전달하려면 진단 설정을 만듭니다.
- [활동 로그](./platform-logs-overview.md) - 구성할 필요 없이 활동 로그가 자동으로 수집되며 Azure Portal에서 볼 수 있습니다. 활동 로그를 Azure Monitor 로그에 복사하거나 Azure 외부에 전달하려면 진단 설정을 만듭니다.

### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
Azure Monitor 로그에 데이터를 수집하려면 Log Analytics 작업 영역이 필요합니다. 새 작업 영역을 만들어 서비스 모니터링을 신속하게 시작할 수 있지만, 다른 서비스에서 데이터를 수집하는 작업 영역을 사용하는 데 따른 이점이 있을 수 있습니다. 작업 영역 만들기에 대한 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../logs/quick-create-workspace.md)를 참조하고, 요구 사항에 맞는 최선의 작업 영역 설계를 결정하려면 [Designing your Azure Monitor Logs deployment](../logs/design-logs-deployment.md)(Azure Monitor 로그 배포 설계)를 참조하세요. 조직에서 기존 작업 영역을 사용하는 경우 [Azure Monitor의 로그 데이터 및 작업 영역에 대한 액세스 관리](../logs/manage-access.md)에 설명된 대로 적절한 사용 권한이 필요합니다. 





## <a name="diagnostic-settings"></a>진단 설정
진단 설정은 특정 리소스에 대한 리소스 로그 및 메트릭을 보낼 위치를 정의합니다. 가능한 대상은 다음과 같습니다.

- [Log Analytics 작업 영역](./resource-logs.md#send-to-log-analytics-workspace) - 강력한 로그 쿼리를 사용하여 Azure Monitor에서 수집한 다른 모니터링 데이터로 데이터를 분석할 수 있으며 로그 경고 및 시각화와 같은 기타 Azure Monitor 기능을 활용할 수 있습니다. 
- [이벤트 허브](./resource-logs.md#send-to-azure-event-hubs) - 타사 SIEM 및 기타 로그 분석 솔루션과 같은 외부 시스템으로 데이터를 스트리밍합니다. 
- [Azure 스토리지 계정](./resource-logs.md#send-to-azure-storage) - 감사, 정적 분석 또는 백업에 유용합니다.

[Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../essentials/diagnostic-settings.md)의 절차에 따라 Azure Portal을 통해 진단 설정을 만들고 관리할 수 있습니다. 템플릿에서 진단 설정을 정의하고, 생성되는 리소스에 대한 완전한 모니터링이 가능하도록 설정하려면 [Azure에서 Resource Manager 템플릿을 사용하여 진단 설정 만들기](./resource-manager-diagnostic-settings.md)를 참조하세요.


## <a name="monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링
 Azure Portal의 리소스 메뉴에서 대부분의 Azure 리소스에 대한 모니터링 데이터에 액세스할 수 있습니다. 이를 통해 표준 Azure Monitor 도구를 사용하여 단일 리소스의 데이터에 액세스할 수 있습니다. 일부 Azure 서비스는 다른 옵션을 제공하므로 추가 정보는 해당 서비스에 대한 설명서를 참조해야 합니다. **Azure Monitor** 메뉴를 사용하여 모니터링되는 모든 리소스의 데이터를 분석할 수 있습니다. 

### <a name="overview"></a>개요
많은 서비스가 작업을 한눈에 파악할 수 있도록 **개요** 페이지에 모니터링 데이터를 포함할 것입니다. 개요는 일반적으로 Azure Monitor 메트릭에 저장된 플랫폼 메트릭의 하위 세트를 기반으로 합니다. 다른 모니터링 옵션은 일반적으로 서비스 메뉴의 **모니터링** 섹션에서 사용할 수 있습니다.

![개요 페이지](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>인사이트 및 솔루션 
일부 서비스는 Azure Monitor 표준 기능 외의 도구를 제공합니다. [인사이트](../monitor-reference.md)는 Azure Monitor 데이터 플랫폼과 표준 기능을 기반으로 한 사용자 지정된 모니터링 환경을 제공합니다. [솔루션](../insights/solutions.md)은 Azure Monitor 로그를 기반으로 한 미리 정의된 모니터링 논리를 제공합니다. 

서비스에 Azure Monitor 인사이트가 있는 경우에는 각 리소스 메뉴의 **모니터링** 에서 액세스할 수 있습니다. **Azure Monitor** 메뉴에서 모든 인사이트 및 솔루션에 액세스할 수 있습니다.

![Azure Portal의 인사이트](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>메트릭
대부분의 서비스에 대한 **메트릭** 메뉴 항목에서 제공되는 [메트릭 탐색기](./metrics-getting-started.md)를 사용하여 Azure Portal의 메트릭을 분석합니다. 이 도구를 사용하면 개별 메트릭에 대해 작업하거나 여러 메트릭을 결합하여 상관 관계 및 추세를 식별할 수 있습니다. 

- 메트릭 탐색기 사용에 대한 기본 사항은 [Azure 메트릭 탐색기 시작](./metrics-getting-started.md)을 참조하세요.
- 여러 메트릭을 사용하고 필터 및 분할을 적용하는 것과 같은 메트릭 탐색기의 고급 기능에 대해서는 [Azure 메트릭 탐색기의 고급 기능](../essentials/metrics-charts.md)을 참조하세요.

![Azure Portal의 메트릭 탐색기](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>활동 로그 
현재 리소스로 설정된 초기 필터를 사용하여 Azure Portal의 활동 로그에서 항목을 봅니다. 활동 로그를 Log Analytics 작업 영역에 복사하여 로그 쿼리 및 통합 문서에서 사용할 수 있도록 합니다. 

- 다양한 방법을 사용하여 활동 로그를 보고 항목을 검색하는 방법에 대한 자세한 내용은 [Azure 활동 로그 이벤트 보기 및 검색](../essentials/activity-log.md#view-the-activity-log)을 참조하세요.
- 기록되는 특정 이벤트에 대해서는 해당 Azure 서비스의 설명서를 참조하세요.

![활동 로그](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor 로그
Azure Monitor 로그는 강력한 쿼리 도구를 사용하여 분석을 위해 여러 서비스 및 기타 데이터 원본의 로그와 메트릭을 통합합니다. 위에서 설명된 것처럼 Azure Monitor의 Log Analytics 작업 영역으로 플랫폼 메트릭, 활동 로그 및 리소스 로그를 수집하는 진단 설정을 만듭니다.

[Log Analytics](../logs/log-analytics-tutorial.md)를 사용하면 [로그 쿼리](../logs/log-query-overview.md)로 작업할 수 있습니다. 로그 쿼리는 완전한 기능을 갖춘 쿼리 언어를 사용하여 로그 데이터에 대한 고급 분석을 수행할 수 있는 Azure Monitor의 강력한 기능입니다. Azure 리소스에 대한 **모니터링** 메뉴의 **로그** 에서 Log Analytics를 열어 리소스를 [쿼리 범위](../logs/scope.md#query-scope)로 사용하면서 로그 쿼리로 작업할 수 있습니다. 이를 통해 해당 리소스에 대한 여러 테이블의 데이터를 분석할 수 있습니다. Azure Monitor 메뉴에서 **로그** 를 사용하여 모든 리소스에 대한 로그에 액세스할 수 있습니다. 

- 로그 쿼리를 작성하는 데 사용되는 쿼리 언어를 사용하는 방법에 대한 자습서는 [Azure Monitor에서 로그 쿼리 시작](../logs/get-started-queries.md)을 참조하세요.
- Azure Monitor 로그에서 리소스 로그를 수집하는 방법에 대한 자세한 내용과 쿼리에서 액세스하는 방법에 대한 자세한 내용은 [Azure Monitor의 Log Analytics 작업 영역에서 Azure 리소스 로그 수집](./resource-logs.md#send-to-log-analytics-workspace)을 참조하세요.
- Azure Monitor 로그에서 리소스 로그 데이터를 구성하는 방법에 대한 설명은 [컬렉션 모드](./resource-logs.md#send-to-log-analytics-workspace)를 참조하세요.
- Azure Monitor 로그의 해당 테이블에 대한 자세한 내용은 각 Azure 서비스에 대한 설명서를 참조하세요.

![Azure Portal의 Log Analytics](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>명령줄에서 모니터링
명령줄에서 리소스에서 수집한 모니터링 데이터에 액세스하거나, [Azure PowerShell](/powershell/azure/) 또는 [Azure 명령줄 인터페이스](/cli/azure/)를 사용하여 스크립트에 포함할 수 있습니다. 

- CLI에서 메트릭 데이터에 액세스하는 방법은 [CLI 메트릭 참조](/cli/azure/monitor/metrics)를 참조하세요.
- CLI에서 로그 쿼리를 사용하여 Azure Monitor 로그 데이터에 액세스하는 방법은 [CLI Log Analytics 참조](/cli/azure/monitor/log-analytics)를 참조하세요.
- Azure PowerShell에서 메트릭 데이터에 액세스하는 방법은 [Azure PowerShell 메트릭 참조](/powershell/module/azurerm.insights/get-azurermmetric)를 참조하세요.
- Azure PowerShell의 로그 쿼리를 사용하여 Azure Monitor 로그 데이터에 액세스하는 방법은 [Azure PowerShell 로그 쿼리 참조](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)를 참조하세요.

## <a name="monitoring-from-rest-api"></a>REST API에서 모니터링
사용자 지정 애플리케이션에서 REST API를 사용하여 리소스에서 수집한 모니터링 데이터를 포함합니다.

- Azure Monitor REST API에서 메트릭에 액세스하는 방법에 대한 자세한 내용은 [Azure Monitoring REST API 연습](./rest-api-walkthrough.md)을 참조하세요.
- Azure Monitor에서 로그 쿼리를 사용하여 Azure PowerShell 로그 데이터에 액세스하는 방법에 대한 자세한 내용은 [Azure Log Analytics REST API](https://dev.loganalytics.io/)를 참조하세요.

## <a name="alerts"></a>경고
[경고](../alerts/alerts-overview.md)는 모니터링 데이터에서 중요한 조건이 발견되면 사전에 알려주고 조치를 취할 수도 있습니다. 경고의 대상, 경고를 만들지 여부를 결정하는 조건 및 수행할 대응 작업을 정의하는 경고 규칙을 만듭니다.

다양한 종류의 모니터링 데이터가 다양한 종류의 경고 규칙에 사용됩니다.

- [활동 로그 경고](../alerts/alerts-activity-log.md) - 활동 로그에서 특정 조건과 일치하는 항목이 만들어지면 경고를 생성합니다. 이렇게 하여 특정 리소스 종류가 만들어지거나 구성 변경이 실패한 경우 등에 사용자에게 알릴 수 있습니다.
- [메트릭 경고](../alerts/alerts-metric.md) - 메트릭 값이 특정 임계값을 초과하는 경우 경고를 만듭니다. 메트릭 경고는 다른 경고보다 응답성이 뛰어나므로 문제가 해결되면 자동으로 해결될 수 있습니다.
- [로그 쿼리 경고](../alerts/alerts-log.md) - 정기적으로 로그 쿼리를 실행하고 특정 조건이 발견되면 경고를 만듭니다. 이를 통해 여러 데이터 세트 등에서 복잡한 분석을 수행할 수 있습니다.

리소스 메뉴에서 **경고** 를 사용하여 경고를 보고 해당 리소스에 대한 경고 규칙을 관리합니다. 활동 로그 경고 및 메트릭 경고만 개별 Azure 리소스를 대상으로 사용합니다. 로그 쿼리 경고는 Log Analytics 작업 영역을 대상으로 사용하며 해당 작업 영역에 저장된 모든 로그에 액세스할 수 있는 쿼리를 기준으로 합니다. Azure Monitor 메뉴를 사용하여 모든 리소스에 대한 경고를 보고 관리하며 로그 쿼리 경고 규칙을 관리합니다.

- 경고 규칙을 만드는 방법에 대한 자세한 내용은 위에서 여러 종류의 경고에 대한 문서를 참조하세요.
- 경고에 대한 응답을 관리할 수 있는 작업 그룹을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 작업 그룹 만들기 및 관리](../alerts/action-groups.md)를 참조하세요.



## <a name="next-steps"></a>다음 단계

* 다른 Azure 서비스에 해당하는 리소스 로그에 대한 자세한 내용은 [Azure 리소스 로그를 지원하는 서비스, 스키마 및 범주](./resource-logs-schema.md)를 참조하세요.