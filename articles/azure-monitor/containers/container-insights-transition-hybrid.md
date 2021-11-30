---
title: Azure Arc 사용 Kubernetes 클러스터에서 컨테이너 Insights 사용으로 전환
ms.date: 04/05/2021
ms.topic: article
author: austonli
ms.author: aul
description: 스크립트 기반 하이브리드 모니터링 솔루션을 사용 하 여 Azure Arc 사용 Kubernetes 클러스터의 컨테이너 Insights로 마이그레이션하는 방법에 대해 알아봅니다.
ms.openlocfilehash: eb6fd5a65aaf6a6d17b6b61ea05bbdf227c72832
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289576"
---
# <a name="transition-to-using-container-insights-on-azure-arc-enabled-kubernetes"></a>Azure Arc에서 컨테이너 Insights 사용-사용 Kubernetes 전환

5 월 31 일에 2022 컨테이너 Insights Azure Red Hat openshift v4. x에 대 한 지원이 사용 중지 됩니다. azure Red Hat openshift. x에 대 한 컨테이너 Insights의 스크립트 기반 모델을 사용 하는 경우 해당 날짜 이전에 [azure Arc enabled Kubernetes](./container-insights-enable-arc-enabled-clusters.md) 의 컨테이너 Insights로 전환 해야 합니다.

## <a name="steps-to-complete-the-transition"></a>전환을 완료하기 위한 단계

Azure Arc 사용 Kubernetes의 컨테이너 Insights로 전환 하려면 다음 방법을 사용 하는 것이 좋습니다.

1. 컨테이너 Insights azure Red Hat openshift v4 .x 및 azure Arc enabled Kubernetes의 기능 차이점에 대해 알아봅니다.
2. Azure Red Hat OpenShift 클러스터에 대 한 [기존 모니터링 사용 안 함](./container-insights-optout-openshift-v4.md)
3. 요구 사항을 이해 하려면 [Azure Arc 사용 Kubernetes 클러스터 확장](../../azure-arc/kubernetes/extensions.md) 및 [컨테이너 Insights 온 보 딩 전제 조건](./container-insights-enable-arc-enabled-clusters.md#prerequisites) 에 대 한 설명서를 참조 하십시오.
4. [클러스터](../../azure-arc/kubernetes/quickstart-connect-cluster.md) 를 Azure Arc 사용 Kubernetes 플랫폼으로 커넥트
5. Azure Portal, CLI 또는 ARM을 사용 하 여 Azure Arc enabled Kubernetes의 [컨테이너 Insights 설정](./container-insights-enable-arc-enabled-clusters.md) 합니다.
6. 현재 구성이 작동 [하는지 확인](./container-insights-enable-arc-enabled-clusters.md#verify-extension-installation-status) 합니다.

## <a name="container-insights-on-azure-red-hat-openshift-v4x-vs-azure-arc-enabled-kubernetes"></a>azure Red Hat openshift v4 .x 및 azure Arc enabled Kubernetes의 컨테이너 Insights

다음 표에서는 azure Red Hat OpenShift v4. x 스크립트와 Azure Arc 사용 Kubernetes 클러스터 확장을 통한 모니터링 간의 주요 차이점을 보여 줍니다. azure Arc 사용 Kubernetes의 컨테이너 Insights는 azure Red Hat openshift v4. x에서의 실제 업그레이드를 제공 합니다.

| 기능 차이점  | Azure Red Hat OpenShift v. 4x 모니터링 | Azure Arc Kubernetes 모니터링 사용 |
| ------------------- | ----------------- | ------------------- |
| 온보딩 | 수동 스크립트 기반 설치만 | Azure Portal, CLI 또는 ARM을 통해 Azure Arc 클러스터 확장을 사용 하 여 단일 클릭으로 온 보 딩 |
| 경고 | 로그 기반 경고만 | 로그 기반 경고 및 [권장 되는 메트릭 기반](./container-insights-metric-alerts.md) 경고 |
| 메트릭 | Azure Monitor 메트릭을 지원 하지 않습니다. | Azure Monitor 메트릭 지원 |
| 소비 | Azure Monitor 블레이드에서 볼 수 있습니다. | Azure Monitor 및 Azure Arc enabled Kubernetes 리소스 블레이드에서 모두 액세스할 수 있습니다. |
| 에이전트 | 수동 에이전트 업그레이드 | Azure Arc 클러스터 확장을 통해 버전 제어를 사용 하는 모니터링 에이전트의 자동 업데이트 |
| 기능 패리티 | 2022 년 5 월을 초과 하는 추가 업데이트 없음 | 첫 번째 클래스 패리티 및 AKS의 컨테이너 Insights를 사용 하 여 인라인으로 업데이트 |

## <a name="next-steps"></a>다음 단계

- Azure Red Hat OpenShift v4. x 클러스터에 대 한 [기존 모니터링 사용 안 함](./container-insights-optout-openshift-v4.md) 
- [클러스터](../../azure-arc/kubernetes/quickstart-connect-cluster.md) 를 Azure Arc 사용 Kubernetes 플랫폼으로 커넥트
- Azure Arc 사용 Kubernetes에서 [컨테이너 Insights 구성](./container-insights-enable-arc-enabled-clusters.md)
