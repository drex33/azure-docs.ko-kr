---
title: 컨테이너 인사이트에 대한 모니터링 비용 | Microsoft Docs
description: 이 문서에서는 고객이 사용량 및 관련 비용을 관리하는 데 도움이 되도록, 컨테이너 인사이트에서 수집된 메트릭과 인벤토리 데이터에 대한 모니터링 비용을 설명합니다.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 2ff3616b3c78bbd7202acb08ffb3e46e0f7591b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528795"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>컨테이너 인사이트에 대한 모니터링 비용 이해

이 문서에서는 다음을 이해하는 데 도움이 되도록 컨테이너 인사이트에 대한 가격 책정 지침을 제공합니다.

* 이 인사이트를 사용하도록 설정하기 전에 비용을 미리 예측하는 방법

* 하나 이상의 컨테이너에 대해 컨테이너 인사이트를 사용하도록 설정한 후 비용을 측정하는 방법

* 데이터 컬렉션을 제어하고 비용을 절감하는 방법

Azure Monitor 로그는 Kubernetes 클러스터에서 생성된 데이터를 수집, 인덱싱, 저장합니다. 

Azure Monitor 가격 책정 모델은 주로 Log Analytics 작업 영역에 매일 기가바이트 단위로 수집되는 데이터의 양을 기반으로 합니다. Log Analytics 작업 영역의 비용은 수집된 데이터 볼륨만을 기반으로 하지 않으며, 선택한 계획 및 클러스터에서 생성된 데이터를 저장하기 위해 선택한 기간에 따라서도 달라집니다.

