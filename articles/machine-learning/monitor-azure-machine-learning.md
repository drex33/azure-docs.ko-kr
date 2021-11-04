---
title: Azure Machine Learning 모니터링 | Microsoft Docs
description: Azure Monitor를 사용하여 Azure Machine Learning의 메트릭에 대한 경고를 보고 분석하고 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/21/2021
ms.openlocfilehash: f3164b4ca499c7078801ea3ffc825325a0ef1881
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561388"
---
# <a name="monitor-azure-machine-learning"></a>Azure Machine Learning 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure Machine Learning에서 생성되는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터에 대해 분석하고 경고하는 방법을 설명합니다.

> [!TIP]
> 이 문서의 정보는 Azure Machine Learning Service 및 연결된 Azure 서비스에 대한 모니터링을 설명하므로 주로 __관리자__ 를 위한 것입니다. __데이터 과학자__ 또는 __개발자__ 로서 '모델 학습 실행'과 관련된 정보를 모니터링하려는 경우 다음 문서를 참조하세요.
>
> * [학습 실행 시작, 모니터링 및 취소](how-to-track-monitor-analyze-runs.md)
> * [학습 실행에 대한 메트릭 로그](how-to-log-view-metrics.md)
> * [MLflow로 실험 추적](how-to-use-mlflow.md)
> * [TensorBoard로 시각화 실행](how-to-monitor-tensorboard.md)
>
> 웹 서비스로 배포된 모델에서 생성된 정보를 모니터링하려는 경우 [모델 데이터 수집](how-to-enable-data-collection.md)과 [Application Insights로 모니터링](how-to-enable-app-insights.md)을 참조하세요.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Machine Learning은 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 생성합니다. Azure Monitor는 Azure 리소스를 모니터링하기 위한 완전한 기능 세트를 제공합니다. 또한 다른 클라우드 및 온-프레미스의 리소스도 모니터링할 수 있습니다.

다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링 문서](../azure-monitor/essentials/monitor-azure-resource.md)로 시작하세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

이 문서의 다음 섹션에서는 Azure Machine Learning에 대해 수집하는 데이터를 설명합니다. 또한 이 섹션에서는 Azure 도구를 사용하여 데이터 수집을 구성하고 이 데이터를 분석하는 예제를 제공합니다.

