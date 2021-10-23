---
title: 클러스터 확장-Azure Arc-사용 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes의 클러스터 확장 기능에 대 한 개념적 개요를 제공 합니다.
ms.openlocfilehash: 1f6bc02e111041e9a89e8066a57586c331c1a297
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273956"
---
# <a name="cluster-extensions"></a>클러스터 확장

Azure Monitor 및 Azure Defender for Kubernetes와 같은 관리 서비스 또는 Azure 앱 서비스와 같은 서비스는 클러스터 확장 기능을 통해 Kubernetes 클러스터에서 인스턴스화될 수 있습니다. [Helm 차트](https://helm.sh/)는 가장 복잡한 Kubernetes 애플리케이션을 정의, 설치 및 업그레이드하는 데 필요한 빌딩 블록도 제공하여 Kubernetes 애플리케이션을 관리하는 데 도움이 됩니다. 클러스터 확장 기능은 kubernetes 클러스터에서 사용 하도록 설정 하려는 서비스 인스턴스의 설치 및 수명 주기 관리에 대 한 Azure Resource Manager 구동 환경을 제공 하 여 투구의 패키징 구성 요소 위에 구축 됩니다. 클러스터 운영자 또는 관리자는 클러스터 확장 기능을 사용 하 여 

- 원하는 기능에 대해 다양 한 확장을 설치 하 고 Azure Portal, CLI, SDK 등과 같은 Azure 인터페이스에서 해당 클러스터에 설치 된 모든 클러스터 및 확장의 인벤토리를 가져옵니다. 
- 다른 Azure 리소스와 마찬가지로 Azure 역할 기반 Access Control (RBAC)를 사용 하 여 클러스터 확장 리소스에 대 한 액세스를 제어할 수 있습니다.
- Azure Policy를 사용 하 여 사용자 환경의 모든 클러스터에서 클러스터 확장의 배포 규모를 자동화할 수 있습니다. 
- 각 확장에 대 한 릴리스 학습 (예: 미리 보기, 안정적인)을 구독 합니다.
- 확장에 대 한 자동 업그레이드를 설정 하거나 특정 버전에 고정 하 여 업데이트를 관리 합니다.
- 확장 속성 또는 하나 이상의 확장 인스턴스 삭제에 대 한 업데이트를 포함 하 여 확장의 수명 주기를 관리 합니다.

확장은 클러스터 범위 또는 네임 스페이스 범위를 지정할 수 있습니다. 각 확장 유형 (예: Azure Monitor, Azure Defender, Azure 앱 services)은 클러스터에서 작동 하는 범위를 정의 합니다. 

## <a name="architecture-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 사용 Kubernetes 클러스터의 아키텍처

[ ![클러스터 확장 아키텍처](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

클러스터 확장 인스턴스는 `Microsoft.KubernetesConfiguration/extensions` Azure Resource Manager에서로 표현 되는 Azure Arc 사용 Kubernetes 리소스 ()의 맨 위에 있는 확장 Azure Resource Manager 리소스 ()로 만들어집니다 `Microsoft.Kubernetes/connectedClusters` . Azure Resource Manager 표현은 특정 클러스터 확장을 사용 하거나 사용 하지 않고 모든 Azure Arc 사용 Kubernetes 리소스를 확인 하는 정책을 작성할 수 있습니다. 원하는 속성 값이 있는 클러스터 확장을 누락 한 클러스터를 확인 한 후 Azure Policy를 사용 하 여 이러한 비호환 리소스를 수정할 수 있습니다.

`config-agent`클러스터에서 실행 되는는 새로 만든 확장 리소스 또는 Azure Arc 사용 Kubernetes 리소스의 기존 확장 리소스에 대 한 업데이트를 모니터링 하 고 추적 합니다. `extensions-manager`클러스터에서 실행 되는 구성 요소는 Azure Container Registry 또는 Microsoft Container Registry에서 클러스터 확장과 연결 된 투구 차트를 끌어와 클러스터에 설치 합니다. 

`config-agent` `extensions-manager` 클러스터에서 실행 되는 및 구성 요소는 모두 새 버전의 업데이트 및 확장 인스턴스 속성 업데이트 및 삭제와 같은 기타 작업을 처리 합니다. 이러한 에이전트는 시스템 할당 관리 id를 사용 하 여 Azure의 백 엔드 서비스와 안전 하 게 통신 합니다. 

> [!NOTE]
> * `config-agent` Azure Arc 사용 Kubernetes 리소스에서 새로 만들었거나 기존 확장 리소스에 대 한 업데이트를 모니터링 합니다. 따라서 에이전트는 클러스터로 끌어올 확장의 원하는 상태를 연결 해야 합니다. 에이전트가 Azure에 연결할 수 없는 경우 원하는 상태를 클러스터로 전파하는 작업이 지연됩니다.
> * 클러스터 확장에 대해 설정할 수 있는 속성 중 하나는 ProtectedConfiguration 설정입니다. 확장에 대 한 보호 된 구성 설정은 Azure Arc 사용 Kubernetes 서비스에서 최대 48 시간 동안 저장 됩니다. 결과적으로 Azure에서 확장 리소스를 만든 후 48시간 동안 클러스터가 분리된 상태를 유지하는 경우 확장은 `Pending` 상태에서 `Failed` 상태로 전환됩니다. 클러스터를 최대한 정기적으로 온라인 상태로 전환하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* Azure Arc 사용 Kubernetes 클러스터에 [클러스터 확장을 배포](./extensions.md) 합니다.
