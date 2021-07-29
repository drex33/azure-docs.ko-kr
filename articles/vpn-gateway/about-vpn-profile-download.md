---
title: 지점 및 사이트 간 VPN 클라이언트 프로필 정보
titleSuffix: Azure VPN Gateway
description: P2S VPN 클라이언트 프로필 파일에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d26f65dfa8419b3c07825774423dec4bd5557a05
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227815"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>P2S VPN 클라이언트 프로필 파일 사용

프로필 파일에는 VPN 연결을 구성하는 데 필요한 정보가 포함되어 있습니다. 이 문서는 VPN 클라이언트 프로필에 필요한 정보를 얻고 이해하는 데 도움이 됩니다.

## <a name="generate-and-download-profile"></a>프로필 생성 및 다운로드

PowerShell을 사용하거나 Azure Portal을 사용하여 클라이언트 구성 파일을 생성할 수 있습니다. 두 메서드 모두 동일한 zip 파일을 반환합니다.

### <a name="portal"></a>포털

1. Azure Portal에서 연결하려는 가상 네트워크의 가상 네트워크 게이트웨이로 이동합니다.
1. 가상 네트워크 게이트웨이 페이지에서 **지점 및 사이트 간 구성** 을 선택합니다.
1. 지점 및 사이트 간 구성 페이지의 맨 위에서 **VPN 클라이언트 다운로드** 를 선택합니다. 클라이언트 구성 패키지를 생성하는 데 몇 분이 소요됩니다.
1. 클라이언트 구성 zip 파일을 사용할 수 있으면 브라우저에 표시됩니다. 게이트웨이와 동일한 이름이 지정됩니다. 파일의 압축을 풀어 폴더를 확인합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 생성하려면 다음 예제를 사용할 수 있습니다.

1. VPN 클라이언트 구성 파일을 생성하는 경우 '-AuthenticationMethod' 값은 'EapTls'입니다. 다음 명령을 사용하여 VPN 클라이언트 구성 파일을 생성합니다.

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. 브라우저에 URL을 복사하여 zip 파일을 다운로드한 다음 파일의 압축을 풀어 폴더를 봅니다.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 폴더** 에는 *ovpn* 프로필이 포함되어 있으며, 키 및 인증서를 포함하도록 이 프로필을 수정해야 합니다. 자세한 내용은 [Azure VPN Gateway에서 OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md#windows)을 참조하세요. VPN 게이트웨이에서 Azure AD 인증을 선택하는 경우 이 폴더는 zip 파일에 표시되지 않습니다. 대신 AzureVPN 폴더로 이동하여 azurevpnconfig.xml을 찾습니다.

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 정보](point-to-site-about.md)를 참조하세요.
