---
title: Azure 네트워크 간 연결
description: 이 페이지에서는 Azure 네트워킹 기능을 기반으로 하는 네트워크 간 연결 및 솔루션의 애플리케이션 시나리오를 설명합니다.
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 9e991e31d93e28373df55ae50a3ef7b0a666b879
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353975"
---
# <a name="cross-network-connectivity"></a>네트워크 간 연결

Fabrikam Inc.는 미국 동부에 있는 대규모의 실제 Azure 배포 기업입니다. Fabrikam은 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다. 마찬가지로, Contoso Ltd.는 미국 서부에 Azure 배포가 있는 기업입니다. Contoso는 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다.  

Fabrikam Inc.가 Contoso ltd.를 인수합니다. 합병 후에 Fabrikam은 네트워크를 상호 연결하려고 합니다. 다음 그림은 이 시나리오를 보여 줍니다.

![애플리케이션 시나리오](./media/cross-network-connectivity/premergerscenario.png)

위 그림 중간 부분의 점선 화살표는 필요한 네트워크 상호 연결을 나타냅니다. 특히 다음 세 가지 유형의 교차 연결이 필요합니다. 1) Fabrikam 및 Contoso VNet 교차 연결, 2) 지역 간 온-프레미스 및 VNet 교차 연결(즉, Fabrikam 온-프레미스 네트워크를 Contoso VNet에 연결하고 Contoso 온-프레미스 네트워크를 Fabrikam VNet에 연결), 3) Fabrikam 및 Contoso 온-프레미스 네트워크 교차 연결 

다음 표에서는 합병 이전 Contoso Ltd. ExpressRoute의 프라이빗 피어링 경로 테이블을 보여 줍니다.

