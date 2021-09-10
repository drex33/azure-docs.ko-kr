---
ms.author: cherylmc
author: cherylmc
ms.date: 08/18/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 53c6980ea6e5d868a842b1e14031f27ff51f655a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638272"
---
1. **가상 HUB -> VPN(사이트 간)** 으로 이동합니다.

1. 필터를 지우고 사이트를 보려면 **허브 연결: 이 허브에 연결됨** 을 클릭해야 할 수도 있습니다.

1. 연결하려는 사이트의 확인란을 선택하고 **VPN 사이트 연결** 을 클릭합니다.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect-site.png" alt-text="사이트 연결을 보여주는 스크린샷":::

1. **사이트 연결** 페이지에서 필요한 설정을 구성합니다.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="스크린샷은 미리 공유한 키 및 관련 설정에 대해 준비된 Virtual HUB의 연결된 사이트 창을 보여줍니다.":::

   * **미리 공유한 키(PSK)** : VPN 디바이스에서 사용하는 미리 공유한 키를 입력합니다. 키를 입력하지 않으면 Azure에서 자동으로 키를 생성합니다. 그런 다음, VPN 디바이스를 구성할 때 해당 키를 사용합니다.
   * **프로토콜 및 IPsec**: 프로토콜(IKEv2) 및 IPsec(기본값)에 대한 기본 설정을 그대로 두거나 사용자 지정 설정을 구성할 수 있습니다. 자세한 내용은 [기본/사용자 지정 IPsec](../articles/virtual-wan/virtual-wan-ipsec.md)을 참조하세요.
   * **기본 경로 전파**: 기본 경로를 전파하려는 경우에만 이 설정을 **사용** 으로 변경합니다. 그 밖의 경우에는 **사용 안 함** 으로 둡니다. 나중에 항상 이 설정을 수정할 수 있습니다. 
   
     **사용** 옵션을 설정하면 가상 허브가 학습한 기본 경로를 이 연결에 전파합니다. 허브에 방화벽을 배포하여 Virtual WAN 허브에서 기본 경로를 이미 학습한 경우나 연결된 다른 사이트에서 강제로 터널링을 사용한 경우에만 이 플래그가 연결에 기본 경로 전파를 사용하도록 설정합니다. 기본 경로는 Virtual WAN 허브에서 시작되지 않습니다. 
   * **정책 기반 트래픽 선택기 사용**: 이 설정을 사용하는 디바이스에 대한 연결을 구성하지 않은 한 이 설정은 **사용 안 함** 으로 둡니다.

1. 페이지 맨 아래에 있는 **Connect** 를 클릭합니다.

1. **연결** 을 클릭하면 연결 상태가 **업데이트 중** 으로 표시됩니다. 업데이트 완료 후 사이트에 연결 및 연결 상태가 표시됩니다.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png" alt-text="스크린샷은 사이트 간 연결 및 연결 상태를 보여줍니다." lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png":::

   **연결 프로비전 상태:** VPN 사이트를 Azure 허브의 VPN 게이트웨이에 연결하는 연결에 대한 Azure 리소스의 상태입니다. 이 제어 영역 작업이 성공적이면 Azure VPN 게이트웨이와 온-프레미스 VPN 디바이스가 연결 설정을 진행합니다.

   **연결 상태:** 허브에 있는 Azure의 VPN 게이트웨이와 VPN 사이트 사이의 실제 연결(데이터 경로) 상태입니다. 업데이트를 완료한 후에는 다음 상태를 표시할 수 있습니다.

    * **알 수 없음**: 이 상태는 대개 백엔드 시스템이 다른 상태로 전환 작업 중인 경우 표시됩니다.
    * **연결 중**: Azure VPN 게이트웨이가 실제 온-프레미스 VPN 사이트에 연결하는 중입니다.
    * **연결됨**: VPN 게이트웨이와 온-프레미스 VPN 사이트 사이에 연결이 설정되었습니다.
   * **연결 안 됨**: 연결되지 않았습니다.
    * **연결 끊김**: 이 상태는 어떤 이유로(온-프레미스 또는 Azure에서) 연결이 끊어진 경우 표시됩니다.
1. 사이트를 변경하려면 사이트를 선택한 다음, **...** 바로 가기 메뉴를 클릭합니다.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/edit.png" alt-text="편집, 삭제 및 다운로드를 보여주는 스크린샷" lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/expand/edit.png":::

   이 페이지에서는 다음 작업을 수행할 수 있습니다. 

   * VPN 연결을 편집하거나 삭제할 수 있습니다.
   * 이 허브에 대한 VPN 연결을 삭제합니다.
   * Azure 사이트에 대한 세부 정보는 분기 관련 구성을 다운로드하세요. 허브에 있는 모든 연결된 사이트와 관련된 구성 파일을 다운로드하려면, 이 대신에 페이지 맨 위에 있는 메뉴에서 **VPN 구성 다운로드** 를 선택합니다.