> [!TIP]
> Azure Monitor와 관련된 비용을 이해하려면 [사용량 및 예상 비용](../azure-monitor//usage-estimated-costs.md)을 참조하세요. 데이터가 Azure Monitor에 표시되는 데 걸리는 시간을 이해하려면 [로그 데이터 수집 시간](../azure-monitor/logs/data-ingestion-time.md)을 참조하세요.

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure Machine Learning에서 데이터 모니터링

Azure Machine Learning은 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 이에 대해서는 [Azure 리소스에서 데이터 모니터링](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명되어 있습니다. 

Azure Machine Learning에서 만든 로그 및 메트릭에 대한 자세한 내용은 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md)를 참조하세요.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다. 여러 Azure Machine Learning 작업 영역을 관리해야 하는 경우 모든 작업 영역에 대한 로그를 동일한 로깅 대상으로 라우팅하고 한곳에서 모든 로그를 쿼리할 수 있습니다.

Azure Portal, Azure CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 방법의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure Machine Learning의 범주는 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md#resource-logs)에 나열되어 있습니다.

> [!IMPORTANT]
> 이러한 설정을 사용하려면 추가 Azure 서비스(스토리지 계정, 이벤트 허브 또는 Log Analytics)가 필요하므로 비용이 늘어날 수 있습니다. 예상 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 방문하세요.

Azure Machine Learning에 대해 다음 로그를 구성할 수 있습니다.

| 범주 | Description |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 컴퓨팅 클러스터에서 발생한 이벤트입니다. |
| AmlComputeClusterNodeEvent | Azure Machine Learning 컴퓨팅 클러스터 내의 노드에서 발생한 이벤트입니다. |
| AmlComputeJobEvent | Azure Machine Learning 컴퓨팅에서 실행되는 작업에서 발생한 이벤트입니다. |


> [!NOTE]
> 진단 설정에서 메트릭을 사용하도록 설정하면 현재는 스토리지 계정, 이벤트 허브 또는 Log Analytics로 전송되는 정보에 차원 정보가 포함되지 않습니다.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

**Azure Monitor** 메뉴에서 **메트릭** 을 열어 Azure Machine Learning에 대한 메트릭을 다른 Azure 서비스의 메트릭과 함께 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

수집되는 플랫폼 메트릭의 목록은 [Azure Machine Learning 모니터링 데이터 참조 메트릭](monitor-resource-reference.md#metrics)을 참조하세요.

Azure Machine Learning에 대한 모든 메트릭은 네임스페이스 **Machine Learning 서비스 작업 영역** 에 있습니다.

![Machine Learning 서비스 작업 영역이 선택된 메트릭 탐색기](./media/monitor-azure-machine-learning/metrics.png)

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

> [!TIP]
> Azure Monitor 메트릭 데이터는 90일 동안 사용할 수 있습니다. 그러나 차트를 만드는 경우 30일 동안만 시각화할 수 있습니다. 예를 들어 90일 시각화하려는 경우 90일 기간 중 30일에 해당하는 차트 3개로 분할해야 합니다.
### <a name="filtering-and-splitting"></a>필터링 및 분할

차원을 지원하는 메트릭의 경우 차원 값으로 필터를 적용할 수 있습니다. 예를 들어 **클러스터 이름** `cpu-cluster`에 대해 **활성 코어** 를 필터링합니다. 

또한 차원별로 메트릭을 분할하여 메트릭의 세그먼트가 서로 어떻게 비교되는지 시각화할 수 있습니다. 예를 들어 **파이프라인 단계 유형** 을 분할하여 파이프라인에서 사용되는 단계 유형의 수를 확인합니다.

필터링 및 분할에 대한 자세한 내용은 [Azure Monitor의 고급 기능](../azure-monitor/essentials/metrics-charts.md)을 참조하세요.

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Log Analytics를 사용하려면 진단 구성을 만들고 __Log Analytics에 정보 보내기__ 를 사용하도록 설정해야 합니다. 자세한 내용은 [컬렉션 및 라우팅](#collection-and-routing) 섹션을 참조하세요.

Azure Monitor 로그의 데이터는 각 테이블에 고유한 속성 집합이 있는 테이블에 저장됩니다. Azure Machine Learning은 다음 테이블에 데이터를 저장합니다.

| 테이블 | Description |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 컴퓨팅 클러스터에서 발생한 이벤트입니다.|
| AmlComputeClusterNodeEvent | Azure Machine Learning 컴퓨팅 클러스터 내의 노드에서 발생한 이벤트입니다. |
| AmlComputeJobEvent | Azure Machine Learning 컴퓨팅에서 실행되는 작업에서 발생한 이벤트입니다. |
| AmlComputeInstanceEvent | ML 컴퓨팅 인스턴스에 액세스(읽기/쓰기)할 때 발생하는 이벤트입니다. 범주에는 ComputeInstanceEvent(대화량이 많음)가 포함됩니다. |
| AmlDataLabelEvent | 데이터 레이블이나 해당 프로젝트에 액세스(읽기, 생성 또는 삭제)할 때 발생하는 이벤트입니다. 범주에는 DataLabelReadEvent, DataLabelChangeEvent가 포함됩니다.  |
| AmlDataSetEvent | 등록되었거나 등록되지 않은 ML 데이터 세트에 액세스(읽기, 생성 또는 삭제)할 때 발생하는 이벤트입니다. 범주에는 DataSetReadEvent, DataSetChangeEvent가 포함됩니다. |
| AmlDataStoreEvent | ML 데이터 저장소에 액세스(읽기, 생성 또는 삭제)할 때 발생하는 이벤트입니다. 범주에는 DataStoreReadEvent, DataStoreChangeEvent가 포함됩니다. |
| AmlDeploymentEvent | ACI 또는 AKS에서 모델을 배포할 때 발생하는 이벤트입니다. 범주에는 DeploymentReadEvent, DeploymentEventACI, DeploymentEventAKS가 포함됩니다. |
| AmlInferencingEvent | AKS 또는 ACI 컴퓨팅 형식에 대한 유추 또는 관련 작업의 이벤트입니다. 범주에는 InferencingOperationACI(대화량이 많음), InferencingOperationAKS(대화량이 많음)가 포함됩니다. |
| AmlModelsEvent | ML 모델에 액세스(읽기, 생성 또는 삭제)할 때 발생하는 이벤트입니다. 모델 및 자산 패키징이 즉시 빌드할 수 있는 패키지를 생성할 때 발생하는 이벤트를 포함합니다. 범주에는 ModelsReadEvent, ModelsActionEvent가 포함됩니다.|
| AmlPipelineEvent | ML 파이프라인 초안, 엔드포인트 또는 모듈에 액세스(읽기, 생성 또는 삭제)할 때 발생하는 이벤트입니다. 범주에는 PipelineReadEvent, PipelineChangeEvent가 포함됩니다. |
| AmlRunEvent | ML 실험에 액세스(읽기, 생성 또는 삭제)할 때 발생하는 이벤트입니다. 범주에는 RunReadEvent, RunEvent가 포함됩니다. |
| AmlEnvironmentEvent | ML 환경 구성에 액세스(읽기, 생성 또는 삭제)할 때 발생하는 이벤트입니다. 범주에는 EnvironmentReadEvent(대화량이 많음), EnvironmentChangeEvent가 포함됩니다. |


> [!IMPORTANT]
> Azure Machine Learning 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 작업 영역으로 설정된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 Azure 서비스의 데이터 또는 다른 데이터베이스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/logs/scope.md)를 참조하세요.

만든 로그 및 메트릭에 대한 자세한 참조는 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md)를 참조하세요.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> [서비스 이름] 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 Azure Machine Learning 작업 영역으로 설정된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 작업 영역의 데이터 또는 다른 Azure 서비스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/logs/scope.md)를 참조하세요.

