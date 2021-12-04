---
title: Always On VPN 터널 구성
titleSuffix: Azure VPN Gateway
description: Windows 10 Always On에서 게이트웨이를 사용하여 Azure에 대한 영구적 디바이스 터널을 설정하고 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 8df6f2d495b18bb825f7cb5230d646fa68ef9a62
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133540556"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Always On VPN 디바이스 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

이 문서는 Always On VPN 디바이스 터널을 구성하는 데 도움이 됩니다. 사용자 터널을 구성하는 방법에 대한 자세한 내용은 [Always On VPN 사용자 터널 구성을 참조하세요.](vpn-gateway-howto-always-on-user-tunnel.md)

## <a name="configure-the-gateway"></a>게이트웨이 구성

[지점 및 사이트 간 VPN 연결 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 문서를 참조하여 IKEv2 및 인증서 기반 인증을 사용하도록 VPN 게이트웨이를 구성합니다.

## <a name="configure-the-device-tunnel"></a>디바이스 터널 구성

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>프로필을 제거하려면

프로필을 제거하려면 다음 명령을 실행합니다.

![Remove-VpnConnection -Name MachineCertTest 명령을 실행하는 PowerShell 창 스크린샷](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>다음 단계

문제 해결은 [Azure 지점 및 사이트 간 연결 문제](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)를 참조하세요.
