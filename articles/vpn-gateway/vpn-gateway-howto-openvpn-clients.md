---
title: P2S VPN 게이트웨이용 OpenVPN 클라이언트를 구성하는 방법
titleSuffix: Azure VPN Gateway
description: Azure VPN Gateway용 OpenVPN 클라이언트를 구성하는 방법을 알아봅니다. 이 문서는 Windows, Linux, iOS 및 Mac 클라이언트를 구성하는 데 도움을 줍니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/27/2021
ms.author: cherylmc
ms.openlocfilehash: 8c9ddff536c74182e1c13d51dde2900f07fb7470
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566474"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Azure VPN Gateway에 대해 OpenVPN 클라이언트 구성

이 문서는 **OpenVPN &reg; 프로토콜** 클라이언트를 구성하는 데 도움이 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

VPN 게이트웨이에 대해 OpenVPN을 구성하는 단계를 완료했는지 확인합니다. 자세한 내용은 [Azure VPN Gateway에 대해 OpenVPN 구성](vpn-gateway-howto-openvpn.md)을 참조하세요.

## <a name="vpn-client-configuration-files"></a>VPN 클라이언트 구성 파일

Portal 또는 PowerShell를 사용하여 VPN 클라이언트 구성 파일을 생성하고 다운로드할 수 있습니다. 두 메서드 모두 동일한 zip 파일을 반환합니다. 파일의 압축을 풀고 OpenVPN 폴더를 확인합니다.

:::image type="content" source="./media/howto-openvpn-clients/download.png" alt-text="다운로드한 VPN 클라이언트가 강조 표시된 스크린샷" :::

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>다음 단계

VPN 클라이언트가 다른 VNet의 리소스에 액세스할 수 있게 하려면 [VNet 간](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 문서의 지침에 따라 vnet 간 연결을 설정합니다. 게이트웨이 및 연결에서 BGP를 사용하도록 설정해야 합니다. 그렇지 않으면 트래픽이 흐르지 않습니다.

**"OpenVPN"은 OpenVPN Inc.의 상표입니다.**
