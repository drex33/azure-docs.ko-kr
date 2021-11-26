---
title: Azure Arc가 지원되는 Kubernetes의 개요
services: azure-arc
ms.service: azure-arc
author: shashankbarsin
ms.author: shasb
ms.date: 11/23/2021
ms.topic: overview
description: 이 문서에서는 Azure Arc가 지원되는 Kubernetes의 개요를 제공합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 37d18c85cbc90c35a071d844b3180b5ef234cc98
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133073315"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes란?

Azure Arc 지원 Kubernetes를 사용하면 어디서나 실행되는 Kubernetes 클러스터를 연결하고 구성할 수 있습니다. 다른 퍼블릭 클라우드 공급자(GCP, AWS)에서 실행되는 클러스터 또는 온-프레미스 데이터 센터에서 실행되는 클러스터(VMware vSphere Azure Stack HCI)를 Azure Arc에 연결할 수 있습니다. Kubernetes 클러스터를 Azure Arc에 연결하는 경우 다음 작업을 수행합니다.
* 고유 ID를 사용하여 Azure Resource Manager 표현을 가져옵니다.
* Azure 구독 및 리소스 그룹에 배치됩니다.
* 다른 Azure 리소스와 마찬가지로 태그를 수신합니다.

Azure Arc가 지원되는 Kubernetes는 전송 중인 데이터를 보호하기 위해 업계 표준 SSL을 지원합니다. 연결된 클러스터의 경우 미사용 데이터는 데이터 기밀성을 보장하기 위해 Azure Cosmos DB 데이터베이스에 암호화된 상태로 저장됩니다.

Azure Arc 지원 Kubernetes는 연결된 클러스터에 대해 다음과 같은 시나리오를 지원합니다. 

* 인벤토리, 그룹화 및 태그 지정을 위해 Azure 외부에서 실행되는 [Kubernetes를 연결](quickstart-connect-cluster.md)합니다.

* [GitOps 기반 구성 관리](tutorial-use-gitops-connected-cluster.md)를 사용하여 애플리케이션을 배포하고 구성을 적용합니다. 

* [컨테이너에 대한 Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)를 사용하여 클러스터를 확인 및 모니터링합니다.

* [Microsoft Defender for Kubernetes](../../defender-for-cloud/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)를 사용하여 위협 방지를 적용합니다.

* [Kubernetes용 Azure Policy](../../governance/policy/concepts/policy-for-kubernetes.md?toc=/azure/azure-arc/kubernetes/toc.json)를 사용하여 정책 정의를 적용합니다.

* 클러스터에서 [인증 및 권한 부여 확인을 위해 Azure Active Directory](azure-rbac.md)를 사용합니다.

* [Cluster Connect](cluster-connect.md)를 사용하여 방화벽에서 인바운드 포트를 열지 않고도 어디서나 Kubernetes 클러스터에 안전하게 액세스할 수 있습니다.

* 서비스 간 상호 작용에 대한 관찰 가능성 및 정책 적용을 위해 클러스터 위에 [Open Service Mesh](tutorial-arc-enabled-open-service-mesh.md) 배포

* [Azure Machine Learning for Kubernetes 클러스터](../../machine-learning/how-to-attach-arc-kubernetes.md?toc=/azure/azure-arc/kubernetes/toc.json)를 사용하여 기계 학습 워크로드를 배포합니다.

* [사용자 지정 위치](./custom-locations.md)를 Azure Arc 지원 Data Services(SQL Managed Instances, PostgreSQL 하이퍼스케일), [Azure Arc의 App Services](../../app-service/overview-arc-integration.md)(웹, 함수 및 논리 앱 포함) 및 [Kubernetes의 Event Grid](../../event-grid/kubernetes/overview.md)를 배포하기 위한 대상 위치로 만듭니다.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-kubernetes-distributions"></a>지원되는 Kubernetes 배포

Azure Arc 지원 Kubernetes는 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터에서 작동합니다. Azure Arc 팀은 Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 배포의 [적합성을 검증하기 위해 주요 업계 파트너](./validation-program.md)와 협력했습니다.

## <a name="next-steps"></a>다음 단계

기존 Kubernetes 클러스터를 Azure Arc에 연결하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Azure Arc에 기존 Kubernetes 클러스터 연결](./quickstart-connect-cluster.md)
