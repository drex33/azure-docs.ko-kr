---
title: '아키텍처: 가상 WAN 및 SD-WAN 연결'
titleSuffix: Azure Virtual WAN
description: 프라이빗 SD-WAN을 Azure Virtual WAN과 상호 연결하는 방법을 알아봅니다.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: 5ef4695b4655841d7cdfb5b40c56d4c21f4f979f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004913"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Azure Virtual WAN을 사용한 SD-WAN 연결 아키텍처

Azure Virtual WAN은 단일 운영 인터페이스를 사용하여 여러 클라우드 연결과 보안 서비스를 함께 제공하는 네트워킹 서비스입니다. 이러한 서비스에는 분기(사이트 간 VPN), 원격 사용자(지점 및 사이트 간 VPN), 프라이빗(ExpressRoute) 연결, Vnet에 대한 클라우드 간 전이적 연결, VPN 및 ExpressRoute 상호 연결, 라우팅, Azure Firewall 및 프라이빗 연결에 대한 암호화가 포함됩니다.

Azure 가상 WAN 자체는 SD-WAN(Software Defined WAN)이지만 프레미스 기반 SD-WAN 기술 및 서비스와 원활하게 상호 연결할 수 있도록 설계되었습니다. 이러한 서비스는 [가상 WAN](virtual-wan-locations-partners.md) 에코시스템과 Azure 네트워킹 [MSP(관리 서비스 파트너)](../networking/networking-partners-msp.md)에서 제공합니다. 프라이빗 WAN을 SD-WAN으로 변환하는 엔터프라이즈에는 프라이빗 SD-WAN을 Azure 가상 WAN과 상호 연결하는 옵션이 있습니다. 엔터프라이즈는 다음 옵션 중에서 선택할 수 있습니다.

* 직접 상호 연결 모델
* NVA-in-VWAN 허브를 사용하는 직접 상호 연결 모델
* 간접 상호 연결 모델
* 즐겨 사용하는 관리 서비스 공급자 [MSP](../networking/networking-partners-msp.md)를 사용하여 관리되는 하이브리드 WAN 모델

이러한 모든 경우에서 가상 WAN을 SD-WAN과 상호 연결하는 것은 연결 측면에서 유사하지만 오케스트레이션 및 작업 측면에서는 다를 수 있습니다.

## <a name="direct-interconnect-model"></a><a name="direct"></a>직접 상호 연결 모델

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="직접 상호 연결 모델":::

이 아키텍처 모델에서 SD-WAN 분기 CPE(고객-프레미스 장비)는 IPsec 연결을 통해 가상 WAN 허브에 직접 연결됩니다. 또한 분기 CPE는 프라이빗 SD-WAN을 통해 다른 분기에 연결하거나 분기 연결을 위해 Virtual WAN을 사용할 수 있습니다. Azure에서 워크로드에 액세스해야 하는 분기는 가상 WAN 허브에서 종료되는 IPsec 터널을 통해 Azure에 직접 안전하게 액세스할 수 있습니다.

SD-WAN CPE 파트너는 자동화가 가능하여 각 CPE 장치에서 일반적으로 번거롭고 오류가 발생하기 쉬운 IPsec 연결을 자동화할 수 있습니다. 자동화를 통해 SD-WAN 컨트롤러는 Virtual WAN API를 통해 Azure와 통신하여 Virtual WAN 사이트를 구성하고 필요한 IPsec 터널 구성을 해당 분기 CPE에 푸시할 수 있습니다. 다양한 SD-WAN 파트너의 가상 WAN 상호 관계 자동화에 대한 설명은 [자동화 지침](virtual-wan-configure-automation-providers.md)을 참조하세요.

SD-WAN CPE는 트래픽 최적화 및 경로 선택이 구현되고 적용되는 위치를 유지합니다. 

이 모델에서는 Virtual WAN에 대한 연결이 IPsec을 통해 발생하고 IPsec VPN이 Virtual WAN VPN 게이트웨이에서 종료되기 때문에 실시간 트래픽 특성을 기반으로 하는 일부 공급 업체의 독점 트래픽 최적화가 지원되지 않을 수 있습니다. 예를 들어 분기 디바이스에서 다른 SD-WAN 노드와 다양한 네트워크 패킷 정보를 교환하여 분기에서 동적 경로를 선택할 수 있으므로 분기에서 동적으로 우선순위가 지정된 트래픽에 사용할 최적 링크를 식별합니다. 이 기능은 마지막 마일 최적화(가장 가까운 Microsoft POP로 분기)가 필요한 영역에서 유용할 수 있습니다.

