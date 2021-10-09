---
title: Container Insights 개요 | Microsoft Docs
description: 이 문서에서는 AKS Container Insights 솔루션을 모니터링하는 Container Insights 및 Container Insights가 Azure의 AKS 클러스터 및 Container Instances 상태를 모니터링하여 제공하는 가치에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: b1e7b3e6d3c72dbc65169c48f1d12de6ca712a45
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708897"
---
# <a name="container-insights-overview"></a>컨테이너 인사이트 개요

Container Insights는 다음을 위해 배포된 컨테이너 워크로드의 성능을 모니터링하도록 설계된 기능입니다.

- [AKS(Azure Kubernetes Service)](../../aks/intro-kubernetes.md)에서 호스트되는 관리형 Kubernetes 클러스터
- [AKS 엔진](https://github.com/Azure/aks-engine)을 사용하여 Azure에서 호스트되는 자체 관리형 Kubernetes 클러스터
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) 또는 온-프레미스에서 호스트되는 자체 관리형 Kubernetes 클러스터
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Azure Arc 지원 Kubernetes(미리](../../azure-arc/kubernetes/overview.md) 보기)

Container Insights는 Linux 및 Windows Server 2019 운영 체제를 실행하는 클러스터를 지원합니다. 지원되는 컨테이너 런타임은 Docker, Moby 및 CRI 호환 런타임(예: CRI-O 및 ContainerD)입니다.

컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

Container Insights는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 컨테이너 로그도 수집됩니다.  Kubernetes 클러스터에서 모니터링을 사용하도록 설정하면 메트릭 및 로그가 Linux용 Log Analytics 에이전트의 컨테이너화된 버전을 통해 자동으로 수집됩니다. 메트릭은 메트릭 저장소에 기록되고 로그 데이터는 [Log Analytics](../logs/log-query-overview.md) 작업 영역과 연결된 로그 저장소에 기록됩니다.

![Container Insights 아키텍처](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>Container Insights는 무엇을 제공하나요?

Container Insights는 다양한 Azure Monitor 기능을 사용하여 포괄적인 모니터링 환경을 제공합니다. 이러한 기능을 사용하면 Linux 및 Windows Server 2019 운영 체제를 실행하는 Kubernetes 클러스터와 컨테이너 워크로드의 성능 및 상태를 이해할 수 있습니다. Container Insights를 사용하면 다음을 수행할 수 있습니다.

* 노드에서 실행 중인 AKS 컨테이너와 평균 프로세서 및 메모리 사용률을 확인합니다. 이 정보를 통해 리소스 병목 상태를 파악할 수 있습니다.
* Azure Container Instances에 호스트된 컨테이너 그룹 및 해당 컨테이너의 프로세서 및 메모리 사용률을 식별합니다.
* 컨트롤러 또는 Pod에서 컨테이너가 상주하는 위치를 확인합니다. 이 정보를 통해 컨트롤러 또는 Pod의 전반적인 성능을 볼 수 있습니다.
* Pod를 지원하는 표준 프로세스와 관련이 없는 호스트에서 실행되는 워크로드의 리소스 사용률을 검토합니다.
* 평균 부하 및 최고 부하 상태에서.클러스터의 동작을 이해합니다. 이 정보를 통해 용량 요구 사항을 파악하고 클러스터를 유지할 수 있는 최대 부하를 확인할 수 있습니다.
* 노드 또는 컨테이너의 CPU 및 메모리 사용률이 임계값을 초과하거나, 인프라 또는 노드 상태 롤업의 클러스터에서 상태 변경이 발생하는 경우 사전에 알리거나 기록하도록 경고를 구성합니다.
* [Prometheus](https://prometheus.io/docs/introduction/overview/)와 통합하여 [쿼리](container-insights-log-query.md)를 사용해 노드 및 Kubernetes에서 수집하는 애플리케이션 및 워크로드 메트릭을 확인함으로써 사용자 지정 경고, 대시보드를 만들고 세부 분석을 수행할 수 있습니다.
* 온-프레미스의 [AKS 엔진](https://github.com/Azure/aks-engine) 및 [Azure Stack의 AKS 엔진에 배포된](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) 컨테이너 워크로드를 모니터링합니다.
* [Azure Red Hat OpenShift에 배포된](../../openshift/intro-openshift.md) 컨테이너 워크로드를 모니터링합니다.

    >[!NOTE]
    >Azure Red Hat OpenShift에 대한 지원은 현재 공개 미리 보기 기능입니다.
    >

* Azure Arc 지원 [Kubernetes(미리 보기)에 배포된](../../azure-arc/kubernetes/overview.md)컨테이너 워크로드를 모니터링합니다.

Windows Server 클러스터를 모니터링할 때 Linux 클러스터와 크게 다른 점은 다음과 같습니다.

- Windows에는 메모리 RSS 메트릭이 없으므로 Windows 노드와 컨테이너에 사용할 수 없습니다. [작업 집합](/windows/win32/memory/working-set) 메트릭을 사용할 수 있습니다.
- Windows 노드에 대한 디스크 스토리지 용량 정보는 제공되지 않습니다.
- Pod 환경만 모니터링되고 Docker 환경은 모니터링되지 않습니다.
- 미리 보기 릴리스에서는 최대 30개의 Windows Server 컨테이너가 지원됩니다. Linux 컨테이너에는 이 제한이 적용되지 않습니다.

Container Insights를 사용하여 AKS 클러스터를 모니터링하는 방법을 알아보는 데 도움이 되는 중간 수준의 심층 분석을 제공하는 다음 동영상을 확인하세요.

> [!VIDEO https://www.youtube.com/embed/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>이 기능에 액세스하려면 어떻게 하나요?

Container Insights는 Azure Monitor에서 액세스하거나 선택한 AKS 클러스터에서 직접 액세스하는 등 두 가지 방법으로 액세스할 수 있습니다. Azure Monitor에서 배포된 모든 컨테이너, 모니터링되는 컨테이너 및 모니터링되지 않는 컨테이너에 대한 전역 큐브 뷰를 사용하면 구독 및 리소스 그룹 전체를 검색하고 필터링한 다음, 선택한 컨테이너의 Container Insights를 상세히 검색할 수 있습니다.  또는 AKS 페이지에서 선택한 AKS 컨테이너의 기능에 바로 액세스할 수 있습니다.

![Container Insights에 액세스하는 방법 개요](./media/container-insights-overview/azmon-containers-experience.png)

AKS 외부에서 실행되는 Docker 및 Windows 컨테이너 호스트를 모니터링하고 관리하여 구성, 감사 및 리소스 사용률을 확인하려는 경우 [컨테이너 모니터링 솔루션](./containers.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터 모니터링을 시작하려면 [Container Insights를 사용하도록 설정하는 방법](container-insights-onboard.md)을 검토하여 모니터링 사용을 위한 요구 사항과 사용 가능한 방법을 파악하세요.
