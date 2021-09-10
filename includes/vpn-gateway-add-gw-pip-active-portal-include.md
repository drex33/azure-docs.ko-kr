---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/16/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ff30bf22f0f435db8ae0888440bb0ea26948bef3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481649"
---
4. **공용 IP 주소** 에 대한 값을 지정합니다. 이러한 설정은 VPN 게이트웨이에 연결되는 공용 IP 주소 개체를 지정합니다. VPN Gateway가 생성될 때 공용 IP 주소가 이 개체에 동적으로 할당됩니다. [영역 중복](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)이 아닌 게이트웨이의 경우 게이트웨이를 삭제하고 다시 만드는 경우에만 공용 IP 주소가 변경됩니다. VPN Gateway의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 시에는 변경되지 않습니다.

   :::image type="content" source="./media/vpn-gateway-add-gw-pip-active-portal/pip-details.png" alt-text="공용 IP 주소 필드의 스크린샷":::

     * **공용 IP 주소**: **새로 만들기** 를 선택한 상태로 둡니다.
     * **공용 IP 주소 이름**: 텍스트 상자에서 공용 IP 주소 인스턴스의 이름을 입력합니다.
     * **할당**: VPN 게이트웨이는 '동적'만 지원합니다.
     * **활성-활성 모드 사용**: **사용** 을 선택합니다.
     * **두 번째 공용 IP 주소**: **새로 만들기** 를 선택합니다.
     * **공용 IP 주소 이름**: 두번째 공용 IP 주소 이름입니다.
     * 구성에서 이 설정이 명시적으로 필요한 경우를 제외하고 **BGP 구성** 을 **사용 안 함** 상태로 둡니다. 이 설정이 필요한 경우 기본 ASN은 65515이며 변경 가능합니다.
5. **검토 + 만들기** 를 선택하여 유효성 검사를 실행합니다.
6. 유효성 검사를 통과하면 **만들기** 를 선택하여 VPN 게이트웨이를 배포합니다.