---
title: Azure NetApp Files 네트워크 계획 지침 | Microsoft Docs
description: Azure NetApp Files를 사용하여 효과적인 네트워크 아키텍처를 설계하는 데 도움이 되는 지침을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: ramakk
ms.openlocfilehash: 6d82310eea944d91124025c3d894f543448f82e1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218682"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp Files 네트워크 계획 지침

네트워크 아키텍처 계획은 모든 애플리케이션 인프라 설계의 핵심 요소입니다. 이 문서는 Azure NetApp Files의 다양한 기능을 활용하기 위해 워크로드에 대한 효과적인 네트워크 아키텍처를 설계하는 데 도움이 됩니다.

Azure NetApp Files 볼륨은 Microsoft Azure Virtual Network 내에서 [위임된 서브넷](../virtual-network/virtual-network-manage-subnet.md)이라는 특수 용도의 서브넷에 포함되도록 설계되었습니다. 따라서 필요에 따라 Virtual Network 게이트웨이(ExpressRoute 또는 VPN Gateway)를 통해 VNet, 같은 지역의 피어링된 VNet 또는 온-프레미스에서 볼륨에 직접 액세스할 수 있습니다. 서브넷은 Azure NetApp Files 전용이며 다른 Azure 서비스나 인터넷에 연결되어 있지 않습니다.

## <a name="considerations"></a>고려 사항  

Azure NetApp Files 네트워크를 계획하는 경우 몇 가지 고려 사항을 잘 알고 있어야 합니다.

### <a name="constraints"></a>제약 조건

아래 기능은 현재 Azure NetApp Files에 대해 지원되지 않습니다. 

* 위임된 서브넷에 적용된 NSG(네트워크 보안 그룹)
* 위임된 서브넷에 적용된 UDR(사용자 정의 경로)
* Azure NetApp Files 인터페이스의 사용자 지정 Azure 정책 (예: 사용자 지정 명명 정책)
* Azure NetApp Files 트래픽의 부하 분산 장치
* Azure 가상 WAN 
* 영역 중복 Virtual Network 게이트웨이(Az를 포함한 게이트웨이 SKU) 
* 활성/활성 Virtual Network GW 
* 이중 스택(IPv4 및 IPv6) VNet

Azure NetApp Files에는 다음과 같은 네트워크 제한이 적용됩니다.

* Azure NetApp Files(*즉시* 피어링된 VNet 포함)와 함께 VNet에 사용 중인 IP 수는 1000개를 초과할 수 없습니다. 고객 규모의 수요를 충족하기 위해 이 한도를 늘리고자 노력하고 있습니다. 
* 각 Azure Virtual Network(VNet)에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.


### <a name="supported-network-topologies"></a>지원되는 네트워크 토폴로지

다음 표에서는 Azure NetApp Files에서 지원하는 네트워크 토폴로지에 대해 설명합니다.  지원되지 않는 토폴로지의 해결 방법에 대해서도 설명합니다. 

|    토폴로지    |    지원됨    |     해결 방법    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    로컬 VNet의 볼륨에 연결    |    예    |         |
|    피어링된 VNet의 볼륨에 연결(동일한 지역)    |    예    |         |
|    피어링된 VNet의 볼륨에 연결(지역 간 또는 글로벌 피어링)    |    예    |    None    |
|    ExpressRoute 게이트웨이를 통해 볼륨에 연결    |    예    |         |
|    게이트웨이 전송과 함께 ExpressRoute 게이트웨이 및 VNet 피어링을 통해 온-프레미스에서 스포크 VNet의 볼륨에 연결    |    예    |        |
|    VPN 게이트웨이를 통해 온-프레미스에서 스포크 VNet의 볼륨에 연결    |    예    |         |
|    게이트웨이 전송과 함께 VPN 게이트웨이 및 VNet 피어링을 통해 온-프레미스에서 스포크 VNet의 볼륨에 연결    |    예    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨용 가상 네트워크

이 섹션에서는 가상 네트워크를 계획하는 데 도움이 되는 개념을 설명합니다.

### <a name="azure-virtual-networks"></a>Azure 가상 네트워크

Azure NetApp Files 볼륨을 프로비저닝하기 전에 Microsoft Azure Virtual Network(VNet)를 만들거나 구독에 이미 있는 VNet을 사용해야 합니다. VNet은 볼륨의 네트워크 경계를 정의합니다.  가상 네트워크를 만들기에 대한 자세한 내용은 [Microsoft Azure Virtual Network 설명서](../virtual-network/virtual-networks-overview.md)를 참조하세요.

### <a name="subnets"></a>서브넷

서브넷은 그 안의 Azure 리소스가 사용할 수 있는 개별 주소 공간으로 가상 네트워크를 구분합니다.  Azure NetApp Files 볼륨은 [위임된 서브넷](../virtual-network/virtual-network-manage-subnet.md)이라는 특수 용도의 서브넷에 포함되어 있습니다. 

서브넷 위임은 서브넷에서 서비스 관련 리소스를 만들 수 있는 명시적 권한을 Azure NetApp Files 서비스에 부여합니다.  서비스를 배포하는 데 고유한 식별자가 사용됩니다. 이 경우 Azure NetApp Files에 연결할 수 있도록 네트워크 인터페이스가 만들어집니다.

새 VNet을 사용하는 경우 [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)의 지침에 따라 서브넷을 만들고 Azure NetApp Files에 위임할 수 있습니다. 다른 서비스에 아직 위임되지 않은 기존의 빈 서브넷을 위임할 수도 있습니다.

VNet이 다른 VNet과 피어링되는 경우 VNet 주소 공간을 확장할 수 없습니다. 이러한 이유로 VNet 주소 공간 내에 위임된 서브넷을 새로 만들어야 합니다. 주소 공간을 확장해야 하는 경우 먼저 VNet 피어링을 삭제해야 합니다.

