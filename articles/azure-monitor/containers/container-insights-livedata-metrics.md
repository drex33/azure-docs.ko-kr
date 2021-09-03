---
title: 컨테이너 인사이트로 실시간으로 메트릭 보기 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트와 함께 kubectl을 사용하지 않고 메트릭의 실시간 보기를 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 5dffc98e73a9d9e85b301d3272869b7fb5118ab5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536740"
---
# <a name="how-to-view-metrics-in-real-time"></a>메트릭을 실시간으로 보는 방법

컨테이너 인사이트 라이브 데이터(미리 보기) 기능을 사용하면 클러스터의 노드 및 Pod 상태에 대한 메트릭을 실시간으로 시각화할 수 있습니다. 이 인사이트에 포함된 성능 차트의 데이터를 호출, 구문 분석 및 시각화하기 위해 `kubectl top nodes`, `kubectl get pods –all-namespaces` 및 `kubectl get nodes` 명령에 대한 직접 액세스를 에뮬레이트합니다.

이 문서는 자세한 개요를 제공하고 이 기능을 사용하는 방법을 이해하는 데 도움이 됩니다.

>[!NOTE]
>[프라이빗 클러스터](https://azure.microsoft.com/updates/aks-private-cluster/)로 설정된 AKS 클러스터는 이 기능에서 지원되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스할 수 밖에 없습니다. 이 프록시의 Kubernetes API를 차단하도록 네트워킹 보안을 사용하도록 설정하면 이 트래픽이 차단됩니다.

라이브 데이터(미리 보기) 기능을 설정하거나 문제를 해결하는 데 도움이 필요하면 [설치 가이드](container-insights-livedata-setup.md)를 검토하세요.

## <a name="how-it-works"></a>작동 방식

라이브 데이터(미리 보기) 기능은 Kubernetes API에 직접 액세스할 수 있으며 인증 모델에 대한 추가 정보는 [여기](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)에서 찾을 수 있습니다.

이 기능은 기본적으로 5초마다 메트릭 엔드포인트(`/api/v1/nodes`, `/apis/metrics.k8s.io/v1beta1/nodes` 및 `/api/v1/pods` 포함)에 대해 폴링 작업을 수행합니다. 이 데이터는 브라우저에 캐시되고 **라이브 전환(미리 보기)** 을 선택하여 **클러스터** 탭의 컨테이너 인사이트에 포함된 4개의 성능 차트에 차트로 표시됩니다. 각 후속 폴링은 5분 동안 진행되는 시각화 창에 차트로 표시됩니다.

![클러스터 보기의 라이브 전환 옵션](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

폴링 간격은 **간격 설정** 드롭다운에서 구성되어 1, 5, 15, 30초마다 새 데이터에 대한 폴링을 설정할 수 있습니다.

![라이브 전환 드롭다운 폴링 간격](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>짧은 시간 동안 문제를 해결하는 동안에는 폴링 간격을 1초로 설정하는 것이 좋습니다. 이러한 요청은 클러스터에서 Kubernetes API의 가용성 및 조절에 영향을 줄 수 있습니다. 그런 다음 더 긴 폴링 간격으로 재구성합니다.

>[!IMPORTANT]
>이 기능을 수행하는 동안에는 데이터가 영구적으로 저장되지 않습니다. 이 세션 동안 캡처된 모든 정보는 브라우저를 닫거나 기능에서 벗어나면 즉시 삭제됩니다. 5분 창 내에서 시각화를 위한 데이터만 남습니다. 5분이 지난 모든 메트릭도 영구적으로 삭제됩니다.

이러한 차트는 라이브 모드에서 본 마지막 Azure 대시보드에 고정할 수 없습니다.

## <a name="metrics-captured"></a>캡처된 메트릭

### <a name="node-cpu-utilization---node-memory-utilization-"></a>노드 CPU 사용률(%)/노드 메모리 사용률(%)

이 두 성능 차트는 `kubectl top nodes`를 호출하고 **CPU%** 및 **MEMORY%** 열의 결과를 각 차트에 캡처하는 것과 동일하게 매핑됩니다.

![Kubectl 상위 노드 예시 결과](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![노드 CPU 사용률 차트](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![노드 메모리 사용률 차트](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

백분위수 계산은 클러스터에서 이상값 노드를 식별하는 데 도움이 되도록 더 큰 클러스터에서 작동합니다. 예를 들어 노드가 축소 목적으로 충분히 활용되지 않는지 이해합니다. **최소** 집계를 활용하면 클러스터에서 사용률이 낮은 노드를 확인할 수 있습니다. 더 자세히 조사하려면 **노드** 탭을 선택하고 CPU 또는 메모리 사용률을 기준으로 그리드를 정렬합니다.

이는 또한 어떤 노드가 한계에 도달했는지 그리고 확장이 필요한지 이해하는 데 도움이 됩니다. **최대** 및 **P95** 집계를 모두 활용하면 클러스터에 리소스 사용률이 높은 노드가 있는지 확인할 수 있습니다. 추가 조사를 위해 다시 **노드** 탭으로 전환합니다.

### <a name="node-count"></a>노드 수

이 성능 차트는 `kubectl get nodes`를 호출하고 **STATUS** 열을 상태 유형별로 그룹화된 차트에 매핑하는 것과 동일하게 매핑됩니다.

![Kubectl 노드 가져오기 결과 예](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![노드 수 차트](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

노드는 **준비** 또는 **준비 안 됨** 상태로 보고됩니다. 계산되고(총 수가 만들어짐) 이 두 집계의 결과가 차트로 표시됩니다.
예를 들어, 노드가 실패 상태로 전환되는지 파악할 수 있습니다. **준비 안 됨** 집계를 사용하면 현재 **준비 안 됨** 상태에 있는 클러스터의 노드 수를 빠르게 확인할 수 있습니다.

### <a name="active-pod-count"></a>활성 Pod 개수

이 성능 차트는 `kubectl get pods –all-namespaces` 호출에 해당하는 항목에 매핑하고 **STATUS** 열을 상태 유형별로 그룹화된 차트에 매핑합니다.

![Kubectl Pod 가져오기 결과 예](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![노드 Pod 수 차트](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>`kubectl`에 의해 해석되는 상태 이름은 차트에서 정확히 일치하지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[로그 쿼리 예](container-insights-log-query.md)를 보면서 미리 정의된 쿼리 및 예제를 확인하고 경고, 시각화를 만들거나 클러스터에 대한 추가 분석을 수행합니다.