![합병 이전 Contoso ExpressRoute 경로 테이블](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

다음 표에서는 합병 전에 Contoso 구독에 있던 VM의 유효 경로를 보여 줍니다. 테이블을 기준으로 VNet의 VM이 기본 항목과 별도로 VNet 주소 공간 및 Contoso 온-프레미스 네트워크를 인식합니다.

![합병 이전 Contoso VM 경로](./media/cross-network-connectivity/contosovm-routes-premerger.png)

다음 표에서는 합병 이전 Fabrikam Inc. ExpressRoute의 프라이빗 피어링 경로 테이블을 보여 줍니다.

![합병 이전 Fabrikam ExpressRoute 경로 테이블](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

다음 표에서는 합병 전에 Fabrikam 구독에 있던 VM의 유효 경로를 보여 줍니다. 테이블을 기준으로 VNet의 VM이 기본 항목과 별도로 VNet 주소 공간 및 Fabrikam 온-프레미스 네트워크를 인식합니다.

![합병 이전 Fabrikam VM 경로](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

이 문서에서는 다음 Azure 네트워크 기능을 사용하여 원하는 교차 연결을 구현하는 방법을 단계별로 살펴봅시다.

* [가상 네트워크 피어링][Virtual network peering] 
* [가상 네트워크 ExpressRoute 연결][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>교차 연결 VNet

VNet 피어링(가상 네트워크 피어링)은 두 개의 가상 네트워크를 연결할 때 가장 최적의 네트워크 성능을 제공합니다. VNet 피어링은 동일한 Azure 지역(일반적으로 VNet 피어링 이라고 함)과 서로 다른 두 Azure 지역(일반적으로 글로벌 VNet 피어링이라고 함)에서 두 VNet의 피어링을 지원합니다. 

Contoso 및 Fabrikam Azure 구독의 VNet 간에 글로벌 VNet 피어링을 구성해 봅시다. 두 가상 네트워크 간에 가상 네트워크 피어링을 만드는 방법에 대한 자세한 내용은 [가상 네트워크 피어링 만들기][Configure VNet peering] 문서를 참조하세요.

다음 그림은 글로벌 VNet 피어링을 구성한 후의 네트워크 아키텍처를 보여 줍니다.

![VNet 피어링 이후 아키텍처](./media/cross-network-connectivity/vnet-peering.png )

다음 표에서는 Contoso 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 마지막 항목에 주의합니다. 이 항목은 가상 네트워크 교차 연결의 결과입니다.

![VNet 피어링 이후 Contoso VM 경로](./media/cross-network-connectivity/contosovm-routes-peering.png)

다음 표에서는 Fabrikam 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 마지막 항목에 주의합니다. 이 항목은 가상 네트워크 교차 연결의 결과입니다.

![VNet 피어링 이후 Fabrikam VM 경로](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

VNet 피어링은 두 개의 가상 네트워크를 직접 연결합니다(위의 두 테이블에서 *VNetGlobalPeering* 항목에는 다음 홉이 없음).

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>온-프레미스 네트워크에 VNet 교차 연결

ExpressRoute 회로를 여러 가상 네트워크에 연결할 수 있습니다. ExpressRoute 회로에 연결할 수 있는 최대 가상 네트워크 수는 [구독 및 서비스 한도][Subscription limits]를 참조하세요. 

Fabrikam ExpressRoute 회로를 Contoso 구독 VNet에 연결하고, 마찬가지로 Contoso ExpressRoute 회로를 Fabrikam 구독 VNet에 연결하여 가상 네트워크와 온-프레미스 네트워크 간 교차 연결을 사용하도록 설정합시다. 다른 구독의 ExpressRoute 회로에 가상 네트워크를 연결하려면 권한 부여를 만들고 사용해야 합니다.  [ExpressRoute 회로에 가상 네트워크 연결][Connect-ER-VNet] 문서를 참조하세요.

다음 그림은 가상 네트워크에 대한 ExpressRoute 교차 연결을 구성한 후의 네트워크 아키텍처를 보여 줍니다.

![ExpressRoute 교차 연결 이후 아키텍처](./media/cross-network-connectivity/exr-x-connect.png)

다음 표에서는 ExpressRoute를 통해 가상 네트워크를 온-프레미스 네트워크에 교차 연결한 후 Contoso ltd. ExpressRoute의 프라이빗 피어링 경로 테이블을 보여 줍니다. 경로 테이블에 두 가상 네트워크에 속하는 경로가 있는지 확인합니다.

![ExR과 VNet을 교차 연결한 후의 Contoso ExpressRoute 경로 테이블](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

다음 표에서는 ExpressRoute를 통해 가상 네트워크를 온-프레미스 네트워크에 교차 연결한 후 Fabrikam Inc. ExpressRoute의 프라이빗 피어링 경로 테이블을 보여 줍니다. 경로 테이블에 두 가상 네트워크에 속하는 경로가 있는지 확인합니다.

![ExR과 VNet을 교차 연결한 후의 Fabrikam ExpressRoute 경로 테이블](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

다음 표에서는 Contoso 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 *가상 네트워크 게이트웨이* 항목에 주의합니다. VM에서 두 온-프레미스 네트워크의 경로가 모두 표시됩니다.

![ExR과 VNet을 교차 연결한 후의 Contoso VM 경로](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

다음 표에서는 Fabrikam 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 *가상 네트워크 게이트웨이* 항목에 주의합니다. VM에서 두 온-프레미스 네트워크의 경로가 모두 표시됩니다.

![ExR과 VNet을 교차 연결한 후의 Fabrikam VM 경로](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Fabrikam 및/또는 Contoso 구독에 해당 허브 VNet에 대한 스포크 VNet도 있을 수 있습니다(이 문서의 아키텍처 다이어그램에는 허브 및 스포크 설계가 표시되지 않음). ExpressRoute에 대한 허브 VNet 게이트웨이 간 교차 연결을 사용하면 동부 및 서부 허브와 스포크 간 통신이 가능합니다.
>

## <a name="cross-connecting-on-premises-networks"></a>온-프레미스 네트워크 교차 연결

ExpressRoute Global Reach는 서로 다른 ExpressRoute 회로에 연결된 온-프레미스 네트워크 간 연결을 제공합니다. Contoso 및 Fabrikam ExpressRoute 회로 간에 Global Reach를 구성해 봅시다. ExpressRoute 회로가 서로 다른 구독에 있으므로 권한 부여를 만들고 사용해야 합니다. 단계별 지침은 [ExpressRoute Global Reach 구성][Configure Global Reach] 문서를 참조하세요.

다음 그림은 Global Reach 구성 후의 네트워크 아키텍처를 보여 줍니다.

![Global Reach 구성 이후 아키텍처](./media/cross-network-connectivity/globalreach.png)

다음 표에서는 Global Reach 구성 이후 Contoso Ltd. ExpressRoute의 프라이빗 피어링 경로 테이블을 보여 줍니다. 경로 테이블에 온-프레미스 네트워크에 속하는 경로가 있는지 확인합니다. 

![Global Reach 이후 Contoso ExpressRoute 경로 테이블](./media/cross-network-connectivity/contosoexr-rt-gr.png)

다음 표에서는 Global Reach 구성 이후 Fabrikam Inc. ExpressRoute의 프라이빗 피어링 경로 테이블을 보여 줍니다. 경로 테이블에 온-프레미스 네트워크에 속하는 경로가 있는지 확인합니다.

![Global Reach 이후 Fabrikam ExpressRoute 경로 테이블]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>다음 단계

VNet 및 VNet 피어링에 대한 추가 질문이 있으면 [가상 네트워크 FAQ][VNet-FAQ]를 참조하세요. ExpressRoute 및 가상 네트워크 연결에 대한 추가 질문이 있으면 [ExpressRoute FAQ][ER-FAQ]를 참조하세요.

Global Reach는 국가/지역별로 국가/지역에서 출시됩니다. 원하는 국가/지역에서 Global Reach를 사용할 수 있는지 확인하려면 [ExpressRoute Global Reach][Global Reach]를 참조하세요.

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: ../virtual-network/virtual-networks-faq.md