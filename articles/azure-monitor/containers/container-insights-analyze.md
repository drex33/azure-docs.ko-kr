---
title: 컨테이너 인사이트로 Kubernetes 모니터링 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트를 사용하여 Kubernetes 클러스터의 성능을 보고 분석하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 2751d96a0527328a78030cba618d2026059f04fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566977"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-container-insights"></a>컨테이너 인사이트를 사용하여 Kubernetes 클러스터 성능 모니터링

컨테이너 인사이트를 사용하면 성능 차트 및 상태를 사용하여 AKS(Azure Kubernetes Service), Azure Stack 또는 기타 환경에서 호스팅되는 Kubernetes 클러스터의 워크로드를 두 가지 관점에서 모니터링할 수 있습니다. 클러스터에서 직접 모니터링하거나 Azure Monitor에서 구독의 모든 클러스터를 볼 수 있습니다. 특정 AKS 클러스터를 모니터링할 때에도 Azure Container Instances를 볼 수 있습니다.

이 문서는 두 가지 관점과 Azure Monitor가 검색된 문제를 신속하게 평가, 조사 및 해결하는 데 어떻게 도움이 되는지 이해하는 데 도움이 됩니다.

컨테이너 인사이트를 사용하도록 설정하는 방법에 대한 자세한 내용은 [컨테이너 인사이트 온보딩](container-insights-onboard.md)을 참조하세요.

Azure Monitor는 구독의 전체 리소스 그룹에 배포된 Linux 및 Windows Server 2019가 실행되고 모니터링되는 모든 Kubernetes 클러스터의 상태를 표시하는 다중 클러스터 보기를 제공합니다. 솔루션에서 모니터링하지 않는 모든 환경에서 검색된 클러스터를 보여 줍니다. 즉시 클러스터 상태를 이해할 수 있으며, 여기서 노드 및 컨트롤러 성능 페이지로 드릴다운하거나 클러스터의 성능 차트로 이동할 수 있습니다. 모니터링되지 않는 것으로 식별된 AKS 클러스터의 경우 언제든지 해당 클러스터에 모니터링을 사용할 수 있습니다.

