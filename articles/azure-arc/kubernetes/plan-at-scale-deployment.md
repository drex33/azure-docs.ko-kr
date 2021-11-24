---
title: Azure Arc 사용 Kubernetes을 계획 하 고 배포 하는 방법
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Azure Arc에 많은 수의 클러스터를 등록-구성 관리를 위해 사용 Kubernetes
ms.openlocfilehash: b0a4f63073c69cf0e37c28b8149ca1947917127b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051725"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Azure Arc 사용 Kubernetes 계획 및 배포

IT 인프라 서비스 또는 비즈니스 애플리케이션을 배포하는 것은 그 어떤 회사에게나 어려운 일입니다. 예기치 못한 상황과 계획에 없던 비용이 발생하는 것을 방지하려면 가능한 한 최선의 준비 상태를 유지하도록 철저히 계획해야 합니다. 이러한 계획을 통해 작업을 완료하기 위해 충족해야 하는 설계 및 배포 기준을 파악할 수 있어야 합니다.

배포가 원활하게 진행되도록 하려면 다음에 대한 명확한 이해가 수반되어야 합니다.

* 역할 및 책임
* 모든 Kubernetes 클러스터의 인벤토리
* 네트워킹 요구 사항 충족
* 성공적인 배포 및 지속적인 관리를 가능하게 하기 위해 필요한 기술 집합 및 교육
* 수용 기준 및 성공 추적 방법
* 배포를 자동화하는 데 사용할 도구 또는 방법
* 지연, 중단 등을 방지하기 위한 위험 식별 및 완화 계획
* 배포 시에 중단을 방지하는 방법
* 중요한 문제가 발생하는 경우에 대비하는 에스컬레이션 경로

이 문서의 목적은 사용자 환경의 여러 프로덕션 클러스터에서 Azure Arc 사용 Kubernetes를 성공적으로 배포할 수 있도록 준비 하는 것입니다.

## <a name="prerequisites"></a>필수 구성 요소

