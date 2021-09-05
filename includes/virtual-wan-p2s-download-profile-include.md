---
ms.author: cherylmc
author: cherylmc
ms.date: 07/29/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: dc99ee5068819b67338ae84dd2fef561105d8351
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734694"
---
1. **가상 WAN** 에 대한 페이지에서 **사용자 VPN 구성** 을 선택합니다.
1. **사용자 VPN 구성** 페이지에서 구성을 선택한 다음, **가상 WAN 사용자 VPN 프로필 다운로드** 를 선택합니다.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/download.png" alt-text="가상 WAN 사용자 VPN 프로필 다운로드의 스크린샷.":::

   * WAN 수준 구성을 다운로드하면 기본 제공 Traffic Manager 기반 사용자 VPN 프로필이 제공됩니다. 
   
   * 글로벌 프로필 및 허브 기반 프로필에 대한 자세한 내용은 [Hub 프로필](../articles/virtual-wan/global-hub-profile.md)을 참조하세요. 장애 조치 시나리오는 글로벌 프로필을 사용하여 간소화됩니다.

   * 어떤 이유로 허브를 사용할 수 없는 경우 서비스에서 제공하는 기본 제공 트래픽 관리를 사용하면 지점 및 사이트 간 사용자를 위해 Azure 리소스에 연결(다른 허브를 통해)할 수 있습니다. 언제든지 허브로 이동하여 허브별 VPN 구성을 다운로드할 수 있습니다. **사용자 VPN(지점 및 사이트 간)** 에서 가상 허브 **사용자 VPN** 프로필을 다운로드합니다.
1. **가상 WAN 사용자 VPN 프로필 다운로드** 페이지에서 **인증 유형** 을 선택한 다음, **프로필 생성 및 다운로드** 를 클릭합니다. 클라이언트 구성 설정이 포함된 프로필 패키지(zip 파일)가 생성되고 컴퓨터에 다운로드됩니다.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/generate.png" alt-text="프로필 생성 및 다운로드의 스크린샷.":::

