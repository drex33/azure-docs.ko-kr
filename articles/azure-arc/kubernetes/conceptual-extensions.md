---
title: 클러스터 확장 - Azure Arc 지원 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 11/24/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 지원 Kubernetes의 클러스터 확장 기능에 대한 개념적 개요를 제공합니다.
ms.openlocfilehash: 5b3067e3f40f297211d39e24bbade252b0b6ae60
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042444"
---
# <a name="cluster-extensions"></a>클러스터 확장

[Helm 차트](https://helm.sh/)는 가장 복잡한 Kubernetes 애플리케이션을 정의, 설치 및 업그레이드하는 데 필요한 빌딩 블록도 제공하여 Kubernetes 애플리케이션을 관리하는 데 도움이 됩니다. 클러스터 확장 기능은 Kubernetes 클러스터 위에 있는 다양한 Azure 기능의 설치 및 수명 주기 관리를 위한 Azure Resource Manager 기반 환경을 제공하여 Helm의 패키징 구성 요소를 기반으로 빌드됩니다. 클러스터 운영자 또는 관리자는 클러스터 확장 기능을 사용하여 다음을 수행할 수 있습니다.

- Kubernetes 클러스터에 키 관리, 데이터 및 애플리케이션 제품을 설치하고 관리합니다. 사용 가능한 확장 목록은 [여기에서](extensions.md#currently-available-extensions) 찾을 수 있습니다.
- Azure Policy 사용하여 사용자 환경의 모든 클러스터에서 클러스터 확장의 대규모 배포를 자동화합니다. 
- 각 확장에 대해 릴리스 훈련(예: 미리 보기 또는 안정적)을 구독합니다.
- 확장에 대한 자동 업그레이드를 설정하거나 특정 버전에 고정하고 수동으로 버전을 업그레이드합니다.
- 확장 속성을 업데이트하거나 확장 인스턴스를 삭제합니다.

확장은 클러스터 범위 또는 네임스페이스로 범위가 지정될 수 있습니다. 각 확장 유형(예: 컨테이너용 Azure Monitor, Microsoft Defender for Cloud Azure 앱 서비스)은 클러스터에서 작동하는 범위를 정의합니다. 

## <a name="architecture"></a>아키텍처

[ ![클러스터 확장 아키텍처](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

클러스터 확장 인스턴스는 Azure Resource Manager Azure Arc `Microsoft.KubernetesConfiguration/extensions` 지원 Kubernetes 리소스(로 표시)를 기반으로 확장 `Microsoft.Kubernetes/connectedClusters` Azure Resource Manager 리소스()로 만들어집니다. Azure Resource Manager 이 표현을 사용하면 특정 클러스터 확장 유무에 관계없이 Azure Arc 지원 Kubernetes 리소스를 모두 확인하는 정책을 작성할 수 있습니다. 원하는 속성 값을 가진 클러스터 확장이 누락된 클러스터를 결정한 후에는 Azure Policy 사용하여 이러한 비규격 리소스를 수정할 수 있습니다.

클러스터에서 실행되는 는 `config-agent` Azure Arc 지원 Kubernetes 리소스에서 새 확장 리소스와 업데이트된 확장 리소스를 추적합니다. `extensions-manager`클러스터에서 실행되는 에이전트는 설치해야 하는 확장 유형을 읽고 Azure Container Registry 또는 Microsoft Container Registry 연결된 Helm 차트를 끌어와서 클러스터에 설치합니다. 

클러스터에서 실행되는 및 구성 요소는 모두 `config-agent` `extensions-manager` 확장 인스턴스 업데이트, 버전 업데이트 및 확장 인스턴스 삭제를 처리합니다. 이러한 에이전트는 클러스터의 시스템 할당 관리 ID를 사용하여 Azure 서비스와 안전하게 통신합니다. 

> [!NOTE]
> * `config-agent` 는 Azure Arc 지원 Kubernetes 클러스터를 기반으로 새 확장 인스턴스 또는 업데이트된 확장 인스턴스를 확인합니다. 에이전트는 원하는 확장 상태를 클러스터로 끌어오려면 연결이 필요합니다. 에이전트가 Azure에 연결할 수 없는 경우 원하는 상태를 클러스터로 전파하는 작업이 지연됩니다.
> * 확장 인스턴스에 대한 보호된 구성 설정은 Azure Arc 지원 Kubernetes 서비스에 최대 48시간 동안 저장됩니다. 결과적으로 Azure에서 확장 리소스를 만든 후 48시간 동안 클러스터가 분리된 상태를 유지하는 경우 확장은 `Pending` 상태에서 `Failed` 상태로 전환됩니다. 따라서 클러스터를 정기적으로 온라인으로 만드는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* Azure Arc 지원 Kubernetes 클러스터에 클러스터 [확장을 배포합니다.](./extensions.md)
