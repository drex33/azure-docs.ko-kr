---
title: '지점 및 사이트 간 인증서 생성 및 내보내기: Linux: CLI'
description: Linux(strongSwan) CLI를 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성하는 방법을 알아봅니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: alzam
ms.openlocfilehash: e26c73aba047c5dfb1c1169ec2d100519c87f45f
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108291247"
---
# <a name="generate-and-export-certificates"></a>인증서 생성 및 내보내기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 이 문서에서는 Linux CLI 및 strongSwan을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 다른 인증서 지침을 찾는 경우 [Powershell](vpn-gateway-certificates-point-to-site.md) 또는 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 문서를 참조하세요. CLI 대신 GUI를 사용하여 strongSwan을 설치하는 방법에 대한 자세한 내용은 [클라이언트 구성](point-to-site-vpn-client-configuration-azure-cert.md#install) 문서의 단계를 참조하세요.

## <a name="install-strongswan"></a>strongSwan 설치

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>인증서 생성 및 내보내기

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>다음 단계

[VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)에 대한 지점 및 사이트 간 구성을 계속합니다.
