---
title: Azure Arc 사용 Kubernetes 에이전트 아키텍처
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes 에이전트의 아키텍처 개요를 제공 합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 43b6035ac5a4889fa55dbce6b5f85be8c4e386b4
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077546"
---
# <a name="azure-arc-enabled-kubernetes-agent-overview"></a>Azure Arc-사용 Kubernetes 에이전트 개요

[Kubernetes](https://kubernetes.io/) 는 하이브리드 및 다중 클라우드 환경에서 컨테이너 화 된 워크 로드를 일관 되 게 배포할 수 있습니다. Azure Arc 사용 Kubernetes는 중앙 집중식으로 일관 된 제어 평면을 제공 하 여 이러한 다른 유형의 환경에서 Kubernetes 클러스터에 걸쳐 정책, 거 버 넌 스 및 보안을 관리 합니다. 이 문서에서는 클러스터를 Azure Arc에 연결 하는 과정의 일부로 Kubernetes 클러스터에 배포 된 Azure Arc 에이전트의 개요를 제공 합니다.

## <a name="deploy-agents-to-your-cluster"></a>클러스터에 에이전트 배포

대부분 온-프레미스 데이터 센터는 네트워크 경계 방화벽에서 인바운드 통신을 차단하는 엄격한 네트워크 규칙을 적용합니다. Azure Arc 사용 Kubernetes는 방화벽에서 인바운드 포트를 요구 하지 않고 이러한 제한에 따라 작동 합니다. Azure Arc 에이전트는 네트워크 끝점의 필수 조건 목록에 대 한 아웃 바운드 통신만 필요 합니다.

![아키텍처 개요](./media/architectural-overview.png)

다음 단계는 Kubernetes 클러스터를 Azure Arc에 연결 하는 것과 관련이 있습니다.

1. 선택한 인프라(VMware vSphere, Amazon Web Services, Google Cloud Platform 등)에서 Kubernetes 클러스터를 만듭니다. 

    > [!NOTE]
    > Azure Arc 사용 Kubernetes 현재 Azure Arc에 기존 Kubernetes 클러스터를 연결 하는 것만 지원 하므로 고객은 Kubernetes 클러스터 자체의 수명 주기를 만들고 관리 해야 합니다.  

1. Azure CLI를 사용하여 클러스터에 대한 Azure Arc 등록을 시작합니다.
    * Azure CLI는 Helm을 사용하여 클러스터에 에이전트 Helm 차트를 배포합니다.
    * 클러스터 노드는 [Microsoft Container Registry](https://github.com/microsoft/containerregistry)에 대한 아웃바운드 통신을 시작하고 `azure-arc` 네임스페이스에 다음 에이전트를 만드는 데 필요한 이미지를 끌어옵니다.

        | 에이전트 | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc 사용 Kubernetes 현재 [시스템 할당 id](../../active-directory/managed-identities-azure-resources/overview.md)만 지원 합니다. `clusteridentityoperator`는 첫 번째 아웃바운드 통신을 시작합니다. 첫 번째 통신은 다른 에이전트가 Azure와 통신하기 위해 사용하는 MSI(관리되는 서비스 ID) 인증서를 가져옵니다. |
        | `deployment.apps/config-agent` | 클러스터에 적용된 원본 제어 구성 리소스의 연결된 클러스터를 감시합니다. 준수 상태를 업데이트합니다. |
        | `deployment.apps/controller-manager` | Azure Arc 구성 요소 간에 상호 작용을 오케스트레이션하는 연산자의 연산자입니다. |    
        | `deployment.apps/metrics-agent` | 다른 Arc 에이전트의 메트릭을 수집하여 최적 성능을 확인합니다. |
        | `deployment.apps/cluster-metadata-operator` | 클러스터 버전, 노드 수 및 Azure Arc 에이전트 버전을 포함한 클러스터 메타데이터를 수집합니다. |
        | `deployment.apps/resource-sync-agent` | 위에서 언급한 클러스터 메타데이터를 Azure에 동기화합니다. |
        | `deployment.apps/flux-logs-agent` | 원본 제어 구성의 일부로 배포된 flux 연산자에서 로그를 수집합니다. |
        | `deployment.apps/extension-manager` | 확장 Helm 차트의 수명 주기를 설치 및 관리합니다. |
        | `deployment.apps/kube-aad-proxy` | 클러스터 커넥트를 사용 하 여 클러스터로 전송 된 요청을 인증 하는 데 사용 됩니다. |
        | `deployment.apps/clusterconnect-agent` | 클러스터 커넥트 기능을 사용 하 여 클러스터에 대 한 액세스를 제공 하는 역방향 프록시 에이전트입니다 `apiserver` . `cluster-connect`기능이 클러스터에서 사용 되는 경우에만 배포 되는 선택적 구성 요소입니다.   |
        | `deployment.apps/guard` | AAD RBAC 기능에 사용되는 인증 및 권한 부여 웹후크 서버입니다. `azure-rbac`기능이 클러스터에서 사용 되는 경우에만 배포 되는 선택적 구성 요소입니다.   |

1. 모든 Azure Arc 사용 Kubernetes agent pod가 `Running` 상태 이면 클러스터가 Azure arc에 연결 되어 있는지 확인 합니다. 다음이 표시 됩니다.
    * [Azure Resource Manager](../../azure-resource-manager/management/overview.md)의 Azure Arc 사용 Kubernetes 리소스입니다. Azure는 실제 Kubernetes 클러스터 자체가 아닌 고객 관리형 Kubernetes 클러스터의 프로젝션으로 이 리소스를 추적합니다.
    * 클러스터 메타 데이터 (예: Kubernetes 버전, 에이전트 버전 및 노드 수)는 Azure Arc 사용 Kubernetes 리소스에 메타 데이터로 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 통해 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* [Azure Arc 사용 Kubernetes을 사용](./conceptual-configurations.md)하 여 클러스터와 Git 리포지토리 간의 연결을 구성 리소스로 만드는 방법에 대해 자세히 알아보세요.
