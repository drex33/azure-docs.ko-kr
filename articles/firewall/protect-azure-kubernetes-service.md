---
title: Azure Firewall을 사용하여 AKS(Azure Kubernetes Service) 배포 보호
description: Azure Firewall을 사용하여 AKS(Azure Kubernetes Service) 배포를 보호하는 방법을 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 08/03/2021
ms.author: victorh
ms.openlocfilehash: b331180e40d1baf92a3c1408f3e003a257fa114a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528773"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Azure Firewall을 사용하여 AKS(Azure Kubernetes Service) 배포 보호

AKS(Azure Kubernetes Service)는 Azure에서 관리되는 Kubernetes 클러스터를 제공합니다. 이는 대부분의 부담을 Azure에 오프로딩하여 Kubernetes를 관리하는 복잡성 및 운영 오버헤드를 감소시킵니다. AKS는 상태 모니터링 및 유지 관리와 같은 중요한 작업을 처리하고, 거버넌스를 용이하게 하는 엔터프라이즈급 보안 클러스터를 제공합니다.

Kubernetes는 가상 머신의 클러스터를 오케스트레이션하고 사용 가능한 컴퓨팅 리소스 및 각 컨테이너의 리소스 요구 사항에 따라 해당 가상 머신에서 실행하도록 컨테이너를 예약합니다. 컨테이너는 Pod, Kubernetes의 기본 운영 단위로 그룹화되며, 이러한 Pod는 원하는 상태로 스케일링됩니다.

관리와 운영을 목적으로 AKS 클러스터의 노드는 특정 포트와 FQDN(정규화된 도메인 이름)에 액세스해야 합니다. 이러한 작업에는 API 서버와 통신 또는 코어 Kubernetes 클러스터 구성 요소와 노드 보안 업데이트를 다운로드하여 설치하기 등이 있습니다. Azure Firewall을 통해 사용자 환경을 잠그고 아웃바운드 트래픽을 필터링할 수 있습니다.

개요를 보려면 Jorge Cortes의 다음 비디오를 시청하세요.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWIcAo]

이 문서의 지침에 따라 Azure Firewall을 사용하여 Azure Kubernetes 클러스터에 대한 추가 보호를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 애플리케이션을 실행하는 배포된 Azure Kubernetes 클러스터입니다.

   자세한 내용은 [자습서: AKS(Azure Kubernetes Service) 클러스터 배포](../aks/tutorial-kubernetes-deploy-cluster.md) 및 [자습서: AKS(Azure Kubernetes Service)에서 애플리케이션 실행](../aks/tutorial-kubernetes-deploy-application.md)을 참조하세요.


## <a name="securing-aks"></a>AKS 보안

Azure Firewall은 구성을 간소화하는 AKS FQDN 태그를 제공합니다. 아웃바운드 AKS 플랫폼 트래픽을 허용하려면 다음 단계를 사용합니다.

- Azure Firewall을 사용하여 아웃바운드 트래픽을 제한하고 UDR(사용자 정의 경로)을 만들어 모든 아웃바운드 트래픽을 보내는 경우에는 아웃바운드 트래픽을 올바르게 허용하도록 방화벽에서 적절한 DNAT 규칙을 만들어야 합니다. 

   UDR과 함께 Azure Firewall을 사용하면 비대칭 라우팅으로 인해 인바운드 설정이 손상됩니다. 이 문제는 AKS 서브넷에 방화벽의 개인 IP 주소로 이동하는 기본 경로가 있지만 퍼블릭 부하 분산 장치를 사용하는 경우 발생합니다. 예를 들어, *LoadBalancer* 형식의 인바운드 또는 Kubernetes 서비스입니다.

   이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태를 저장하고 방화벽이 설정된 세션을 인식하지 못하므로 반환 패킷을 삭제합니다. Azure Firewall을 수신 또는 서비스 부하 분산 장치와 통합하는 방법을 알아보려면 [Azure Firewall을 Azure 표준 Load Balancer와 통합](integrate-lb.md)을 참조하세요.
- 애플리케이션 규칙 컬렉션을 만들고 *AzureKubernetesService* FQDN 태그를 사용하도록 설정하는 규칙을 추가합니다. 원본 IP 주소 범위는 호스트 풀 가상 네트워크이고, 프로토콜은 https이고, 대상은 AzureKubernetesService입니다.
- 다음 아웃바운드 포트/네트워크 규칙은 AKS 클러스터에 대한 필수 사항입니다.

   - TCP 포트 443
   - TCP [*IPAddrOfYourAPIServer*]:443은 API 서버와 통신해야 하는 앱이 있는 경우 필요합니다. 이 변경 내용은 클러스터를 만든 후에 설정할 수 있습니다.
   - 터널 앞 Pod의 TCP 포트 9000, UDP 포트 1194는 API 서버의 터널 끝과 통신하는 데 필요합니다.

      보다 구체적으로 하려면 다음 표의 주소를 참조하세요.

   | 대상 엔드포인트                                                             | 프로토콜 | 포트    | 사용  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 |
   | **`*:9000`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 |

   - UDP 포트 123은 NTP(Network Time Protocol) 시간 동기화(Linux 노드)에 필요합니다.
   - API 서버에 직접 액세스하는 포드가 있으면 DNS용 UDP 포트 53도 필요합니다.

   자세한 내용은 [AKS(Azure Kubernetes Service)에서 클러스터 노드의 송신 트래픽 제어](../aks/limit-egress-traffic.md)를 참조하세요.
- AzureMonitor 및 Storage 서비스 태그를 구성합니다. Azure Monitor는 로그 분석 데이터를 수신합니다.

   작업 영역 URL을 개별적으로 허용할 수도 있습니다(`<worksapceguid>.ods.opinsights.azure.com` 및 `<worksapceguid>.oms.opinsights.azure.com`). 다음 방법 중 하나를 사용하여 이 문제를 해결할 수 있습니다.

    - 호스트 풀 서브넷에서 `*. ods.opinsights.azure.com` 및 `*.oms. opinsights.azure.com`으로 https 액세스를 허용합니다. 이러한 와일드카드 FQDN은 필요한 액세스를 지원하지만 덜 제한적입니다.
    - 다음 로그 분석 쿼리를 사용하여 정확한 필수 FQDN을 나열한 다음 방화벽 애플리케이션 규칙에서 명시적으로 허용합니다.
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>다음 단계

- Azure Kubernetes Service에 대한 자세한 내용은 [AKS(Azure Kubernetes service)에 대한 Kubernetes 핵심 개념](../aks/concepts-clusters-workloads.md)을 참조하세요.
