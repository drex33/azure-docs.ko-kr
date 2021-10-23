---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: ded79a3f13f2f25cf348d2c767f775a18116e85f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288034"
---
1. **가상 허브 만들기** 페이지에서 **사이트 간** 을 클릭하여 **사이트 간** 탭을 엽니다.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/hub-site-to-site.png" alt-text="스크린샷은 사이트 간 선택된 가상 허브 만들기 창을 보여줍니다.":::

1. **사이트 간** 탭에서 다음 필드를 완료합니다.

   * **예** 를 선택하여 사이트 간 VPN을 만듭니다.
   * **AS 번호**: AS 번호 필드는 편집할 수 없습니다.
   * **게이트웨이 배율 단위**: 드롭다운에서 **게이트웨이 배율 단위** 값을 선택합니다. 배율 단위를 사용하여 사이트를 연결할 가상 허브에서 만들 VPN Gateway의 집계 처리량을 선택할 수 있습니다. 

     1 배율 단위 = 500Mbps를 선택하는 경우 중복성을 위해 두 인스턴스가 생성되고 각각은 최대 500Mbps의 처리량을 갖게 됩니다. 예를 들어, 각각 10Mbps를 수행하는 5개의 분기가 있는 경우 헤드 끝에서 50Mbps로 집계되어야 합니다. 허브에 대한 분기 수를 지원하는 데 필요한 용량을 평가한 후 Azure VPN Gateway의 집계 용량 계획을 수립해야 합니다.
   * **라우팅 기본 설정**: Azure 라우팅 기본 설정을 사용하면 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택할 수 있습니다. Microsoft 네트워크를 통해 또는 ISP 네트워크(공용 인터넷)를 통해 트래픽을 라우팅하도록 선택할 수 있습니다. 이러한 옵션을 각각 콜드 포테이토 라우팅 및 핫 포테이토 라우팅이라고도 합니다. 

     Virtual WAN의 공용 IP 주소는 선택한 라우팅 옵션을 기반으로 서비스에서 할당합니다. Microsoft 네트워크 또는 ISP를 통한 라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정](../articles/virtual-network/ip-services/routing-preference-overview.md) 문서를 참조하세요.
1. **검토 + 만들기** 를 선택하여 유효한지 확인합니다.
1. **만들기** 를 선택하여 허브 및 게이트웨이를 만듭니다. 이 작업은 최대 30분까지 걸릴 수 있습니다. 30분 후에 **새로 고침** 을 선택하여 **허브** 페이지에서 허브를 봅니다. **리소스로 이동** 을 선택하여 리소스로 이동합니다.