다음은 Azure Machine Learning 리소스를 모니터링하는 데 사용할 수 있는 쿼리입니다. 

+ 지난 5일간 실패한 작업을 가져옵니다.

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 특정 작업 이름에 대한 레코드를 가져옵니다.

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ VM 크기가 Standard_D1_V2인 클러스터의 최근 5일간 클러스터 이벤트를 가져옵니다.

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 지난 8일간 할당된 노드를 가져옵니다.

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

여러 Azure Machine Learning 작업 영역을 동일한 Log Analytics 작업 영역에 연결하는 경우 모든 리소스를 쿼리할 수 있습니다. 

+ 마지막 날 작업 영역과 클러스터에서 실행 중인 노드 수를 가져옵니다.

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(1d)
    | summarize avgRunningNodes=avg(TargetNodeCount), maxRunningNodes=max(TargetNodeCount)
             by Workspace=tostring(split(_ResourceId, "/")[8]), ClusterName, ClusterType, VmSize, VmPriority
    ```

## <a name="alerts"></a>경고

**Azure Monitor** 메뉴에서 **경고** 를 열어 Azure Machine Learning에 대한 경고에 액세스할 수 있습니다. 경고 만들기에 대한 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

다음 표에서는 Azure Machine Learning에 대한 일반적인 메트릭 경고 규칙과 권장 메트릭 경고 규칙을 보여 줍니다.

| 경고 유형 | 조건 | Description |
|:---|:---|:---|
| 모델 배포 실패 | 집계 유형: 합계, 연산자: 보다 큼, 임계값: 0 | 하나 이상의 모델 배포가 실패한 경우 |
| 할당량 사용률 | 집계 유형: 평균, 연산자: 보다 큼, 임계값: 90| 할당량 사용률이 90%보다 큰 경우 |
| 사용 불가 노드 | 집계 유형: 합계, 연산자: 보다 큼, 임계값: 0 | 사용할 수 없는 노드가 하나 이상 있는 경우 |

## <a name="next-steps"></a>다음 단계

- 로그 및 메트릭에 대한 참조는 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md)를 참조하세요.
- Azure Machine Learning과 관련된 할당량을 사용하는 방법에 대한 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](how-to-manage-quotas.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
