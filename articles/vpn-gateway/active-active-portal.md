---
title: '활성–활성 VPN Gateway 구성: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Azure Portal을 사용하여 활성–활성 가상 네트워크 게이트웨이를 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: JackStromberg
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/22/2021
ms.author: jstrom
ms.openlocfilehash: 1a5ca58b9e56826c7173c499b64ebabc80ddf11e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529158"
---
# <a name="configure-active-active-vpn-gateways-using-the-portal"></a>Portal을 사용하여 활성–활성 VPN Gateway 구성

이 문서는 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md) 및 Azure Portal을 사용하여 고가용성 활성-활성 VPN Gateway를 만드는 데 도움이 됩니다. [PowerShell](vpn-gateway-activeactive-rm-powershell.md)을 사용하여 활성-활성 게이트웨이를 구성할 수도 있습니다.

크로스-프레미스 및 VNet 간 연결에 대해 고가용성을 달성하려면 여러 VPN Gateway를 배포하고 네트워크와 Azure 간에 여러 병렬 연결을 설정해야 합니다. 연결 옵션 및 토폴로지에 대한 개요는 [고가용성 프레미스 간 연결 및 VNet 간 연결](vpn-gateway-highlyavailable.md)을 참조하세요.

> [!IMPORTANT]
> 활성-활성 모드는 기본 또는 표준을 제외한 모든 SKU에서 사용할 수 있습니다. 자세한 내용은 [구성 설정](vpn-gateway-about-vpn-gateway-settings.md#gwsku)을 참조하세요.
>

이 문서의 단계는 활성-활성 모드에서 VPN Gateway를 구성하는 데 도움이 됩니다. 활성-활성 및 활성-대기 모드 간에는 몇 가지 차이점이 있습니다. 다른 속성은 비 활성-활성 게이트웨이와 동일합니다. 

* 활성-활성 게이트웨이에는 두 개의 게이트웨이 IP 구성과 두 개의 공용 IP 주소가 있습니다.
* 활성-활성 게이트웨이에는 활성-활성 설정이 사용하도록 설정되어 있습니다.
* 가상 네트워크 게이트웨이 SKU는 기본 또는 표준일 수 없습니다.

VPN Gateway가 이미 있는 경우 [기존 VPN Gateway](#update)를 활성-대기에서 활성-활성 모드로 또는 활성-활성-활성-대기 모드로 업데이트할 수 있습니다.

## <a name="create-a-vnet"></a><a name="vnet"></a>VNet 만들기

사용하려는 VNet이 아직 없는 경우 다음 값을 사용하여 VNet을 만듭니다.

* **리소스 그룹:** TestRG1
* **이름:** VNet1
* **지역:** (미국) 미국 동부
* **IPv4 주소 공간:** 10.1.0.0/16
* **서브넷 이름:** FrontEnd
* **서브넷 주소 공간:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-an-active-active-vpn-gateway"></a><a name="gateway"></a>활성-활성 VPN Gateway 만들기

이 단계에서는 VNet에 대한 활성-활성 가상 네트워크 게이트웨이(VPN Gateway)를 만듭니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

다음 값을 사용하여 가상 네트워크 게이트웨이를 만듭니다.

* **이름:** VNet1GW
* **지역:** 미국 동부
* **게이트웨이 유형:** VPN
* **VPN 유형:** 경로 기반
* **SKU:** VpnGw2
* **세대:** Generation2
* **가상 네트워크:** VNet1
* **게이트웨이 서브넷 주소 범위:** 10.1.255.0/27
* **공용 IP 주소:** 새로 만들기
* **공용 IP 주소 이름:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-active-portal-include.md)]

배포 상태는 게이트웨이에 대한 [개요] 페이지에서 확인할 수 있습니다. 게이트웨이를 만든 후 포털에서 가상 네트워크를 살펴보면 게이트웨이에 할당된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 디바이스로 표시됩니다.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="update-an-existing-vpn-gateway"></a><a name ="update"></a> 기존 VPN Gateway 업데이트

이 섹션은 기존 Azure VPN Gateway를 활성-대기 모드에서 활성-활성 모드로 또는 활성-활성 모드에서 활성-대기 모드로 변경하는 데 도움이 됩니다. 활성-대기 게이트웨이가 활성-활성으로 변경되면, 다른 공용 IP 주소를 만든 다음, 두 번째 게이트웨이 IP 구성을 추가합니다. 

### <a name="change-active-standby-to-active-active"></a>활성-대기에서 활성-활성으로 변경

활성-대기 모드 게이트웨이를 활성-활성 모드로 변환하려면 다음 단계를 사용합니다. [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)을 사용하여 게이트웨이를 만든 경우 이 페이지에서 SKU를 업그레이드할 수도 있습니다.

1. 가상 네트워크 게이트웨이에 대한 페이지를 엽니다.

1. 왼쪽 메뉴에서 **구성** 을 선택합니다.

1. **구성** 페이지에서 다음 설정을 구성합니다. 

   * 활성-활성 모드를 **사용** 으로 변경합니다.
   * **다른 게이트웨이 IP 구성 만들기** 를 클릭합니다.

   :::image type="content" source="./media/active-active-portal/configuration.png" alt-text="구성 페이지를 보여 주는 스크린샷":::

1. **공용 IP 주소 선택** 페이지에서 조건을 충족하는 기존 공용 IP 주소를 지정하거나 **+새로 만들기** 를 선택하여 두 번째 VPN Gateway 인스턴스에 사용할 새 공용 IP 주소를 만듭니다.

1. **공용 IP 주소 만들기** 페이지에서 **기본** SKU를 선택한 후 **확인** 을 클릭합니다.

1. **구성** 페이지의 맨 위에서 **저장** 을 클릭합니다. 이 업데이트를 완료하는 데 약 30-45분이 걸릴 수 있습니다.

### <a name="change-active-active-to-active-standby"></a>활성-활성을 활성-대기로 변경

활성-활성 모드 게이트웨이를 활성-대기 모드로 변환하려면 다음 단계를 사용합니다.

1. 가상 네트워크 게이트웨이에 대한 페이지를 엽니다.

1. 왼쪽 메뉴에서 **구성** 을 선택합니다.

1. **구성** 페이지에서 활성-활성 모드를 **사용 안 함** 으로 변경합니다.

1. **구성** 페이지의 맨 위에서 **저장** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

연결을 구성하려면 다음 문서를 참조하세요.

* [사이트 간 VPN 연결](./tutorial-site-to-site-portal.md)
* [VNet 간 연결](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md#configure-the-vnet1-gateway-connection)