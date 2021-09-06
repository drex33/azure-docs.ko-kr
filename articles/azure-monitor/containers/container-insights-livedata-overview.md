---
title: 컨테이너 인사이트를 사용하여 라이브 데이터 보기 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트에서 kubectl을 사용하지 않고 Kubernetes 로그, 이벤트 및 Pod 메트릭을 실시간으로 보는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: references_regions
ms.openlocfilehash: 9c1cbaf09d8f0b0cfe23f7014c1b5046efc5aab9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536300"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Kubernetes 로그, 이벤트 및 Pod 메트릭을 실시간으로 보는 방법

컨테이너 인사이트에는 AKS(Azure Kubernetes Service) 컨테이너 로그(stdout/stderror), 이벤트 및 Pod 메트릭에 직접 액세스할 수 있는 고급 진단 기능인 라이브 데이터 기능이 포함되어 있습니다. 컨테이너 인사이트는 `kubectl logs -c`, `kubectl get` 이벤트 및 `kubectl top pods`에 대한 직접 액세스를 노출합니다. 콘솔 창에는 실시간 문제 해결에 도움이 되도록 컨테이너 엔진에서 생성한 로그, 이벤트 및 메트릭이 표시됩니다.

이 문서는 자세한 개요를 제공하고 이 기능을 사용하는 방법을 설명합니다.

라이브 데이터 기능을 설정하거나 문제를 해결하는 데 도움이 필요하면 [설치 가이드](container-insights-livedata-setup.md)를 검토하세요. 이 기능은 Kubernetes API에 직접 액세스하며, 인증 모델에 대한 추가 정보는 [여기](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)에서 찾을 수 있습니다.

## <a name="view-aks-resource-live-logs"></a>AKS 리소스 라이브 로그 보기
AKS 리소스 뷰에서 컨테이너 인사이트를 사용하여 또는 사용하지 않고 Pod, 배포 및 복제본 세트에 대한 라이브 로그를 보려면 다음 절차를 수행합니다.

1. Azure Portal에서 AKS 클러스터 리소스 그룹으로 이동하여 AKS 리소스를 선택합니다.

2. 메뉴의 **Kubernetes 리소스** 섹션에서 **워크로드** 를 선택합니다.

3. 각 탭에서 Pod, 배포, 복제본 세트를 선택합니다.

4. 리소스의 메뉴에서 **라이브 로그** 를 선택합니다.

