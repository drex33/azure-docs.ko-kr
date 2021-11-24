---
title: Azure Arc-사용 Kubernetes 연결 모드
services: azure-arc
ms.service: azure-arc
ms.date: 11/23/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes에서 지 원하는 연결 모드에 대 한 개요를 제공 합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: f0dfd43cc8cc45574f98b1f86c04b55dd7027902
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077525"
---
# <a name="azure-arc-enabled-kubernetes-connectivity-modes"></a>Azure Arc-사용 Kubernetes 연결 모드

Azure Arc 사용 Kubernetes를 사용 하려면 클러스터에서 사용할 수 있는 구성 (GitOps), 확장, 클러스터 커넥트 및 사용자 지정 위치와 같은 기능을 사용 하 여 Kubernetes 클러스터에 Azure Arc 에이전트를 배포 해야 합니다. 에 지에 배포 된 Kubernetes 클러스터는 네트워크 연결이 일정 하지 않을 수 있으므로 에이전트가 항상 Azure Arc 서비스에 도달 하지 못할 수 있습니다. 그러나 이러한 반 연결 된 모드는 지원 되는 시나리오입니다. 구성 및 확장과 같은 기능을 위해 반 연결 된 배포 모드를 지원 하기 위해 에이전트는 Arc 서비스에서 필요한 상태 사양을 풀링 하 고 나중에 클러스터에서이 상태를 인식 합니다.

## <a name="understand-connectivity-modes"></a>연결 모드 이해

| 연결 모드 | 설명 |
| ----------------- | ----------- |
| 완전히 연결됨 | 에이전트는 GitOps 구성 전파, Azure Policy와 Gatekeeper 정책 적용, Azure Monitor의 워크로드 메트릭 및 로그 수집에서 약간 지연을 사용하여 Azure와 일관되게 통신할 수 있습니다. |
| 반연결됨 | 에서 끌어온 관리 id 인증서는 `clusteridentityoperator` 인증서가 만료 될 때까지 최대 90 일 동안 유효 합니다. 만료 되 면 Azure Arc 사용 Kubernetes 리소스의 작동이 중지 됩니다. 클러스터에서 모든 Azure Arc 기능을 다시 활성화 하려면 Azure Arc 사용 Kubernetes 리소스 및 에이전트를 삭제 하 고 다시 만듭니다. 90일 동안 30일마다 한 번 이상 클러스터를 연결합니다. |
| 연결 끊김 | Azure에 액세스할 수 없는 연결 되지 않은 환경의 Kubernetes 클러스터는 현재 Azure Arc 사용 Kubernetes에서 지원 되지 않습니다. 이 기능에 관심이 있는 경우 [Azure Arc의 UserVoice 포럼](https://feedback.azure.com/d365community/forum/5c778dec-0625-ec11-b6e6-000d3a4f0858)에서 아이디어를 제출하거나 아이디어에 투표합니다.


## <a name="connectivity-status"></a>연결 상태

클러스터의 연결 상태는 클러스터에 배포 된 Arc 에이전트에서 받은 최신 하트 비트의 시점에 따라 결정 됩니다.

| 상태 | 설명 |
| ------ | ----------- |
| Connecting | Azure Arc 사용 Kubernetes 리소스가 Azure Resource Manager에서 만들어지지만 서비스에서 에이전트 하트 비트를 아직 받지 못했습니다. |
| 연결됨 | Azure Arc 사용 Kubernetes 서비스가 지난 15 분 내에 에이전트 하트 비트를 받았습니다. |
| 오프라인 | Azure Arc 사용 Kubernetes 리소스가 이전에 연결 되었지만 서비스가 15 분 동안 에이전트 하트 비트를 받지 못했습니다. |
| 만료됨 | 클러스터의 관리 되는 id 인증서가 발급 된 지 90 일이 지나면 만료 기간이 만료 됩니다. 이 인증서가 만료되면 리소스가 `Expired`로 간주되며 모든 기능(예: 구성, 모니터링 및 정책)이 해당 클러스터에서 작동을 중지합니다. 만료 된 Azure Arc 사용 Kubernetes 리소스를 해결 하는 방법에 대 한 자세한 내용은 [FAQ 문서](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)를 참조 하세요. |

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 통해 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* [Azure Arc 사용 Kubernetes을 사용](./conceptual-configurations.md)하 여 클러스터와 Git 리포지토리 간의 연결을 구성 리소스로 만드는 방법에 대해 자세히 알아보세요.
