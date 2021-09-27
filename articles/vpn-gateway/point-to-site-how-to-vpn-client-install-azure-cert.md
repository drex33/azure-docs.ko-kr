---
title: 지점 및 사이트 간 클라이언트 인증서 설치
titleSuffix: Azure VPN Gateway
description: P2S 인증서 인증용 클라이언트 인증서를 설치하는 방법에 대해 알아봅니다(Windows, Mac, Linux).
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2021
ms.author: cherylmc
ms.openlocfilehash: 917f60440d98924e5339f29fb99587eacf40b415
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766258"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>P2S 인증서 인증 연결용 클라이언트 인증서 설치

P2S VPN 게이트웨이가 인증서 인증을 요구하도록 구성된 경우 각 클라이언트 컴퓨터에는 로컬로 설치된 클라이언트 인증서가 있어야 합니다. 이 문서는 클라이언트 컴퓨터에 로컬로 클라이언트 인증서를 설치하는 데 도움이 됩니다. [Intune을](/mem/intune/configuration/vpn-settings-configure) 사용하여 특정 VPN 클라이언트 프로필 및 인증서를 설치할 수도 있습니다.

자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하려면 다음 문서 중 하나를 참조하세요.

* [인증서 생성 - PowerShell](vpn-gateway-certificates-point-to-site.md)
* [인증서 생성 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [인증서 생성 - Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN 클라이언트는 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에서만 지원됩니다. 클래식 배포 모델에서는 지원되지 않습니다.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux 클라이언트 인증서는 클라이언트 구성의 일부로 클라이언트에 설치됩니다. [클라이언트 구성 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) 지침을 참조하세요.

## <a name="next-steps"></a>다음 단계

[VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)에 대한 지점 및 사이트 간 구성 단계를 계속합니다.