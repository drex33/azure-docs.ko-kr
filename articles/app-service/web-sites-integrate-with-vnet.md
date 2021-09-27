---
title: Azure Virtual Network에 앱 통합
description: Azure 가상 네트워크로 Azure App Service와 앱 통합
author: madsd
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 09/20/2021
ms.author: madsd
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: b861ac7f2b9d0a3d8935ff0e16579fd4f5e224d1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641937"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure 가상 네트워크에 앱 통합

이 문서에서는 Azure App Service VNet 통합 기능 및 앱으로 [Azure App Service](./overview.md) 설정 방법을 설명합니다. [Azure VNet(Virtual Network)][VNETOverview]으로 다양한 Azure 리소스를 인터넷이 아닌 라우팅 가능한 네트워크에 배치할 수 있습니다. VNet 통합 기능을 사용하면 앱이 VNet에서 또는 VNet을 통해 리소스에 액세스할 수 있습니다. VNet 통합을 사용하면 앱이 비공개적으로 엑세스할 수 없습니다.

Azure App Service에는 두 가지 종류가 있습니다.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet 통합 사용

1. App Service 포털에서 **네트워킹** 으로 이동합니다. **VNet 통합** 에서 **구성하려면 클릭** 을 선택하세요.

1. **VNet 추가** 를 선택합니다.

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-app.png" alt-text="VNET 통합 선택":::

1. 드롭다운 목록에는 동일한 지역에서 사용자가 구독하는 모든 Azure Resource Manager 가상 네트워크가 포함됩니다. 그 아래에는 다른 모든 지역의 Resource Manager 가상 네트워크가 나열됩니다. 통합하려는 VNet를 선택하세요.

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-add-vnet.png" alt-text="VNet을 선택":::

    * VNet이 동일한 지역에 있는 경우 새 서브넷을 만들거나 빈 기존 서브넷을 선택합니다.
    * 다른 지역에서 VNet을 선택하려면 사이트 간 사용이 프로비전된 VNet 게이트웨이가 필요합니다.
    * 클래식 VNet과 통합하려면 **Virtual Network** 드롭다운 목록을 선택하는 대신 **여기를 클릭 하여 클래식 VNet** 을 선택하세요. 원하는 클래식 가상 네트워크를 선택하세요. 대상 VNet에는 지점 및 사이트 간 사용으로 프로비전된 Virtual Network 게이트웨이가 이미 있어야 합니다.

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-classic.png" alt-text="클래식 VNet을 선택":::

통합하는 동안에 앱이 다시 시작됩니다. 통합이 완료되면 통합된 VNet에 대한 세부 정보가 표시됩니다.

## <a name="regional-vnet-integration"></a>지역 VNet 통합

지역 VNet 통합은 동일한 지역에 있는 VNet 연결을 지원하며 게이트웨이가 필요하지 않습니다. 지역 VNet 통합을 사용하면 앱에서 다음에 액세스할 수 있습니다.

* 통합된 VNet의 리소스
* 앱이 통합된 VNet에 피어링된 VNet의 리소스에는 글로벌 피어링 연결이 포함됩니다.
* Azure ExpressRoute 연결의 리소스
* 서비스 엔드포인트 보안 서비스
* 프라이빗 엔드포인트 사용 서비스.

지역 VNet 통합을 사용하는 경우 다음 Azure 네트워킹 기능을 사용할 수 있습니다.

* **NSG(네트워크 보안 그룹)** : 통합 서브넷에 배치된 NSG를 사용하여 아웃바운드 트래픽을 차단할 수 있습니다. 인바운드 규칙은 적용되지 않습니다. VNet 통합을 사용하여 앱에 대한 인바운드 액세스를 제공할 수 없기 때문입니다.
* **경로 테이블(UDR)** : 통합 서브넷에 경로 테이블을 배치하여 원하는 위치에 아웃바운드 트래픽을 보낼 수 있습니다.

이 기능은 [사용자 지정 컨테이너](./quickstart-custom-container.md)를 포함하여 Windows 및 Linux 앱 모두에서 완벽하게 지원됩니다. 모든 동작은 Windows 앱과 Linux 앱 간에 동일하게 작동합니다.

### <a name="how-regional-vnet-integration-works"></a>지역 VNet 통합 작동 원리

