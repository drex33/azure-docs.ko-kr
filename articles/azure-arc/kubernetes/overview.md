---
title: Azure Arc 지원 Kubernetes 개요
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: 이 문서에서는 Azure Arc 지원 Kubernetes 개요를 제공합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 80d1b741c4a3a1d60cc44e3afdeac22a61a7db4b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768291"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes란?

Azure Arc 지원 Kubernetes를 사용하면 Azure 내부 또는 외부에 있는 Kubernetes 클러스터를 연결하고 구성할 수 있습니다. Kubernetes 클러스터를 Azure Arc에 연결하면 다음과 같이 됩니다.
* Azure Resource Manager ID 및 관리 ID와 함께 Azure Portal에 표시됩니다. 
* Azure 구독 및 리소스 그룹에 배치됩니다.
* 다른 Azure 리소스와 마찬가지로 태그를 수신합니다. 

Kubernetes 클러스터를 Azure에 연결하려면 클러스터 관리자가 에이전트를 배포해야 합니다. 이러한 에이전트는 다음과 같습니다.
* 표준 Kubernetes 배포로 `azure-arc` Kubernetes 네임스페이스에서 실행합니다.
* Azure에 대한 연결 처리
* Azure Arc 로그 및 메트릭을 수집합니다.
* 구성 요청을 감시합니다. 

Azure Arc 지원 Kubernetes는 전송 중인 데이터를 보호하기 위해 업계 표준 SSL을 지원합니다. 또한 미사용 데이터는 데이터 기밀성을 보장하기 위해 Azure Cosmos DB 데이터베이스에 암호화된 상태로 저장됩니다.

## <a name="supported-kubernetes-distributions"></a>지원되는 Kubernetes 배포

Azure Arc 지원 Kubernetes는 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터에서 작동합니다. Azure Arc 팀은 Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 배포의 [적합성을 검증하기 위해 주요 업계 파트너](./validation-program.md)와 협력했습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오 

Azure Arc 지원 Kubernetes는 다음과 같은 시나리오를 지원합니다. 

* 인벤토리, 그룹화 및 태그 지정을 위해 Azure 외부에서 실행되는 Kubernetes를 연결합니다.

* GitOps 기반 구성 관리를 사용하여 애플리케이션을 배포하고 구성을 적용합니다. 

* 컨테이너에 대한 Azure Monitor를 사용하여 클러스터를 확인 및 모니터링합니다.

* Azure Defender for Kubernetes를 사용하여 위협 보호를 적용합니다.

* Kubernetes용 Azure Policy를 사용하여 정책 정의를 적용합니다.

* Azure Arc 지원 Data Services, [Azure Arc의 App Services](../../app-service/overview-arc-integration.md)(웹, 함수 및 논리 앱 포함), [Kubernetes의 Event Grid](../../event-grid/kubernetes/overview.md)를 배포하기 위한 대상 위치로 [사용자 지정 위치](./custom-locations.md)를 만듭니다.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="next-steps"></a>다음 단계

클러스터를 Azure Arc에 연결하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)