---
title: '게이트웨이 IP 주소 설정 수정: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Azure Portal을 사용하여 로컬 네트워크 게이트웨이의 IP 주소 접두사를 변경하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/28/2021
ms.author: cherylmc
ms.openlocfilehash: d52d7bb4134c23192c083a61b2aa1d86d472f6ce
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448579"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure Portal을 사용하여 로컬 네트워크 게이트웨이 설정 수정

때로는 로컬 네트워크 게이트웨이 AddressPrefix 또는 GatewayIPAddress에 대한 설정을 변경합니다. 이 문서는 로컬 네트워크 게이트웨이 설정을 수정하는 방법을 안내합니다. 다음 목록에서 다른 옵션을 선택해 다른 방법으로 이러한 설정을 수정할 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

>[!NOTE]
> 연결 된 로컬 네트워크 게이트웨이를 변경 하면 터널의 연결이 끊어지고 가동 중지 시간이 발생할 수 있습니다.
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>로컬 네트워크 게이트웨이 구성

아래 스크린샷은 공용 IP 주소 엔드포인트를 사용하는 로컬 네트워크 게이트웨이 리소스의 **구성** 페이지를 보여 줍니다.

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="IP 주소 설정" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

FQDN 엔드포인트가 있는 구성 페이지입니다.

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="FQDN 설정":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>게이트웨이 IP 주소 또는 FQDN을 수정하려면

> [!NOTE]
> FQDN 엔드포인트와 IP 주소 엔드포인트 간에 로컬 네트워크 게이트웨이를 변경할 수 없습니다. 이 로컬 네트워크 게이트웨이와 연결된 모든 연결을 삭제하고, 새 엔드포인트(IP 주소 또는 FQDN)를 사용하여 새 연결을 만든 다음, 연결을 다시 만들어야 합니다.
>

연결하려는 VPN 디바이스의 공용 IP 주소가 변경된 경우 다음 단계를 사용하여 로컬 네트워크 게이트웨이를 수정합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성** 을 선택합니다.
2. **IP 주소** 상자에서 IP 주소를 수정합니다.
3. **저장** 을 선택하여 설정을 저장합니다.

연결하려는 VPN 디바이스가 FQDN(정규화된 도메인 이름)을 변경한 경우 다음 단계를 사용하여 로컬 네트워크 게이트웨이를 수정합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성** 을 선택합니다.
2. **FQDN** 상자에서 도메인 이름을 수정합니다.
3. **저장** 을 선택하여 설정을 저장합니다.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP 주소 접두사를 수정하려면

추가 주소 접두사를 추가하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성** 을 선택합니다.
2. *추가 주소 범위 추가* 상자에 IP 주소 공간을 추가합니다.
3. **저장** 을 선택하여 설정을 저장합니다.

주소 접두사를 제거하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성** 을 선택합니다.
2. 제거하려는 접두사를 포함하는 줄에서 **'...'** 를 선택합니다.
3. **제거** 를 선택합니다.
4. **저장** 을 선택하여 설정을 저장합니다.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>BGP 설정을 수정하려면

BGP 설정을 추가하거나 업데이트하려면 다음을 수행합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성** 을 선택합니다.
2. **"BGP 설정 구성"** 을 선택하여 이 로컬 네트워크 게이트웨이에 대한 BGP 구성을 표시하거나 업데이트합니다.
3. 해당 필드에 자치 시스템 번호 또는 BGP 피어 IP 주소 추가 또는 업데이트
4. **저장** 을 선택하여 설정을 저장합니다.

BGP 설정을 제거하려면 다음을 수행합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성** 을 선택합니다.
2. 기존 BGP ASN 및 BGP 피어 IP 주소를 제거하려면 **"BGP 설정 구성"** 을 선택 취소합니다.
3. **저장** 을 선택하여 설정을 저장합니다.

## <a name="next-steps"></a>다음 단계

게이트웨이 연결을 확인할 수 있습니다. [게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.
