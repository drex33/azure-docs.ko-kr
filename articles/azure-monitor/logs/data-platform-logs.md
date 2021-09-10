---
title: Azure Monitor 로그
description: 모니터링 데이터의 고급 분석에 사용되는 Azure Monitor Logs의 기본 사항을 알아봅니다.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 616d1f5cf4fc2806d56c34335e66418452c7684b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566748"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor 로그 개요
Azure Monitor 로그는 [모니터링되는 리소스](../monitor-reference.md)에서 로그와 성능 데이터를 수집하고 구성하는 Azure Monitor의 기능입니다. 여러 원본의 데이터를 단일 작업 영역으로 통합할 수 있습니다. 이러한 원본에는 다음 항목이 포함됩니다.

- Azure 서비스의 [플랫폼 로그](../essentials/platform-logs-overview.md)
- [가상 머신 에이전트](../agents/agents-overview.md)의 로그 및 성능 데이터
- [애플리케이션](../app/app-insights-overview.md)의 사용량 및 성능 데이터 

그런 다음, 수백만 개의 레코드를 빠르게 분석할 수 있는 정교한 쿼리 언어를 사용하여 데이터를 분석할 수 있습니다. 

특정 레코드 집합을 검색하거나 정교한 데이터 분석을 수행하여 모니터링 데이터에서 중요한 패턴을 식별하는 간단한 쿼리를 수행할 수 있습니다. Log Analytics를 사용하여 대화형으로 로그 쿼리 및 해당 결과를 작업하고, 이를 경고 규칙에서 사용하여 문제를 사전에 통보하거나, 통합 문서 또는 대시보드에서 결과를 시각화할 수 있습니다.

> [!NOTE]
> Azure Monitor 로그는 Azure Monitor를 지원하는 데이터 플랫폼의 절반입니다. 다른 절반은 시계열 데이터베이스에 숫자 데이터를 저장하는 [Azure Monitor 메트릭](../essentials/data-platform-metrics.md)입니다. 숫자 데이터는 Azure Monitor Logs의 데이터보다 가볍습니다. Azure Monitor 메트릭은 근 실시간 시나리오를 지원할 수 있으므로 문제를 경고하고 신속하게 감지하는 데 유용합니다. 
>
> Azure Monitor 메트릭은 특정 구조의 숫자 데이터만 저장할 수 있으며 Azure Monitor 로그는 고유한 구조가 있는 다양한 데이터 형식을 저장할 수 있습니다. Azure Monitor 메트릭 데이터 분석에는 사용할 수 없는 로그 쿼리를 사용하여 Azure Monitor 로그 데이터에 대한 복잡한 분석을 수행할 수도 있습니다.

## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Monitor 로그로 무엇을 할 수 있나요?
다음 표에는 Azure Monitor 로그를 사용할 수 있는 몇 가지 방법이 나와 있습니다.

