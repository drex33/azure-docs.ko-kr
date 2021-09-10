---
title: Azure Stack Edge Pro GPU Kubernetes 네트워킹
description: Kubernetes 네트워킹이 Azure Stack Edge Pro GPU 디바이스에서 작동하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 06/24/2021
ms.author: alkohli
ms.openlocfilehash: ece1d03787308613961fe56087f8b621a753c856
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572584"
---
# <a name="kubernetes-networking-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 Kubernetes 네트워킹

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스에서 컴퓨팅 역할을 구성하면 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터를 만든 후 Pod의 Kubernetes 클러스터에 컨테이너화된 애플리케이션을 배포할 수 있습니다. Kubernetes 클러스터의 Pod에 네트워킹을 사용하는 고유한 방법이 있습니다. 

이 문서에서는 일반적인 Kubernetes 클러스터의 네트워킹에 대해 설명하고 Azure Stack Edge Pro GPU 디바이스의 컨텍스트에서 자세히 설명합니다. 

## <a name="networking-requirements"></a>네트워킹 요구 사항

다음은 Kubernetes 클러스터에 배포되는 일반적인 2계층 앱의 예입니다.

- 앱에는 백 엔드에 웹 서버 프런트 엔드 및 데이터베이스 애플리케이션이 있습니다. 
- 모든 Pod에는 IP가 할당되지만 IP는 Pod의 다시 시작 및 장애 조치(failover) 시 변경될 수 있습니다. 
- 각 앱은 여러 Pod으로 구성되며, 모든 Pod 복제본에 트래픽의 부하가 분산됩니다. 

![Kubernetes 네트워킹 요구 사항](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1-m.png)

위의 시나리오의 네트워킹 요구 사항은 다음과 같은 같습니다.

 - 애플리케이션 사용자가 이름 또는 IP 주소를 통해 Kubernetes 클러스터 외에 외부 대상 애플리케이션에 액세스할 수 있어야 합니다. 
 - Kubernetes 클러스터 내의 애플리케이션(예: 여기서 프런트 엔드 및 백 엔드 Pod)은 서로 통신할 수 있어야 합니다.

위의 요구 사항을 모두 해결하기 위해 Kubernetes 서비스를 도입했습니다. 


## <a name="networking-services"></a>네트워킹 서비스

Kubernetes 서비스는 다음 두 가지 유형이 있습니다. 

- **클러스터 IP 서비스** - 애플리케이션 Pod에 대한 상수 엔드포인트를 제공한다고 생각해보세요. 서비스와 연결된 모든 Pod은 Kubernetes 클러스터 외부에서 액세스할 수 없습니다. 서비스에 사용되는 IP 주소는 개인 네트워크의 주소 공간에서 제공됩니다. 
    
    외부에 노출된 Load Balancer 서비스가 아닌 다른 Pod로 액세스할 수 있도록 Kubernetes 클러스터 내의 Pod를 노출하려면 [내부 통신을 위해 Kubernetes 서비스를 클러스터 IP 서비스로 노출]()하는 방법을 참조하세요.

- **Load balancer IP** - 클러스터 IP 서비스와 비슷하지만 연결된 IP는 외부 네트워크에서 제공되며 Kubernetes 클러스터 외부에서 액세스할 수 있습니다.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->

## <a name="kubernetes-network-configuration"></a>Kubernetes 네트워크 구성

Kubernetes 노드와 외부 서비스에 사용되는 IP 주소는 디바이스의 로컬 UI에서 **컴퓨팅** 페이지를 통해 제공됩니다.

