---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure VPN Gateway
description: VPN 게이트웨이에 대한 Always On VPN 사용자 터널을 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: bb86840df8e7ae1f6149884b2995dc2fe1e4f2b9
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133539613"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN 사용자 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

이 문서는 Always On VPN 사용자 터널을 구성하는 데 도움이 됩니다. 디바이스 터널을 구성하는 방법에 대한 자세한 내용은 [Always On VPN 디바이스 터널 구성을 참조하세요.](vpn-gateway-howto-always-on-device-tunnel.md)

## <a name="configure-the-gateway"></a>게이트웨이 구성

 [지점 및 사이트 간 VPN 연결 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 문서의 지침을 적용하여 IKEv2 및 인증서 기반 인증을 사용하도록 VPN Gateway를 구성합니다.

## <a name="configure-a-user-tunnel"></a>사용자 터널 구성

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>프로필 제거 방법

프로필을 제거하려면 다음 단계를 사용합니다.

1. 다음 명령 실행:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 연결을 끊고 **자동으로 연결** 확인란의 선택을 취소합니다.

   ![정리](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>다음 단계

발생할 수 있는 연결 문제를 해결하려면 [Azure 지점 및 사이트 간 연결 문제](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)를 참조하세요.