5. Pod를 선택하여 라이브 데이터 수집을 시작합니다.

    [![배포 라이브 로그](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>로그 보기

**노드**, **컨트롤러** 및 **컨테이너** 보기에서 컨테이너 엔진이 생성하는 실시간 로그 데이터를 볼 수 있습니다. 로그 데이터를 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 AKS 클러스터 리소스 그룹으로 이동하여 AKS 리소스를 선택합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **인사이트** 를 선택합니다.

3. **노드**, **컨트롤러** 또는 **컨테이너** 탭을 선택합니다.

4. 성능 그리드에서 개체를 선택하고, 오른쪽에 있는 속성 창에서 **라이브 데이터 보기** 옵션을 선택합니다. Azure AD를 사용하여 AKS 클러스터에 SSO가 구성된 경우에는 해당 브라우저 세션 동안 처음 사용할 때 인증을 요구하는 메시지가 표시됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.

    >[!NOTE]
    >속성 창에서 **분석에서 보기** 옵션을 선택하여 Log Analytics 작업 영역에서 데이터를 볼 때, 로그 검색 결과에 더 이상 존재하지 않는 **노드**, **디먼 세트**, **복제본 세트**, **작업**, **cron 작업**, **Pod** 및 **컨테이너** 가 표시될 수 있습니다. 로그에서 `kubectl`에 사용할 수 없는 컨테이너를 검색하려고 하면 마찬가지로 여기서 실패합니다. 기록 로그, 이벤트 및 메트릭을 보는 방법에 대한 자세한 내용은 [컨테이너 인사이트에서 로그를 쿼리하는 방법](container-insights-log-query.md)을 검토하세요.

인증이 성공하면 라이브 데이터 콘솔 창이 성능 데이터 그리드 아래에 표시되며, 여기서 로그 데이터를 연속 스트림으로 볼 수 있습니다. 페치 상태 표시기에 창의 맨 오른쪽에 있는 녹색 확인 표시가 나타나면 데이터를 검색할 수 있으며 콘솔에 대한 스트리밍을 시작합니다.

![노드 속성 창 데이터 보기 옵션](./media/container-insights-livedata-overview/node-properties-pane.png)

창 제목에는 컨테이너가 그룹화된 Pod 이름이 표시됩니다.

## <a name="view-events"></a>이벤트 보기

컨테이너, Pod, 노드, ReplicaSet, DaemonSet, 작업, CronJob 또는 배포가 선택된 경우 **노드**, **컨트롤러**, **컨테이너** 및 **배포** 보기에서 컨테이너 엔진이 생성하는 실시간 이벤트 데이터를 볼 수 있습니다. 이벤트를 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 AKS 클러스터 리소스 그룹으로 이동하여 AKS 리소스를 선택합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **인사이트** 를 선택합니다.

3. **노드**, **컨트롤러**, **컨테이너** 또는 **배포** 탭을 선택합니다.

4. 성능 그리드에서 개체를 선택하고, 오른쪽에 있는 속성 창에서 **라이브 데이터 보기** 옵션을 선택합니다. Azure AD를 사용하여 AKS 클러스터에 SSO가 구성된 경우에는 해당 브라우저 세션 동안 처음 사용할 때 인증을 요구하는 메시지가 표시됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.

    >[!NOTE]
    >속성 창에서 **분석에서 보기** 옵션을 선택하여 Log Analytics 작업 영역에서 데이터를 볼 때, 로그 검색 결과에 더 이상 존재하지 않는 **노드**, **디먼 세트**, **복제본 세트**, **작업**, **cron 작업**, **Pod** 및 **컨테이너** 가 표시될 수 있습니다. 로그에서 `kubectl`에 사용할 수 없는 컨테이너를 검색하려고 하면 마찬가지로 여기서 실패합니다. 기록 로그, 이벤트 및 메트릭을 보는 방법에 대한 자세한 내용은 [컨테이너 인사이트에서 로그를 쿼리하는 방법](container-insights-log-query.md)을 검토하세요.

인증이 성공하면 성능 데이터 그리드 아래에 라이브 데이터 콘솔 창이 나타납니다. 페치 상태 표시기에 창의 맨 오른쪽에 있는 녹색 확인 표시가 나타나면 데이터를 검색할 수 있으며 콘솔에 대한 스트리밍을 시작합니다.

선택한 개체가 컨테이너인 경우 창에서 **이벤트** 옵션을 선택합니다. 노드, Pod 또는 컨트롤러를 선택한 경우 이벤트 보기가 자동으로 선택됩니다.

![컨트롤러 속성 창 보기 이벤트](./media/container-insights-livedata-overview/controller-properties-live-event.png)

창 제목에는 컨테이너가 그룹화된 Pod 이름이 표시됩니다.

### <a name="filter-events"></a>이벤트 필터링

이벤트를 보는 동안 검색 창의 오른쪽에 있는 **필터** 필을 사용하여 결과를 추가로 제한할 수 있습니다. 선택한 리소스에 따라 필에는 선택 가능한 Pod, 네임스페이스 또는 클러스터가 나열됩니다.

## <a name="view-metrics"></a>메트릭 보기

**Pod** 를 선택한 경우에만 **노드** 또는 **컨트롤러** 보기에서 컨테이너 엔진이 생성하는 실시간 메트릭 데이터를 볼 수 있습니다. 메트릭을 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 AKS 클러스터 리소스 그룹으로 이동하여 AKS 리소스를 선택합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **인사이트** 를 선택합니다.

3. **노드** 또는 **컨트롤러** 탭을 선택합니다.

4. 성능 그리드에서 **Pod** 개체를 선택하고, 오른쪽에 있는 속성 창에서 **라이브 데이터 보기** 옵션을 선택합니다. Azure AD를 사용하여 AKS 클러스터에 SSO가 구성된 경우에는 해당 브라우저 세션 동안 처음 사용할 때 인증을 요구하는 메시지가 표시됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.

    >[!NOTE]
    >속성 창에서 **분석에서 보기** 옵션을 선택하여 Log Analytics 작업 영역에서 데이터를 볼 때, 로그 검색 결과에 더 이상 존재하지 않는 **노드**, **디먼 세트**, **복제본 세트**, **작업**, **cron 작업**, **Pod** 및 **컨테이너** 가 표시될 수 있습니다. 로그에서 `kubectl`에 사용할 수 없는 컨테이너를 검색하려고 하면 마찬가지로 여기서 실패합니다. 기록 로그, 이벤트 및 메트릭을 보는 방법에 대한 자세한 내용은 [컨테이너 인사이트에서 로그를 쿼리하는 방법](container-insights-log-query.md)을 검토하세요.

인증이 성공하면 성능 데이터 그리드 아래에 라이브 데이터 콘솔 창이 나타납니다. 메트릭 데이터가 검색되고 2개 차트에 표시하기 위해 데이터가 콘솔로 스트리밍되기 시작합니다. 창 제목에는 컨테이너가 그룹화된 Pod 이름이 표시됩니다.

![Pod 메트릭 예제 보기](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>라이브 데이터 보기 사용
다음 섹션에서는 다양한 라이브 데이터 보기에서 사용할 수 있는 기능에 대해 설명합니다.

### <a name="search"></a>검색
라이브 데이터 기능에는 검색 기능이 포함되어 있습니다. **검색** 필드에서 키워드 또는 용어를 입력하여 결과를 필터링할 수 있으며, 빠르게 검토할 수 있도록 일치하는 결과가 강조 표시됩니다. 이벤트를 보는 동안 검색 창의 오른쪽에 있는 **필터** 필을 사용하여 결과를 추가로 제한할 수 있습니다. 선택한 리소스에 따라 필에는 선택 가능한 Pod, 네임스페이스 또는 클러스터가 나열됩니다.

![라이브 데이터 콘솔 창 필터 예제](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![배포에 대한 라이브 데이터 콘솔 창 필터 예제](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>스크롤 잠금 및 일시 중지

자동 스크롤을 일시 중단하고 창의 동작을 제어하여 새 데이터 읽기를 수동으로 스크롤하려면 **스크롤** 옵션을 사용하면 됩니다. 자동 스크롤을 다시 사용하려면 **스크롤** 옵션을 다시 선택하면 됩니다. 또한 **일시 중지** 옵션을 선택하여 로그 또는 이벤트 데이터 검색을 일시 중지할 수 있으며, 다시 시작할 준비가 되면 **재생** 을 선택하면 됩니다.

![라이브 데이터 콘솔 창 라이브 보기 일시 중지](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![라이브 데이터 콘솔 창 배포에 대한 라이브 보기 일시 중지](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>문제를 해결하는 동안 잠시 자동 스크롤을 일시 중단하거나 일시 중지하는 것이 좋습니다. 이러한 요청은 클러스터의 Kubernetes API 가용성 및 제한에 영향을 줄 수 있습니다.

>[!IMPORTANT]
>이 기능을 수행하는 동안에는 데이터가 영구적으로 저장되지 않습니다. 세션 중에 캡처된 모든 정보는 브라우저를 닫거나 다른 곳으로 이동하면 삭제됩니다. 데이터는 메트릭 기능의 5분 동안만 시각화를 위해 남아 있고, 5분이 지나면 메트릭도 영구적으로 삭제됩니다. 라이브 데이터 버퍼는 적절한 메모리 사용 한도 내에서 쿼리합니다.

## <a name="next-steps"></a>다음 단계

- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.

- [컨테이너 인사이트에서 로그를 쿼리하는 방법](container-insights-log-query.md)에서 미리 정의된 쿼리와 예제를 보면서 경고와 시각화를 만들거나 클러스터 추가 분석을 수행합니다.
