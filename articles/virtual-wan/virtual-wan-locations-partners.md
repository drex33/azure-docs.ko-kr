---
title: Azure Virtual WAN 파트너 및 위치 | Microsoft Docs
description: 이 문서에는 Azure Virtual WAN 파트너 및 허브 위치 목록이 포함되어 있습니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: references_regions
ms.openlocfilehash: a7c94118b1b1a08f4bbd7b58faec6008f9ebc0f8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002741"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>가상 WAN 파트너 및 가상 허브 위치

이 문서에서는 가상 허브에 연결하기 위한 Virtual WAN 지원 지역 및 파트너에 대한 정보를 제공합니다.

Azure Virtual WAN은 Azure를 통해 최적화 및 자동화된 분기 간 연결을 제공하는 네트워킹 서비스입니다. Virtual WAN을 사용하여 Azure와 통신하도록 분기 디바이스를 연결 및 구성할 수 있습니다. 연결과 구성은 수동으로 수행하거나 또는 Virtual WAN 파트너를 통해 공급자 디바이스를 사용하여 수행할 수 있습니다. 파트너 디바이스는 쉽게 사용할 수 있고 간편하게 연결할 수 있으며 구성을 관리할 수 있습니다.

온-프레미스 디바이스에서 가상 허브로의 연결은 자동화된 방식으로 설정됩니다. 가상 허브는 Microsoft에서 관리하는 가상 네트워크입니다. 허브는 온-프레미스 네트워크(vpnsite)에서 연결을 활성화하는 다양한 서비스 엔드포인트를 포함합니다. 

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>파트너의 분기 IPsec 연결 자동화

Azure Virtual WAN에 연결하는 디바이스에는 연결 자동화가 기본으로 제공됩니다. 이 기능은 일반적으로 device-management UI(또는 이와 동등)에 설정되며, VPN 분기 디바이스와 Azure Virtual Hub VPN 엔드포인트(VPN 게이트웨이) 간의 연결 및 구성 관리를 설정합니다.

다음 상위 레벨 자동화가 디바이스 콘솔/관리 센터에 설정됩니다.

* 디바이스가 Azure Virtual WAN 리소스 그룹에 액세스하기 위한 적절한 권한
* Azure Virtual WAN에 분기 디바이스 업로드
* Azure 연결 정보 자동 다운로드
* 온-프레미스 분기 디바이스 구성 

일부 연결 파트너는 Azure Virtual Hub VNet과 VPN 게이트웨이 만들기를 포함하도록 자동화를 확장할 수 있습니다. 자동화에 대해 자세히 알아보려면 [Virtual WAN 파트너에 대한 자동화 지침](virtual-wan-configure-automation-providers.md)을 참조하세요.

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>분기 IPsec 연결 파트너

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

다음 파트너는 파트너 디바이스와 Azure Virtual WAN VPN 게이트웨이 사이의 IPsec 연결을 자동화할 작업 범위를 나타내는 회사 간의 서명된 사용 약관(128 Technologies, Arista, F5 Networks, Oracle SD-WAN(Talari), SharpLink)을 기반으로 로드맵을 계획합니다.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>통합된 가상 허브 제품을 사용하는 파트너

일부 파트너는 자동화된 지점 IPsec 연결 외에도 Azure Virtual WAN 허브에 직접 통합될 수 있는 **NVA(네트워크 가상 어플라이언스)** 를 제공합니다.  이 옵션을 이용하는 고객은 가상 허브에서 호환되는 타사 어플라이언스에 대한 분기 연결을 종료할 수 있습니다.  

Virtual WAN 허브에서 NVA를 제공하는 파트너는 다음을 준수해야 합니다.

* 분기 디바이스에서 IPsec 연결 자동화를 구현하고 Azure Virtual WAN 허브에 NVA 제품을 등록합니다.
* Azure Marketplace에서 사용할 수 있는 기존 네트워크 가상 어플라이언스 제품이 있어야 합니다.

파트너로서 가상 허브 제품의 관리형 NVA에 대한 질문이 있으면 다음 메일로 문의하세요. vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>통합 가상 허브 NVA 파트너

이러한 파트너에는 현재 Virtual WAN 허브에 배포할 수 있는 **관리형 애플리케이션** 제품이 있습니다.

|파트너|구성/방법/배포 가이드|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN 배포 가이드](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco CSR(Cloud Service Router) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Cisco SD-WAN 솔루션을 Azure Virtual WAN과 통합하면 다중 클라우드 배포를 위한 Cloud OnRamp가 향상되고 Azure Virtual WAN Hubs에서 Cisco Catalyst 8000V Edge Software(Cisco Catalyst 8000V)를 NVA(네트워크 가상 어플라이언스)로 구성할 수 있습니다. [Cisco SD-WAN Cloud OnRamp, Cisco IOS XE 릴리스 17.x 구성 가이드 보기](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) 
|[Virtual WAN 허브의 VMware SD-WAN](https://sdwan.vmware.com/partners/microsoft) | Virtual WAN 허브에서 VMware SD-WAN 어플라이언스를 비활성화하는 방법에 대한 지침은 Virtual WAN [Hub 배포 가이드에서 VMware SD-WAN을](https://kb.vmware.com/s/article/82746)참조하세요. 배포하려면 [여기에서](https://azuremarketplace.microsoft.com/marketplace/apps/velocloud.vmware_sdwan_in_vwan) 관리되는 애플리케이션에 액세스하세요.|

가까운 미래에 가상 허브 제품에 네트워크 가상 어플라이언스를 도입할 예정인 파트너는 Aviatrix, Citrix, Versa Networks 및 Silver Peak입니다.

## <a name="locations"></a><a name="locations"></a>위치

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>다음 단계

* 가상 WAN에 대한 자세한 내용은 [가상 WAN FAQ](virtual-wan-faq.md)를 참조하세요.

* Azure Virtual WAN에 대한 연결을 자동화하는 방법에 대한 내용은 [Virtual WAN 파트너를 위한 자동화 지침](virtual-wan-configure-automation-providers.md)을 참조하세요.
