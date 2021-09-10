---
title: '온-프레미스 네트워크를 VNet에 연결: 사이트 간 VPN(클래식): Portal'
titleSuffix: Azure VPN Gateway
description: 공용 인터넷을 통해 온-프레미스 네트워크와 클래식 Azure 가상 네트워크 사이에 IPsec을 만드는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 71b365a2034e40831d4216f83d046c0b897bbe26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566471"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Azure Portal(클래식)을 사용하여 사이트 간 연결 만들기

이 문서에서는 Azure Portal을 사용하여 온-프레미스 네트워크에서 VNet으로 사이트 간 VPN Gateway 연결을 만드는 방법을 보여줍니다. 이 문서의 단계는 클래식 배포 모델에 적용되며 현재 배포 모델인 Resource Manager에는 적용되지 않습니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

사이트 간 VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure Virtual Network에 연결하는 데 사용됩니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 디바이스를 필요로 합니다. VPN Gateway에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

![사이트 간 VPN Gateway 크로스-프레미스 연결 다이어그램](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 클래식 배포 모델에서 작업할 것인지 확인합니다. [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에서 작업하려면 [사이트 간 연결 만들기(Resource Manager)](./tutorial-site-to-site-portal.md)를 참조하세요. 클래식 모델이 레거시이므로 Resource Manager 배포 모델을 사용하는 것이 좋습니다.
* 호환되는 VPN 디바이스 및 이 디바이스를 구성할 수 있는 사람이 있는지 확인합니다. 호환되는 VPN 디바이스 및 디바이스 구성에 대한 자세한 내용은 [VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.
* VPN 디바이스에 대한 외부 연결 공용 IPv4 주소가 있는지 확인합니다.
* 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다. 이 구성을 만들 때 Azure가 온-프레미스 위치에 라우팅할 IP 주소 범위 접두사를 지정해야 합니다. 온-프레미스 네트워크의 어떤 서브넷도 사용자가 연결하려는 가상 네트워크 서브넷과 중첩될 수 없습니다.
* 공유 키를 지정하고 VPN Gateway 연결을 만드는 데 PowerShell이 필요합니다. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>이 연습에 대한 샘플 구성 값

이 문서의 예제에서는 다음 값을 사용합니다. 이러한 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다. 일반적으로 주소 공간에 대해 IP 주소 값을 사용할 때 라우팅을 유발할 수 있는 주소 공간 겹침을 방지하기 위해 네트워크 관리자와 조정하고자 합니다. 이 경우 작동하는 연결을 만들려면 IP 주소 값을 사용자 고유한 값으로 바꿉니다.

* **리소스 그룹:** TestRG1
* **VNet 이름:** TestVNet1
* **주소 공간:** 10.11.0.0/16
* **서브넷 이름:** FrontEnd
* **서브넷 주소 범위:** 10.11.0.0/24
* **게이트웨이 서브넷:** 10.11.255.0/27
* **지역:** (미국) 미국 동부
* **로컬 사이트 이름:** Site2
* **클라이언트 주소 공간:** 온-프레미스 사이트에 있는 주소 공간입니다.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>가상 네트워크 만들기

S2S 연결에 사용할 가상 네트워크를 만들 때 지정한 주소 공간이 연결하려는 로컬 사이트의 클라이언트 주소 공간과 겹치지 않는지 확인해야 합니다. 겹치는 서브넷에 있으면 연결이 제대로 작동하지 않습니다.

* VNet이 이미 있는 경우 설정이 VPN 게이트웨이 설계와 호환되는지 확인합니다. 다른 네트워크와 겹칠 수 있는 서브넷에 특히 주의합니다.

* 가상 네트워크가 아직 없는 경우 만듭니다. 스크린샷은 예제로 제공됩니다. 사용자 고유의 값으로 대체해야 합니다.

### <a name="to-create-a-virtual-network"></a>가상 네트워크를 만들려면

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>사이트 및 게이트웨이 구성

### <a name="to-configure-the-site"></a>사이트 구성

로컬 사이트는 일반적으로 온-프레미스 위치를 가리킵니다. VPN 디바이스의 IP 주소를 포함하며 여기에서 VPN 디바이스에 대한 VPN Gateway를 통해 라우팅되는 연결 및 IP 주소 범위를 만듭니다.

1. VNet에 대한 페이지의 **설정** 에서 **사이트 간 연결** 을 선택합니다.
1. 사이트 간 연결 페이지에서 **+ 추가** 를 선택합니다.
1. **VPN 연결 및 게이트웨이 구성** 페이지에서 **연결 형식** 으로 **사이트 간** 을 선택한 상태로 둡니다. 이 연습에서는 [예제 값](#values)과 고유한 값을 조합하여 사용해야 합니다.

   * **VPN Gateway IP 주소:** 온-프레미스 네트워크에 대한 VPN 디바이스의 공용 IP 주소입니다. VPN 디바이스에는 IPv4 공용 IP 주소가 필요합니다. 연결하려는 VPN 디바이스에 유효한 공용 IP 주소를 지정합니다. Azure에서 연결할 수 있어야 합니다. VPN 디바이스의 IP 주소를 모르는 경우 항상 자리 표시자 값을 넣고(반드시 유효한 공용 IP 주소 형식으로) 나중에 변경할 수 있습니다.

   * **클라이언트 주소 공간:** 이 게이트웨이를 통해 로컬 온-프레미스 네트워크에 라우팅할 IP 주소 범위를 나열합니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 자체 가상 네트워크가 연결된 다른 네트워크의 범위 또는 가상 네트워크 자체의 주소 범위와 겹치지 않도록 합니다.
1. 페이지 맨 아래에서 검토 + 만들기를 선택하지 않습니다. 대신 **다음: 게이트웨이>** 를 선택합니다.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>가상 네트워크 게이트웨이 구성

1. **게이트웨이** 페이지에서 다음 값을 선택합니다.

   * **크기:** 가상 네트워크 게이트웨이를 만드는 데 사용하는 게이트웨이 SKU입니다. 클래식 VPN 게이트웨이는 이전(레거시) 게이트웨이 SKU를 사용합니다. 레거시 게이트웨이 SKU에 대한 자세한 내용은 [가상 네트워크 게이트웨이 SKU(이전 SKU) 작업](vpn-gateway-about-skus-legacy.md)을 참조하세요. 이 연습에서는 **표준** 을 선택할 수 있습니다.

   * **라우팅 유형:** 게이트웨이에 대한 라우팅 유형을 선택합니다. VPN 유형이라고도 합니다. 게이트웨이를 한 가지 유형에서 다른 유형으로 변환할 수 없기 때문에 올바른 유형을 선택해야 합니다. VPN 디바이스는 선택한 라우팅 유형과 호환되어야 합니다. 라우팅 유형에 대한 자세한 내용은 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#vpntype)를 참조하세요. '경로 기반' 및 '정책 기반' VPN 유형을 참조하는 문서를 볼 수 있습니다. '동적'은 '경로 기반'에 해당하고 '고정'은 '정책 기반'에 해당합니다. 일반적으로 동적 라우팅을 원합니다.

   * **게이트웨이 서브넷:** 지정하는 게이트웨이 서브넷의 크기는 만들려는 VPN Gateway 구성에 따라 달라집니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 /27 또는 /28을 사용하는 것이 좋습니다. 이렇게 하면 더 많은 주소를 포함하는 큰 서브넷이 만들어집니다. 더 큰 게이트웨이 서브넷을 사용하면 향후 구성을 수용할 수 있을 만큼 충분한 IP 주소를 확보할 수 있습니다.

1. 페이지 맨 아래에서 **검토 + 만들기** 를 선택하여 설정의 유효성을 검사합니다. **만들기** 를 선택하여 배포합니다. 선택한 게이트웨이 SKU에 따라 가상 네트워크 게이트웨이를 만드는 데 최대 45분까지 걸릴 수 있습니다.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>VPN 디바이스 구성

온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 디바이스가 필요합니다. 이 단계에서는 VPN 디바이스를 구성합니다. VPN 디바이스를 구성할 때 다음 값이 필요합니다.

* 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.
* 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>값 검색

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>연결 만들기

> [!NOTE]
> 클래식 배포 모델의 경우 Azure Portal 또는 Azure Cloud Shell을 통해 이 단계를 수행할 수 없습니다. 데스크톱에서 로컬로 Azure PowerShell cmdlet의 SM(서비스 관리) 버전을 사용해야 합니다.
>

이 단계에서는 이전 단계의 값을 사용하여 공유 키를 설정하고 연결을 만듭니다. 설정한 키는 VPN 디바이스 구성에 사용된 동일한 키여야 합니다.

1. 공유 키를 설정하고 연결을 만듭니다.

   * -VNetName 값과 -LocalNetworkSiteName 값을 변경합니다. 공백이 포함된 이름을 지정할 때는 값을 작은따옴표로 묶습니다.
   * '-SharedKey'는 사용자가 생성하고 지정하는 값입니다. 이 예제에서는 'abc123'을 사용했지만 좀 더 복잡한 항목을 생성할 수 있습니다. 중요한 점은 여기에서 지정한 값이 VPN 디바이스를 구성하는 경우 지정한 것과 동일한 값이어야 한다는 것입니다.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. 연결이 생성된 경우 결과는 **상태: 성공** 입니다.

## <a name="verify-your-connection"></a><a name="verify"></a>연결 확인

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

연결에 문제가 있는 경우 왼쪽 창의 목차에서 **문제 해결** 섹션을 참조하세요.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>VPN 게이트웨이를 다시 설정하는 방법

Azure VPN Gateway 재설정은 하나 이상의 사이트 간 VPN 터널에서 크로스-프레미스 VPN 연결이 손실되는 경우에 유용합니다. 이 상황에서 온-프레미스 VPN 디바이스는 모두 올바르게 작동하지만 Azure VPN 게이트웨이와 IPsec 터널을 설정할 수 없습니다. 자세한 단계는 [VPN 게이트웨이 다시 설정](./reset-gateway.md#resetclassic)을 참조하세요.

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>게이트웨이 SKU를 변경하는 방법

게이트웨이 SKU를 변경하는 단계는 [게이트웨이 SKU 크기 조정](vpn-gateway-about-SKUS-legacy.md#classicresize)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](../index.yml)를 참조하세요.
* 강제 터널링에 대한 내용은 [강제 터널링 정보](vpn-gateway-about-forced-tunneling.md)를 참조하세요.