가상 WAN을 사용하면 지점 CPE에서 Virtual WAN VPN 게이트웨이에 이르는 여러 ISP 링크에서 정책 기반 경로 선택인 Azure 경로 선택을 얻을 수 있습니다. 가상 WAN을 사용하면 동일한 SD-WAN 분기 CPE에서 여러 링크(경로)를 설정할 수 있으며, 각 링크는 SD-WAN CPE의 고유한 공용 IP에서 두 개의 다른 Azure Virtual WAN VPN 게이트웨이 인스턴스로 이중 터널 연결을 나타냅니다. SD-WAN 공급업체는 CPE 링크에서 정책 엔진이 설정한 트래픽 정책을 기반으로 가장 최적의 Azure 경로를 구현할 수 있습니다. Azure 끝에서 들어오는 모든 연결은 동일하게 처리됩니다.

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct-nva"></a>NVA-in-VWAN 허브를 사용하는 직접 상호 연결 모델

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="NVA-in-VWAN 허브를 사용하는 직접 상호 연결 모델":::

이 아키텍처 모델은 타사 [NVA(네트워크 가상 어플라이언스)를 가상 허브에 직접](./about-nva-hub.md) 배포할 수 있도록 지원합니다. 그러면 분기 CPE를 가상 허브의 동일한 브랜드 NVA에 연결하려는 고객이 사용할 수 있으므로 Azure 워크로드에 연결할 때 독점 종단 간 SD WAN 기능을 사용할 수 있습니다. 

몇몇 Virtual WAN 파트너는 배포 프로세스의 일환으로 NVA를 자동으로 구성하는 환경을 제공해 왔습니다. NVA가 가상 허브로 프로비전되면 NVA에 필요할 수 있는 추가 구성은 NVA 파트너 포털 또는 관리 애플리케이션을 통해 수행해야 합니다. NVA에 대한 직접 액세스 권한은 제공되지 않습니다. Azure Virtual WAN 허브에 직접 배포할 수 있는 NVA는 가상 허브에서 사용하도록 엔지니어링되었습니다. VWAN 허브에서 NVA를 지원하는 파트너 및 해당 배포 가이드는 [Virtual WAN 파트너](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) 문서를 참조하세요.

SD-WAN CPE는 트래픽 최적화 및 경로 선택이 구현되고 적용되는 위치를 유지합니다.
이 모델에서는 Virtual WAN에 대한 연결이 허브의 SD WAN NVA를 통해 제공되므로 실시간 트래픽 특성을 기반으로 하는 공급업체 독점 트래픽 최적화가 지원됩니다.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>간접 상호 연결 모델

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="간접 상호 연결 모델":::

이 아키텍처 모델에서 SD-WAN 분기 CPE는 가상 WAN 허브에 간접적으로 연결됩니다. 그림에 표시된 것처럼 SD-WAN 가상 CPE는 엔터프라이즈 VNet에 배포됩니다. 이 가상 CPE는 IPsec을 사용하여 가상 WAN 허브에 연결됩니다. 가상 CPE는 Azure에 대해 SD-WAN 게이트웨이 역할을 합니다. Azure에서 워크로드에 액세스해야 하는 분기는 v-CPE 게이트웨이를 통해 액세스할 수 있습니다.

v-CPE 게이트웨이(NVA)를 통해 Azure에 연결되므로 Azure 워크로드 VNet에서 다른 SD로 들어오고 나가는 모든 트래픽이 NVA를 통해 이동합니다. 이 모델에서는 사용자가 고가용성, 확장성 및 라우팅을 포함하여 SD-WAN NVA를 관리하고 운영해야 합니다.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>관리되는 하이브리드 WAN 모델

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="관리되는 하이브리드 WAN 모델":::

이 아키텍처 모델에서 엔터프라이즈는 MSP(관리 서비스 공급자) 파트너에서 제공하는 관리형 SD-WAN 서비스를 활용할 수 있습니다. 이 모델은 위에서 설명한 직접 또는 간접 모델과 유사합니다. 그러나 이 모델에서는 SD-WAN 디자인, 오케스트레이션 및 작업이 SD-WAN 공급자에 의해 전달됩니다.

[Azure 네트워킹 MSP 파트너](../networking/networking-partners-msp.md)는 [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/)를 사용하여 엔터프라이즈 고객의 Azure 구독에서 SD-WAN 및 가상 WAN 서비스를 구현하고 고객 대신 엔드투엔드 하이브리드 WAN을 운영할 수 있습니다. 또한 이러한 MSP는 Azure ExpressRoute를 가상 WAN으로 구현하고 이를 엔드투엔드 관리 서비스로 운영할 수도 있습니다.

## <a name="additional-information"></a>추가 정보

* [Azure 가상 WAN FAQ](virtual-wan-faq.md)
* [원격 연결 해결](work-remotely-support.md)
