---
title: 가상 네트워크를 사용하는 시나리오
description: Azure 가상 네트워크에 컨테이너 그룹을 배포하는 시나리오, 리소스 및 제한 사항입니다.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 7f0e2719d6949037e2268f66bf1dce8904530992
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247497"
---
# <a name="virtual-network-scenarios-and-resources"></a>가상 네트워크 시나리오 및 리소스

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md)는 Azure 및 온-프레미스 리소스를 위한 안전한 프라이빗 네트워킹 기능을 제공합니다. 컨테이너 그룹을 Azure Virtual Network에 배포하면 컨테이너가 가상 네트워크의 다른 리소스와 안전하게 통신할 수 있습니다. 

이 문서에서는 가상 네트워크 시나리오, 제한 사항 및 리소스에 대한 배경 정보를 제공합니다. Azure CLI를 사용하는 배포 예제는 [Azure Virtual Network에 컨테이너 인스턴스 배포](container-instances-vnet.md)를 참조하세요.

> [!IMPORTANT]
> 가상 네트워크에 대한 컨테이너 그룹 배포는 일반적으로 Azure Container Instances를 사용할 수 있는 대부분의 지역에서 Linux 컨테이너에 사용할 수 있습니다. 자세한 내용은 [지역 및 리소스 가용성](container-instances-region-availability.md)을 참조하세요. 

## <a name="scenarios"></a>시나리오

Azure Virtual Network에 컨테이너 그룹을 배포하는 경우 다음과 같은 시나리오를 수행할 수 있습니다.

* 동일한 서브넷의 컨테이너 그룹 간 직접 통신
* 컨테이너 인스턴스에서 가상 네트워크의 데이터베이스로 [작업 기반](container-instances-restart-policy.md) 워크로드 전송
* 가상 네트워크의 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)에서 컨테이너 인스턴스의 콘텐츠 검색
* [VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md)를 통한 온-프레미스 리소스와 컨테이너 간 통신 사용
* [Azure Firewall](../firewall/overview.md)과 통합하여 컨테이너에서 시작되는 아웃바운드 트래픽 식별 
* 가상 머신과 같은 가상 네트워크의 Azure 리소스와 통신하기 위해 내부 Azure DNS를 통해 이름 확인
* NSG 규칙을 사용하여 서브넷 또는 다른 네트워크 리소스에 대한 컨테이너 액세스 제어

## <a name="unsupported-networking-scenarios"></a>지원되지 않는 네트워킹 시나리오 

* **Azure Load Balancer** - 네트워크 컨테이너 그룹의 컨테이너 인스턴스 앞에 Azure Load Balancer를 배치하는 시나리오는 지원되지 않습니다.
* **글로벌 가상 네트워크 피어링** - 글로벌 피어링(Azure 지역에서 가상 네트워크 연결)은 지원되지 않습니다.
* **공용 IP 또는 DNS 레이블** - 가상 네트워크에 배포된 컨테이너 그룹은 현재 공용 IP 주소 또는 정규화된 도메인 이름으로 컨테이너를 인터넷에 직접 노출하는 시나리오를 지원하지 않습니다.
* **Virtual Network NAT** - 가상 네트워크에 배포된 컨테이너 그룹은 현재 아웃바운드 인터넷 연결에 대한 NAT 게이트웨이 리소스 사용을 지원하지 않습니다.

## <a name="other-limitations"></a>기타 제한 사항

* 현재는 가상 네트워크에 배포된 컨테이너 그룹에서만 Linux 컨테이너를 지원합니다.
* 컨테이너 그룹을 배포하려는 서브넷에는 다른 리소스 종류가 포함되어 있지 않아야 합니다. 컨테이너 그룹을 배포하기 전에 기존 서브넷에서 기존 리소스를 모두 제거하거나 새 서브넷을 만들어야 합니다.
* 가상 네트워크에 배포된 컨테이너 그룹의 [관리 ID](container-instances-managed-identity.md)를 사용할 수 없습니다.
* 가상 네트워크에 배포된 컨테이너 그룹에서 [활동성 프로브](container-instances-liveness-probe.md)나 [준비 상태 프로브](container-instances-readiness-probe.md)를 사용하도록 설정할 수 없습니다.
* 가상 네트워크에 배포할 때는 네트워킹 리소스가 추가로 사용되므로 대개 표준 컨테이너 인스턴스를 배포할 때보다 속도가 느립니다.
* 포트 25에 대한 아웃바운드 연결은 현재 지원되지 않습니다.
* 컨테이너 그룹을 Azure Storage 계정에 연결하는 경우 해당 리소스에 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 추가해야 합니다.
* 현재 [IPv6 주소](../virtual-network/ip-services/ipv6-overview.md)는 지원되지 않습니다. 

