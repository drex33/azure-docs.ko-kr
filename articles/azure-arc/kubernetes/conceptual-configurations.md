---
title: 구성 및 GitOps-Azure Arc 사용 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes의 GitOps 및 구성 기능에 대 한 개념적 개요를 제공 합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너, 구성, GitOps
ms.openlocfilehash: 44b434b5140bbb60c41ce683833426afa4d4be02
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407497"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Azure Arc를 사용 하는 Kubernetes 구성 및 GitOps

> [!NOTE]
> 이 문서는 Flux v1을 사용 하는 GitOps를 위한 것입니다.  이제 Flux v2를 사용 하는 GitOps를 Azure Arc 사용 Kubernetes 및 AKS (Azure Kubernetes Service) 클러스터의 미리 보기로 사용할 수 있습니다. [Flux v2를 사용 하는 GitOps에 대해 알아봅니다](./conceptual-gitops-flux2.md).

Kubernetes와 관련해서 GitOps는 Git 리포지토리에서 Kubernetes 클러스터 구성(배포, 네임스페이스 등)의 원하는 상태를 선언하는 사례입니다. 이 선언 후에 연산자를 사용하여 이 클러스터 구성의 폴링 및 끌어오기 기반 배포를 수행합니다. Git 리포지토리에는 다음이 포함될 수 있습니다.
* 네임스페이스, ConfigMaps, 배포, DaemonSets 등을 비롯한 유효한 Kubernetes 리소스를 설명하는 YAML 형식 매니페스트
* 애플리케이션 배포를 위한 Helm 차트

GitOps 공간에서 널리 사용되는 오픈 소스 도구인 [Flux](https://docs.fluxcd.io/)를 Kubernetes 클러스터에 배포하여 Git 리포지토리에서 Kubernetes 클러스터로 구성을 쉽게 이동할 수 있습니다. Flux는 클러스터 및 네임스페이스 범위에서 둘 다 해당 연산자의 배포를 지원합니다. 네임스페이스 범위를 사용하여 배포된 flux 연산자는 해당 특정 네임스페이스 내에서 Kubernetes 개체만 배포할 수 있습니다. 클러스터 또는 네임스페이스 범위 중에 선택하는 기능은 동일한 Kubernetes 클러스터에서 다중 테넌트 배포 패턴을 구현하는 데 도움이 됩니다.

## <a name="configurations"></a>구성

[ ![구성 아키텍처](./media/conceptual-configurations.png) ](./media/conceptual-configurations.png#lightbox)

클러스터와 Git 리포지토리 간의 연결은 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure Resource Manager에서로 표시 되는 Azure Arc 사용 Kubernetes 리소스 () 위에 구성 리소스 ()로 생성 됩니다 `Microsoft.Kubernetes/connectedClusters` . 

구성 리소스 속성은 매니페스트를 끌어올 Git 리포지토리 및 폴링 간격과 같은 적절한 매개 변수를 사용하여 클러스터에 Flux 연산자를 배포하는 데 사용됩니다. 구성 리소스 데이터는 데이터 기밀을 보장하기 위해 Azure Cosmos DB 데이터베이스에 미사용 암호화 상태로 저장됩니다.

클러스터에서 실행되는 `config-agent`는 다음을 수행해야 합니다.
* Azure Arc 사용 Kubernetes 리소스에서 새로운 또는 업데이트 된 구성 리소스를 추적 합니다.
* 각 구성 리소스에 대한 Git 리포지토리를 감시하기 위해 Flux 연산자 배포.
* 구성 리소스에 대한 업데이트 적용. 

다중 테 넌 트를 얻기 위해 동일한 Azure Arc 사용 Kubernetes 클러스터에서 네임 스페이스 범위 구성 리소스를 여러 개 만들 수 있습니다.

> [!NOTE]
> * `config-agent` Azure Arc 사용 Kubernetes 리소스에서 사용할 수 있는 새로운 또는 업데이트 된 구성 리소스를 모니터링 합니다. 따라서 에이전트는 클러스터로 끌어올 수 있도록 원하는 상태를 연결해야 합니다. 에이전트가 Azure에 연결할 수 없는 경우 원하는 상태를 클러스터로 전파하는 작업이 지연됩니다.
> * 개인 키, 알려진 호스트 콘텐츠, HTTPS 사용자 이름 및 토큰/암호와 같은 중요 한 고객 입력은 Azure Arc 사용 Kubernetes 서비스에서 48 시간 넘게 저장 되지 않습니다. 구성에 중요한 입력을 사용하는 경우 최대한 정기적으로 클러스터를 온라인으로 전환합니다.

## <a name="apply-configurations-at-scale"></a>대규모 구성 적용

Azure Resource Manager는 구성을 관리 하므로 구독 또는 리소스 그룹의 범위 내에서 Azure Policy를 사용 하 여 모든 Azure Arc 사용 Kubernetes 리소스에 대해 동일한 구성 만들기를 자동화할 수 있습니다. 

이 크기 조정 적용은 Azure Arc 사용 Kubernetes 클러스터의 전체 재고 또는 인벤토리에 걸쳐 공통 기준 구성 (ClusterRoleBindings, RoleBindings 및 NetworkPolicy와 같은 구성 포함)을 적용할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* Azure Arc 사용 Kubernetes 클러스터에 대 한 [구성을 만듭니다](./tutorial-use-gitops-connected-cluster.md) .
* [Azure Policy를 사용하여 대규모로 구성을 적용](./use-azure-policy.md)합니다.
