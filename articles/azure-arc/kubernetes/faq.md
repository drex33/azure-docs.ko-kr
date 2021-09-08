---
title: Azure Arc 지원 Kubernetes 질문과 대답
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에는 Azure Arc 지원 Kubernetes와 관련된 질문과 대답 목록이 포함되어 있습니다.
keywords: Kubernetes, Arc, Azure, 컨테이너, 구성, GitOps, FAQ
ms.openlocfilehash: f678bd23bc4e9e40f718d72ccde8069c36673ac6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272928"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>질문과 대답 - Azure Arc 지원 Kubernetes

이 문서에서는 Azure Arc 지원 Kubernetes에 대한 질문과 대답을 다룹니다.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Azure Arc를 지원하는 Kubernetes 및 AKS(Azure Kubernetes Service)의 차이점은 무엇인가요?

AKS는 Azure에서 제공하는 관리되는 Kubernetes 제품입니다. AKS는 복잡성 및 운영 오버헤드의 대부분을 Azure로 오프로드하여 Azure에서의 관리되는 Kubernetes 클러스터 배포를 간소화합니다. Kubernetes 마스터는 Azure에서 관리되므로 에이전트 노드만 관리하고 유지 관리합니다.

Azure Arc를 지원하는 Kubernetes를 사용하면 Kubernetes 클러스터를 Azure에 연결하여 Azure Monitor 및 Azure Policy와 같은 Azure의 관리 기능을 확장할 수 있습니다. 기본 Kubernetes 클러스터 자체를 유지 관리합니다.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Azure에서 실행되는 AKS 클러스터를 Azure Arc에 연결해야 하나요?

AKS(Azure Kubernetes Service) 클러스터를 Azure Arc에 연결하는 것은 클러스터 위에서 App Services 및 Data Services와 같은 Arc 지원 서비스를 실행하는 경우에만 필요합니다. 이는 Arc 지원 Kubernetes의 [사용자 지정 위치](custom-locations.md) 기능을 사용하여 수행할 수 있습니다. 이는 클러스터 확장 및 사용자 지정 위치가 기본적으로 AKS 클러스터 위에 도입될 때까지의 지정 시간 제한입니다.

사용자 지정 위치를 사용하지 않고 Azure Monitor 및 Azure Policy(Gatekeeper)와 같은 관리 기능만 사용하려는 경우 AKS에서 기본적으로 사용할 수 있으며 이러한 경우 Azure Arc에 연결할 필요가 없습니다.
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Azure Stack Edge와 Azure Stack Hub에 있는 AKS-HCI 클러스터 및 Kubernetes 클러스터를 Azure Arc에 연결해야 하나요?

예, Azure Stack Edge 또는 Azure Stack Hub에 있는 AKS-HCI 클러스터 또는 Kubernetes 클러스터를 Azure Arc에 연결하면 Azure Resource Manager에서 리소스를 표시하는 클러스터가 제공됩니다. 이 리소스 표현을 통해 클러스터 구성, Azure Monitor, Azure Policy(Gatekeeper) 등의 기능을 이러한 연결된 Kubernetes 클러스터로 확장할 수 있습니다.

Azure Arc 지원 Kubernetes 클러스터가 Edge Azure Stack에 있는 경우 Azure Stack HCI의 AKS(2021년 4월 업데이트 이상 버전) 또는 Windows Server 2019 Datacenter의 AKS(2021년 4월 업데이트 이상 버전)에서 Kubernetes 구성은 무료로 포함됩니다.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>만료된 Azure Arc 지원 Kubernetes 리소스 문제를 해결하는 방법

Azure Arc 지원 Kubernetes 클러스터와 연결된 시스템이 할당한 관리 ID는 Azure Arc 서비스와 통신하기 위해 Arc 에이전트에서만 사용됩니다. 이 시스템이 할당한 관리 ID와 연결된 인증서의 만료 기간은 90일이며 에이전트는 46일에서 90일 사이에 이 인증서 갱신을 계속 시도합니다. 이 인증서가 만료되면 리소스가 `Expired`로 간주되고 모든 기능(예: 구성, 모니터링 및 정책)이 이 클러스터에서 작동을 중지하므로 클러스터를 삭제하고 Azure Arc에 다시 연결해야 합니다. 따라서 관리 ID 인증서의 갱신을 보장하기 위해 46일에서 90일 사이에 클러스터를 온라인 상태로 만드는 것이 좋습니다.