## <a name="required-network-resources"></a>필요한 네트워크 리소스

가상 네트워크에 컨테이너 그룹을 배포할 때 필요한 세 가지 Azure Virtual Network 리소스는 [가상 네트워크](#virtual-network) 자체, 가상 네트워크 내의 [위임된 서브넷](#subnet-delegated) 및 [네트워크 프로필](#network-profile)입니다. 

### <a name="virtual-network"></a>가상 네트워크

가상 네트워크는 주소 공간을 정의합니다. 이 주소 공간에 서브넷을 하나 이상 만든 다음 컨테이너 그룹 등의 Azure 리소스를 가상 네트워크의 서브넷에 배포합니다.

### <a name="subnet-delegated"></a>서브넷(위임)

서브넷은 가상 네트워크를 서브넷에 배치하는 Azure 리소스가 사용할 수 있는 개별 주소 공간으로 구분합니다. 가상 네트워크 내에 하나 이상의 서브넷을 만들 수 있습니다.

컨테이너 그룹용으로 사용하는 서브넷은 컨테이너 그룹만 포함할 수 있습니다. 서브넷에 컨테이너 그룹을 처음 배포할 때 Azure는 Azure Container Instances에 해당 서브넷을 위임합니다. 위임된 서브넷은 컨테이너 그룹에만 사용할 수 있습니다. 위임된 서브넷에 컨테이너 그룹 이외의 리소스를 배포하려고 하면 작업이 실패합니다.

### <a name="network-profile"></a>네트워크 프로필

Azure 리소스용 네트워크 구성 템플릿인 네트워크 프로필은 리소스를 배포해야 하는 서브넷 등 리소스의 특정 네트워크 속성을 지정합니다. 처음 [az container create][az-container-create] 명령을 사용하여 서브넷(가상 네트워크)에 컨테이너 그룹을 배포하는 경우 Azure가 사용자를 위해 네트워크 프로필을 만듭니다. 그러면 다음에 컨테이너 그룹을 서브넷에 배포할 때 해당 네트워크 프로필을 사용할 수 있습니다. 

Resource Manager 템플릿, YAML 파일 또는 프로그래밍 방식을 사용하여 서브넷에 컨테이너 그룹을 배포하려면 네트워크 프로필의 전체 Resource Manager 리소스 ID를 제공해야 합니다. [az container create][az-container-create]를 사용하여 이전에 만든 프로필을 사용할 수도 있고 Resource Manager 템플릿을 사용하여 프로필을 만들 수도 있습니다([템플릿 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet) 및 [참조](/azure/templates/microsoft.network/networkprofiles) 참조). 이전에 만든 프로필의 ID를 가져오려면 [az network profile list][az-network-profile-list] 명령을 사용합니다. 

아래 다이어그램에서는 Azure Container Instances에 위임된 서브넷에 컨테이너 그룹이 여러 개 배포되어 있습니다. 컨테이너 그룹 하나를 서브넷에 배포한 후에는 같은 네트워크 프로필을 지정하여 추가 컨테이너 그룹을 배포할 수 있습니다.

![가상 네트워크 내의 컨테이너 그룹][aci-vnet-01]

## <a name="next-steps"></a>다음 단계

* Azure CLI를 사용하는 배포 예제는 [Azure Virtual Network에 컨테이너 인스턴스 배포](container-instances-vnet.md)를 참조하세요.
* Resource Manager 템플릿을 사용하여 새 가상 네트워크, 서브넷, 네트워크 프로필 및 컨테이너 그룹을 배포하려면 [VNet을 사용하여 Azure 컨테이너 그룹 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet
)를 참조하세요.
* [Azure Portal](container-instances-quickstart-portal.md)을 사용하여 컨테이너 인스턴스를 만들 때 **네트워킹** 탭에서 새 가상 네트워크 또는 기존 가상 네트워크에 대한 설정을 제공할 수도 있습니다.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list