>[!NOTE]
>모든 크기 및 가격 책정은 샘플 예측만을 위한 것입니다. Azure Monitor Log Analytics 가격 책정 모델 및 Azure 지역을 기반으로 한 최신 가격 책정은 Azure Monitor [가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 페이지를 참조하세요.

비용에 영향을 미치고 사용량에 따라 사용자 지정할 수 있는 컨테이너 인사이트를 사용하여 Kubernetes 클러스터에서 수집되는 데이터 유형을 요약하면 다음과 같습니다.

- 클러스터의 모든 Kubernetes 네임스페이스에 있는 모니터링되는 모든 컨테이너의 stdout, stderr 컨테이너 로그

- 클러스터에서 모니터링되는 모든 컨테이너의 컨테이너 환경 변수

- 모니터링이 필요하지 않은 클러스터의 완료된 Kubernetes 작업/Pod

- Prometheus 메트릭의 활성 스크래핑

- *kube-apiserver* 및 *kube-controller-manager* 와 같은 마스터 구성 요소에 의해 생성된 로그 데이터를 분석하기 위한 AKS 클러스터에 있는 Kubernetes 마스터 노드 로그의 [진단 로그 컬렉션](../../aks/monitor-aks.md#configure-monitoring)

## <a name="what-is-collected-from-kubernetes-clusters"></a>Kubernetes 클러스터에서 수집되는 내용

컨테이너 인사이트는 Log Analytics 작업 영역에서 로그 데이터로 기록되어 수집된 미리 정의된 메트릭 및 인벤토리 항목 세트를 포함합니다. 아래에 나열된 모든 메트릭은 기본적으로 매분 수집됩니다.

### <a name="node-metrics-collected"></a>수집되는 노드 메트릭

다음 목록은 수집되는 노드당 24개의 메트릭입니다.

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- used (disk)
- free (disk)
- used_percent (disk)
- io_time (diskio)
- writes (diskio)
- reads (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (kubelet)

### <a name="container-metrics"></a>컨테이너 메트릭

다음 목록은 수집되는 컨테이너당 8개의 메트릭입니다.

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>클러스터 인벤토리

다음 목록은 기본적으로 수집되는 클러스터 인벤토리 데이터입니다.

- KubePodInventory – 컨테이너당 분당 1
- KubeNodeInventory – 분당 노드당 1
- KubeServices – 분당 서비스당 1
- ContainerInventory – 분당 컨테이너당 1

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>AKS 클러스터를 모니터링하기 위한 비용 예측

아래의 예측은 다음 크기 조정 예제가 있는 AKS(Azure Kubernetes Service) 클러스터를 기반으로 합니다. 또한 예상치는 수집되는 메트릭 및 인벤토리 데이터에만 적용됩니다. 컨테이너 로그(stdout, stderr 및 환경 변수)의 경우 워크로드에서 생성된 로그 크기에 따라 달라지며 예측에서 제외됩니다.

다음과 같이 구성된 AKS 클러스터의 모니터링을 사용하도록 설정한 경우

- 노드 3개
- 노드당 디스크 2개
- 노드당 네트워크 인터페이스 1개
- Pod 20개(각 Pod에 컨테이너 1개 = 총 20개 컨테이너)
- Kubernetes 네임스페이스 2개
- Kubernetes 서비스 5개(kube-system Pod, 서비스, 네임스페이스 포함)
- 컬렉션 빈도 = 60초(기본값)

할당된 Log Analytics 작업 영역에서 시간당 생성된 데이터 볼륨 및 테이블을 볼 수 있습니다. 각 테이블에 대한 자세한 내용은 [Azure Monitor 로그 테이블](../../aks/monitor-aks-reference.md#azure-monitor-logs-tables)을 참조하세요.

|테이블 | 크기 예측(MB/시간) |
|------|---------------|
|Perf | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0.75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

합계 = 31MB/시간 = 23.1GB/월(1개월 = 31일)

종량제 모델인 Log Analytics의 기본 [가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 사용하여 월별 Azure Monitor 비용을 예측할 수 있습니다. 용량 예약을 포함하면 선택한 예약에 따라 월별 가격이 높아질 수 있습니다.

## <a name="controlling-ingestion-to-reduce-cost"></a>비용 절감을 위한 수집 제어

조직의 여러 사업부가 Kubernetes 인프라와 Log Analytics 작업 영역을 공유하는 시나리오를 고려해 보겠습니다. 각 사업부를 Kubernetes 네임스페이스로 구분합니다. **통합 문서 보기** 드롭다운에서 사용할 수 있는 **데이터 사용량** Runbook을 사용하여 각 작업 영역에서 수집되는 데이터의 양을 시각화할 수 있습니다.

[![통합 문서 보기 드롭다운](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


이 통합 문서를 사용하면 설명서에서 공유하는 쿼리 라이브러리를 직접 작성하지 않고도 데이터 원본을 시각화할 수 있습니다. 이 통합 문서에는 다음과 같은 관점에서 청구 가능한 데이터를 볼 수 있는 차트가 있습니다.

- 솔루션별로 수집된 총 청구 가능한 데이터(GB)
- 컨테이너 로그(애플리케이션 로그)별로 수집된 청구 가능한 데이터
- Kubernetes 네임스페이스별로 수집된 청구 가능한 컨테이너 로그 데이터
- 클러스터 이름별로 수집 및 분리된 청구 가능한 컨테이너 로그 데이터
- 로그 소스 항목별로 수집된 청구 가능한 컨테이너 로그 데이터
- 진단 마스터 노드 로그별로 수집된 청구 가능한 진단 데이터

[![데이터 사용량 통합 문서](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

통합 문서에 대한 권한을 관리하는 방법에 대한 자세한 내용은 [액세스 제어](../visualize/workbooks-access-control.md)를 검토하세요.

어떤 원본이 요구 사항을 초과하는 가장 많은 데이터 또는 더 많은 데이터를 생성하는지 확인하기 위한 분석을 완료한 후 데이터 수집을 다시 구성할 수 있습니다. Stdout, stderr, 환경 변수의 컬렉션을 구성하는 방법에 대한 자세한 내용은 [에이전트 데이터 컬렉션 설정 구성](container-insights-agent-config.md) 문서에 설명되어 있습니다.

다음은 비용을 제어할 수 있도록 ConfigMap 파일을 수정하여 클러스터에 적용할 수 있는 변경 내용의 예입니다.

1. ConfigMap 파일에서 다음을 수정하여 클러스터의 모든 네임스페이스에서 stdout 로그를 사용하지 않도록 설정합니다.

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. 개발 네임스페이스(예: **dev-test**)에서 stderr 로그 수집을 사용하지 않도록 설정하고, ConfigMap 파일에서 다음을 수정하여 다른 네임스페이스(예: **prod** 및 **default**)에서 stderr 로그를 계속 수집합니다.

    >[!NOTE]
    >기본적으로 kube-system 로그 컬렉션은 사용되지 않습니다. 기본 설정은 유지되며, **dev-test** 네임스페이스를 제외 네임스페이스 목록에 추가하는 것이 stderr 로그 컬렉션에 적용됩니다.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. ConfigMap 파일에서 다음을 수정하여 클러스터에서 환경 변수 컬렉션을 사용하지 않도록 설정합니다. 이는 모든 Kubernetes 네임스페이스의 모든 컨테이너에 적용됩니다. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. 완료된 작업을 정리하려면 ConfigMap 파일에서 다음을 수정하여 작업 정의에서 정리 정책을 지정합니다.

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

하나 이상의 이러한 변경 내용을 ConfigMaps에 적용한 후 클러스터에 적용하려면 [업데이트된 ConfigMap 적용](container-insights-prometheus-integration.md#applying-updated-configmap)을 참조하세요.

### <a name="prometheus-metrics-scraping"></a>Prometheus 메트릭 스크래핑

[Prometheus 메트릭 스크래핑](container-insights-prometheus-integration.md)을 활용하는 경우 클러스터에서 수집하는 메트릭 수를 제한하려면 다음 사항을 고려하세요.

- 스크래핑 빈도가 최적으로 설정되었는지 확인합니다(기본값은 60초). 빈도를 15초로 늘릴 수 있지만, 스크래핑하는 메트릭이 해당 빈도로 게시되는지 확인해야 합니다. 그러지 않으면 많은 중복 메트릭이 스크랩되어 일정한 간격으로 Log Analytics 작업 영역으로 전송되는데, 이에 따라 데이터 수집 및 보존 비용이 늘어나지만 가치는 그에 미치지 못합니다. 

- 컨테이너 인사이트는 메트릭 이름별로 제외 및 포함 목록을 지원합니다. 예를 들어 클러스터에서 **kubedns** 메트릭을 스크랩하는 경우, 기본적으로 수백 개가 스크랩될 수 있지만 사용자는 하위 집합에만 관심이 있을 가능성이 높습니다. 스크랩할 메트릭 목록을 지정했는지 확인하거나, 데이터 수집 볼륨을 줄이기 위해 일부 외의 나머지를 제외합니다. 스크래핑을 사용하도록 설정하고 해당 메트릭 중 다수를 사용하지 않는 것은 어렵지 않지만, Log Analytics 청구서에 추가 요금이 발생하게 됩니다.

- Pod 주석을 스크래핑할 때 네임스페이스별로 필터링하여 사용하지 않는 네임스페이스(예: **dev-test** 네임스페이스)에서 Pod 메트릭의 스크래핑을 제외해야 합니다.

## <a name="next-steps"></a>다음 단계

컨테이너 인사이트를 사용하여 수집된 데이터의 최근 사용 패턴을 기반으로 하는 비용을 파악하는 방법에 대한 자세한 내용은 [사용량 관리 및 비용 예측](../logs/manage-cost-storage.md)을 참조하세요.