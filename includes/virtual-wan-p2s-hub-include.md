---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: b5e80ecb8f5d0896abb4be61d60d2fbdbea77fa8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734607"
---
1. **가상 WAN** 페이지의 왼쪽 창에서 **허브** 를 선택합니다. **허브** 페이지에서 **+새 허브** 를 선택합니다.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="새 허브의 스크린샷.":::

1. **가상 허브 만들기** 페이지에서 **기본 사항** 탭을 봅니다.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="가상 허브 만들기의 스크린샷.":::

1. **기본 사항** 탭에서 다음 설정을 구성합니다.

   * **지역** - 가상 허브를 배포할 지역을 선택합니다.
   * **이름** - 가상 허브를 호출할 이름을 입력합니다.
   * **허브 프라이빗 주소 공간** - CIDR 표기법으로 된 허브의 주소 범위입니다.

1. **지점 대 사이트** 탭을 클릭하여 지점 대 사이트에 대한 구성 페이지를 엽니다. 지점 대 사이트 설정을 보려면 **예** 를 클릭합니다.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="지점 및 사이트 간이 선택된 가상 허브 구성의 스크린샷.":::

1. 다음 설정을 구성합니다.

   * **게이트웨이 배율 단위** - 사용자 VPN 게이트웨이의 집계 용량을 나타냅니다. 40 이상의 게이트웨이 배율 단위를 선택하는 경우 클라이언트 주소 풀을 적절하게 계획합니다. 이 설정이 클라이언트 주소 풀에 영향을 주는 방법에 대한 자세한 내용은 [클라이언트 주소 풀 정보](../articles/virtual-wan/about-client-address-pools.md)를 참조하세요.
   * **지점 대 사이트 구성** - 이전 단계에서 만든 사용자 VPN 구성을 선택합니다.
   * **라우팅 기본 설정** - Azure 라우팅 기본 설정을 사용하면 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택할 수 있습니다. Microsoft 네트워크를 통해 또는 ISP 네트워크(공용 인터넷)를 통해 트래픽을 라우팅하도록 선택할 수 있습니다. 이러한 옵션을 각각 콜드 포테이토 라우팅 및 핫 포테이토 라우팅이라고도 합니다. Virtual WAN의 공용 IP 주소는 선택한 라우팅 옵션을 기반으로 서비스에서 할당합니다. Microsoft 네트워크 또는 ISP를 통한 라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정](../articles/virtual-network/routing-preference-overview.md) 문서를 참조하세요.
   * **클라이언트 주소 풀** - IP 주소가 VPN 클라이언트에 자동으로 할당되는 주소 풀입니다. 자세한 내용은 [클라이언트 주소 풀 정보](../articles/virtual-wan/about-client-address-pools.md)를 참조하세요.
   * **사용자 지정 DNS 서버** - 클라이언트에서 사용하는 DNS 서버의 IP 주소입니다. 최대 5개를 지정할 수 있습니다.

1. **검토 + 만들기** 를 선택하여 설정의 유효성을 검사합니다.

1. 유효성 검사를 통과하면 **만들기** 를 선택합니다. 허브 만들기를 완료하는 데 30분 이상 걸릴 수 있습니다.