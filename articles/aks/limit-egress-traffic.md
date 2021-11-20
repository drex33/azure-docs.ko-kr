---
title: AKS(Azure Kubernetes Service)에서 송신 트래픽 제한
description: AKS(Azure Kubernetes Service)에서 송신 트래픽을 제어하는 데 필요한 포트 및 주소에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 826db0c64ce826d8853aa5c4bc4b8671f9a7e700
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132869253"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 클러스터 노드의 송신 트래픽 제어

이 문서에서는 AKS(Azure Kubernetes Service)에서 아웃바운드 트래픽을 보호하는 데 필요한 세부 정보를 제공합니다. 여기에는 기본 AKS 배포에 대한 클러스터 요구 사항과 선택적 추가 기능 및 기능에 대한 추가 요구 사항이 포함되어 있습니다. [Azure Firewall을 사용하여 이러한 요구 사항을 구성하는 방법에 대한 예제가 끝 부분에 나와 있습니다](#restrict-egress-traffic-using-azure-firewall). 이 정보는 아웃바운드 제한 방법 또는 어플라이언스에 적용할 수 있습니다.

## <a name="background"></a>배경

AKS 클러스터는 가상 네트워크에 배포됩니다. 이 네트워크는 관리하거나(AKS에서 생성) 사용자 지정(사용자가 미리 구성)할 수 있습니다. 두 경우 모두 클러스터에는 해당 가상 네트워크 외부의 서비스에 대한 **아웃바운드** 종속성이 있습니다(서비스에 인바운드 종속성이 없음).

관리와 운영을 목적으로 AKS 클러스터의 노드는 특정 포트와 FQDN(정규화된 도메인 이름)에 액세스해야 합니다. 이러한 엔드포인트는 노드가 API 서버와 통신하거나 핵심 Kubernetes 클러스터 구성 요소 및 노드 보안 업데이트를 다운로드 및 설치하는 데 필요합니다. 예를 들어 클러스터는 MCR(Microsoft Container Registry)에서 기본 시스템 컨테이너 이미지를 가져와야 합니다.

AKS 아웃바운드 종속성은 FQDN으로 거의 완전히 정의되며 뒤에 고정 주소가 없습니다. 고정 주소가 없으면 네트워크 보안 그룹을 사용하여 ASE 클러스터의 아웃바운드 트래픽을 잠글 수 없습니다.

기본적으로 AKS 클러스터에는 무제한 아웃바운드(송신) 인터넷 액세스가 있습니다. 이러한 수준의 네트워크 액세스가 있으면 사용자가 실행하는 노드와 서비스는 필요할 때마다 외부 리소스에 액세스할 수 있습니다. 송신 트래픽을 제한하려면 정상적인 클러스터 유지 관리 작업을 유지할 수 있도록 제한된 수의 포트 및 주소에 액세스할 수 있어야 합니다. 아웃바운드 주소를 보호하는 가장 간단한 솔루션은 도메인 이름에 따라 아웃바운드 트래픽을 제어할 수 있는 방화벽 디바이스를 사용하는 것입니다. 예를 들어 Azure Firewall은 대상의 FQDN을 기반으로 아웃바운드 HTTP 및 HTTPS 트래픽을 제한할 수 있습니다. 이러한 필수 포트와 주소를 허용하도록 기본 방화벽 및 보안 규칙을 구성할 수도 있습니다.

> [!IMPORTANT]
> 이 문서에서는 AKS 서브넷에서 나가는 트래픽을 잠그는 방법에 대해서만 설명합니다. 기본적으로 AKS에는 수신 요구 사항이 없습니다.  NSG(네트워크 보안 그룹) 및 방화벽을 사용하여 **내부 서브넷 트래픽** 을 차단하는 것은 지원되지 않습니다. 클러스터 내에서 트래픽을 제어하고 차단하려면 [**_네트워크 정책_**][network-policy]을 사용하세요.

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>AKS 클러스터에 필요한 아웃바운드 네트워크 규칙 및 FQDN

AKS 클러스터에는 다음과 같은 네트워크 및 FQDN/애플리케이션 규칙이 필요합니다. Azure Firewall 이외의 솔루션을 구성하려는 경우에는 이 규칙을 사용할 수 있습니다.

* IP 주소 종속성은 HTTP/S가 아닌 트래픽(TCP 및 UDP 모두)에 대한 것입니다.
* FQDN HTTP/HTTPS 엔드포인트는 방화벽 디바이스에 배치할 수 있습니다.
* 와일드카드 HTTP/HTTPS 엔드포인트는 몇 가지 한정자를 기반으로 하여 AKS에 따라 달라질 수 있는 종속성입니다.
* AKS는 허용 컨트롤러를 사용하여 kube-system 및 gatekeeper-system 아래의 모든 배포에 FQDN을 환경 변수로 삽입합니다. 여기서 노드와 API 서버 간의 모든 시스템 통신이 API 서버 IP가 아닌 API 서버 FQDN을 사용합니다.
* API 서버와 통신해야 하는 앱이나 솔루션이 있는 경우 **추가** 네트워크 규칙을 추가하여 *API 서버 IP의 포트 443에 TCP 통신* 을 허용해야 합니다.
* 드문 경우지만 API 서버 IP가 변경될 수 있는 유지 관리 작업이 있는 경우가 있습니다. API 서버 IP를 변경할 수 있는 계획된 유지 관리 작업은 항상 미리 전달됩니다.

### <a name="azure-global-required-network-rules"></a>Azure 글로벌 필수 네트워크 규칙

필수 네트워크 규칙 및 IP 주소 종속성은 다음과 같습니다.

| 대상 엔드포인트                                                             | 프로토콜 | 포트    | 사용  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 [프라이빗 클러스터](private-clusters.md)에는 필요하지 않습니다.|
| **`*:9000`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 [프라이빗 클러스터](private-clusters.md)에는 필요하지 않습니다. |
| **`*:123`** 또는 **`ntp.ubuntu.com:123`** (Azure Firewall 네트워크 규칙을 사용하는 경우)  | UDP      | 123     | Linux 노드에서 NTP(Network Time Protocol) 시간 동기화(Linux 노드)에 필요합니다.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 사용자 지정 DNS 서버를 사용하는 경우 클러스터 노드에서 액세스할 수 있는지 확인해야 합니다. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API 서버에 액세스하는 Pod/배포를 실행하는 경우 필요하며 해당 Pod/배포는 API IP를 사용합니다. [프라이빗 클러스터](private-clusters.md)에는 필요하지 않습니다.  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure 글로벌 필수 FQDN/애플리케이션 규칙

다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 FQDN                 | 포트            | 사용      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꿉니다. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 자사 이미지/차트(예: coreDNS 등)가 포함되어 있습니다. 이러한 이미지는 확장 및 업그레이드 작업을 포함하여 클러스터를 올바르게 생성하고 작동하는 데 필요합니다.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| **`management.azure.com`**       | **`HTTPS:443`** | Azure API에 대한 Kubernetes 작업에 필요합니다. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Azure Active Directory 인증에 필요합니다. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI가 포함됩니다. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | 이 주소는 kubenet 및 Azure CNI와 같은 필수 이진 파일을 다운로드 및 설치하는 데 필요한 리포지토리입니다. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure 중국 21Vianet 필수 네트워크 규칙

필수 네트워크 규칙 및 IP 주소 종속성은 다음과 같습니다.

| 대상 엔드포인트                                                             | 프로토콜 | 포트    | 사용  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.Region:1194`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 |
| **`*:9000`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 |
| **`*:22`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:22`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:22`** <br/> *Or* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 |
| **`*:123`** 또는 **`ntp.ubuntu.com:123`** (Azure Firewall 네트워크 규칙을 사용하는 경우)  | UDP      | 123     | Linux 노드에서 NTP(Network Time Protocol) 시간 동기화(Linux 노드)에 필요합니다.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 사용자 지정 DNS 서버를 사용하는 경우 클러스터 노드에서 액세스할 수 있는지 확인해야 합니다. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API 서버에 액세스하는 Pod/배포를 실행하는 경우 필요하며 해당 Pod/배포는 API IP를 사용합니다.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure 중국 21Vianet 필수 FQDN/애플리케이션 규칙

다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 FQDN                               | 포트            | 사용      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꿉니다. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꿉니다. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 자사 이미지/차트(예: coreDNS 등)가 포함되어 있습니다. 이러한 이미지는 확장 및 업그레이드 작업을 포함하여 클러스터를 올바르게 생성하고 작동하는 데 필요합니다. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Azure API에 대한 Kubernetes 작업에 필요합니다. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Azure Active Directory 인증에 필요합니다. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI가 포함됩니다. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | 이 주소는 kubenet 및 Azure CNI와 같은 필수 이진 파일을 다운로드 및 설치하는 데 필요한 리포지토리입니다. |

### <a name="azure-us-government-required-network-rules"></a>Azure 미국 정부 필수 네트워크 규칙

필수 네트워크 규칙 및 IP 주소 종속성은 다음과 같습니다.

| 대상 엔드포인트                                                             | 프로토콜 | 포트    | 사용  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 |
| **`*:9000`** <br/> *Or* <br/> [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [지역 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 노드와 컨트롤 평면 간의 터널링된 보안 통신의 경우 |
| **`*:123`** 또는 **`ntp.ubuntu.com:123`** (Azure Firewall 네트워크 규칙을 사용하는 경우)  | UDP      | 123     | Linux 노드에서 NTP(Network Time Protocol) 시간 동기화(Linux 노드)에 필요합니다.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 사용자 지정 DNS 서버를 사용하는 경우 클러스터 노드에서 액세스할 수 있는지 확인해야 합니다. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API 서버에 액세스하는 Pod/배포를 실행하는 경우 필요하며 해당 Pod/배포는 API IP를 사용합니다.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure 미국 정부 필수 FQDN/애플리케이션 규칙

다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 FQDN                                        | 포트            | 사용      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꿉니다.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 자사 이미지/차트(예: coreDNS 등)가 포함되어 있습니다. 이러한 이미지는 확장 및 업그레이드 작업을 포함하여 클러스터를 올바르게 생성하고 작동하는 데 필요합니다. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Azure API에 대한 Kubernetes 작업에 필요합니다. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Azure Active Directory 인증에 필요합니다. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI가 포함됩니다. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | 이 주소는 kubenet 및 Azure CNI와 같은 필수 이진 파일을 설치하는 데 필요한 리포지토리입니다. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>AKS 클러스터용 권장 FQDN/애플리케이션 규칙(선택 사항)

다음 FQDN/애플리케이션 규칙은 선택 사항이지만 AKS 클러스터에 권장됩니다.

| 대상 FQDN                                                               | 포트          | 사용      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | 이 주소를 통해 Linux 클러스터 노드가 필요한 보안 패치와 업데이트를 다운로드할 수 있습니다. |

이러한 FQDN을 차단/허용 안 함으로 선택하는 경우 노드는 [노드 이미지 업그레이드](node-image-upgrade.md) 또는 [클러스터 업그레이드](upgrade-cluster.md)를 수행할 때만 OS 업데이트를 수신합니다.

## <a name="gpu-enabled-aks-clusters"></a>GPU 사용 AKS 클러스터

### <a name="required-fqdn--application-rules"></a>필수 FQDN/애플리케이션 규칙

GPU를 사용하는 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 FQDN                        | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작동에 사용됩니다. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작동에 사용됩니다. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작동에 사용됩니다. |

## <a name="windows-server-based-node-pools"></a>Windows Server 기반 노드 풀

### <a name="required-fqdn--application-rules"></a>필수 FQDN/애플리케이션 규칙

Windows Server 기반 노드 풀을 사용하려면 다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 FQDN                                                           | 포트      | 사용      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Windows 관련 이진 파일을 설치하려면 필요합니다. |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Windows 관련 이진 파일을 설치하려면 필요합니다. |

## <a name="aks-addons-and-integrations"></a>AKS 추가 기능 및 통합

### <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor

컨테이너의 Azure Monitor에 대한 액세스를 제공하는 두 가지 옵션이 있습니다. Azure Monitor [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) **또는** 에서 필수 FQDN/애플리케이션 규칙에 대한 액세스를 제공할 수 있습니다.

#### <a name="required-network-rules"></a>필수 네트워크 규칙

다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 엔드포인트                                                             | 프로토콜 | 포트    | 사용  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureMonitor:443`**  | TCP           | 443      | 이 엔드포인트는 Azure Monitor 및 Log Analytics에 메트릭 데이터와 로그를 전송하는 데 사용됩니다. |

#### <a name="required-fqdn--application-rules"></a>필수 FQDN/애플리케이션 규칙

컨테이너에 대해 Azure Monitor를 사용하도록 설정된 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | 이 엔드포인트는 Azure Monitor를 사용하는 메트릭 및 모니터링 원격 분석에 사용됩니다. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | 이 엔드포인트는 Azure Monitor가 로그 분석 데이터를 수집하는 데 사용됩니다. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | 이 엔드포인트는 로그 분석 서비스를 인증하는 데 사용되는 omsagent에서 사용됩니다. |
| *.monitoring.azure.com | **`HTTPS:443`** | 이 엔드포인트는 Azure Monitor에 메트릭 데이터를 전송하는 데 사용됩니다. |

### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>필수 FQDN/애플리케이션 규칙

Azure Policy를 사용하도록 설정된 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                          | 포트      | 사용      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | 이 주소는 Kubernetes 정책을 가져오고 클러스터 준수 상태를 정책 서비스에 보고하는 데 사용됩니다. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | 이 주소는 기본 제공 정책의 Gatekeeper 아티팩트를 가져오는 데 사용됩니다. |
| **`dc.services.visualstudio.com`** | **`HTTPS:443`** | 원격 분석 데이터를 Application Insights 엔드포인트로 보내는 Azure Policy 추가 기능입니다. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure 중국 21Vianet 필수 FQDN/애플리케이션 규칙

Azure Policy를 사용하도록 설정된 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                          | 포트      | 사용      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | 이 주소는 Kubernetes 정책을 가져오고 클러스터 준수 상태를 정책 서비스에 보고하는 데 사용됩니다. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | 이 주소는 기본 제공 정책의 Gatekeeper 아티팩트를 가져오는 데 사용됩니다. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure 미국 정부 필수 FQDN/애플리케이션 규칙

Azure Policy를 사용하도록 설정된 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                          | 포트      | 사용      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | 이 주소는 Kubernetes 정책을 가져오고 클러스터 준수 상태를 정책 서비스에 보고하는 데 사용됩니다. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | 이 주소는 기본 제공 정책의 Gatekeeper 아티팩트를 가져오는 데 사용됩니다. |

## <a name="cluster-extensions"></a>클러스터 확장

### <a name="required-fqdn--application-rules"></a>필수 FQDN / 애플리케이션 규칙

AKS 클러스터에서 클러스터 확장을 사용 하려면 다음 FQDN/응용 프로그램 규칙이 필요 합니다.

| FQDN | 포트 | 사용 |
|-----------------------------------------------|-----------|----------|
| **`<region>.dp.kubernetesconfiguration.azure.com`** | **`HTTPS:443`** | 이 주소는 클러스터 확장 서비스에서 구성 정보를 가져오고 확장 상태를 서비스에 보고 하는 데 사용 됩니다.|
| **`mcr.microsoft.com, *.data.mcr.microsoft.com`** | **`HTTPS:443`** | 이 주소는 AKS 클러스터에 클러스터 확장 에이전트를 설치 하기 위해 컨테이너 이미지를 끌어오는 데 필요 합니다.|

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure 미국 정부 필수 FQDN/애플리케이션 규칙

AKS 클러스터에서 클러스터 확장을 사용 하려면 다음 FQDN/응용 프로그램 규칙이 필요 합니다.

| FQDN | 포트 | 사용 |
|-----------------------------------------------|-----------|----------|
| **`<region>.dp.kubernetesconfiguration.azure.us`** | **`HTTPS:443`** | 이 주소는 클러스터 확장 서비스에서 구성 정보를 가져오고 확장 상태를 서비스에 보고 하는 데 사용 됩니다. |
| **`mcr.microsoft.com, *.data.mcr.microsoft.com`** | **`HTTPS:443`** | 이 주소는 AKS 클러스터에 클러스터 확장 에이전트를 설치 하기 위해 컨테이너 이미지를 끌어오는 데 필요 합니다.|

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Azure Firewall을 사용하여 송신 트래픽 제한

Azure Firewall은 이 구성을 단순화하기 위해 Azure Kubernetes Service(`AzureKubernetesService`) FQDN 태그를 제공합니다.

> [!NOTE]
> FQDN 태그에는 위에 나열된 모든 FQDN이 포함되며 자동으로 최신 상태를 유지합니다.
>
> SNAT 포트 소모 문제가 발생하지 않도록 프로덕션 시나리오에 Azure Firewall에서 최소 20개의 프런트 엔드 IP를 사용하는 것이 좋습니다.

다음은 배포의 예제 아키텍처입니다.

![잠긴 토폴로지](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* 퍼블릭 수신이 방화벽 필터를 통해 강제 전달됩니다.
  * AKS 에이전트 노드가 전용 서브넷에서 격리됩니다.
  * [Azure Firewall](../firewall/overview.md)은 자체 서브넷에 배포됩니다.
  * DNAT 규칙은 FW 공용 IP를 LB 프런트 엔드 IP로 변환합니다.
* [사용자 정의 경로](egress-outboundtype.md)를 사용하여 에이전트 노드에서 Azure Firewall 내부 IP로 아웃바운드 요청이 시작됩니다.
  * AKS 에이전트 노드의 요청은 AKS 클러스터가 배포된 서브넷에 배치된 UDR을 따릅니다.
  * Azure Firewall은 공용 IP 프런트 엔드의 가상 네트워크 외부로 송신합니다.
  * 공용 인터넷 또는 기타 Azure 서비스에 대한 액세스는 방화벽 프런트 엔드 IP 주소를 통해 수행됩니다.
  * 필요에 따라 AKS 컨트롤 플레인에 대한 액세스는 방화벽 퍼블릭 프런트 엔드 IP 주소를 포함하는 [API 서버 승인 IP 범위](./api-server-authorized-ip-ranges.md)에 의해 보호됩니다.
* 내부 트래픽
  * 필요에 따라 [공용 부하 분산 장치](load-balancer-standard.md)와 함께 또는 대신 내부 트래픽에 대해 [내부 부하 분산 장치](internal-lb.md)를 사용할 수 있으며, 자체 서브넷에서도 격리할 수 있습니다.

아래 단계에서는 Azure Firewall의 `AzureKubernetesService` FQDN 태그를 사용하여 AKS 클러스터의 아웃바운드 트래픽을 제한하고 방화벽을 통해 퍼블릭 인바운드 트래픽을 구성하는 방법의 예제를 제공합니다.

### <a name="set-configuration-via-environment-variables"></a>환경 변수를 통한 구성 설정

리소스 만들기에 사용할 환경 변수 집합을 정의합니다.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>여러 서브넷이 있는 가상 네트워크 만들기

2개의 개별 서브넷(클러스터용, 방화벽용 각 1개)을 사용하여 가상 네트워크를 프로비전합니다. 필요에 따라 내부 서비스를 수신하기 위해 하나를 만들 수도 있습니다.

![빈 네트워크 토폴로지](media/limit-egress-traffic/empty-network.png)

모든 리소스를 저장할 리소스 그룹을 만듭니다.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

AKS 클러스터 및 Azure 방화벽을 호스트하는 두 개의 서브넷이 있는 가상 네트워크를 만듭니다. 각각 자체 서브넷이 있습니다. AKS 네트워크부터 시작해 보겠습니다.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>UDR을 사용하여 Azure Firewall 만들기 및 설정

Azure Firewall 인바운드 및 아웃바운드 규칙을 구성해야 합니다. 방화벽의 주요 목적은 조직에서 AKS 클러스터에 대한 세분화된 수신 및 송신 트래픽 규칙을 구성할 수 있도록 하는 것입니다.

![방화벽 및 UDR](media/limit-egress-traffic/firewall-udr.png)

> [!IMPORTANT]
> 클러스터나 애플리케이션이 동일하거나 소규모 대상 하위 집합으로 향하는 많은 수의 아웃바운드 연결을 생성하는 경우, 프런트 엔드 IP당 포트 수가 최대가 되지 않도록 더 많은 방화벽 프런트 엔드 IP가 필요할 수 있습니다.
> 여러 IP로 Azure Firewall을 만드는 방법에 대한 자세한 내용은 [**여기**](../firewall/quick-create-multiple-ip-template.md)를 참조하세요.

Azure Firewall 프런트 엔드 주소로 사용되는 표준 SKU 공용 IP 리소스를 만듭니다.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Azure Firewall을 만들려면 미리 보기 CLI 확장을 등록합니다.

```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```

이제 이전에 만든 IP 주소를 방화벽 프런트 엔드에 할당할 수 있습니다.

> [!NOTE]
> Azure Firewall에 대한 공용 IP 주소를 설정하는 데 몇 분 정도 걸릴 수 있습니다.
> 네트워크 규칙에서 FQDN을 사용하려면 DNS 프록시를 사용하도록 설정해야 합니다. 사용 하도록 설정되면 방화벽은 포트 53에서 수신 대기되며 위에서 지정된 DNS 서버로 DNS 요청을 전달합니다. 그러면 방화벽에서 FQDN을 자동으로 변환할 수 있습니다.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

이전 명령이 성공한 경우 나중에 구성할 수 있도록 방화벽 프런트 엔드 IP 주소를 저장합니다.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> [권한 있는 IP 주소 범위](./api-server-authorized-ip-ranges.md)가 있는 AKS API 서버에 대한 보안 액세스를 사용하는 경우 방화벽 공용 IP를 권한 있는 IP 범위에 추가해야 합니다.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>홉을 사용하여 Azure Firewall에 대한 UDR 만들기

Azure는 Azure 서브넷, 가상 네트워크 및 온-프레미스 네트워크 간에 트래픽을 자동으로 라우트합니다. Azure의 기본 라우팅 중 하나를 변경하려면 경로 테이블을 만듭니다.

지정된 서브넷과 연결할 빈 경로 테이블을 만듭니다. 경로 테이블은 위에서 만든 Azure Firewall로 다음 홉을 정의합니다. 각 서브넷에는 0 또는 하나의 경로 테이블이 연결될 수 있습니다.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Azure의 기본 시스템 경로를 재정의하거나 서브넷의 경로 테이블에 추가 경로를 추가하는 방법에 대한 자세한 내용은 [가상 네트워크 경로 테이블 설명서](../virtual-network/virtual-networks-udr-overview.md#user-defined)를 참조하세요.

### <a name="adding-firewall-rules"></a>방화벽 규칙 추가

다음은 방화벽에서 구성하는 데 사용할 수 있는 세 가지 네트워크 규칙입니다. 배포에 따라 이러한 규칙을 조정해야 할 수 있습니다. 첫 번째 규칙은 TCP를 통해 포트 9000에 대한 액세스를 허용합니다. 두 번째 규칙은 UDP를 통해 포트 1194 및 123에 대한 액세스를 허용합니다(Azure 중국 21Vianet에 배포하는 경우 [더 많은](#azure-china-21vianet-required-network-rules)가 필요할 수 있음). 이러한 두 규칙은 모두 사용 중인 Azure 지역 CIDR(이 경우 미국 동부)로 향하는 트래픽만 허용합니다. 마지막으로 UDP를 통해 포트 123을 여는 세 번째 네트워크 규칙을 `ntp.ubuntu.com` FQDN에 추가합니다(네트워크 규칙으로 FQDN을 추가하는 것은 Azure Firewall의 특정 기능 중 하나이며 사용자 고유의 옵션을 사용할 때 이를 조정해야 함).

네트워크 규칙을 설정한 후에는 필요한 모든 FQDN을 포함한 `AzureKubernetesService`를 사용하는 애플리케이션 규칙도 추가하며 이 FQDN은 TCP 포트 443 및 포트 80을 통해 액세스할 수 있습니다.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Azure Firewall 서비스에 대한 자세한 내용은 [Azure Firewall 설명서](../firewall/overview.md)를 참조하세요.

### <a name="associate-the-route-table-to-aks"></a>AKS에 경로 테이블 연결

클러스터를 방화벽과 연결하려면 클러스터의 서브넷에 대한 전용 서브넷이 위에서 만든 경로 테이블을 참조해야 합니다. 클러스터와 방화벽을 모두 보유하는 가상 네트워크에 대한 명령을 실행하여 클러스터의 서브넷에 대한 경로 테이블을 업데이트하면 연결을 수행할 수 있습니다.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>아웃바운드 형식의 UDR을 사용하여 기존 네트워크에 AKS 배포

이제 AKS 클러스터는 기존 가상 네트워크로 배포될 수 있습니다. [아웃바운드 유형`userDefinedRouting`](egress-outboundtype.md)도 사용합니다. 이 기능은 모든 아웃바운드 트래픽이 방화벽을 통과하고 다른 송신 경로가 존재하지 않도록 합니다(기본적으로 부하 분산 장치 아웃바운드 유형을 사용할 수 있음).

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>기존 가상 네트워크 내에서 프로비저닝할 수 있는 액세스 권한이 있는 서비스 주체 만들기

클러스터 ID(관리 ID 또는 서비스 주체)는 AKS에서 클러스터 리소스를 만드는 데 사용됩니다. 생성 시 전달되는 서비스 주체는 AKS에서 사용하는 스토리지 리소스, IP 및 부하 분산 장치와 같은 기본 AKS 리소스를 만드는 데 사용됩니다(대신 [관리 ID](use-managed-identity.md)를 사용할 수도 있음). 아래에서 적절한 권한이 부여되지 않으면 AKS 클러스터를 프로비전할 수 없습니다.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp"
```

이제 `APPID` 및 `PASSWORD`를 이전 명령 출력에 의해 자동으로 생성된 서비스 주체 appid 및 서비스 주체 암호로 바꿉니다. AKS가 리소스를 배포할 수 있도록 VNET 리소스 ID를 참조하여 서비스 주체에 권한을 부여합니다.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

필요한 자세한 사용 권한을 [여기](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)에서 확인할 수 있습니다.

> [!NOTE]
> kubenet 네트워크 플러그인을 사용하는 경우, kubenet에 필요한 경로 규칙을 추가하려면 경로 테이블이 필요하기 때문에 AKS 서비스 주체 또는 관리 ID 권한을 미리 생성된 경로 테이블에 부여해야 합니다.
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>AKS 배포

마지막으로, AKS 클러스터를 클러스터 전용의 기존 서브넷에 배포할 수 있습니다. 배포할 대상 서브넷은 환경 변수 `$SUBNETID`로 정의됩니다. 이전 단계에서는 `$SUBNETID` 변수를 정의하지 않았습니다. 서브넷 ID의 값을 설정하려면 다음 명령을 사용할 수 있습니다.

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

서브넷에 이미 있는 UDR을 사용하도록 아웃바운드 유형을 정의합니다. 이 구성을 통해 AKS는 부하 분산 장치에 대한 설정 및 IP 프로비전을 건너뛸 수 있습니다.

> [!IMPORTANT]
> 제한 사항을 포함한 아웃바운드 유형 UDR에 대한 자세한 내용은 [**송신 아웃바운드 유형 UDR**](egress-outboundtype.md#limitations)을 참조하세요.

> [!TIP]
> [**프라이빗 클러스터**](private-clusters.md)와 같은 클러스터 배포에 추가 기능을 추가할 수 있습니다. 
>
> [**API 서버 권한 있는 IP 범위**](api-server-authorized-ip-ranges.md)에 대해 AKS 기능을 추가하여 API 서버 액세스를 방화벽의 공용 엔드포인트로만 제한할 수 있습니다. 권한 있는 IP 범위 기능은 다이어그램에서 선택 사항으로 표시됩니다. 권한 있는 IP 범위 기능을 사용하도록 설정하여 API 서버 액세스를 제한하는 경우 개발자 도구는 방화벽의 가상 네트워크에서 jumpbox를 사용하거나 모든 개발자 엔드포인트를 권한 있는 IP 범위에 추가해야 합니다.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>API 서버에 대한 개발자 액세스 사용

이전 단계에서 클러스터에 대해 권한 있는 IP 범위를 사용한 경우 여기에서 API 서버에 액세스하려면 권한 있는 IP 범위의 AKS 클러스터 목록에 개발자 도구 IP 주소를 추가해야 합니다. 또 다른 옵션은 Firewall의 가상 네트워크에서 별도의 서브넷 내에 필요한 도구를 사용하여 jumpbox를 구성하는 것입니다.

다음 명령을 사용하여 승인된 범위에 또 다른 IP 주소를 추가합니다.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 새로 생성된 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다.

```azurecli
az aks get-credentials -g $RG -n $AKSNAME
```

### <a name="deploy-a-public-service"></a>공용 서비스 배포

이제 이 클러스터에 서비스 공개 및 애플리케이션 배포를 시작할 수 있습니다. 이 예제에서는 공용 서비스를 제공하지만 [내부 부하 분산 장치](internal-lb.md)를 통해 내부 서비스를 제공하도록 선택할 수도 있습니다.

![공용 서비스 DNAT](media/limit-egress-traffic/aks-create-svc.png)

다음 YAML을 `example.yaml`이라는 파일로 복사하여 Azure 투표 앱 애플리케이션을 배포합니다.

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

다음을 실행하여 서비스를 배포합니다.

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Azure Firewall에 DNAT 규칙 추가

> [!IMPORTANT]
> Azure Firewall을 사용하여 송신 트래픽을 제한하고 UDR(사용자 정의 경로)을 만들어서 모든 송신 트래픽을 강제로 적용하는 경우에는 수신 트래픽을 올바르게 허용하도록 방화벽에서 적절한 DNAT 규칙을 만들어야 합니다. UDR과 함께 Azure Firewall을 사용하면 비대칭 라우팅으로 인해 수신 설정이 손상됩니다. (AKS 서브넷에 방화벽의 개인 IP 주소로 이동하는 기본 경로가 있는데, 공용 부하 분산 장치 - 수신 또는 LoadBalancer 유형의 Kubernetes 서비스를 사용하는 경우 문제가 발생합니다.) 이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태를 저장하고 방화벽이 설정된 세션을 인식하지 못하므로 반환 패킷을 삭제합니다. Azure Firewall을 수신 또는 서비스 부하 분산 장치와 통합하는 방법을 알아보려면 [Azure Firewall을 Azure 표준 Load Balancer와 통합](../firewall/integrate-lb.md)을 참조하세요.

인바운드 연결을 구성하려면 Azure Firewall에 DNAT 규칙을 작성해야 합니다. 클러스터에 대한 연결을 테스트하기 위해 내부 서비스에서 노출되는 내부 IP로 라우팅하는 방화벽 프런트 엔드 공용 IP 주소에 대한 규칙이 정의됩니다.

액세스할 방화벽의 포트인 대상 주소를 사용자 지정할 수 있습니다. 변환된 주소는 내부 부하 분산 장치의 IP 주소여야 합니다. 변환된 포트는 Kubernetes 서비스에 대해 노출된 포트여야 합니다.

Kubernetes 서비스에서 만든 부하 분산 장치에 할당되는 내부 IP 주소를 지정해야 합니다. 다음을 실행하여 주소를 검색합니다.

```bash
kubectl get services
```

필요한 IP 주소는 다음과 유사하게 EXTERNAL-IP 열에 나열됩니다.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

다음을 실행하여 서비스 IP를 가져옵니다.

```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

다음을 실행하여 NAT 규칙을 추가합니다.

```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>연결 유효성 검사

브라우저에서 Azure Firewall 프런트 엔드 IP 주소로 이동하여 연결의 유효성을 검사합니다.

그러면 AKS Voting 앱이 표시됩니다. 이 예제에서는 방화벽 공용 IP가 `52.253.228.132`였습니다.

![스크린샷은 고양이, 개, 다시 설정 버튼과 총계가 있는 A K S Voting 앱을 보여줍니다.](media/limit-egress-traffic/aks-vote.png)

### <a name="clean-up-resources"></a>리소스 정리

Azure 리소스를 정리하려면 AKS 리소스 그룹을 삭제합니다.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터의 송신 트래픽을 제한하는 경우 허용할 포트와 주소를 알아보았습니다. 또한 Azure Firewall을 사용하여 아웃바운드 트래픽을 보호하는 방법도 살펴보았습니다.

필요한 경우 [아웃바운드 유형 `userDefinedRoute`문서 ](egress-outboundtype.md)에 따라 위의 단계를 일반화하여 선호하는 송신 솔루션으로 트래픽을 전달할 수 있습니다.

Pod가 자체적으로 및 클러스터 내 동-서 트래픽 제한 간에 통신하는 방법을 제한하려면 [AKS의 네트워크 정책을 사용하여 Pod 간 트래픽 보안][network-policy]을 참조하세요.

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
