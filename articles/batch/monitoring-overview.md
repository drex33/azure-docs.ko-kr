---
title: Azure Batch 모니터링
description: Azure Batch를 위한 Azure 모니터링 서비스, 메트릭, 진단 로그 및 기타 모니터링 기능에 대해 알아봅니다.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: b2b73261b07a7e5eb269c1f58a45f97d99133ac4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768889"
---
# <a name="monitor-batch-solutions"></a>Batch 솔루션 모니터링

[Azure Monitor](../azure-monitor/overview.md) 및 Batch 서비스는 Batch 솔루션을 모니터링하는 다양한 서비스, 도구 및 API를 제공합니다. 이 개요 문서에서는 요구 사항에 적합한 모니터링 방법을 선택하도록 도와줍니다.

## <a name="subscription-level-monitoring"></a>구독 수준 모니터링

Batch 계정을 포함하는 구독 수준에서, [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)는 여러 범주에서 작업 이벤트 데이터를 수집합니다.

특히 Batch 계정의 경우 활동 로그는 계정 생성/삭제 및 키 관리와 관련된 이벤트를 수집합니다.

Azure Portal 활동 로그를 보거나 Azure CLI, PowerShell cmdlet 또는 Azure Monitor REST API 사용하여 이벤트를 쿼리할 수 있습니다. 활동 로그를 내보내거나 [활동 로그 경고](../azure-monitor/alerts/alerts-activity-log.md)를 구성할 수도 있습니다.

## <a name="batch-account-level-monitoring"></a>Batch 계정 수준 모니터링

[Azure Monitor](../azure-monitor/overview.md)의 기능을 사용하여 각 Batch 계정을 모니터링합니다. Azure Monitor는 배치 계정 내에서 풀, 작업, 태스크 등의 리소스에 대한 [메트릭](../azure-monitor/essentials/data-platform-metrics.md), 그리고 필요에 따라 [리소스 로그](../azure-monitor/essentials/resource-logs.md)를 수집합니다. 이 데이터를 수동으로 또는 프로그래밍 방식으로 수집하고 사용하여 Batch 계정의 활동을 모니터링하고 문제를 진단할 수 있습니다. 자세한 내용은 [진단 평가 및 모니터링을 위한 Batch 메트릭, 경고 및 로그](batch-diagnostics.md)를 참조하세요.

> [!NOTE]
> 메트릭은 추가 구성 없이 Batch 계정에서 기본적으로 사용할 수 있으며 30일의 롤링 기록이 있습니다. 리소스 로그를 Log Analytics 작업 영역으로 보내려면 배치 계정에 대한 진단 설정을 만들어야 하며, 리소스 로그 데이터를 저장하거나 처리하는 데 추가 비용이 발생할 수 있습니다.

## <a name="batch-resource-monitoring"></a>Batch 리소스 모니터링

Batch 애플리케이션에서 Batch API를 사용하여 작업, 태스크, 노드, 풀을 포함한 리소스 상태를 모니터링하거나 쿼리할 수 있습니다. 다음은 그 예입니다.

- [상태별 태스크 및 컴퓨팅 노드 카운팅](batch-get-resource-counts.md)
- [Batch 리소스를 효율적으로 나열하는 쿼리 만들기](batch-efficient-list-queries.md)
- [태스크 종속성 만들기](batch-task-dependencies.md)
- [작업 관리자 태스크](/rest/api/batchservice/job/add#jobmanagertask) 사용
- [태스크 상태](/rest/api/batchservice/task/list#taskstate) 모니터링
- [노드 상태](/rest/api/batchservice/computenode/list#computenodestate) 모니터링
- [풀 상태](/rest/api/batchservice/pool/get#poolstate) 모니터링
- [계정의 풀 사용량](/rest/api/batchservice/pool/listusagemetrics) 모니터링
- [상태별로 풀 노드](/rest/api/batchservice/account/listpoolnodecounts) 집계

## <a name="additional-monitoring-solutions"></a>추가 모니터링 솔루션

[Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 Batch 작업 및 태스크의 가용성, 성능, 사용량을 프로그래밍 방식으로 모니터링합니다. Application Insights를 사용하면 컴퓨팅 노드(VM)에서 성능 카운터를 모니터링하고 해당 노드에서 실행되는 작업에 대한 사용자 지정 정보를 검색할 수 있습니다.

예를 들어 [Application Insights를 사용하여 Batch .NET 애플리케이션 모니터링 및 디버그](monitor-application-insights.md) 및 함께 제공되는 [코드 샘플](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)을 참조하세요.

> [!NOTE]
> Application Insights를 사용하는 추가 비용이 발생할 수 있습니다. [가격 정보](https://azure.microsoft.com/pricing/details/application-insights/)를 참조하세요.

[Batch Explorer](https://github.com/Azure/BatchExplorer)는 Azure Batch 애플리케이션을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. Mac, Linux 또는 Windows의 경우 [설치 패키지](https://azure.github.io/BatchExplorer/)를 다운로드합니다. 필요에 따라 [Azure Batch Insights](https://github.com/Azure/batch-insights)를 사용하여 Batch Explorer의 VM 성능 카운터와 같은 Batch 노드에 대한 시스템 통계를 받으세요.

## <a name="next-steps"></a>다음 단계

- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
- Batch를 사용하는 [진단 로깅](batch-diagnostics.md)에 대해 자세히 알아봅니다.