|  | 설명 |
|:---|:---|
| **분석** | Azure Portal에서 [Log Analytics](./log-analytics-tutorial.md)를 사용하여 [로그 쿼리](./log-query-overview.md)를 작성하고 강력한 분석 엔진을 사용하여 로그 데이터를 대화형으로 분석합니다. |
| **경고** | 쿼리의 결과가 특정 결과와 일치할 때 알림을 보내거나 [자동화된 작업](../alerts/action-groups.md)을 수행하는 [로그 경고 규칙](../alerts/alerts-log.md)을 구성합니다. |
| **시각화** | 테이블 또는 차트로 렌더링된 쿼리 결과를 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정합니다.<br>[통합 문서](../visualize/workbooks-overview.md)를 만들어 대화형 보고서의 여러 데이터 집합을 결합합니다. <br>쿼리의 결과를 [Power BI](../visualize/powerbi.md)로 내보내 서로 다른 시각화를 사용하고 Azure 외부의 사용자와 공유합니다.<br>쿼리의 결과를 [Grafana](../visualize/grafana-plugin.md)로 내보내 대시보드를 사용하고 다른 데이터 원본과 결합합니다.|
| **정보 활용** | [인사이트](../monitor-reference.md#insights-and-core-solutions)는 특정 애플리케이션 및 서비스에 대한 사용자 지정 모니터링 환경을 제공합니다.  |
| **장치** | [Azure CLI](/cli/azure/monitor/log-analytics)를 사용하여 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[PowerShell cmdlet](/powershell/module/az.operationalinsights)을 사용하여 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[REST API](https://dev.loganalytics.io/)를 사용하여 사용자 지정 애플리케이션에서 로그 쿼리 결과에 액세스합니다. |
| **내보내기** | Azure Storage 계정 또는 Azure Event Hubs에 대한 [로그 데이터의 자동화된 내보내기](./logs-data-export.md)를 구성합니다.<br>워크플로를 빌드하여 로그 데이터를 검색하고 [Azure Logic Apps](./logicapp-flow-connector.md)를 사용하여 이를 외부 위치에 복사합니다. |

![Azure Monitor 로그의 개요를 보여주는 다이어그램](media/data-platform-logs/logs-overview.png)

## <a name="data-collection"></a>데이터 수집
Log Analytics 작업 영역을 만든 후에는 원본을 구성하여 데이터를 전송해야 합니다. 데이터가 자동으로 수집되지 않습니다. 

이 구성은 데이터 원본에 따라 달라집니다. 예를 들면 다음과 같습니다.

- Azure 리소스에서 작업 영역으로 리소스 로그를 전송하는 [진단 설정을 만듭니다](../essentials/diagnostic-settings.md). 
- [VM 인사이트](../vm/vminsights-enable-overview.md)를 사용하도록 설정하여 가상 머신에서 데이터를 수집합니다. 
- [작업 영역에서 데이터 원본을 구성](../agents/data-sources.md)하여 추가 이벤트 및 성능 데이터를 수집합니다.

Azure Monitor Logs로 데이터를 전송하도록 구성할 수 있는 데이터 원본의 전체 목록은 [Azure Monitor에서 모니터링되는 항목](../monitor-reference.md)을 참조하세요.

## <a name="log-analytics-and-workspaces"></a>Log Analytics 및 작업 영역
Log Analytics는 Azure Portal의 도구입니다. 이를 사용하여 로그 쿼리를 편집 및 실행하고 결과를 대화형으로 분석할 수 있습니다. 그런 다음, 해당 쿼리를 사용하여 로그 쿼리 경고 및 통합 문서와 같은 Azure Monitor의 다른 기능을 지원할 수 있습니다. Azure Monitor 메뉴의 **로그** 옵션 또는 Azure Portal의 기타 대부분의 서비스에서 Log Analytics에 액세스할 수 있습니다.

Log Analytics에 대한 설명은 [Azure Monitor의 Log Analytics 개요](./log-analytics-overview.md)를 참조하세요. 간단한 로그 쿼리를 만들고 해당 결과를 분석하는 Log Analytics 기능을 사용하는 과정을 알아보려면 [Log Analytics 자습서](./log-analytics-tutorial.md)를 참조하세요.

Azure Monitor Logs는 수집하는 데이터를 하나 이상의 [Log Analytics 작업 영역](./design-logs-deployment.md)에 저장합니다. 작업 영역은 다음을 정의합니다.

- 데이터의 지리적 위치
- 데이터에 액세스할 수 있는 사용자를 정의하는 액세스 권한
- 가격 책정 계층 및 데이터 보존과 같은 구성 설정  

Azure Monitor 로그를 사용하려면 작업 영역을 하나 이상 만들어야 합니다. 단일 작업 영역이 모든 모니터링 데이터를 수용하기 충분하거나 요구 사항에 따라 여러 작업 영역을 만들도록 선택할 수 있습니다. 예를 들어, 프로덕션 데이터에 대해 하나의 작업 영역이 있고 테스트에 다른 작업 영역이 있을 수 있습니다. 

새 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](./quick-create-workspace.md)를 참조하세요. 여러 작업 영역을 만들기 위한 고려 사항은 [Azure Monitor Logs 배포 디자인](design-logs-deployment.md)을 참조하세요.

## <a name="log-queries"></a>로그 쿼리
데이터를 처리하고 결과를 반환하는 읽기 전용 요청인 로그 쿼리를 통해 Log Analytics 작업 영역에서 데이터를 검색합니다. 로그 쿼리는 [KQL(Kusto 쿼리 언어)](/azure/data-explorer/kusto/query/)로 작성됩니다. KQL은 Azure Data Explorer에서 사용하는 것과 동일한 쿼리 언어입니다. 

Log Analytics에서 로그 쿼리를 작성하여 결과를 대화형으로 분석하고, 이 정보를 경고 규칙에서 사용하여 문제를 사전에 통보하거나, 해당 결과를 통합 문서 또는 대시보드에 포함할 수 있습니다. 인사이트에는 해당 보기와 통합 문서를 지원하는 미리 작성한 쿼리가 포함되어 있습니다.

로그 쿼리의 사용 위치와 시작을 안내하는 자습서 및 기타 설명서에 대한 참조 목록을 보려면 [Azure Monitor의 로그 쿼리](./log-query-overview.md)를 참조하세요.

![Log Analytics의 쿼리를 보여주는 스크린샷](media/data-platform-logs/log-analytics.png)

## <a name="data-structure"></a>데이터 구조
로그 쿼리는 Log Analytics 작업 영역에서 데이터를 검색합니다. 각 작업 영역에는 여러 행의 데이터를 포함하는 별도의 열로 구성된 여러 테이블이 포함되어 있습니다. 각 테이블은 고유한 열 집합으로 정의됩니다. 데이터 원본에서 제공하는 데이터 행은 해당 열을 공유합니다. 

[![Azure Monitor 로그 구조를 보여주는 다이어그램](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)

Application Insights의 로그 데이터는 Azure Monitor 로그에도 저장되지만 애플리케이션이 구성된 방식에 따라 다르게 저장됩니다. 

- 작업 영역 기반 애플리케이션의 경우 데이터는 표준 테이블 집합의 Log Analytics 작업 영역에 저장됩니다. 데이터 형식에는 애플리케이션 요청, 예외 및 페이지 보기가 포함됩니다. 여러 애플리케이션에서 동일한 작업 영역을 사용할 수 있습니다. 

- 클래식 애플리케이션의 경우 데이터가 Log Analytics 작업 영역에 저장되지 않습니다. 동일한 쿼리 언어를 사용하며 Azure Portal에서 동일한 Log Analytics 도구를 사용하여 쿼리를 만들고 실행합니다. 클래식 애플리케이션의 데이터 항목은 서로 별도로 저장됩니다. 테이블 이름과 열 이름이 서로 다르지만 일반적인 구조는 작업 영역 기반 애플리케이션과 동일합니다. 

작업 영역 기반 및 클래식 애플리케이션의 스키마에 대한 자세한 내용은 [작업 영역 기반 리소스 변경 내용](../app/apm-tables.md)을 참조하세요.

> [!NOTE]
> 클래식 Application Insights 환경에는 리소스 쿼리, 통합 문서 및 로그 기반 경고에 대한 이전 버전과의 호환성이 포함됩니다. [새 작업 영역 기반 테이블 구조 또는 스키마](../app/apm-tables.md)를 쿼리하거나 확인하려면 먼저 Log Analytics 작업 영역으로 이동해야 합니다. 미리 보기 중에 Application Insights 창 내에서 **로그** 를 선택하면 클래식 Application Insights 쿼리 환경에 액세스할 수 있습니다. 자세한 내용은 [쿼리 범위](./scope.md)를 참조하세요.

[![Application Insights에 대한 Azure Monitor 로그 구조를 보여주는 다이어그램](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="relationship-to-azure-data-explorer"></a>Azure Data Explorer와의 관계
Azure Monitor 로그는 Azure Data Explorer를 기반으로 합니다. Log Analytics 작업 영역은 Azure Data Explorer 내의 데이터베이스와 거의 동일합니다. 테이블은 동일하게 구조화되며 둘 다 KQL을 사용합니다. 

Log Analytics를 사용하여 Azure Portal에서 Azure Monitor 쿼리로 작업하는 환경은 Azure Data Explorer 웹 UI를 사용하는 환경과 비슷합니다. [Log Analytics 작업 영역의 데이터를 Azure Data Explorer 쿼리에 포함](/azure/data-explorer/query-monitor-data)할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

- Log Analytics 작업 영역에서 데이터를 검색하고 분석하는 [로그 쿼리](./log-query-overview.md)에 대해 알아봅니다.
- [Azure Monitor의 메트릭](../essentials/data-platform-metrics.md)을 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](../agents/data-sources.md)에 대해 알아봅니다.
