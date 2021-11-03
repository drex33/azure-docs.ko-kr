---
title: Azure Monitor를 사용하여 SSIS 작업 모니터링
description: Azure Monitor를 사용 하 여 Azure Data Factory에서 SSIS 작업을 모니터링 하는 방법에 대해 알아봅니다.
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 8e3160f3bd6fd7cd68c8fe26257090a70d9a43dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033155"
---
# <a name="monitor-ssis-operations-with-azure-monitor"></a>Azure Monitor를 사용하여 SSIS 작업 모니터링

SSIS 워크로드를 리프트 앤 시프트하려면 다음을 지원하는 [ADF에서 SSIS IR을 프로비전](./tutorial-deploy-ssis-packages-azure.md)합니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

프로비전된 후에는 [Azure PowerShell을 사용하거나 ADF 포털의 **모니터** 허브에서 SSIS IR 운영 상태를 확인](./monitor-integration-runtime.md#azure-ssis-integration-runtime)할 수 있습니다. 프로젝트 배포 모델을 사용하면 SSIS 패키지 실행 로그가 SSISDB 내부 테이블 또는 뷰에 저장되므로 SSMS와 같은 지정된 도구를 사용하여 해당 로그를 쿼리, 분석 및 시각적으로 표현할 수 있습니다. 패키지 배포 모델을 사용하면 SSIS 패키지 실행 로그를 파일 시스템 또는 Azure Files에 CSV 파일로 저장할 수 있습니다. 이 로그는 다른 지정된 도구를 사용하여 구문 분석하고 처리한 후에야 쿼리, 분석 및 시각적 표현이 가능합니다.

이제는 [Azure Monitor](../azure-monitor/data-platform.md) 통합을 통해, Azure Portal의 SSIS IR 작업 및 SSIS 패키지 실행에서 생성된 모든 메트릭 및 로그를 쿼리, 분석 및 시각적으로 표현할 수 있습니다. 또한 경고를 발생시킬 수도 있습니다.

## <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>SSIS 작업을 위한 진단 설정 및 작업 영역 구성

SSIS IR 작업 및 SSIS 패키지 실행에서 생성된 모든 메트릭과 로그를 Azure Monitor로 보내려면 [ADF에 대한 진단 설정 및 작업 영역을 구성](monitor-configure-diagnostics.md)해야 합니다.

## <a name="ssis-operational-metrics"></a>SSIS 운영 메트릭

SSIS 운영 [메트릭은](../azure-monitor/essentials/data-platform-metrics.md) SSIS IR 시작 및 중지 작업의 상태는 물론 특정 시점의 SSIS 패키지 실행을 설명하는 성능 카운터 또는 숫자 값입니다. [Azure Monitor의 ADF 메트릭](monitor-metrics-alerts.md)에 속합니다.

Azure Monitor에서 ADF에 대한 진단 설정 및 작업 영역을 구성할 때 _AllMetrics_ 확인란을 선택하면 SSIS 운영 메트릭을 [Azure Metrics Explorer를 사용한 대화형 분석](../azure-monitor/essentials/metrics-getting-started.md), [Azure 대시보드의 프레젠테이션](../azure-monitor/app/tutorial-app-dashboards.md) 및 [근 실시간 경고](../azure-monitor/alerts/alerts-metric.md)에 사용할 수 있습니다.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="설정 이름 지정 및 Log Analytics 작업 영역 선택":::

## <a name="ssis-operational-alerts"></a>SSIS 운영 경고

ADF 포털에서 SSIS 운영 메트릭에 대한 경고를 발생시키려면 [ADF **모니터** 허브의 **경고 및 메트릭** 페이지를 선택하고 제공된 단계별 지침을 따릅니다](./monitor-visually.md#alerts).

:::image type="content" source="media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png" alt-text="ADF 포털에서 SSIS 운영 경고 발생":::

Azure Portal에서 SSIS 운영 메트릭에 대한 경고를 발생시키려면 [Azure **모니터** 허브의 **경고** 페이지를 선택하고 제공된 단계별 지침을 따릅니다](monitor-metrics-alerts.md).

:::image type="content" source="media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png" alt-text="Azure Portal에서 SSIS 운영 경고 발생":::

## <a name="ssis-operational-logs"></a>SSIS 운영 로그

SSIS 운영 [로그](../azure-monitor/logs/data-platform-logs.md)는 SSIS IR 운영 및 SSIS 패키지 실행에 의해 생성되는 이벤트이며, 식별된 문제에 대한 충분한 컨텍스트를 제공하고 근본 원인을 분석하는 데 유용합니다. 

Azure Monitor에서 ADF에 대한 진단 설정 및 작업 영역을 구성할 때 관련 SSIS 운영 로그를 선택하여 Azure Data Explorer를 기반으로 하는 Log Analytics에 보낼 수 있습니다. 여기에서 [풍부한 쿼리 언어를 사용한 분석](../azure-monitor/logs/log-query-overview.md), [Azure 대시보드의 프레젠테이션](../azure-monitor/app/tutorial-app-dashboards.md) 및 [근 실시간 경고](../azure-monitor/alerts/alerts-log.md)에 사용할 수 있습니다.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="설정 이름 지정 및 Log Analytics 작업 영역 선택":::

Azure Monitor 및 Log Analytics에서 SSIS 패키지 실행 로그의 스키마 및 콘텐츠는 SSISDB 내부 테이블 또는 뷰의 스키마와 비슷합니다.

| Azure Monitor 로그 범주          | Log Analytics 테이블                     | SSISDB 내부 테이블/뷰              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

SSIS 운영 로그 특성/속성에 대한 자세한 내용은 [ADF의 Azure Monitor 및 Log Analytics 스키마](monitor-schema-logs-events.md)를 참조하십시오.

선택한 SSIS 패키지 실행 로그는 호출 메서드에 관계 없이 항상 Log Analytics로 전송됩니다. 예를 들어, Azure 지원 SSDT에서 패키지 실행은 SSMS, SQL Server 에이전트 또는 기타 지정된 도구의 T-SQL을 통해, ADF 파이프라인에서 SSIS 패키지 실행 활동의 트리거된 실행 또는 디버그 실행으로 호출할 수 있습니다.

Logs Analytics에서 SSIS IR 작업 로그를 쿼리할 때는 각각 `Start/Stop/Maintenance/Heartbeat` 및 `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy`로 설정 된 **OperationName** 및 **ResultType** 속성을 사용할 수 있습니다.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query.png" alt-text="Log Analytics에서 SSIS IR 작업 로그 쿼리":::

SSIS IR 노드 상태를 쿼리하려면 **OperationName** 속성을 `Heartbeat`로 설정하면 됩니다. 각 노드는 일반적으로 상태를 반영하는 **ResultType** 속성을 사용하여 분당 하나의 `Heartbeat` 레코드를 Log Analytics에 보냅니다. 이 속성은 패키지 실행에 사용할 수 있는 경우 `Healthy`이고 그렇지 않은 경우 `Unhealthy`입니다. 예를 들어 SSIS IR에 사용 가능한 노드가 2개 있는 경우 1분 이내에 **ResultType** 속성이 `Healthy`로 설정된 `Heartbeat` 레코드 2개가 항상 표시됩니다.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query-3.png" alt-text="Log Analytics에서 SSIS IR 하트비트 쿼리":::

다음 패턴을 쿼리하여 SSIS IR 노드의 비활성을 검색할 수 있습니다.

* SSIS IR이 아직 실행되고 있는 경우에는 1분 동안 누락된 `Heartbeat` 레코드가 있습니다.
* SSIS IR이 아직 실행되고 있는 경우에는 1분 동안 **ResultType** 속성이 `Unhealthy`로 설정된 `Heartbeat` 레코드가 있습니다.

위의 쿼리를 [경고](../azure-monitor/alerts/alerts-unified-log.md)로 전환하고 [SSIS IR 모니터링 페이지](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)로 이동하여 해당 경고를 받은 시기를 확인할 수 있습니다.

Logs Analytics에서 SSIS 패키지 실행 로그를 쿼리할 때 **OperationId**/**ExecutionId**/**CorrelationId** 속성을 사용하여 조인할 수 있습니다. **OperationId**/**ExecutionId** 는 SSISDB에 저장되지 **않은**/T-SQL을 통해 호출된 패키지와 관련된 모든 작업/실행에 대해 항상 `1`로 설정됩니다.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query2.png" alt-text="Log Analytics에서 SSIS 패키지 실행 로그 쿼리":::

## <a name="next-steps"></a>다음 단계

[로그 및 이벤트 스키마](monitor-schema-logs-events.md)