* 기존 Kubernetes 클러스터 클러스터가 없는 경우 이러한 옵션 중 하나를 사용하여 클러스터를 만들 수 있습니다.
    - [Docker의 Kubernetes(KIND)](https://kind.sigs.k8s.io/)
    - [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 또는 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)용 Docker를 사용하여 Kubernetes 클러스터 만들기
    - [클러스터 API](https://cluster-api.sigs.k8s.io/user/quick-start.html)를 사용하는 자체 관리되는 Kubernetes 클러스터

* 컴퓨터가 직접적이든 프록시 서버를 통해서든 온-프레미스 네트워크 또는 다른 클라우드 환경에서 Azure의 리소스에 대해 연결되어 있습니다. 자세한 내용은 [네트워크 사전 요구 사항](quickstart-connect-cluster.md#meet-network-requirements)에서 확인하세요.

* Azure Arc에 연결하고자 하는 클러스터를 가리키는 `kubeconfig` 파일.
* Azure Arc 사용 Kubernetes 리소스 유형을 만드는 사용자 또는 서비스 사용자에 대 한 ' 읽기 ' 및 ' 쓰기 ' 권한입니다 `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>파일럿

모든 프로덕션 클러스터에 배포하려면 환경에서 광범위하게 채택하기 전에 본 배포 프로세스를 평가하는 것부터 시작해야 합니다. 파일럿으로 회사에서 비즈니스를 수행하는 데 있어 중요하지 않은 클러스터의 대표적인 샘플링을 파악합니다. 파일럿을 실행하고 영향을 평가하려면 충분한 시간을 확보하는 것이 좋으며, 약 30일이 권장됩니다.

파일럿의 범위 및 세부 정보를 설명하는 공식 계획을 구성합니다. 다음 샘플 계획은 시작하는 데 도움이 됩니다.

* **목표** - 파일럿이 필요하다는 결정을 내리게끔 하는 비즈니스 및 기술 요인을 설명합니다.
* **선택 기준** - 파일럿을 통해 표시되는 솔루션의 측면을 선택하는 데 사용되는 기준을 지정합니다.
* **범위** - 솔루션 구성 요소, 예상 일정, 파일럿 기간 및 대상 클러스터 수를 포함합니다.
* **성공 기준 및 메트릭** - 파일럿의 성공 기준과 성공 수준을 결정하는 데 사용되는 특정 측정값을 정의합니다.
* **교육 계획** - 파일럿 진행 중에 Azure와 해당 서비스를 처음 접하는 시스템 엔지니어, 관리자 등의 교육에 대한 계획을 설명합니다.
* **전환 계획** - 파일럿에서 프로덕션으로의 전환을 안내하는 데 사용되는 전략과 기준을 설명합니다.
* **롤백** - 파일럿을 배포 전 상태로 롤백하는 절차를 설명합니다.
* **위험** - 파일럿 수행 및 프로덕션 배포와 관련하여 식별된 모든 위험을 나열합니다.

## <a name="phase-1-build-a-foundation"></a>1단계: 기반 빌드

이 단계에서 시스템 엔지니어 또는 관리자는 Azure Arc 사용 Kubernetes 리소스를 만들고 작동할 수 있도록 리소스 그룹, 태그, 역할 할당 생성 등의 핵심 활동을 수행 합니다.

|Task |세부 정보 |Duration |
|-----|-------|---------|
| [리소스 그룹 만들기](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Azure Arc 사용 Kubernetes 리소스만 포함 하 고 이러한 리소스에 대 한 중앙 집중식 관리 및 모니터링을 위한 전용 리소스 그룹입니다. | 1시간 |
| 컴퓨터를 구성하는 데 도움이 되는 [태그](../../azure-resource-manager/management/tag-resources.md) 적용 | IT에 맞춘 [태그 지정 전략](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)을 평가 및 개발합니다. 이를 통해 Azure Arc 사용 Kubernetes 리소스를 관리 하 고 관리 결정을 간소화 하는 복잡성을 줄일 수 있습니다. | 1일 |
| GitOps에 대한 [구성](tutorial-use-gitops-connected-cluster.md) 식별 | 클러스터에 배포하려는 애플리케이션 또는 기준 구성(예: `PodSecurityPolicy`, `NetworkPolicy`)을 식별합니다. | 1일 |
| [Azure Policy](../../governance/policy/overview.md) 거버넌스 계획 개발 | Azure Policy를 사용 하 여 구독 또는 리소스 그룹 범위에서 Azure Arc 사용 Kubernetes 클러스터의 관리를 구현 하는 방법을 결정 합니다. | 1일 |
| RBAC([역할 기반 액세스 제어](../../role-based-access-control/overview.md)) 구성 | 클러스터에 대한 읽기/쓰기/모든 권한이 있는 사용자를 식별하는 액세스 계획을 개발합니다. | 1일 |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>2 단계: Azure Arc 사용 Kubernetes 배포

이 단계에서는 Kubernetes 클러스터를 Azure에 연결합니다.

|Task |세부 정보 |Duration |
|-----|-------|---------|
| [첫 번째 Kubernetes 클러스터를 Azure Arc에 연결](quickstart-connect-cluster.md) | 첫 번째 클러스터를 Azure Arc에 연결하는 과정에서 Azure CLI, Helm 및 Azure CLI용 `connectedk8s` 확장과 같은 모든 필수 도구를 사용하여 온보딩 환경을 설정합니다. | 15분 |

## <a name="phase-3-manage-and-operate"></a>3단계: 관리 및 운영

이 단계에서는 Kubernetes 클러스터에 응용 프로그램 및 기준 구성을 배포합니다.

|Task |세부 정보 |Duration |
|-----|-------|---------|
|클러스터에 대한 [구성 만들기](tutorial-use-gitops-connected-cluster.md) | Azure Arc 사용 Kubernetes 리소스에 응용 프로그램을 배포 하기 위한 구성을 만듭니다. | 15분 |
|대규모 구성 적용을 위한 [Azure Policy](use-azure-policy.md) 사용 | 구독 또는 리소스 그룹 범위의 모든 클러스터에서 기준 구성 배포를 자동화하는 정책 할당을 만듭니다. | 15분 |
| [Azure Arc 에이전트 업그레이드](agent-upgrade.md) | 클러스터에서 에이전트 자동 업그레이드를 사용하지 않도록 설정한 경우 에이전트를 최신 버전으로 수동 업데이트하여 최신 보안 및 버그 수정이 있는지 확인합니다. | 15분 |

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* Azure Arc 사용 Kubernetes 클러스터에 대 한 [구성을 만듭니다](./tutorial-use-gitops-connected-cluster.md) .
* [Azure Policy를 사용하여 대규모로 구성을 적용](./use-azure-policy.md)합니다.
