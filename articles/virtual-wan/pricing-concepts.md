---
title: Virtual WAN 가격 책정 정보
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 일반적인 Virtual WAN 가격 책정 질문에 답변합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: cherylmc
ms.custom: references_pricing
ms.openlocfilehash: 853005cdc9e44357661cdb0b3b50e7f6ba5a0bbf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730100"
---
# <a name="about-virtual-wan-pricing"></a>Virtual WAN 가격 책정 정보

Azure Virtual WAN은 많은 네트워킹, 보안 및 라우팅 기능을 결합하여 단일 운영 인터페이스를 제공하는 네트워킹 서비스입니다. 이러한 기능에는 분기 연결(SD-WAN 또는 VPN CPE와 같은 Virtual WAN 파트너 디바이스에서 연결 자동화를 통해), 사이트 간 VPN 연결, 원격 사용자 VPN(지점 및 사이트 간) 연결, 프라이빗(ExpressRoute) 연결, 클라우드 연결(가상 네트워크에 대한 전이 연결), VPN ExpressRoute 상호 연결, 라우팅, Azure Firewall, 프라이빗 연결용 암호화 등이 포함됩니다.

이 문서에서는 Azure Virtual WAN 있는 일반적으로 배포되는 세 가지 시나리오와 나열된 가격을 기반으로 하는 배포에 대한 일반적인 예상 가격을 설명합니다. 또한 Virtual WAN 유용할 수 있는 다른 많은 시나리오가 있을 수 있습니다.