![로컬 UI에 Kubernetes IP 할당](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 할당은 다음에 대한 것입니다.

- **Kubernetes 노드 IP**:이 IP 범위는 Kubernetes 마스터 및 작업자 노드에 사용됩니다. 이러한 IP는 Kubernetes 노드가 서로 통신할 때 사용됩니다.
- **Kubernetes 외부 서비스 IP**:이 IP 범위는 Kubernetes 클러스터 외부에 노출되는 외부 서비스(Load Balancer 서비스라고도 함)에 사용됩니다.

## <a name="kubernetes-networking-components"></a>Kubernetes 네트워킹 구성 요소

Calico, Metallb, Core DNS는 모두 Azure Stack Edge Pro GPU에서 네트워킹에 대해 설치되는 구성 요소입니다. 

- **Calico** 는 개인 IP 범위에서 모든 Pod에 IP 주소를 할당하고, Pod에 대한 네트워킹을 구성하여 한 노드의 Pod이 다른 노드의 Pod과 통신할 수 있도록 합니다. 
- **Metallb** 는 클러스터 내 Pod에서 실행되며, `load balancer` 유형의 서비스에 IP 주소를 할당합니다. Load Balancer IP 주소는 로컬 UI를 통해 제공되는 서비스 IP 범위에서 선택됩니다. 
- **핵심 DNS** - 이 추가 기능은 DNS 레코드 매핑 서비스 이름을 클러스터 IP 주소에 구성합니다.

디바이스의 PowerShell 인터페이스에 연결하면 Kubernetes 클러스터에서 실행 중인 위의 네트워킹 구성 요소를 볼 수 있습니다.

## <a name="network-interfaces-switches"></a>네트워크 인터페이스, 스위치 

디바이스는 인프라 클러스터를 구성하는 1노드 구성으로 사용할 수 있습니다. Kubernetes 클러스터는 인프라 클러스터와 별개이며 인프라 클러스터 위에 배포됩니다. Kubernetes 클러스터에는 마스터 노드와 작업자 노드가 있습니다. 두 Kubernetes 노드는 모두 애플리케이션 및 클라우드 워크플로를 실행하는 가상 머신입니다.

마스터 VM과 작업자 VM에는 각각 내부 가상 스위치에 연결하는 네트워크 인터페이스와 외부 가상 스위치에 연결하는 네트워크 인터페이스가 있습니다. 

- **외부 가상 스위치**: 이 스위치는 로컬 UI의 **컴퓨팅** 페이지를 통해 컴퓨팅용 디바이스 포트를 사용할 때 생성됩니다. 컴퓨팅 인프라에 사용하는 스위치입니다. 예를 들어 이 스위치는 디바이스에 배포하는 가상 머신에 사용됩니다. 
- **내부 가상 스위치**: 이 스위치는 디바이스에서 공장 기본 설정의 일부로 생성됩니다. 내부 가상 스위치는 NAT(Network Address Translation)를 사용하여 기본 게이트웨이로 구성된 포트를 통해 트래픽을 라우팅합니다. 예를 들어 이 스위치는 VM의 모든 IoT 런타임 요청을 Azure Portal로 라우팅합니다. 

   <!--![Azure Stack Edge networking simplified diagram](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-0.png)-->

## <a name="network-routes"></a>네트워크 경로 

디바이스의 Kubernetes VM의 경우 새 경로 구성을 추가하여 트래픽을 라우팅할 수 있습니다. 경로 구성은 다음 필드를 포함하는 라우팅 테이블 항목입니다.

| 매개 변수     | Description                                                                              |
|---------------|------------------------------------------------------------------------------------------|
| 대상   | IP 주소 또는 IP 주소 접두사입니다.                                            |
| 접두사 길이 | 대상의 주소 또는 주소 범위에 해당하는 접두사 길이입니다. |
| 다음 홉      | 패킷이 전달되는 IP 주소입니다.                                         |
| 인터페이스     | IP 패킷을 전달하는 네트워크 인터페이스입니다.                                       |
| 메트릭        | 라우팅 메트릭은 대상에 도달하는 데 사용되는 기본 네트워크 인터페이스를 결정합니다. |


## <a name="change-routing-on-compute-network"></a>컴퓨팅 네트워크에서 라우팅 변경

`Add-HcsNetRoute` cmdlet을 사용하여 Kubernetes 작업자 및 마스터 VM의 라우팅을 수정합니다. 아래 다이어그램의 레이아웃을 고려하세요. 

![Azure Stack Edge 네트워킹 다이어그램](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-1.png)

- 포트 2는 인터넷에 연결되어 있으며 원하는 아웃바운드 트래픽 경로입니다. 
- 포트 3에서 컴퓨팅을 사용하도록 설정했으며 이 네트워크 인터페이스에 외부 가상 스위치가 생성되었습니다. 
- 포트 3은 처리를 위해 Azure Stack Edge 디바이스에 원시 데이터를 공급하는 카메라 및 기타 센서가 있는 개인 네트워크에 연결됩니다. 


게이트웨이가 개인 네트워크의 환경에 구성된 경우 관련 트래픽에 대해서만 게이트웨이와 통신할 수 있도록 Kubernetes 마스터 및 작업자 VM에 대한 사용자 지정 경로를 설정하는 것이 좋습니다. 이를 통해 Azure Stack Edge 디바이스에서 구성했을 수 있는 다른 포트와 비교하여 컴퓨팅 네트워크에서 흐르는 트래픽을 제어할 수 있습니다. 예를 들어 다른 모든 인터넷 연결 트래픽이 디바이스의 다른 실제 포트를 통해 흐르도록 할 수 있습니다. 이 경우 인터넷 연결 트래픽이 포트 2를 통과할 수 있습니다. 

또한 다음과 같은 기타 고려 사항을 고려해야 합니다.

- 플랫 서브넷이 있는 경우 이러한 경로를 개인 네트워크에 추가할 필요가 없습니다. 개인 네트워크에 여러 서브넷이 있는 경우 필요에 따라 이러한 경로를 추가할 수 있습니다.
- 이러한 경로는 Kubernetes 마스터 및 작업자 VM에만 추가할 수 있고 디바이스(Windows 호스트)에는 추가할 수 없습니다.
- 이 경로를 추가하기 전에 Kubernetes 컴퓨팅을 구성할 필요가 없습니다. Kubernetes 컴퓨팅이 구성된 후에도 경로를 추가 하거나 업데이트할 수 있습니다. 
- 로컬 UI를 통하지 않고 디바이스의 PowerShell 인터페이스를 통해 새 경로 구성만 추가할 수 있습니다.
- 사용할 네트워크 인터페이스에 정적 구성이 있는지 확인합니다.

## <a name="add-a-route-configuration"></a>경로 구성 추가

개인 네트워크에 새 사용자 지정 경로를 추가하려면 다음과 같이 cmdlet을 사용합니다.

```powershell
Add-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP address or IP address prefix> -NextHop <IP address of next hop> -RouteMetric <Route metric number> 
```
출력의 예제는 다음과 같습니다.

```output
Add-HcsNetRoute -InterfaceAlias "Port3" -DestinationPrefix "192.168.21.0/24" -NextHop "192.168.20.1" -RouteMetric 100 
```

위의 명령은 대상 서브넷 192.168.21.0/24를 정의하는 라우팅 테이블에 항목을 만들고, 다음 홉을 192.168.20.1로 지정하며 이 라우팅 항목에 라우팅 메트릭 100을 할당합니다. 라우팅 메트릭이 낮을수록 경로에 할당된 우선 순위가 높습니다.

## <a name="check-route-configuration"></a>경로 구성 확인

이 cmdlet을 사용하여 디바이스에 추가한 모든 사용자 지정 경로 구성을 확인합니다. 이러한 경로에는 디바이스에 이미 있는 모든 시스템 경로 또는 기본 경로가 포함되지 않습니다.

```powershell
Get-HcsNetRoute -InterfaceAlias <Port number>
```

## <a name="remove-a-route-configuration"></a>경로 구성 제거

이 cmdlet을 사용하여 디바이스에 추가한 경로 구성을 제거할 수 있습니다.

```powershell
Remove-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP or IP prefix>
```

## <a name="routing-with-multiple-network-interfaces"></a>여러 네트워크 인터페이스로 라우팅

여러 디바이스 포트가 연결된 경우 Hyper-V 환경에서 여러 실제 네트워크 어댑터를 단일 가상 네트워크 어댑터로 그룹화할 수 있는 표준 NIC 팀 또는 SET(Switch Embedded Teaming)가 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro GPU에 Kubernetes 네트워킹을 구성하려면 다음을 참조하세요.

- [IoT Edge를 통해 Azure Stack Edge Pro GPU에 외부에서 상태 비저장 애플리케이션 노출](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)

- [을 통해 Azure Stack Edge Pro GPU에 외부에서 상태 비저장 애플리케이션 노출](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)