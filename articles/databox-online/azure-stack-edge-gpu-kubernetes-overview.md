---
title: Microsoft Azure Stack Edge Pro 디바이스의 Kubernetes 클러스터 개요 | Microsoft Docs
description: Kubernetes가 Azure Stack Edge Pro 디바이스에 구현되는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 22a66ca16d8d2cb7ade82cb665f50cc9aae4b27b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567194"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 Kubernetes

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Kubernetes는 컨테이너화된 애플리케이션을 오케스트레이션하는 인기 있는 오픈 소스 플랫폼입니다. 이 문서에서는 Kubernetes에 대한 개요를 제공하고 kubernetes가 Azure Stack Edge Pro 디바이스에서 작동하는 방식을 설명합니다. 

## <a name="about-kubernetes"></a>Kubernetes 정보 

Kubernetes는 컨테이너 기반 애플리케이션과 연결된 네트워킹 및 스토리지 구성 요소를 관리하는 쉽고 안정적인 플랫폼을 제공합니다. Kubernetes를 통해 컨테이너화된 앱을 신속하게 빌드, 제공, 스케일링할 수 있습니다.

오픈 플랫폼인 Kubernetes를 사용하여 기본 설정 프로그래밍 언어, OS, 라이브러리 또는 메시지 버스를 사용하여 애플리케이션을 빌드할 수 있습니다. 릴리스를 예약하고 배포하기 위해 Kubernetes는 기존 연속 통합 및 지속적인 업데이트 도구와 통합할 수 있습니다.

자세한 내용은 [How Kubernetes works](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)(Kubernetes 작동 방식)를 참조하세요.

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro의 Kubernetes

Azure Stack Edge Pro 디바이스에서 컴퓨팅을 구성하여 Kubernetes 클러스터를 만들 수 있습니다. 컴퓨팅 역할이 구성되면 마스터 및 작업자 노드를 포함한 Kubernetes 클러스터가 모두 배포되고 자동으로 구성됩니다. 그러면 이 클러스터는 `kubectl`, IoT Edge 또는 Azure Arc를 통한 워크로드 배포에 사용됩니다.

Azure Stack Edge Pro 디바이스는 인프라 클러스터를 구성하는 1노드 구성으로 사용할 수 있습니다. Kubernetes 클러스터는 인프라 클러스터와 별개이며 인프라 클러스터 위에 배포됩니다. 인프라 클러스터는 Azure Stack Edge Pro 디바이스에 영구적 스토리지를 제공하며 Kubernetes 클러스터는 애플리케이션 오케스트레이션만 담당합니다. 

이 경우 Kubernetes 클러스터에는 마스터 노드와 작업자 노드가 있습니다. 클러스터의 Kubernetes 노드는 애플리케이션 및 클라우드 워크플로를 실행하는 가상 머신입니다. 

Kubernetes 마스터 노드는 클러스터에 대해 원하는 상태를 유지 관리합니다. 또한 마스터 노드는 작업자 노드를 제어하며 컨테이너화된 애플리케이션을 실행합니다. 

다음 다이어그램에서는 1노드 Azure Stack Edge Pro 디바이스에서 Kubernetes를 구현하는 것을 보여 줍니다. 1노드 디바이스는 고가용성이 아니므로 단일 노드가 실패하면 디바이스가 중단됩니다. Kubernetes 클러스터도 중단됩니다.