지정된 클러스터에 대해 인증서가 만료되는 시점을 확인하려면 다음 명령을 실행합니다.

```console
az connectedk8s show -n <name> -g <resource-group>
```

출력에서 `managedIdentityCertificateExpirationTime` 값은 관리 ID 인증서가 만료되는 시기를 나타냅니다(해당 인증서의 경우 90D 표시). 

`managedIdentityCertificateExpirationTime` 값이 과거의 타임스탬프를 나타내는 경우 위 출력의 `connectivityStatus` 필드는 `Expired`로 설정됩니다. 이러한 경우 Kubernetes 클러스터에서 Azure Arc를 다시 사용하려면 다음을 수행합니다.

1. 클러스터에서 Azure Arc 지원 Kubernetes 리소스 및 에이전트를 삭제합니다. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. 클러스터에 에이전트를 배포하여 Azure Arc 지원 Kubernetes 리소스를 다시 만듭니다.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete`는 또한 클러스터 상단의 구성 및 클러스터 확장을 삭제합니다. `az connectedk8s connect`를 실행한 후 Azure Policy를 사용하거나 수동으로 클러스터에 구성 및 클러스터 확장을 다시 만듭니다.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>이미 CI/CD 파이프라인을 사용하고 있는 경우 Azure Arc 지원 Kubernetes 및 구성을 계속 사용할 수 있나요?

예, CI/CD 파이프라인을 통해 배포를 수신하는 클러스터에서 구성을 계속 사용할 수 있습니다. 기존 CI/CD 파이프라인과 비교할 때 구성 기능에는 다음과 같은 두 가지 추가 이점이 있습니다.

**드리프트 조정**

CI/CD 파이프라인은 파이프라인 실행 중에 한 번만 변경 내용을 적용합니다. 그러나 클러스터의 GitOps 운영자는 Git 리포지토리를 지속적으로 폴링하여 원하는 상태의 클러스터에 있는 Kubernetes 리소스를 가져옵니다. GitOps 운영자가 클러스터에 있는 리소스의 실제 상태가 필요한 리소스 상태와 다른 것을 알게 되면 이 드리프트가 조정됩니다.

**대규모로 GitOps 적용**

CI/CD 파이프라인은 Kubernetes 클러스터에 대한 이벤트 기반 배포(예: Git 리포지토리에 푸시)에 유용합니다. 그러나 모든 Kubernetes 클러스터에 동일한 구성을 배포하려는 경우에는 CI/CD 파이프라인에 각 Kubernetes 클러스터의 자격 증명을 수동으로 구성해야 합니다. 

Azure Arc 지원 Kubernetes의 경우 Azure Resource Manager에서 구성을 관리하므로 구독 또는 리소스 그룹의 범위 내에서 Azure Policy를 사용하여 모든 Azure Arc 지원 Kubernetes 리소스에 대해 동일한 구성 만들기를 자동화할 수 있습니다. 이 기능은 정책 할당 후에 생성된 Azure Arc 지원 Kubernetes 리소스에도 적용됩니다.

이 기능은 규정 준수 및 거버넌스 요구 사항을 충족하기 위해 전체 Kubernetes 클러스터 인벤토리에 있는 기준 구성(예: 네트워크 정책, 역할 바인딩 및 Pod 보안 정책)을 적용합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 통해 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* 이미 Kubernetes 클러스터와 연결된 Azure Arc가 있나요? [Arc 지원 Kubernetes 클러스터에 대한 구성을 만듭니다](./tutorial-use-gitops-connected-cluster.md).
* [Azure Policy를 사용하여 대규모로 구성을 적용](./use-azure-policy.md)하는 방법에 대해 알아봅니다.