> [!IMPORTANT]
> 이 문서에 표시된 가격 책정은 예제 목적으로만 사용됩니다. 
>  * 가격 책정은 언제든지 변경됩니다. 현재 가격 책정 정보는 [Virtual WAN 가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조하세요. 
>  * 허브 간(허브-허브) 요금은 Virtual WAN 가격 책정 페이지에 표시되지 않습니다. 가격 책정에는 Inter-Region(중간/간) 요금이 부과됩니다. 자세한 내용은 [Azure 데이터 전송 요금을 참조하세요.](https://azure.microsoft.com/pricing/details/bandwidth/)
>

## <a name="pricing-components"></a><a name="pricing"></a>가격 책정 구성 요소

다음 다이어그램에서는 Virtual WAN 관련된 네트워크의 일반적인 데이터 경로와 시간당 및 GB당 다양한 가격 책정 구성 요소를 보여줍니다.

:::image type="content" source="./media/pricing-concepts/data-routes.png" alt-text="데이터 경로 다이어그램." lightbox="./media/pricing-concepts/data-routes.png":::

| 값 | 시나리오| 시간당 비용 | GB당 비용 |
| --- | --- | --- | --- |
| 1 | 미국 동부의 표준 vWAN 허브를 통해 스포크 VNet에서 VPN 사이트간 분기로 데이터 전송|배포 시간($0.25/시간) + VPN S2S 배율 단위($0.261/시간) + VPN S2S 연결 단위($0.05/hr) = $0.561/hr|VNet 피어링(아웃바운드)($0.01/GB) + 표준 아웃바운드 영역 1($0.087/GB) = $0.097/GB|
| 1'|미국 동부의 표준 vWAN 허브를 통해 VPN 사이트-사이트 분기에서 스포크 VNet으로 데이터 전송|배포 시간($0.25/시간) + VPN S2S 배율 단위($0.261/시간) + VPN S2S 연결 단위($0.05/hr) = $0.561/hr|VNet 피어링(인바운드)($0.01/GB) = $0.01/GB|
|2|표준 vWAN 허브를 통해 VPN 사이트 간 분기에서 미국 동부의 다른 VPN 사이트 간 분기로 데이터 전송|배포 시간($0.25/시간) + VPN S2S 배율 단위($0.261/시간) + VPN S2S 연결 단위(2x$0.05/hr) = $0.611/hr|표준 아웃바운드 영역 1($0.087/GB) = $0.087/GB|
|2'|표준 vWAN 허브를 통해 VPN 사이트간 분기에서 미국 동부의 ExpressRoute 연결된 데이터 센터/HQ로 데이터 전송|배포 시간($0.25/시간) + VPN S2S 배율 단위($0.261/시간) + VPN S2S 연결 단위($0.05/hr) + ExpressRoute 배율 단위($0.42/시간) + ExpressRoute 연결 단위($0.05/hr) = $1.03/hr|ExpressRoute 요금제 아웃바운드 영역 1($0.025/GB) = $0.025/GB|
|3|표준 vWAN 허브를 통해 VPN 사이트간 분기에서 미국 동부의 ExpressRoute 연결된 데이터 센터/HQ로 데이터 전송|배포 시간($0.25/시간) + VPN S2S 배율 단위($0.261/시간) + VPN S2S 연결 단위($0.05/hr) + ExpressRoute 배율 단위($0.42/시간) + ExpressRoute 연결 단위($0.05/hr) = $1.03/hr|ExpressRoute 요금제 아웃바운드 영역 1($0.025/GB) = $0.025/GB|
|4|미국 동부의 표준 vWAN 허브를 통해 스포크 VNet에서 ExpressRoute 연결 데이터 센터/HQ로 데이터 전송|배포 시간($0.25/시간) + ExpressRoute 배율 단위($0.42/시간) + ExpressRoute 연결 단위($0.05/hr) = $0.72/hr|VNet 피어링(아웃바운드)($0.01/GB) + ExpressRoute 요금제 아웃바운드 영역 1($0.025/GB) = $0.035/GB|
|4'|미국 동부의 표준 vWAN 허브를 통해 ExpressRoute 연결 데이터 센터/HQ에서 스포크 VNet으로 데이터 전송|배포 시간($0.25/시간) + ExpressRoute 배율 단위($0.42/시간) + ExpressRoute 연결 단위($0.05/hr) = $0.72/hr|VNet 피어링(인바운드)($0.01/GB) = $0.01/GB|
|4"|ExpressRoute 연결 데이터 센터/HQ에서 유럽 표준 vWAN 허브를 통해 원격 스포크 VNet으로 데이터 전송|배포 시간(2x$0.25/시간) + ExpressRoute 배율 단위($0.42/시간) + ExpressRoute 연결 단위($0.05/hr) = $0.97/hr|VNet 피어링(인바운드)($0.01/GB) + 허브 데이터 처리(유럽)($0.02/GB) + Inter-Region 데이터 전송(미국 동부에서 유럽으로) ($0.05/GB) = $0.08/GB|
|5|미국 동부의 표준 vWAN 허브를 통해 스포크 VNet에서 다른 스포크 VNet으로 데이터 전송|배포 시간($0.25/hr) = $0.25/hr|VNet 피어링(아웃바운드 + 인바운드)(2x$0.01/GB) + 허브 데이터 처리($0.02/GB) = $0. 04/GB|
|6|미국 동부의 허브에 연결된 스포크 VNet에서 유럽(다른 지역)의 허브에 연결된 다른 스포크 VNet으로 데이터 전송|배포 시간(2x$0.25/hr) = $0.50/hr|VNet 피어링(아웃바운드 + 인바운드)(2x$0.01/GB) + 허브 데이터 처리(2x$0.02/GB) + Inter-Region 데이터 전송(미국 동부에서 유럽으로) ($0.05/GB) = $0. 11/GB|
|7|유럽 표준 vWAN 허브를 통해 스포크 VNet에서 사용자 VPN(지점 및 사이트간)으로 데이터 전송|배포 시간($0.25/시간) + VPN P2S 배율 단위($0.261/시간) + VPN P2S 연결 단위($0.0125/hr) = $0.524/hr|VNet 피어링(아웃바운드)($0.01/GB) + 표준 아웃바운드 영역 1($0.087/GB) = $0.097/GB|
|7'|유럽 표준 vWAN 허브를 통해 사용자 VPN(지점 및 사이트간)에서 스포크 VNet으로 데이터 전송|배포 시간($0.25/시간) + VPN P2S 배율 단위($0.261/시간) + VPN P2S 연결 단위($0.0125/hr) = $0.524/hr|VNet 피어링(인바운드)($0.01/GB) = $0.01/GB|
|8|싱가포르 허브의 네트워크 가상 어플라이언스 를 통해 SD-WAN 분기에서 동일한 지역의 다른 SD-WAN 분기로 데이터 전송|표준 vWAN 허브 배포 시간($0.25/시간) + NVA 인프라 단위($0.25/시간) = $0.50/시간<br> *추가 타사 라이선스 요금이 적용될 수 있음|표준 아웃바운드 영역 2($0.12/GB) = $0.12/GB|

## <a name="common-topology-scenarios"></a><a name="topologies"></a>일반적인 토폴로지 시나리오

### <a name="microsoft-global-backbone-wan"></a><a name="global"></a>Microsoft 글로벌 백본 WAN

이 시나리오에서는 글로벌 트래픽 연결에 대한 Microsoft 백본 역할을 하는 완전히 메시된 자동 지역 허브를 전역적으로 만듭니다. 일반적인 연결에는 분기(사이트-사이트 VPN 또는 SD-WAN), 원격 사용자(지점 및 사이트 VPN) 및 프라이빗 회로(ExpressRoute)와 같은 엔드포인트가 포함됩니다. 이는 Microsoft 백본을 사용하여 트래픽을 전역적으로 전달합니다.

**시나리오 1 다이어그램: Microsoft 글로벌 백본 WAN**

:::image type="content" source="./media/pricing-concepts/global-backbone.png" alt-text="Microsoft 글로벌 백본 WAN의 다이어그램." lightbox="./media/pricing-concepts/global-backbone.png":::

#### <a name="alternatives"></a>대안

* 또한 보안 vWAN 허브(방화벽 포함)를 각 글로벌 지역에 대한 라우팅 및 보안 요구 사항의 중심점이 되도록 선택할 수도 있습니다.

### <a name="software-defined-wide-area-network-sd-wan"></a><a name="sdwan"></a>SD-WAN(소프트웨어 정의 광역 네트워크)

이 시나리오에서 SD-WAN 기술로 이동하는 저장소는 자동화된 저장소 종료를 위해 vWAN 허브를 사용하여 Azure 및 온-프레미스(데이터 센터)의 리소스에 액세스합니다. 저장소는 VPN 터널을 통해 연결되어 허브를 통해 인터넷을 통해 온-프레미스 데이터 센터로 트래픽을 안전하게 보내거나 Azure의 공유 앱에 액세스합니다.

**시나리오 2 다이어그램: Software-Defined 광역 네트워크(SD-WAN)**

:::image type="content" source="./media/pricing-concepts/sd-wan.png" alt-text="SD-WAN의 다이어그램." lightbox="./media/pricing-concepts/sd-wan.png":::

#### <a name="alternatives"></a>대안

* 허브에서 타사 네트워크 가상 어플라이언스 사용을 선택하고 소매점 및 센터에 연결할 수 있습니다.

* 또한 보안 vWAN 허브(방화벽 포함)를 라우팅 및 보안 요구의 중심점이 되도록 선택할 수도 있습니다.

### <a name="remote-user-connectivity"></a><a name="remote"></a>원격 사용자 연결

이 시나리오에서 원격 사용자 세션은 vWAN 허브에서 종료됩니다. 이러한 세션은 가상 네트워크의 원격 사용자 및/또는 Azure Virtual Desktop 세션일 수 있습니다. 현재 각 허브에서 100,000명의 사용자가 지원됩니다.

다음 다이어그램은 스포크 VNet과 vWAN 허브 간의 터널 간 암호화된 트래픽에 대한 가상 네트워크 연결을 통한 지점 및 사이트 간 VPN을 보여줍니다. 직접 연결을 위해 서로 다른 스포크 VNet 간에 가상 네트워크 피어링 연결을 선택할 수도 있습니다. 예를 들어 공유 VDI와 VDI 스포크 VNet 간을 예로 들 수 있습니다.

**시나리오 3 다이어그램: 원격 사용자 연결**

:::image type="content" source="./media/pricing-concepts/remote.png" alt-text="원격 사용자 연결 다이어그램." lightbox="./media/pricing-concepts/remote.png":::

## <a name="data-flow-calculations"></a>데이터 흐름 계산

> [!IMPORTANT]
> 이 문서에 표시 된 가격은 예를 들어 목적 으로만 제공 되며 변경 될 수 있습니다. 최신 가격은 [가상 WAN 가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조 하세요.
>

### <a name="microsoft-global-backbone-wan-calculation"></a>Microsoft 글로벌 백본 WAN 계산

이 시나리오에서는 아래 다이어그램에 표시 된 것 처럼 vWAN 허브를 통해 글로벌 네트워크를 통과 하는 총 8TB 데이터를 가정 합니다. 이에 대 한 총 데이터 전송 비용은 $600 (아래 다이어그램의 모든 데이터 흐름 비용 합계, Express 경로에 대 한 유료 요금을 가정), 총 허브 비용 (3 개의 배율 단위 + 3 연결 단위 (ER) + 3 허브 배포)이 $1534에 해당 합니다.

**시나리오 1 다이어그램: Microsoft 글로벌 백본 WAN 계산**

:::image type="content" source="./media/pricing-concepts/global-backbone-pricing.png" alt-text="Microsoft 백본 가격 책정의 다이어그램입니다." lightbox="./media/pricing-concepts/global-backbone-pricing.png":::

| 값 | 계산 |
| --- | --- |
|S2S VPN 허브 싱가포르 |(1 개 S2S VPN 배율 단위 ($ 0.361/hr) + 1 연결 단위 ($ 0.05/hr)) x 730 시간 = $300/월|
|Express 경로 허브 미국 동부 |(1 ER 배율 단위 ($ 0.42/hr) + 1 연결 단위 ($ 0.05/hr)) x 730 시간 = $343/월|
|Express 경로 허브 EU|(1 ER 배율 단위 ($ 0.42/hr) + 1 연결 단위 ($ 0.05/hr)) x 730 시간 = $343/월|
|표준 허브 배포 비용 |3 허브 x 730 시간 x $0.25/시간 = $548/월|
|**합계**|**$1534** /월 |

### <a name="software-defined-wide-area-network-sd-wan-calculation"></a>SD (광역 네트워크) 계산 Software-Defined

이 시나리오에서는 미국 동부 지역에서 아래 다이어그램에 표시 된 것 처럼 vWAN 허브를 통과 하는 총 12tb의 데이터를 가정 합니다. 총 데이터 전송 비용은 $434 (아래에 표시 된 모든 데이터 흐름 비용의 합계), 총 허브 비용 (2 개 배율 단위 + 3 연결 단위 (2 개 S2S, 1 일) + 1 허브 배포)이 $863에 포함 됩니다.

**시나리오 2 다이어그램: SD (광역 네트워크) 계산 Software-Defined**

:::image type="content" source="./media/pricing-concepts/sd-wan-pricing.png" alt-text="SD WAN 가격 책정의 다이어그램입니다." lightbox="./media/pricing-concepts/sd-wan-pricing.png":::

| 값 | 계산 |
| --- | --- |
|VPN S2S (분기) |(1 개 S2S VPN 배율 단위 ($ 0.361/hr) + 2 연결 단위 (2x $0.05/hr) x 730 시간 = $337/월|
|Express 경로 허브 (본부) |(1 ER 배율 단위 ($ 0.42/hr) + 1 연결 단위 ($ 0.05/hr) x 730 시간 = $343/월|
|표준 허브 배포 비용|1 허브 x 730 시간 x $0.25/시간 = $183/월|
|**합계**|**$863** /월|

### <a name="remote-user-connectivity-calculation"></a>원격 사용자 연결 계산

이 시나리오에서는 미국 동부 지역에서 네트워크를 통과 하는 총 64TB의 데이터를 가정 합니다. 총 데이터 전송 비용은 $405 (허브 처리 요금, 피어 링, 대역폭, 요금제 ER 데이터 전송 요금 포함)이 고 총 허브 비용 (2 개의 배율 단위 + 2 연결 단위 + 2 허브 배포)은 $708에 해당 합니다.

**시나리오 3 다이어그램: 원격 사용자 연결 계산**
    
:::image type="content" source="./media/pricing-concepts/remote-pricing.png" alt-text="원격 사용자 가격의 다이어그램입니다." lightbox="./media/pricing-concepts/remote-pricing.png":::

| 값 | 계산 |
| --- | --- |
|Express 경로 허브 (본부) |(1 ER 배율 단위 ($ 0.42/hr) + 1 연결 단위 ($ 0.05/hr)) x 730 시간 = $343/월 |
|표준 허브 배포 비용| 2 허브 x 730 시간 x $0.25/시간 = $365/월 |
|**합계** |**$708** /월 |

## <a name="pricing-faq"></a>가격 FAQ

> [!IMPORTANT]
> 이 문서에 표시 된 가격은 예를 들어 목적 으로만 제공 되며 변경 될 수 있습니다. 최신 가격은 [가상 WAN 가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조 하세요.
>

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>배율 단위는 무엇입니까?

**배율 단위** 는 가상 허브의 S2S(사이트 간), P2S(지점 및 사이트 간), ER(ExpressRoute) 집계 용량에 대한 단위를 제공합니다. 예를 들면 다음과 같습니다.

* **1 S2S vpn 배율 단위** 는 가상 허브 비용 $0.361/시간에 500-Mbps vpn 게이트웨이의 총 용량 (복구를 위해 이중 인스턴스를 배포 함)을 의미 합니다.

* **1 ER 배율 단위** 는 가상 허브 비용 $0.42/hr에서 총 2 Gbps ER 게이트웨이를 의미 합니다.

* **5 ER 배율 단위** 는 $0.42 * 5/시간으로 가격이 책정 되는 가상 허브 VNet 내부에 총 10gbps의 ER 게이트웨이를 의미 합니다. ER은 6~10 배율 단위에서 $0.25/시간만큼 증분됩니다.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit">연결 단위는 무엇입니까?</a>

**연결 단위** 는 Azure 게이트웨이에 연결하는 모든 온-프레미스/타사 엔드포인트에 적용됩니다. 사이트 간 VPN의 경우 이 값은 분기를 의미합니다. 사용자 VPN(지점 및 사이트 간)의 경우 이 값은 원격 사용자를 의미합니다. ExpressRoute의 경우 이 값은 ExpressRoute 회로 연결을 의미합니다.<br>예를 들면 다음과 같습니다.

* 가상 허브에서 Azure VPN에 연결하는 단일 분기 연결 비용은 $0.05/시간입니다. 따라서 Azure 가상 허브에 연결 하는 100 분기 연결의 비용은 $0.05 * 100/시간입니다.

* 가상 허브에 연결 하는 2개의 ExpressRoute 회로 연결의 비용은 $0.05*2/시간입니다.

* Azure 가상 허브 P2S 게이트웨이에 연결하는 3개의 원격 사용자 연결의 비용은 $0.03*3/시간입니다.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>데이터 전송 요금은 어떻게 계산됩니까?

* Azure에 진입하는 트래픽에는 요금이 청구되지 않습니다. Azure에서 나가는 트래픽 (VPN, Express 경로 또는 지점 및 사이트 간 사용자 VPN 연결을 통해)은 표준 [Azure 데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/) 또는 express 경로, [express 경로 가격](https://azure.microsoft.com/pricing/details/expressroute/)의 경우에 적용 됩니다.

* 피어 링 요금은 vWAN 허브에 연결 된 VNet에서 데이터를 보내거나 받을 때 적용할 수 있습니다. 자세한 내용은 [가상 네트워크 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network/)을 참조하세요.

* Virtual WAN 허브와 원본 허브가 아닌 다른 지역에 있는 원격 Virtual WAN 허브 또는 VNet 간의 데이터 전송 요금에 대해서는 허브에서 나가는 트래픽에 대한 데이터 전송 요금이 적용됩니다. 예: 미국 동부 허브에서 나가는 트래픽은 미국 서부 허브로 이동하는 데 $0.02/GB의 요금이 청구됩니다. 미국 서부 허브로 들어가는 트래픽에 대해서는 요금이 청구되지 않습니다. 모든 허브 간 트래픽은 지역 간(지역 내/대륙 간) 요금인 [Azure 데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/) 적용 대상입니다. 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>표준 허브 요금과 표준 허브 처리 요금의 차이는 무엇입니까?

Virtual WAN은 다음 두 가지 버전으로 제공됩니다.

* 사용자가 여러 허브를 배포 하 고 VPN 사이트 간 연결을 사용할 수 있는 **기본 가상 WAN** 기본 가상 WAN에는 완벽 하 게 연결 된 허브, Express 경로 연결, 사용자 VPN/지점 및 사이트 간 VPN 연결, VNet 간 전이적 연결, VPN 및 Express 경로 전송 연결, Azure 방화벽과 같은 고급 기능이 없습니다. 기본 가상 WAN에서 허브에 대 한 기본 요금 또는 데이터 처리 요금은 없습니다.

* **표준 가상 WAN** 은 완전히 연결 된 허브, express 경로 연결, 사용자 Vpn/지점 및 사이트 간 vpn 연결, vnet 간 전이적 연결, VPN 및 express 경로 전송 연결, Azure 방화벽 등의 고급 기능을 제공 합니다. 모든 가상 허브 라우팅은 가상 허브에서 여러 서비스를 사용 하도록 설정 하는 라우터에 의해 제공 됩니다. 허브에 대한 기본 요금은 $0.25/시간으로 책정되어 있습니다. 또한 VNet 간 전송 연결에 대한 가상 허브 라우터의 데이터 처리 요금이 청구됩니다. [가격 구성 요소](#pricing)에 표시 된 것 처럼 가상 허브 라우터의 데이터 처리 요금은 동일한 vwan 허브 (시나리오 1, 1 ')를 통해 분기 간 전송 (시나리오 2, 2, 3) 또는 VNet 간 전송에 적용 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

* 최신 가격 책정 [Virtual WAN 가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/)을 참조하세요.

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.


