---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: f5d641a1d017f40081b311e9b9b610b7fcc7c13c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820767"
---
1. 생성한 Virtual WAN을 찾습니다. Virtual WAN 페이지의 **연결** 섹션에서 **허브** 를 선택합니다. **새 Hub** 를 클릭하여 **가상 허브 만들기** 페이지를 엽니다.

   :::image type="content" source="media/virtual-wan-empty-hub/new-hub.jpg" alt-text="새 허브가 선택된 허브 구성 대화 상자를 보여 주는 스크린샷.":::

1. **가상 허브 만들기** 페이지에서 필드를 채웁니다.

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/create-hub.png" alt-text="스크린샷은 값을 입력할 수 있는 기본 탭을 보여줍니다.":::

   * **지역**: 가상 허브를 배포할 지역을 선택합니다.
   * **이름**: 가상 허브에 지정할 이름입니다.
   * **허브 프라이빗 주소 공간**: CIDR 표기법으로 된 허브의 주소 범위입니다.

1. **ExpressRoute 탭** 을 선택합니다. **예** 를 클릭하여 설정을 표시하고 필드를 채웁니다. 게이트웨이 배율 단위에 대한 자세한 내용은 [FAQ](../articles/virtual-wan/virtual-wan-faq.md#what-are-virtual-wan-gateway-scale-units)를 참조하세요.

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/expressroute.png" alt-text="스크린샷은 값을 입력할 수 있는 ExpressRoute 탭을 보여줍니다.":::

1. **검토 + 만들기** 를 선택하여 유효한지 확인합니다.
1. **만들기** 를 선택하여 ExpressRoute 게이트웨이가 있는 허브를 만듭니다. 허브를 완료하는 데 약 30분이 걸릴 수 있습니다. 30분 후에 **새로 고침** 을 선택하여 **허브** 페이지에서 허브를 봅니다. **리소스로 이동** 을 선택하여 리소스로 이동합니다.