Linux 클러스터와 비교하여 컨테이너 인사이트를 사용한 Windows Server 클러스터 모니터링의 주요 차이점은 개요 문서에서 [여기](container-insights-overview.md#what-does-container-insights-provide)에 설명되어 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="multi-cluster-view-from-azure-monitor"></a>Azure Monitor에서 다중 클러스터 보기

배포된 모든 Kubernetes 클러스터의 상태를 보려면 Azure Portal의 왼쪽 창에서 **모니터** 를 선택합니다. **인사이트** 섹션에서 **컨테이너** 를 선택합니다.

![Azure Monitor 다중 클러스터 대시보드 예제](./media/container-insights-analyze/azmon-containers-multiview.png)

그리드에 표시된 결과의 범위를 지정하여 다음과 같은 클러스터를 표시할 수 있습니다.

* **Azure** - Azure Kubernetes Service에서 호스팅되는 AKS 및 AKS 엔진 클러스터
* **Azure Stack(미리 보기)** - Azure Stack에서 호스팅되는 AKS 엔진 클러스터
* **비 Azure(미리 보기)** - 온-프레미스에서 호스팅되는 Kubernetes 클러스터
* **모두** - Azure, Azure Stack 및 컨테이너 인사이트에 온보딩된 온-프레미스 환경에서 호스팅되는 모든 Kubernetes 클러스터를 확인합니다.

특정 환경에서 클러스터를 보려면 페이지 왼쪽 상단 모서리에 있는 **환경** 필에서 선택합니다.

![환경 필 선택기 예](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

**모니터링되는 클러스터** 탭에서 다음 정보를 알아봅니다.

- 중요 또는 비정상 상태인 클러스터 수와 정상 또는 보고하지 않음(알 수 없는 상태라고도 함) 상태인 클러스터 수
- 모든 [Azure Kubernetes 엔진(AKS 엔진)](https://github.com/Azure/aks-engine) 배포가 정상인지 여부
- 클러스터당 배포되는 노드와 사용자 및 시스템 Pod 수
- 사용 가능한 디스크 공간 및 용량 문제가 있는지 여부

다음과 같은 상태가 있습니다.

* **정상**: VM에서 감지된 문제가 없으며 필요에 따라 작동합니다.
* **심각**: 예상대로 정상 작동 상태를 복원하기 위해 해결해야 하는 하나 이상의 심각한 문제가 감지되었습니다.
* **경고**: 해결해야 하는 문제가 하나 이상 검색되었으며, 해결하지 않으면 상태 조건이 위험으로 변경될 수 있습니다.
* **알 수 없음**: 서비스가 노드 또는 Pod에 연결할 수 없는 경우 상태가 알 수 없음으로 변경됩니다.
* **찾을 수 없음**: 이 솔루션의 워크로드를 포함하고 있는 작업 영역, 리소스 그룹 또는 구독이 삭제되었습니다.
* **권한 없음**: 사용자에게 작업 영역의 데이터를 읽는 데 필요한 권한이 없습니다.
* **오류**: 작업 영역에서 데이터를 읽는 동안 발생한 오류가 있습니다.
* **잘못 구성됨**: 지정된 작업 영역에서 컨테이너 인사이트가 올바르게 구성되지 않았습니다.
* **데이터 없음**: 최근 30분 동안 작업 영역에 데이터가 보고되지 않았습니다.

세 상태 중 *최악* 의 상태를 전반적인 클러스터 상태로 계산하며, 한 가지 예외가 있습니다. 세 가지 상태 중 하나라도 알 수 없는 경우 전체 클러스터 상태는 **알 수 없음** 으로 표시됩니다.

다음 표에서는 다중 클러스터 보기에서 모니터링되는 클러스터의 상태를 제어하는 계산 방법을 자세히 분석합니다.

| 모니터링되는 클러스터 |상태 |가용성 |
|-------|-------|-----------------|
|**사용자 Pod**| | |
| |정상 |100% |
| |경고 |90-99% |
| |위험 |<90% |
| |Unknown |지난 30분 동안 보고하지 않은 경우 |
|**시스템 Pod**| | |
| |정상 |100% |
| |경고 |해당 없음 |
| |위험 |<100% |
| |Unknown |지난 30분 동안 보고하지 않은 경우 |
|**Node** | | |
| |정상 |>85% |
| |경고 |60 - 84% |
| |위험 |<60% |
| |Unknown |지난 30분 동안 보고하지 않은 경우 |

클러스터 목록에서 클러스터 이름을 선택하여 **클러스터** 페이지로 드릴다운할 수 있습니다. 그런 다음 특정 클러스터의 **노드** 열에서 노드 롤업을 선택하여 **노드** 성능 페이지로 이동합니다. 또는 **사용자 Pod** 또는 **시스템 Pod** 열의 롤업을 선택하여 **컨트롤러** 성능 페이지로 드릴다운할 수 있습니다.

## <a name="view-performance-directly-from-a-cluster"></a>클러스터에서 직접 성능 보기

컨테이너 인사이트에 대한 액세스는 왼쪽 창에서 **인사이트** > **클러스터** 를 선택하거나 멀티 클러스터 보기에서 클러스터를 선택한 경우 AKS 클러스터에서 직접 사용할 수 있습니다. 클러스터에 대한 정보는 네 가지 관점으로 구성됩니다.

- 클러스터
- 노드
- Controllers
- 컨테이너

>[!NOTE]
>이 문서의 나머지 부분에 설명된 환경은 다중 클러스터 보기에서 선택한 경우 Azure Stack 또는 기타 환경에서 호스팅되는 Kubernetes 클러스터의 성능 및 상태를 보는 데에도 적용할 수 있습니다.

기본 페이지가 열리고 클러스터의 주요 성능 메트릭을 보여 주는 4개의 꺾은선형 성능 차트가 표시됩니다.

![클러스터 탭의 예제 성능 차트](./media/container-insights-analyze/containers-cluster-perfview.png)

성능 차트에는 네 가지 성능 메트릭이 표시됩니다.

- **노드 CPU 사용률&nbsp;%** : 전체 클러스터에 대한 CPU 사용률의 집계된 큐브 뷰를 나타냅니다. 시간 범위에 대한 결과를 필터링하려면 차트 위의 백분위수 선택기에서 **평균**, **최소**, **50번째**, **90번째**, **95번째** 또는 **최대** 를 선택합니다. 필터는 개별적으로 또는 결합하여 사용할 수 있습니다.
- **노드 메모리 사용률&nbsp;%** : 이 차트는 전체 클러스터에 대한 메모리 사용률의 집계된 큐브 뷰를 나타냅니다. 시간 범위에 대한 결과를 필터링하려면 차트 위의 백분위수 선택기에서 **평균**, **최소**, **50번째**, **90번째**, **95번째** 또는 **최대** 를 선택합니다. 필터는 개별적으로 또는 결합하여 사용할 수 있습니다.
- **노드 수**: Kubernetes의 노드 수 및 상태입니다. 표시된 클러스터 노드의 상태는 총계, 준비됨 및 준비 안 됨입니다. 개별적으로 필터링하거나 차트 위의 선택기에서 결합할 수 있습니다.
- **활성 Pod 수**: Kubernetes의 Pod 수 및 상태입니다. 표시된 Pod의 상태는 총계, 보류 중, 실행 중, 알 수 없음, 성공 또는 실패입니다. 개별적으로 필터링하거나 차트 위의 선택기에서 결합할 수 있습니다.

왼쪽 및 오른쪽 화살표 키를 사용하여 차트의 각 데이터 포인트를 순환합니다. 위쪽 및 아래쪽 화살표 키를 사용하여 백분위수 선을 순환합니다. 차트 중 하나의 오른쪽 위 모퉁이에 있는 고정 아이콘을 선택하면 선택한 차트가 마지막으로 본 Azure 대시보드에 고정됩니다. 대시보드에서 차트의 크기를 조정하고 위치를 변경할 수 있습니다. 대시보드에서 차트를 선택하면 컨테이너 인사이트로 리디렉션되고 올바른 범위와 보기가 로드됩니다.

컨테이너 인사이트는 또한 Azure Monitor [메트릭 탐색기](../essentials/metrics-getting-started.md)를 지원합니다. 여기에서 고유한 플롯 차트를 만들고, 추세를 연관시키고 조사하며, 대시보드에 고정할 수 있습니다. 메트릭 탐색기에서 설정한 기준을 사용하여 메트릭을 [메트릭 기반 경고 규칙](../alerts/alerts-metric.md)의 기반으로 시각화할 수도 있습니다.

## <a name="view-container-metrics-in-metrics-explorer"></a>메트릭 탐색기에서 컨테이너 메트릭 보기

컨테이너 인사이트에서 집계된 노드 및 Pod 사용률 메트릭을 메트릭 탐색기에서 볼 수 있습니다. 다음 표에는 메트릭 차트를 사용하여 컨테이너 메트릭을 시각화하는 방법을 이해하는 데 도움이 되는 세부 정보가 요약되어 있습니다.

|네임스페이스 | 메트릭 | 설명 |
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | 클러스터 전체에서 집계된 CPU 사용률에 대한 측정입니다. CPU 코어를 1000단위(milli = 1000)로 나눈 것입니다. 많은 애플리케이션이 하나의 코어를 사용할 수 있는 컨테이너의 코어 사용량을 결정하는 데 사용됩니다.|
| | cpuUsagePercentage | 클러스터 전체에서 집계된 평균 CPU 사용률(백분율) 측정값입니다.|
| | memoryRssBytes | 사용되는 컨테이너 RSS 메모리(바이트)입니다.|
| | memoryRssPercentage | 사용되는 컨테이너 RSS 메모리(%)입니다.|
| | memoryWorkingSetBytes | 사용되는 컨테이너 작업 집합 메모리입니다.|
| | memoryWorkingSetPercentage | 사용되는 컨테이너 작업 집합 메모리(백분율)입니다. |
| | nodesCount | Kubernetes의 노드 수입니다.|
| insights.container/pods | |
| | PodCount | Kubernetes의 Pod 수입니다.|

메트릭을 [분할](../essentials/metrics-charts.md#apply-splitting)하여 차원별로 보고 메트릭의 여러 세그먼트가 서로 비교되는 방식을 시각화할 수 있습니다. 노드의 경우 *호스트* 차원으로 차트를 분할할 수 있습니다. Pod에서 다음 차원으로 분류할 수 있습니다.

* 컨트롤러
* Kubernetes 네임스페이스
* 노드
* 단계

## <a name="analyze-nodes-controllers-and-container-health"></a>노드, 컨트롤러 및 컨테이너 상태 분석

**노드**, **컨트롤러** 및 **컨테이너** 탭으로 전환하면 페이지 오른쪽에 속성 창이 자동으로 표시됩니다. Kubernetes 개체를 구성하기 위해 정의한 레이블을 포함하는 선택한 항목의 속성을 보여 줍니다. Linux 노드를 선택하면 **로컬 디스크 용량** 섹션에 사용 가능한 디스크 공간과 노드에 제공된 각 디스크에 사용된 백분율도 표시됩니다. 창을 보거나 숨기려면 창에서 **>>** 링크를 선택합니다.

계층 구조에서 개체를 펼치면 선택한 개체에 따라 속성 창이 업데이트됩니다. 창에서 창 상단의 **실시간 데이터 보기(미리 보기)** 링크를 선택하여 Kubernetes 컨테이너 로그(stdout/stderror), 이벤트 및 Pod 메트릭을 볼 수도 있습니다. 이 데이터를 보기 위한 액세스 권한을 부여하고 제어하는 데 필요한 구성에 대한 자세한 내용은 [라이브 데이터 설정(미리 보기)](container-insights-livedata-setup.md)을 참조하세요. 클러스터 리소스를 검토하는 동안 컨테이너에서 이 데이터를 실시간으로 볼 수 있습니다. 이 기능에 대한 자세한 내용은 [Kubernetes 로그, 이벤트 및 Pod 메트릭을 실시간으로 보는 방법](container-insights-livedata-overview.md)을 참조하세요. 사전 정의된 로그 검색을 기반으로 작업 영역에 저장된 Kubernetes 로그 데이터를 보려면 **분석에서 보기** 드롭다운 목록에서 **컨테이너 로그 보기** 를 선택합니다. 이 항목에 대한 자세한 내용은 [컨테이너 인사이트에서 로그를 쿼리하는 방법](container-insights-log-query.md)을 참조하세요.

페이지 상단의 **+ 필터 추가** 옵션을 사용하여 **서비스**, **노드**, **네임스페이스** 또는 **노드 풀** 에 따라 보기 결과를 필터링합니다. 필터 범위를 선택한 후 **값 선택** 필드에 표시된 값 중 하나를 선택합니다. 필터가 구성되면 AKS 클러스터의 측면을 살펴볼 때 필터가 전역적으로 적용됩니다. 수식은 등호만 지원됩니다. 첫 번째 필터에 필터를 추가하여 결과를 더 좁힐 수 있습니다. 예를 들어 **노드** 별로 필터를 지정하는 경우 두 번째 필터에 대해 **서비스** 또는 **네임스페이스** 만 선택할 수 있습니다.

한 탭에서 필터를 지정하면 다른 탭을 선택해도 계속 적용됩니다. 지정된 필터 옆에 있는 **x** 기호를 선택하면 삭제됩니다.

**노드** 탭으로 전환하면 행 계층 구조는 클러스터의 노드로 시작하는 Kubernetes 개체 모델을 따릅니다. 노드를 확장하여 노드에서 실행 중인 하나 이상의 Pod를 봅니다. 둘 이상의 컨테이너가 Pod에 그룹화되면 계층 구조의 마지막 행으로 표시됩니다. 또한 호스트에 프로세서 또는 메모리 부족이 있는 경우 호스트에서 실행 중인 비 Pod 관련 워크로드의 수를 볼 수도 있습니다.

![성능 보기의 Kubernetes 노드 계층 예](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server 2019 OS를 실행하는 Windows Server 컨테이너는 목록의 모든 Linux 기반 노드 뒤에 표시됩니다. Windows Server 노드를 확장하면 노드에서 실행되는 하나 이상의 Pod 및 컨테이너를 볼 수 있습니다. 노드를 선택하면 속성 창에 버전 정보가 표시됩니다.

![Windows Server 노드가 나열된 노드 계층 예](./media/container-insights-analyze/nodes-view-windows.png)

Linux OS를 실행하는 Azure Container Instances 가상 노드는 목록에서 마지막 AKS 클러스터 노드 뒤에 표시됩니다. 컨테이너 인스턴스 가상 노드를 확장하면 노드에서 실행되는 하나 이상의 컨테이너 인스턴스 Pod 및 컨테이너를 볼 수 있습니다. 노드가 아닌 Pod에 대한 메트릭만 수집 및 보고됩니다.

![Container Instances가 나열된 노드 계층 구조 예제](./media/container-insights-analyze/nodes-view-aci.png)

펼친 노드에서, 노드에서 실행되는 Pod 또는 컨테이너에서 컨트롤러로 드릴다운하여 해당 컨트롤러에 대해 필터링된 성능 데이터를 볼 수 있습니다. 특정 노드에 대한 **컨트롤러** 열 아래의 값을 선택합니다.

![성능 보기에서 노드에서 컨트롤러로의 드릴다운을 보여 주는 스크린샷](./media/container-insights-analyze/drill-down-node-controller.png)

페이지의 위쪽에서 컨트롤러 또는 컨테이너를 선택하여 해당 개체에 대한 상태 및 리소스 사용률을 검토합니다. 메모리 사용률을 검토하려면 **메트릭** 드롭다운 목록에서 **메모리 RSS** 또는 **메모리 작업 집합** 을 선택합니다. **메모리 RSS** 는 Kubernetes 버전 1.8 이상에서만 지원됩니다. 그렇지 않으면 **Min&nbsp;%** 에 대한 값이 *NaN&nbsp;%* 로 표시됩니다. 이것은 정의되지 않았거나 표현할 수 없는 값을 나타내는 숫자 데이터 형식 값입니다.

![컨테이너 노드 성능 보기](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**메모리 작업 집합** 은 포함된 상주 메모리와 가상 메모리(캐시)를 모두 표시하며 애플리케이션이 사용하는 총계입니다. **메모리 RSS** 는 주 메모리(즉, 상주 메모리에 불과함)만 표시합니다. 이 메트릭은 사용 가능한 메모리의 실제 용량을 보여 줍니다. 상주 메모리와 가상 메모리의 차이점은 무엇인가요?

- 상주 메모리 또는 주 메모리는 클러스터의 노드에서 사용할 수 있는 실제 시스템 메모리 양입니다.

- 가상 메모리는 운영 체제가 메모리 부족 상태일 때 데이터를 메모리에서 디스크로 교환하고 필요할 때 메모리로 다시 가져오기 위해 사용하는 예약된 하드 디스크 공간(캐시)입니다.

기본적으로 성능 데이터는 마지막 6시간을 기준으로 하지만, 왼쪽 위에 있는 **TimeRange** 옵션을 사용하여 기간을 변경할 수 있습니다. 백분위 선택기에서 **최소**, **평균**, **50번째**, **90번째**, **95번째** 및 **최대** 를 선택하여 시간 범위 내에서 결과를 필터링할 수도 있습니다.

![데이터 필터링에 대한 백분위 선택 영역](./media/container-insights-analyze/containers-metric-percentile-filter.png)

**추세** 열 아래 막대 그래프 위로 마우스를 가져가면 선택한 메트릭에 따라 15분의 샘플 기간 동안 각 막대에 CPU 또는 메모리 사용량이 표시됩니다. 키보드를 통해 추세 차트를 선택한 후 Alt+Page Up 키 또는 Alt+Page Down 키를 사용하여 각 막대를 개별적으로 순환합니다. 막대 위로 마우스를 가져갔을 때와 동일한 세부 정보를 얻을 수 있습니다.

![추세 가로 막대형 차트 마우스로 가리키기 예](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

다음 예에서 목록의 첫 번째 노드인 *aks-nodepool1-* 의 경우 **컨테이너** 값은 9입니다. 이 값은 배포된 총 컨테이너 수의 롤업입니다.

![노드당 컨테이너 롤업 예](./media/container-insights-analyze/containers-nodes-containerstotal.png)

이 정보를 통해 클러스터의 노드 간에 컨테이너가 적절히 분산되어 있는지 여부를 빠르게 알 수 있습니다.

**노드** 탭을 볼 때 표시되는 정보는 다음 표에 설명되어 있습니다.

| 열 | 설명 |
|--------|-------------|
| Name | 호스트의 이름입니다. |
| 상태 | 노드 상태의 Kubernetes 보기입니다. |
| 최소&nbsp;%, 평균&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;%, 95번째&nbsp;%, 최대&nbsp;%  | 선택한 기간 동안 백분위에 기반한 평균 노드 백분율입니다. |
| 최소, 평균, 50번째, 90번째, 95번째, 최대 | 선택한 기간 동안 백분위를 기준으로 하는 평균 노드 실제 값입니다. 평균 값은 노드에 대해 설정된 CPU/메모리 제한에서 측정됩니다. Pod 및 컨테이너의 경우 호스트에서 보고한 평균 값입니다. |
| 컨테이너 | 컨테이너의 수입니다. |
| 작동 시간 | 노드가 시작되었거나 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| 컨트롤러 | 컨테이너 및 Pod용입니다. 어떤 컨트롤러에 있는지 보여 줍니다. 모든 Pod가 컨트롤러에 있는 것은 아니므로 **N/A** 가 표시될 수 있습니다. |
| 추세 최소&nbsp;%, 평균&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;%, 95번째&nbsp;%, 최대&nbsp;% | 컨트롤러의 평균 백분위 메트릭 비율을 나타내는 막대 그래프 추세입니다. |

**기타 프로세스** 라는 노드를 확장한 후 워크로드를 확인할 수 있습니다. 노드에서 실행되는 컨테이너화되지 않은 프로세스를 나타내며 다음을 포함합니다.

* 자체 관리형 또는 Kubernetes 관리형 컨테이너화되지 않은 프로세스

* 컨테이너 런타임 프로세스

* kubelet

* 노드에서 실행되는 시스템 프로세스

* 노드 하드웨어 또는 VM에서 실행되는 기타 비 Kubernetes 워크로드

*CAdvisor의 총 사용량* - *컨테이너화된 프로세스의 사용량* 으로 계산됩니다.

선택기에서 **컨트롤러** 를 선택합니다.

![컨트롤러 보기 선택](./media/container-insights-analyze/containers-controllers-tab.png)

여기서 컨트롤러와 Container Instances 가상 노드 컨트롤러 또는 컨트롤러에 연결되지 가상 노드 Pod의 성능 상태를 볼 수 있습니다.

![\<이름> 컨트롤러 성능 보기](./media/container-insights-analyze/containers-controllers-view.png)

행 계층 구조는 컨트롤러로 시작합니다. 컨트롤러를 확장하면 하나 이상의 Pod가 표시됩니다. Pod를 확장하면 마지막 행에 Pod로 그룹화된 컨테이너가 표시됩니다. 펼친 컨트롤러에서 실행되는 노드로 드릴다운하여 해당 노드에 대해 필터링된 성능 데이터를 볼 수 있습니다. 컨트롤러에 연결되지 않은 Container Instances Pod는 목록의 마지막에 나열됩니다.

![Container Instances Pod가 나열된 컨트롤러 계층 구조 예제](./media/container-insights-analyze/controllers-view-aci.png)

특정 컨트롤러의 **노드** 열에서 값을 선택합니다.

![성능 보기에서 컨트롤러에서 노드로 드릴다운 예](./media/container-insights-analyze/drill-down-controller-node.png)

컨트롤러를 볼 때 표시되는 정보는 다음 표에 설명되어 있습니다.

| 열 | 설명 |
|--------|-------------|
| Name | 컨트롤러의 이름입니다.|
| 상태 | *정상*, *종료됨*, *실패함*, *중지됨* 또는 *일시 정지됨* 과 같은 상태로 실행이 완료된 컨테이너의 롤업 상태입니다. 컨테이너가 실행 중이지만 상태가 제대로 표시되지 않았거나 에이전트에 의해 선택되지 않았고 30분 넘게 응답하지 않은 경우 상태는 *알 수 없음* 이 됩니다. 상태 아이콘에 대한 자세한 내용은 다음 표에 나와 있습니다.|
| 최소&nbsp;%, 평균&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;%, 95번째&nbsp;%, 최대&nbsp;%| 선택한 메트릭 및 백분위에 대한 각 엔터티 평균 백분율의 평균 롤업입니다. |
| 최소, 평균, 50번째, 90번째, 95번째, 최대  | 선택된 백분위에 대한 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다. 평균 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| 컨테이너 | 컨트롤러 또는 Pod에 대한 컨테이너의 총 수입니다. |
| Restarts | 컨테이너에서 다시 시작한 횟수의 롤업입니다. |
| 작동 시간 | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| 노드 | 컨테이너 및 Pod용입니다. 어떤 컨트롤러에 있는지 보여 줍니다. |
| 추세 최소&nbsp;%, 평균&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;%, 95번째&nbsp;%, 최대&nbsp;% | 컨트롤러의 평균 백분위 메트릭을 나타내는 막대 그래프 추세입니다. |

상태 필드의 아이콘은 컨테이너의 온라인 상태를 나타냅니다.

| 아이콘 | 상태 |
|--------|-------------|
| ![실행이 준비됨 상태 아이콘](./media/container-insights-analyze/containers-ready-icon.png) | 실행 중(준비됨)|
| ![대기 중 또는 일시 중지됨 상태 아이콘](./media/container-insights-analyze/containers-waiting-icon.png) | 대기 중 또는 일시 중지됨|
| ![마지막으로 보고된 실행 중 상태 아이콘](./media/container-insights-analyze/containers-grey-icon.png) | 하지만 마지막으로 보고된 실행 중 상태는 30분 넘게 응답되지 않았습니다.|
| ![성공 상태 아이콘](./media/container-insights-analyze/containers-green-icon.png) | 성공적으로 중지됨 또는 중지하지 못함|

상태 아이콘은 Pod에서 제공하는 것에 따라 개수를 보여줍니다. 두 가지 나쁜 상태를 보여주고 있으며, 상태를 마우스로 가리키면 컨테이너에 있는 모든 Pod의 롤업 상태가 표시됩니다. 준비 상태가 아니면 상태 값은 **(0)** 을 표시합니다.

선택기에서 **컨테이너** 를 선택합니다.

![컨테이너 보기 선택](./media/container-insights-analyze/containers-containers-tab.png)

여기서 Azure Kubernetes 및 Azure Container Instances 컨테이너의 성능 상태를 볼 수 있습니다.

![\<이름> 컨테이너 성능 보기](./media/container-insights-analyze/containers-containers-view.png)

컨테이너에서 Pod 또는 노드로 드릴다운하여 해당 개체에 대해 필터링된 성능 데이터를 볼 수 있습니다. 특정 컨테이너의 **Pod** 또는 **노드** 열에서 값을 선택합니다.

![성능 보기에서 노드에서 컨테이너로 드릴다운의 예](./media/container-insights-analyze/drill-down-controller-node.png)

컨테이너를 볼 때 표시되는 정보는 다음 표에 설명되어 있습니다.

| 열 | 설명 |
|--------|-------------|
| Name | 컨트롤러의 이름입니다.|
| 상태 | 컨테이너의 상태입니다(있는 경우). 상태 아이콘에 대한 자세한 내용은 아래 표에 나와 있습니다.|
| 최소&nbsp;%, 평균&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;%, 95번째&nbsp;%, 최대&nbsp;% | 선택한 메트릭 및 백분위에 대한 각 엔터티 평균 백분율의 롤업입니다. |
| 최소, 평균, 50번째, 90번째, 95번째, 최대 | 선택된 백분위에 대한 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다. 평균 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| Pod | Pod가 위치한 컨테이너입니다.|
| 노드 |  컨테이너가 있는 노드입니다. |
| Restarts | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| 작동 시간 | 컨테이너가 시작 또는 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| 추세 최소&nbsp;%, 평균&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;%, 95번째&nbsp;%, 최대&nbsp;% | 컨테이너의 평균 백분위 메트릭 비율을 나타내는 막대 그래프 추세입니다. |

상태 필드의 아이콘은 다음 표에 설명된 대로 Pod의 온라인 상태를 나타냅니다.

| 아이콘 | 상태 |
|--------|-------------|
| ![실행이 준비됨 상태 아이콘](./media/container-insights-analyze/containers-ready-icon.png) | 실행 중(준비됨)|
| ![대기 중 또는 일시 중지됨 상태 아이콘](./media/container-insights-analyze/containers-waiting-icon.png) | 대기 중 또는 일시 중지됨|
| ![마지막으로 보고된 실행 중 상태 아이콘](./media/container-insights-analyze/containers-grey-icon.png) | 하지만 마지막으로 보고된 실행 중 상태는 30분 넘게 응답되지 않았습니다.|
| ![종료됨 상태 아이콘](./media/container-insights-analyze/containers-terminated-icon.png) | 성공적으로 중지됨 또는 중지하지 못함|
| ![실패 상태 아이콘](./media/container-insights-analyze/containers-failed-icon.png) | 실패 상태 |

## <a name="monitor-and-visualize-network-configurations"></a>네트워크 구성 모니터링 및 시각화
Azure Network Policy Manager에는 네트워크 구성을 모니터링하고 더 잘 이해할 수 있는 유익한 Prometheus 메트릭이 포함되어 있습니다. Azure Portal 또는 Grafana Labs에서 기본 제공 시각화를 제공합니다. 자세한 내용은 [Azure NPM을 사용하여 네트워크 구성 모니터링 및 시각화](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm)를 참조하세요.


## <a name="workbooks"></a>통합 문서

통합 문서는 텍스트, 로그 쿼리, 메트릭 및 매개 변수를 클러스터 성능을 분석할 수 있는 풍부한 대화형 보고서로 결합합니다. 컨테이너 인사이트에 사용할 수 있는 통합 문서에 대한 설명은 [컨테이너 인사이트의 통합 문서](container-insights-reports.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [컨테이너 인사이트로 성능 알림 만들기](./container-insights-log-alerts.md)를 검토하여 DevOps 또는 운영 프로세스 및 절차를 지원하기 위해 높은 CPU 및 메모리 사용률에 대한 알림을 만드는 방법을 알아봅니다.

- 클러스터를 경고, 시각화 또는 분석하기 위해 평가하거나 사용자 지정하는 미리 정의된 쿼리 및 예제를 보려면 [로그 쿼리 예제](container-insights-log-query.md)를 확인하세요.

- Kubernetes 클러스터의 상태를 보는 방법에 대해 알아보려면 [클러스터 상태 모니터링](./container-insights-overview.md)을 참조하세요.