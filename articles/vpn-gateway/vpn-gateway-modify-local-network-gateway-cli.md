---
title: '게이트웨이 IP 주소 설정 수정: Azure CLI'
titleSuffix: Azure VPN Gateway
description: Azure CLI를 사용하여 로컬 네트워크 게이트웨이의 IP 주소 접두사를 변경하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/28/2021
ms.author: cherylmc
ms.openlocfilehash: 5a11221cc11e99cf989f1fa50f851b51754c7071
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474272"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Azure CLI를 사용하여 로컬 네트워크 게이트웨이 설정 수정

때때로 로컬 네트워크 게이트웨이 주소 접두사 또는 게이트웨이 IP 주소에 대한 설정이 변경됩니다. 이 문서는 로컬 네트워크 게이트웨이 설정을 수정하는 방법을 안내합니다. 다음 목록에서 다른 옵션을 선택해 다른 방법으로 이러한 설정을 수정할 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

>[!NOTE]
> 연결 된 로컬 네트워크 게이트웨이를 변경 하면 터널의 연결이 끊어지고 가동 중지 시간이 발생할 수 있습니다.
>

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

최신 버전의 CLI 명령(2.0 이상)을 설치합니다. CLI 명령을 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP 주소 접두사 수정

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>게이트웨이 IP 주소 수정

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>다음 단계

게이트웨이 연결을 확인할 수 있습니다. [게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.