App Service의 앱은 작업자 역할에서 호스팅 됩니다. 지역 VNet 통합은 위임된 서브넷에 주소가 있는 작업자 역할에 가상 인터페이스를 탑재하여 작동합니다. 보낸 사람 주소는 VNet에 있기 때문에 VNet의 VM과 마찬가지로 VNet을 통해 액세스하거나, 모든 항목에 액세스할 수 있습니다. 네트워킹 구현은 VNet에서 VM 실행하기와 다릅니다. 이러한 이유로 일부 네트워킹 기능은 현재 이 기능을 사용할 수 없습니다.

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-regional-works.png" alt-text="지역 VNet 통합 작동 원리":::

지역 VNet 통합을 사용하도록 설정하면 앱이 VNet을 통해 아웃바운드 호출을 수행합니다. 앱 속성 포털에 나열된 아웃바운드 주소는 앱에서 계속 사용하는 주소입니다. 그러나 아웃바운드 호출이 통합 VNet 또는 피어된 VNet의 가상 머신 또는 프라이빗 엔드포인트에 대한 호출인 경우 아웃바운드 주소는 통합 서브넷의 주소가 됩니다. 인스턴스에 할당된 개인 IP는 환경 변수 를 통해 ```WEBSITE_PRIVATE_IP``` 노출됩니다.

모든 트래픽 라우팅을 사용하도록 설정하면 모든 아웃바운드 트래픽이 VNet으로 전송됩니다. 모든 트래픽 라우팅을 사용하도록 설정하지 않으면 통합 서브넷에 구성된 프라이빗 트래픽(RFC1918) 및 서비스 엔드포인트만 VNet으로 전송되고 인터넷에 대한 아웃바운드 트래픽은 정상적으로 동일한 채널을 통과합니다.

이 기능은 작업자당 가상 인터페이스 하나만 지원합니다. 작업자당 가상 인터페이스 하나는 App Service 계획당 한 지역 내 VNet 통합을 의미합니다. 동일한 App Service 계획의 모든 앱은 동일한 VNet 통합을 사용할 수 있습니다. 추가로 VNet에 연결할 앱이 필요한 경우 다른 App Service 계획을 만들어야 합니다. 여기에 사용되는 가상 인터페이스는 고객이 직접 액세스할 수 있는 리소스가 아닙니다.

이 기술이 작동하는 방법의 특성상 VNet 통합에 사용되는 트래픽은 Azure Network Watcher 또는 NSG 플로 로그에 표시되지 않습니다.

### <a name="subnet-requirements"></a>서브넷 요구 사항

VNet 통합은 전용 서브넷에 따라 다릅니다. 서브넷을 프로비전할 때 Azure 서브넷은 처음부터 5개의 IP를 잃습니다. 각 플랜 인스턴스에 대해 통합 서브넷에서 하나의 주소가 사용됩니다. 앱을 4개의 인스턴스로 스케일링하면 4개의 주소가 사용됩니다. 

크기를 스케일 업 또는 스케일 다운하면, 짧은 시간 동안 필요한 주소 공간이 2배가 됩니다. 이렇게 되면 지정된 서브넷 크기에 대해 실제로 사용 가능한 지원되는 인스턴스에 영향을 줍니다. 다음 표는 CIDR 블록당 사용 가능한 최대 주소와 이것이 수평 스케일링에 미치는 영향을 보여줍니다.

| CIDR 블록 크기 | 사용 가능한 최대 주소 | 최대 수평 스케일링(인스턴스)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>어느 시점에서든 SKU 또는 크기를 스케일 업 또는 스케일 다운해야 한다고 가정합니다. 

할당 후에는 서브넷 크기를 변경할 수 없으므로 앱이 도달할 수 있는 모든 규모를 수용할 수 있을 정도로 충분히 큰 서브넷을 사용합니다. 서브넷 용량 관련 문제를 방지하려면 주소가 64개인 /26을 사용해야 합니다.

다른 플랜의 앱이 다른 플랜의 앱을 통해 이미 연결된 VNet에 도달하도록 하려면 기존 VNet 통합에서 사용 중인 서브넷과 다른 서브넷을 선택합니다.

### <a name="routes"></a>경로

지역 VNet 통합을 구성할 때 고려해야 할 두 가지 유형의 라우팅이 있습니다. 애플리케이션 라우팅은 애플리케이션에서 VNet으로 라우팅되는 트래픽을 정의합니다. 네트워크 라우팅은 VNet에서 트래픽이 라우팅되는 방식을 제어하는 기능입니다.

