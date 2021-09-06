---
author: cherylmc
ms.author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 21bda32ddb1c87bb07b6679499e648e0b2f7a809
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734681"
---
1. 사용자가 만든 가상 WAN으로 이동합니다. 

1. 왼쪽의 메뉴에서 **사용자 VPN 구성** 을 선택합니다.

1. **사용자 VPN 구성** 페이지에서 **+사용자 VPN config 만들기** 를 선택합니다.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="사용자 VPN 구성 페이지의 스크린샷.":::

1. **새 사용자 VPN 구성 만들기** 페이지 **기본 사항** 탭의 **인스턴스 세부 정보** 에서 VPN 구성에 할당할 **이름** 을 입력합니다. 

   :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="사용자 지정으로 전환한 IPsec 스위치의 스크린샷.":::

1. **터널 유형** 에 대해 드롭다운에서 원하는 터널 유형을 선택합니다. 터널 유형 옵션에는 **IKEv2 VPN, OpenVPN** 및 **OpenVpn 및 IKEv2** 가 있습니다. 각 터널 유형에는 서로 다른 필수 설정이 있습니다.

   **요구 사항 및 매개 변수**:

     **IKEv2 VPN**

     * **요구 사항:** **IKEv2** 터널 유형을 선택하면 인증 방법을 선택하도록 지시하는 메시지가 표시됩니다. IKEv2의 경우 인증 방법을 하나만 지정할 수 있습니다. Azure 인증서, Azure Active Directory 또는 RADIUS 기반 인증을 선택할 수 있습니다.

     * **IPSec 사용자 지정 매개 변수:** IKE 1단계 및 IKE 2단계의 매개 변수를 사용자 정의하려면 IPsec 스위치를 **사용자 정의** 로 전환하고 매개 변수 값을 선택합니다. 사용자 지정 가능한 매개 변수에 대한 자세한 내용은 [사용자 지정 IPsec](../articles/virtual-wan/point-to-site-ipsec.md) 문서를 참조하세요.

     **OpenVPN**

     * **요구 사항:** **OpenVPN** 터널 유형을 선택하면 인증 메커니즘을 선택하도록 지시하는 메시지가 표시됩니다. OpenVPN을 터널 유형으로 선택한 경우 여러 인증 방법을 지정할 수 있습니다. Azure 인증서, Azure Active Directory 또는 RADIUS 기반 인증의 하위 집합을 선택할 수 있습니다. RADIUS 기반 인증의 경우 보조 RADIUS 서버 IP 주소와 서버 비밀을 제공할 수 있습니다.

1. 사용하려는 **인증** 방법을 구성합니다. 각 인증 방법은 별도의 탭: **Azure 인증서**, **RADIUS 인증** 및 **Azure Active Directory** 에 있습니다. 일부 인증 방법은 특정 터널 유형에서만 사용할 수 있습니다.

   구성하려는 인증 방법 탭에서 **예** 를 선택하여 사용 가능한 구성 설정을 표시합니다.

   :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-no.png" alt-text="아니요 화면의 스크린샷 - 예가 강조 표시되어 있습니다.":::

   * **예제 - 인증서 인증**

      :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-authentication.png" alt-text="예가 선택된 스크린샷":::

   * **예제 - RADIUS 인증**

      :::image type="content" source="media/virtual-wan-p2s-configuration/radius-authentication.png" alt-text="RADIUS 인증 페이지의 스크린샷":::

   * **예제 - Azure Active Directory 인증**

      :::image type="content" source="media/virtual-wan-p2s-configuration/azure-active-directory.png" alt-text="Azure Active Directory 인증 페이지":::

1. 설정 구성을 마치면 페이지 아래쪽의 **검토 + 만들기** 를 클릭합니다.

1. **만들기** 를 클릭하여 사용자 VPN 구성을 만듭니다.