### <a name="udrs-and-nsgs"></a>UDR 및 NSG

UDR(사용자 정의 경로)와 NSG(네트워크 보안 그룹)는 Azure NetApp Files에 대한 위임된 서브넷에서 지원되지 않습니다. 그러나 Azure NetApp Files에 위임된 서브넷과 동일한 VNet 내 에서도 UDR와 NSG를 다른 서브넷에 적용할 수 있습니다.

* 그러면 UDR에 의해 다른 서브넷에서 Azure NetApp Files 위임된 서브넷으로의 트래픽 흐름이 정의됩니다. 이를 통해 시스템 경로를 사용하여 Azure NetApp Files에서 다른 서브넷으로의 트래픽 흐름에 맞춰 조정할 수 있습니다.  
* 그런 다음, NSG는 Azure NetApp Files 위임된 서브넷과의 트래픽을 허용하거나 거부합니다. 

## <a name="azure-native-environments"></a>Azure 네이티브 환경

다음 다이어그램에서는 Azure 네이티브 환경을 보여줍니다.

![Azure 네이티브 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>로컬 VNet

기본 시나리오는 동일한 VNet의 VM(가상 머신)에서 Azure NetApp Files 볼륨을 만들거나 연결하는 것입니다. 위 다이어그램의 VNet 2의 경우 볼륨 1은 위임된 서브넷에 만들어지고 기본 서브넷의 VM 1에 탑재 가능합니다.

### <a name="vnet-peering"></a>VNet 피어링

동일한 지역에 서로의 리소스에 액세스해야 하는 추가 VNet이 있는 경우 Azure 인프라를 통한 보안 연결이 가능하도록 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)으로 VNet을 연결할 수 있습니다. 

위의 다이어그램에서 VNet 2와 VNet 3을 고려합니다. VM 1이 VM 2 또는 볼륨 2에 연결해야 하거나 VM 2가 VM 1 또는 볼륨 1에 연결해야 하는 경우 VNet 2와 VNet 3 간에 VNet 피어링을 사용하도록 설정해야 합니다. 

또한 VNet 1은 VNet 2와 피어링되고 VNet 2는 동일한 지역의 VNet 3과 피어링되는 시나리오를 고려해야 합니다. VNet 1의 리소스는 VNet 2의 리소스에 연결할 수 있지만 VNet 1과 VNet 3이 피어링되지 않는 한 VNet 3의 리소스에 연결할 수 없습니다. 

위의 다이어그램에서 VM 3은 볼륨 1에 연결할 수 있지만 VM 4는 볼륨 2에 연결할 수 없습니다.  그 이유는 스포크 VNet이 피어링되지 않고 _전송 라우팅이 VNet 피어링을 통해 지원되지 않기_ 때문입니다.

## <a name="hybrid-environments"></a>하이브리드 환경

다음 다이어그램에서는 하이브리드 환경을 보여줍니다. 

![하이브리드 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

하이브리드 시나리오에서 온-프레미스 데이터 센터의 애플리케이션은 Azure의 리소스에 액세스해야 합니다.  데이터 센터를 Azure로 확장하거나 재해 복구에 Azure 네이티브 서비스를 사용하려는 경우가 여기에 해당합니다. 사이트 간 VPN 또는 ExpressRoute를 통해 온-프레미스의 여러 리소스를 Azure의 리소스에 연 하는 방법에 대한 자세한 내용은 [VPN Gateway 계획 옵션](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)을 참조하세요.

하이브리드 허브-스포크 토폴로지에서 Azure의 허브 VNet은 온-프레미스 네트워크에 대한 연결의 중심점 역할을 합니다. 스포크는 허브와 피어링되는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다.

구성에 따라 허브 및 스포크의 리소스에 온-프레미스 리소스를 연결할 수 있습니다.

위에 나와 있는 토폴로지에서 온-프레미스 네트워크는 Azure의 허브 VNet에 연결되어 있으며 허브 VNet과 피어링되는 동일한 지역의 스포크 VNet이 2개 있습니다.  이 시나리오에서 Azure NetApp Files 볼륨에 대해 지원되는 연결 옵션은 다음과 같습니다.

* 온-프레미스 리소스 VM 1과 VM 2는 사이트 간 VPN 또는 ExpressRoute 회로를 통해 허브의 볼륨 1에 연결할 수 있습니다. 
* 온-프레미스 리소스 VM 1과 VM 2는 사이트 간 VPN 및 지역 VNet 피어링을 통해 볼륨 2 또는 볼륨 3에 연결할 수 있습니다.
* 허브 VNet의 VM 3은 스포크 VNet 1의 볼륨 2와 스포크 VNet 2의 볼륨 3에 연결할 수 있습니다.
* 스포크 VNet 1의 VM 4와 스포크 VNet 2의 VM 5는 허브 VNet의 볼륨 1에 연결할 수 있습니다.
* 스포크 VNet 1의 VM 4는 스포크 VNet 2의 볼륨 3에 연결할 수 없습니다. 또한 스포크 VNet2의 VM 5는 스포크 VNet 1의 볼륨 2에 연결할 수 없습니다. 스포크 VNet이 피어링되지 않고 _전송 라우팅이 VNet 피어링을 통해 지원되지 않기_ 때문입니다.
* 위의 아키텍처에서 스포크 VNET에 게이트웨이가 있는 경우 허브의 게이트웨이를 통한 온-프레미스 연결에서 ANF 볼륨에 대한 연결이 끊어집니다. 설계상 스포크 VNet의 게이트웨이가 우선하므로 해당 게이트웨이를 통해 연결하는 머신만 ANF 볼륨에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)