#### <a name="application-routing"></a>애플리케이션 라우팅

애플리케이션 라우팅을 구성할 때 모든 트래픽 또는 프라이빗 트래픽([RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) 트래픽이라고도 함)만 VNet으로 라우팅할 수 있습니다. 모두 라우팅 설정을 통해 이 동작을 구성합니다. 모두 라우팅을 사용하지 않도록 설정하면 앱은 프라이빗 트래픽만 VNet으로 라우팅합니다. 모든 아웃바운드 트래픽을 VNet으로 라우팅하려면 모두 라우팅을 사용하도록 설정되어 있는지 확인합니다.

> [!NOTE]
> * 모두 라우팅을 사용하도록 설정하면 모든 트래픽에는 통합 서브넷에 적용되는 NSG 및 UDR이 적용됩니다. 모든 트래픽 라우팅을 사용하도록 설정하면 트래픽을 다른 곳으로 전달하는 경로를 제공하지 않는 한 아웃바운드 트래픽은 앱 속성에 나열된 주소에서 계속 전송됩니다.
> 
> * 모두 라우팅은 현재 Windows 컨테이너에서 지원되지 않습니다.
>
> * 지역 VNet 통합은 포트 25를 사용할 수 없습니다.

다음 단계를 사용하여 포털을 통해 앱에서 모두 라우팅을 사용하지 않도록 설정할 수 있습니다. 

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-enabled.png" alt-text="모두 라우팅 사용":::

1. 앱 포털에서 **VNet 통합** UI로 이동합니다.
1. **모두 라우팅** 을 사용 안 함으로 설정합니다.
    
    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-disabling.png" alt-text="모두 라우팅 사용 안 함":::

1. **Yes** 를 선택합니다.

CLI를 사용하여 모두 라우팅을 구성할 수도 있습니다(*참고*: 최소 `az version` 2.27.0 필요).

```azurecli-interactive
az webapp config set --resource-group myRG --name myWebApp --vnet-route-all-enabled [true|false]
```

모두 라우팅 구성 설정은 모든 트래픽 라우팅을 사용하도록 설정하는 데 권장되는 방법입니다. 구성 설정을 사용하면 [기본 제공 정책](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33228571-70a4-4fa1-8ca1-26d0aba8d6ef)으로 동작을 감사할 수 있습니다. 기존 `WEBSITE_VNET_ROUTE_ALL` 앱 설정을 계속 사용할 수 있으며 두 설정 중 하나를 사용하여 모든 트래픽 라우팅을 사용하도록 설정할 수 있습니다.

#### <a name="network-routing"></a>네트워크 라우팅