![1노드 Azure Stack Edge Pro 디바이스의 Kubernetes 아키텍처](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Kubernetes 클러스터 아키텍처에 대한 자세한 내용은 [Kubernetes core concepts](https://kubernetes.io/docs/concepts/architecture/)(Kubernetes 핵심 개념)를 참조하세요.

마스터 및 작업자 노드는 CPU 및 메모리를 사용하는 가상 머신입니다. Kubernetes 워크로드를 배포할 때는 마스터 및 작업자 VM에 대한 컴퓨팅 요구 사항을 이해하는 것이 중요합니다.

|Kubernetes VM 유형|CPU 및 메모리 요구 사항|
|---------|---------|
|마스터 VM|4 코어, 4-GB RAM|
|작업자 VM|12 코어, 32-GB RAM|
<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>스토리지 볼륨 프로비저닝

애플리케이션 워크로드를 지원하기 위해 Azure Stack Edge Pro 디바이스 공유에 영구 데이터에 대한 스토리지 볼륨을 탑재할 수 있습니다. 정적 및 동적 볼륨을 모두 사용할 수 있습니다. 

자세한 내용은 [Azure Stack Edge Pro 디바이스의 Kubernetes 스토리지](azure-stack-edge-gpu-kubernetes-storage.md)에서 애플리케이션에 대한 스토리지 프로비저닝 옵션을 참조하세요.

## <a name="networking"></a>네트워킹

Kubernetes 네트워킹을 사용하면 컨테이너 간 네트워킹, Pod 간 네트워킹, Pod와 서비스 간 네트워킹, 인터넷과 서비스 간 네트워킹을 포함하여 Kubernetes 네트워크 내에서 통신을 구성할 수 있습니다. 자세한 내용은 [Azure Stack Edge Pro 디바이스의 Kubernetes 네트워킹](azure-stack-edge-gpu-kubernetes-networking.md)에서 네트워킹 모델을 참조하세요.

## <a name="updates"></a>업데이트

새 Kubernetes 버전을 사용할 수 있게 되면 Azure Stack Edge Pro 디바이스에 사용할 수 있는 표준 업데이트를 사용하여 클러스터를 업그레이드할 수 있습니다. 업그레이드하는 방법에 대한 단계는 [Azure Stack Edge Pro에 업데이트 적용](azure-stack-edge-gpu-install-update.md)을 참조하세요.

## <a name="access-monitoring"></a>액세스, 모니터링

Azure Stack Edge Pro 디바이스의 Kubernetes 클러스터는 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 허용합니다. 자세한 내용은 [Azure Stack Edge Pro GPU 디바이스의 Kubernetes 역할 기반 액세스 제어](azure-stack-edge-gpu-kubernetes-rbac.md)를 참조하세요.

Kubernetes 대시보드를 통해 클러스터 및 리소스의 상태를 모니터링할 수도 있습니다. 컨테이너 로그도 사용할 수 있습니다. 자세한 내용은 [Azure Stack Edge Pro 디바이스에서 Kubernetes 대시보드를 사용하여 Kubernetes 클러스터 상태 모니터링](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)을 참조하세요.

Azure Monitor도 컨테이너, 노드, 컨트롤러에서 상태 데이터를 수집하는 추가 기능으로 할 수 있습니다. 자세한 내용은 [Azure Monitor 개요](../azure-monitor/overview.md)를 참조하세요.

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>애플리케이션 관리

Azure Stack Edge Pro 디바이스에서 Kubernetes 클러스터를 만든 후에는 이 클러스터에 배포된 애플리케이션을 다음 방법 중 하나를 통해 관리할 수 있습니다.

- `kubectl`을 통한 네이티브 액세스
- IoT Edge 
- Azure Arc

이러한 방법에 대해서는 다음 섹션에서 설명합니다.


### <a name="kubernetes-and-kubectl"></a>Kubernetes 및 kubectl

Kubernetes 클러스터가 배포되면 클러스터에 배포된 애플리케이션을 클라이언트 머신에서 로컬로 관리할 수 있습니다. 명령줄을 통해 *kubectl* 같은 네이티브 도구를 사용하여 애플리케이션과 상호 작용합니다. 

Kubernetes 클러스터 배포에 대한 자세한 내용은 [Azure Stack Edge Pro 디바이스에 Kubernetes 클러스터 배포](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조하세요. 관리에 대한 자세한 내용은 [kubectl을 사용하여 Azure Stack Edge Pro 디바이스에서 Kubernetes 클러스터 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조하세요.


### <a name="kubernetes-and-iot-edge"></a>Kubernetes 및 IoT Edge

Kubernetes는 Azure Stack Edge Pro 디바이스의 IoT Edge 워크로드와 통합할 수도 있습니다. 여기서 Kubernetes는 규모와 에코시스템을 제공하고 IoT는 IoT 중심 에코시스템을 제공합니다. Kubernetes 계층은 Azure IoT Edge 워크로드를 배포하는 인프라 계층으로 사용됩니다. 모듈 수명 및 네트워크 부하 분산은 Kubernetes에서 관리되고 에지 애플리케이션 플랫폼은 IoT Edge에서 관리됩니다.

IoT Edge를 통해 Kubernetes 클러스터에 애플리케이션을 배포하는 방법을 자세히 알아보려면 다음으로 이동하세요. 

- [IoT Edge 통해 Azure Stack Edge Pro 디바이스에서 상태 비저장 애플리케이션 노출](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)


### <a name="kubernetes-and-azure-arc"></a>Kubernetes 및 Azure Arc

Azure Arc는 Kubernetes 클러스터에 애플리케이션을 배포하는 데 사용할 수 있는 하이브리드 관리 도구입니다. Azure Arc를 사용하면 컨테이너용 Azure Monitor를 사용하여 클러스터를 보고 모니터링할 수도 있습니다. 자세한 내용은 [Azure Arc 지원 Kubernetes란?](../azure-arc/kubernetes/overview.md)을 참조하세요. Azure Arc 가격 책정에 대한 자세한 내용은 [Azure Arc 가격 책정](https://azure.microsoft.com/services/azure-arc/#pricing)을 참조하세요.

Azure Arc 사용 Kubernetes는 2021년 3월부터 사용자에게 일반 공급되며 표준 사용 요금이 적용됩니다. 미리 보기 고객은 Azure Stack Edge 디바이스에서 Azure Arc 사용 Kubernetes를 무료로 사용할 수 있습니다. 미리 보기 제안을 이용하려면 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 만드세요.

1. **문제점 유형** 에서 **청구** 를 선택합니다.
2. **구독** 아래에서 구독을 선택합니다.
3. **서비스** 에서 **내 서비스** 를 선택한 다음 **Azure Stack Edge** 를 선택합니다.
4. **리소스** 에서 리소스를 선택합니다.
5. **요약** 에서 문제에 대한 설명을 입력합니다.
6. **문제 유형** 에서 **예기치 않은 요금** 을 선택합니다.
7. **문제 하위 유형** 에서 **무료 평가판 요금 이해 도움말** 을 선택합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro 디바이스](azure-stack-edge-gpu-kubernetes-storage.md)의 Kubernetes 스토리지에 대해 자세히 알아봅니다.
- [Azure Stack Edge Pro 디바이스](azure-stack-edge-gpu-kubernetes-networking.md)의 Kubernetes 네트워킹 모델을 이해합니다.
- Azure Portal에서 [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)를 배포합니다.