경로 테이블을 사용하여 앱의 아웃바운드 트래픽을 원하는 위치로 라우팅할 수 있습니다. 경로 테이블은 대상 트래픽에 영향을 줍니다. [애플리케이션 라우팅](#application-routing)에서 모두 라우팅을 사용하지 않도록 설정하면 프라이빗 트래픽(RFC1918)만 경로 테이블의 영향을 받습니다. 일반적인 대상에는 방화벽 디바이스나 게이트웨이가 포함될 수 있습니다. 통합 서브넷에 설정된 경로는 인바운드 앱 요청에 대한 응답에 영향을 주지 않습니다. 

모든 아웃바운드 트래픽을 온-프레미스로 라우팅하려면 경로 테이블을 사용하여 모든 아웃바운드 트래픽을 ExpressRoute 게이트웨이로 보낼 수 있습니다. 게이트웨이로 트래픽을 라우팅하는 경우에는 응답을 다시 보내도록 외부 네트워크에서 경로를 설정해야 합니다.

BGP(Border Gateway Protocol) 경로도 앱 트래픽에 영향을 줍니다. ExpressRoute 게이트웨이와 같은 항목의 BGP 경로가 있는 경우 앱 아웃바운드 트래픽이 영향을 받습니다. 사용자 정의 경로와 마찬가지로 BGP 경로는 라우팅 범위 설정에 따른 트래픽에 영향을 미칩니다.

### <a name="network-security-groups"></a>네트워크 보안 그룹

지역 VNet 통합을 사용하는 앱은 [네트워크 보안 그룹][VNETnsg]을 사용하여 VNet 또는 인터넷의 리소스에 대한 아웃바운드 트래픽을 차단할 수 있습니다. 공용 주소에 대한 트래픽을 차단하려면 VNet에 대해 [모두 라우팅](#application-routing)을 사용하도록 설정해야 합니다. 모두 라우팅을 사용하지 않도록 설정된 경우 NSG는 RFC1918 트래픽에만 적용됩니다.

통합 서브넷에 적용된 NSG는 통합 서브넷에 적용된 경로 테이블과 무관하게 적용됩니다. 

NSG의 인바운드 규칙은 앱에 적용되지 않습니다. VNet 통합은 앱의 아웃바운드 트래픽에만 영향을 미치기 때문입니다. 앱에 대한 인바운드 트래픽을 제어하려면 액세스 제한 기능을 사용합니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

지역 VNet 통합을 사용하면 서비스 엔드포인트로 보안이 유지되는 Azure 서비스에 연결할 수 있습니다. 서비스 엔드포인트 보안 서비스에 액세스하려면 다음 단계를 수행해야 합니다.

* 통합을 위해 특정 서브넷에 연결하도록 웹앱과 지역 VNet 통합을 구성합니다.
* 대상 서비스로 이동하여 통합 서브넷에 대해 서비스 엔드포인트를 구성합니다.

### <a name="private-endpoints"></a>프라이빗 엔드포인트

[프라이빗 엔드포인트][privateendpoints]를 호출하려면 DNS 조회가 프라이빗 엔드포인트로 확인되는지 확인해야 합니다. 이러한 동작은 다음 방법 중 하나로 적용할 수 있습니다. 

* Azure DNS 프라이빗 영역과 통합합니다. VNet에 사용자 지정 DNS 서버가 없는 경우 영역이 VNet에 연결되면 이 통합이 자동으로 수행됩니다.
* 앱에서 사용하는 DNS 서버에서 프라이빗 엔드포인트를 관리합니다. 이렇게 하려면 프라이빗 엔드포인트 IP 주소를 알고 A 레코드를 사용하여 해당 주소에 도달하려는 엔드포인트를 가리킵니다.
* Azure DNS 프라이빗 영역으로 전달하도록 자체 DNS 서버를 구성합니다.

### <a name="azure-dns-private-zones"></a>Azure DNS 프라이빗 영역 

앱에서 VNet과 통합된 이후 VNet이 구성된 동일한 DNS 서버를 사용하고, 사용자 지정 DNS가 지정되지 않는 경우 Azure 기본 DNS과 VNet에 연결된 프라이빗 영역을 사용합니다.

> [!NOTE]
> Linux Apps Azure DNS 프라이빗 영역은 모두 라우팅을 사용하는 경우에만 작동합니다.

### <a name="limitations"></a>제한 사항

지역 VNet 통합 사용과 관련하여 몇 가지 제한 사항:

* 이 기능은 프리미엄 V2 및 프리미엄 V3의 모든 App Service 배율 단위에서 사용할 수 있습니다. 표준에서도 사용할 수 있지만 최신 App Service 배율 단위에서만 사용할 수 있습니다. 이전 배율 단위를 사용하는 경우 프리미엄 V2 App Service 요금제의 기능만 사용할 수 있습니다. 표준 App Service 요금제에서 이 기능을 사용할 수 있도록 하려면 프리미엄 V3 App Service 요금제에서 앱을 만듭니다. 이러한 플랜은 최신 배율 단위에서만 지원됩니다. 계획을 만든 후 원하는 경우 축소할 수 있습니다.
* 이 기능은 App Service Environment에 있는 격리 요금제 앱에서 사용할 수 없습니다.
* 클래식 가상 네트워크를 사용하는 피어링 연결에서 리소스에 연결할 수 없습니다.
* 이 기능에는 Azure Resource Manager VNet에서 /28 이상의 사용하지 않는 서브넷이 필요합니다.
* 앱과 VNet은 동일한 지역에 있어야 합니다.
* 통합 VNet은 IPv6 주소 공간을 정의할 수 없습니다.
* 통합 서브넷은 App Service 요금제 하나에서만 사용할 수 있습니다.
* 통합된 앱이 있는 VNet은 삭제할 수 없습니다. VNet을 삭제하기 전에 통합을 제거하세요.
* 지역 VNet 통합은 App Service 요금제당 하나만 가능합니다. 동일한 App Service 요금제의 여러 앱에서 동일한 VNet을 사용할 수 있습니다.
* 지역 VNet 통합을 사용하는 앱이 있는 경우에는 앱 또는 플랜의 구독을 변경할 수 없습니다.
* 앱은 모두 라우팅을 사용하도록 설정하지 않고 Linux 계획에서 Azure DNS Private Zones 주소를 확인할 수 없습니다.

## <a name="gateway-required-vnet-integration"></a>필수 게이트웨이 VNet 통합

필수 게이트웨이 VNet 통합은 다른 지역의 VNet 또는 클래식 가상 네트워크 관련 연결을 지원합니다. 필수 게이트웨이 VNet 통합

* 앱은 한 번에 1개의 VNet에만 연결 가능합니다.
* VNet App Service 계획에 최대 5개까지 통합 가능합니다.
* App Service 계획에서 사용할 수 있는 총 개수에 영향을 주지 않고 App Service 계획의 여러 앱에서 동일한 VNet을 사용할 수 있습니다. 동일한 App Service 계획에서 동일한 VNet을 사용하는 앱이 6개 있는 경우 사용되는 VNet은 하나로 계산됩니다.
* 게이트웨이의 SLA에 따라 99.9% SLA를 지원합니다.
* 앱에서 VNet이 구성된 DNS를 사용할 수 있도록 합니다.
* 앱에 연결하려면 먼저 SSTP 지점 및 사이트 간 VPN이 구성된 Virtual Network 경로 기반 게이트웨이가 필요합니다.

필수 게이트웨이 VNet 통합을 사용할 수 없습니다.

* Azure ExpressRoute와 연결된 VNet 사용.
* Linux 앱에서.
* [Windows 컨테이너](quickstart-custom-container.md)에서.
* 서비스 엔드포인트의 보안 리소스에 액세스합니다.
* ExpressRoute와 지점 및 사이트 간 또는 사이트 간 VPN을 모두 지원하는 동시 존재 게이트웨이.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Azure 가상 네트워크에서 게이트웨이 설정

게이트웨이를 만들려면,

1. VNet에 [게이트웨이 서브넷을 만듭니다][creategatewaysubnet].

1. [VPN 게이트웨이를 만듭니다][creategateway]. 경로 기반 VPN 종류를 선택합니다.

1. [지점 및 사이트 간 주소를 설정합니다][setp2saddresses]. 게이트웨이가 기본 SKU에 없으면 지점 및 사이트 간 구성에서 IKEV2를 사용하지 않도록 설정해야 하며 SSTP를 선택해야 합니다. 지점 및 사이트 간 주소 공간은 RFC 1918 주소 블록 10.0.0.0/8, 172.16.0.0/12 및 192.168.0.0/16에 있어야 합니다.

App Service VNet 통합에 사용할 게이트웨이를 만드는 경우 인증서를 업로드할 필요가 없습니다. 게이트웨이를 만드는 데 30분이 걸릴 수 있습니다. 게이트웨이가 프로비전될 때까지는 VNet과 앱을 통합할 수 없습니다.

### <a name="how-gateway-required-vnet-integration-works"></a>필수 게이트웨이와 VNet 통합 작동 원리

필수 게이트웨이 VNet 통합은 지점 및 사이트 간 VPN 기술을 기반으로 합니다. 지점 및 사이트 간 VPN은 앱을 호스트 하는 가상 머신의 네트워크 액세스를 제한합니다. 앱은 하이브리드 연결 또는 VNet 통합을 통해서만 트래픽을 인터넷으로 보내도록 제한됩니다. 앱이 포털을 사용하여 필수 게이트웨이 VNet 통합을 사용하도록 구성된 경우 사용자를 대신해 복잡한 협상을 관리하고 게이트웨이 및 애플리케이션 쪽에서 인증서를 만들고 할당합니다. 따라서 앱을 호스트 하는 데 사용되는 작업자는 선택한 VNet의 가상 네트워크 게이트웨이에 직접 연결할 수 있습니다.

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-gateway-works.png" alt-text="필수 게이트웨이와 VNet 통합 작동 원리":::

### <a name="access-on-premises-resources"></a>온-프레미스 리소스에 액세스

앱은 사이트 간 연결이 있는 VNet과 통합되어 온-프레미스 리소스에 액세스할 수 있습니다. 필수 게이트웨이 VNet 통합을 사용하는 경우 지점 및 사이트 간 주소 블록을 사용하여 온-프레미스 VPN gateway 경로를 업데이트합니다. 사이트 간 VPN을 처음 설정하는 경우 이를 구성하는 데 사용되는 스크립트에서 경로를 올바르게 설정해야 합니다. 사이트 간 VPN을 만든 후에 지점 및 사이트 간 VPN을 추가하는 경우 해당 경로를 수동으로 업데이트해야 합니다. 이 작업을 수행하는 방법 관련 세부 정보는 게이트웨이마다 다르며 여기에 설명되어 있지 않습니다. 또한 사이트 간 VPN 연결로 구성된 BGP는 사용할 수 없습니다.

VNet을 통해 온-프레미스 리소스에 연결 하기 위해 지역 VNet 통합 기능에 대 한 추가 구성은 필요 하지 않습니다. ExpressRoute 또는 사이트 간 VPN을 사용하여 VNet을 온-프레미스 리소스에 연결만 하면 됩니다.

> [!NOTE]
> 필수 게이트웨이 VNet 통합 기능은 ExpressRoute 게이트웨이가 있는 VNet과 앱을 통합하지 않습니다. ExpressRoute 게이트웨이가 [동시 존재 모드][VPNERCoex]로 구성되어 있더라도 VNet 통합은 작동하지 않습니다. ExpressRoute 연결을 통해 리소스에 액세스해야 하는 경우에는 VNet에서 실행되는 [App Service Environment][ASE]를 사용할 수 있습니다.
>
>

### <a name="peering"></a>피어링

지역 VNet 통합에서 피어링을 사용하는 경우 추가 구성을 수행할 필요가 없습니다.

피어 링과 함께 게이트웨이 필수 VNet 통합을 사용 하는 경우 몇 가지 항목을 추가로 구성 해야 합니다. 앱에서 작동하도록 피어링을 구성하려면,

1. 앱이 연결되는 VNet에 피어링 연결을 추가합니다. 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용** 을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **게이트 전송 허용** 을 선택하세요.
1. 사용자가 연결한 VNet에 피어링되는 VNet으로 피어링 연결을 추가하세요. 대상 VNet에 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용** 을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **원격 게이트웨이 허용** 을 선택하세요.
1. 포털에서 **App Service 계획** > **네트워킹** > **VNet 통합** UI로 이동합니다. 앱이 연결되는 VNet을 선택합니다. 라우팅 섹션에서 앱이 연결된 VNet과 피어링되는 VNet의 주소 범위를 추가하세요.

## <a name="manage-vnet-integration"></a>VNet 통합 관리

앱 수준에서 VNet에 연결하거나 연결을 끊습니다. 여러 앱의 VNet 통합에 영향을 줄 수 있는 작업은 App Service 계획 수준입니다. 앱 >**네트워킹** > **VNet 통합** 포털에서 VNet 관련 세부 정보를 볼 수 있습니다. **App Service 계획** > **네트워킹** > **VNet 통합** 포털의 App Service 계획 수준에서 유사한 정보를 볼 수 있습니다.

VNet 통합 인스턴스의 앱 보기에서 할 수 있는 유일한 작업은 현재 연결된 VNet과 앱의 연결을 해제하는 것입니다. VNet에서 앱의 연결을 끊으려면 **연결 끊기** 를 선택합니다. VNet과의 연결을 끊으면 앱이 다시 시작됩니다. 연결을 끊더라도 VNet은 변경되지 않습니다. 서브넷 또는 게이트웨이가 제거되지 않습니다. 그다음 VNet을 삭제하려면, 먼저 VNet에서 앱의 연결을 끊고 게이트웨이 등의 리소스를 삭제하세요.

App Service 계획 VNet 통합 UI에는 App Service 계획의 앱에서 사용하는 모든 VNet 통합이 표시됩니다. 각 VNet 관련 세부 정보를 보려면 관심 있는 VNet을 선택하세요. 필수 게이트웨이 VNet 통합에 대해 여기에서 수행할 수 있는 작업은 두 가지가 있습니다.

* **네트워크 동기화** 네트워크 동기화 작업은 게이트웨이 종속 VNet 통합 기능에만 사용합니다. 네트워크 동기화 작업을 수행하면 인증서와 네트워크 정보가 동기화됩니다. VNet의 DNS를 추가하거나 변경하면 네트워크 동기화 작업을 수행해야 합니다. 이 작업은 해당 VNet이 사용하는 모든 앱을 다시 시작합니다. 다른 구독에 속하는 앱과 vnet을 사용하는 경우 해당 작업은 작동하지 않습니다.
* **경로 추가** 경로를 추가하면 아웃바운드 트래픽이 VNet에 추가됩니다.

인스턴스에 할당된 개인 IP는 환경 변수인 **WEBSITE_PRIVATE_IP** 를 통해 노출됩니다. 또한 Kudu 콘솔 UI에서는 Web App에서 사용할 수 있는 환경 변수의 목록을 보여줍니다. 이 IP는 통합된 서브넷의 주소 범위에서 할당됩니다. 지역 VNet 통합의 경우 WEBSITE_PRIVATE_IP 값은 위임된 서브넷의 주소 범위에 속하는 IP이고 VNet 통합이 필요한 게이트웨이의 경우 값은 Virtual Network Gateway에 구성된 지점 및 사이트 간 주소 풀의 주소 범위에 속하는 IP입니다. 이는 Web App이 Virtual Network를 통해 리소스에 연결하는 데 사용하는 IP입니다. 

> [!NOTE]
> WEBSITE_PRIVATE_IP 값은 변경 내용에 바인딩됩니다. 하지만 통합 서브넷의 주소 범위나 지점 및 사이트간 주소 범위 내의 IP이므로 전체 주소 범위에서 액세스하도록 허용해야 합니다.
>

### <a name="gateway-required-vnet-integration-routing"></a>필수 게이트웨이 VNet 통합 라우팅
VNet에 정의된 경로는 트래픽을 앱에서 VNet으로 전달하는 데 사용됩니다. 아웃바운드 트래픽을 VNet에 추가로 보내야 하는 경우 해당 주소 블록을 여기에 추가하세요. 이 기능은 필수 게이트웨이 VNet 통합에서만 작동합니다. 지역 VNet 통합을 사용하는 방식으로 필수 게이트웨이 VNet 통합을 사용하는 경우 경로 테이블은 앱 트래픽에 영향을 주지 않습니다.

### <a name="gateway-required-vnet-integration-certificates"></a>필수 게이트웨이 VNet 통합 인증서
필수 게이트웨이 VNet 통합을 사용하면, 연결 보안을 위해 필요한 인증서 교환이 있습니다. 인증서와 함께 네트워크를 설명하는 DNS 구성, 경로 및 다른 비슷한 항목이 있습니다.

인증서 또는 네트워크 정보가 변경되면 **네트워크 동기화** 를 선택하세요. **네트워크 동기화** 를 클릭하면 앱과 VNet 연결이 잠시 중단됩니다. 앱이 다시 시작되지는 않지만, 연결 손실로 인해 사이트가 제대로 작동하지 않을 수 있습니다.

## <a name="pricing-details"></a>가격 정보
지역 VNet 통합 기능에는 App Service 계획 가격 책정 계층 요금 외에 추가 요금이 부과 되지 않습니다.

다음의 세 가지 가격이 필수 게이트웨이 VNet 통합 기능 사용과 관련된 가격입니다.

* **App Service 요금제 가격 책정 계층 청구**: 앱은 표준, 프리미엄, PremiumV2 또는 PremiumV3 App Service 요금제에 속해야 합니다. 가격 책정에 대한 자세한 내용은 [App Service 가격 책정][ASPricing]을 참조하세요.
* **데이터 전송 비용** VNet이 동일한 데이터 센터에 있더라도 데이터 송신 요금이 있습니다. 이러한 요금은 [데이터 전송 가격 정보][DataPricing]에서 설명합니다.
* **VPN 게이트웨이 비용** 지점 및 사이트 간 VPN에 필요한 가상 네트워크 게이트웨이 관련 비용이 발생합니다. 자세한 내용은 [VPN 게이트웨이 가격 책정][VNETPricing]을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

> [!NOTE]
> App Service의 Docker Compose 시나리오에서는 VNET 통합이 지원되지 않습니다.
> 프라이빗 엔드포인트가 있으면 Azure Functions 액세스 제한이 무시됩니다.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI 지원은 지역 VNet 통합에서 이용할 수 있습니다. 다음 명령에 액세스하여[Azure CLI 에이전트][installCLI]를 설치합니다.

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.
```

지역 VNet 통합을 지원하는 PowerShell도 사용할 수 있습니다. 하지만 서브넷 resourceID의 속성 배열이 있는 일반 리소스를 만들어야 합니다.

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

# Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

# Creation of the VNet Integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```

<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
[VNETnsg]: /azure/virtual-network/security-overview/
