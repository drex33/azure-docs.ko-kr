---
title: Azure Functions 네트워킹 옵션
description: Azure Functions에서 사용할 수 있는 모든 네트워킹 옵션에 대한 개요입니다.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: 99f3bb2dc2095e8c9ae89afe78b4522dcdc23a07
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265365"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 네트워킹 옵션

이 문서에서는 Azure Functions에 대한 호스팅 옵션에서 사용할 수 있는 네트워킹 기능을 설명합니다. 다음 네트워킹 옵션을 통해 인터넷 라우팅 가능한 주소를 사용하지 않고 리소스에 액세스하거나 함수 앱에 대한 인터넷 액세스를 제한할 수 있습니다.

호스팅 모델은 서로 다른 수준의 네트워크 격리를 사용할 수 있습니다. 올바른 격리를 선택하면 네트워크 격리 요구 사항을 충족할 수 있습니다.

다음 두 가지 방법으로 함수 앱을 호스트할 수 있습니다.

* 다양한 수준의 가상 네트워크 연결 및 확장 옵션을 사용하여 다중 테넌트 인프라에서 실행되는 요금제 옵션 중에서 선택할 수 있습니다.
    * [사용 계획](consumption-plan.md)은 부하에 대응하여 동적으로 확장되고 최소한의 네트워크 격리 옵션을 제공합니다.
    * [프리미엄 계획](functions-premium-plan.md)은 동적으로 확장되고 보다 포괄적인 네트워크 격리를 제공합니다.
    * Azure [App Service 계획](dedicated-plan.md)은 고정된 규모에서 작동하며 프리미엄 계획과 비슷한 네트워크 격리를 제공합니다.
* [App Service Environment](../app-service/environment/intro.md)에서 함수를 실행할 수 있습니다. 이 메서드는 가상 네트워크에 함수를 배포하고 전체 네트워크 제어 및 격리를 제공합니다.

## <a name="matrix-of-networking-features"></a>네트워킹 기능 매트릭스

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>인바운드 액세스 제한

액세스 제한을 사용하여 앱 액세스가 허용 또는 거부되는 IP 주소의 목록을 우선 순위대로 정의할 수 있습니다. 이 목록에는 IPv4 및 IPv6 주소나 [서비스 엔드포인트](#use-service-endpoints)를 사용하는 특정 가상 네트워크 서브넷이 포함될 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 암시적 "모두 거부"가 표시됩니다. IP 제한은 모든 함수 호스팅 옵션에서 작동합니다.

액세스 제한은 [프리미엄](functions-premium-plan.md), [사용량](consumption-plan.md), [App Service](dedicated-plan.md)에서 사용할 수 있습니다.

> [!NOTE]
> 네트워크 제한을 사용하는 경우 가상 네트워크 내에서만 배포하거나, 사용 중인 컴퓨터의 IP 주소를 안전 수신자 목록의 Azure Portal에 액세스하는 데 사용할 수 있습니다. 그러나 포털을 사용하여 함수를 관리할 수는 있습니다.

자세히 알아보려면 [Azure App Service 정적 액세스 제한](../app-service/app-service-ip-restrictions.md)을 참조하세요.

## <a name="private-endpoint-connections"></a>프라이빗 엔드포인트 연결

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

스토리지 또는 Service Bus와 같은 개인 엔드포인트 연결이 있는 다른 서비스를 호출하려면 [프라이빗 엔드포인트에 대한 아웃바운드 호출](#private-endpoints)을 수행하도록 앱을 구성해야 합니다.

### <a name="use-service-endpoints"></a>서비스 엔드포인트 사용

서비스 엔드포인트를 사용하여 선택한 Azure 가상 네트워크 서브넷에 대한 액세스를 제한할 수 있습니다. 특정 서브넷에 대한 액세스를 제한하려면 **Virtual Network** 유형을 사용하여 제한 규칙을 만듭니다. 그런 다음 액세스를 허용하거나 거부할 구독, 가상 네트워크 및 서브넷을 선택할 수 있습니다. 

선택한 서브넷에 대한 서비스 엔드포인트가 아직 Microsoft 웹에서 사용하도록 설정되지 않은 경우 **누락된 Microsoft.Web 서비스 엔드포인트를 무시** 확인란을 선택하지 않으면 자동으로 사용하도록 설정됩니다. 앱에서 서비스 엔드포인트는 사용하고 서브넷은 사용하지 않도록 설정할 수 있는 시나리오는 대체로 서브넷에서 사용하도록 설정할 권한이 있는지에 따라 달라집니다. 

다른 사용자가 서브넷에서 서비스 엔드포인트를 사용하도록 설정해야 하는 경우 **누락된 Microsoft.Web 서비스 엔드포인트를 무시** 확인란을 선택합니다. 앱은 나중에 서브넷에서 사용하도록 설정할 것을 예측하여 서비스 엔드포인트에 대해 구성됩니다. 

![Virtual Network 유형이 선택된 "IP 제한 추가" 창의 스크린샷.](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

서비스 엔드포인트를 사용하여 App Service Environment에서 실행되는 앱에 대한 액세스를 제한할 수 없습니다. 앱이 App Service Environment에 있는 경우 IP 액세스 규칙을 적용하여 해당 앱에 대한 액세스를 제어할 수 있습니다. 

서비스 엔드포인트를 설정하는 방법을 알아보려면 [Azure Functions 프라이빗 사이트 액세스 설정](functions-create-private-site-access.md)을 참조하세요.

## <a name="virtual-network-integration"></a>가상 네트워크 통합

가상 네트워크 통합을 사용하면 함수 앱이 가상 네트워크 내의 리소스에 액세스할 수 있습니다.
Azure Functions는 두 종류의 가상 네트워크 통합을 지원합니다.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions의 가상 네트워크 통합은 App Service 웹 앱에 공유 인프라를 사용합니다. 가상 네트워크 통합의 두 유형을 자세히 알아보려면 다음을 참조하세요.

* [지역 가상 네트워크 통합](../app-service/overview-vnet-integration.md#regional-vnet-integration)
* [게이트웨이 필요 가상 네트워크 통합](../app-service/overview-vnet-integration.md#gateway-required-vnet-integration)

가상 네트워크 통합을 설정 하는 방법을 알아보려면 [Vnet 통합 사용](#enable-vnet-integration)을 참조 하세요.

## <a name="enable-vnet-integration"></a>VNet 통합 사용

1. 함수 앱 포털에서 **네트워킹** 블레이드로 이동 합니다. **VNet 통합** 에서 **구성하려면 클릭** 을 선택하세요.

1. **VNet 추가** 를 선택합니다.

    :::image type="content" source="./media/functions-networking-options/vnet-int-function-app.png" alt-text="VNET 통합 선택":::

1. 드롭다운 목록에는 동일한 지역에서 사용자가 구독하는 모든 Azure Resource Manager 가상 네트워크가 포함됩니다. 통합하려는 VNet를 선택하세요.

    :::image type="content" source="./media/functions-networking-options/vnet-int-add-vnet-function-app.png" alt-text="VNet을 선택":::

    * Premium 계획 함수는 지역 VNet 통합만 지원 합니다. VNet이 동일한 지역에 있는 경우 새 서브넷을 만들거나 비어 있는 기존 서브넷을 선택 합니다.
    * 다른 지역에서 VNet을 선택하려면 사이트 간 사용이 프로비전된 VNet 게이트웨이가 필요합니다. 지역 간 VNet 통합은 전용 계획에 대해서만 지원 됩니다.

통합하는 동안에 앱이 다시 시작됩니다. 통합이 완료되면 통합된 VNet에 대한 세부 정보가 표시됩니다. 기본적으로 모두 라우팅은 사용 하도록 설정 되 고 모든 트래픽이 VNet으로 라우팅됩니다.

개인 트래픽 ([RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) 트래픽)만 라우팅하도록 하려면 [app service 설명서](../app-service/overview-vnet-integration.md#application-routing)의 단계를 따르세요.

## <a name="regional-virtual-network-integration"></a>지역 가상 네트워크 통합

지역 VNet 통합을 사용하면 앱에서 다음에 액세스할 수 있습니다.

* 앱과 동일한 지역에 있는 VNet의 리소스
* 앱이 통합된 VNet에 피어링된 VNet의 리소스
* 서비스 엔드포인트 보안 서비스
* Azure ExpressRoute 연결의 리소스
* 통합된 VNet의 리소스
* Azure ExpressRoute 연결을 포함하는 피어링된 연결의 리소스
* 프라이빗 엔드포인트 

동일한 지역에서 VNet과 VNet 통합을 사용하는 경우 다음과 같은 Azure 네트워킹 기능을 사용할 수 있습니다.

* **NSG(네트워크 보안 그룹)** : 통합 서브넷에 배치된 NSG를 사용하여 아웃바운드 트래픽을 차단할 수 있습니다. 인바운드 규칙은 적용되지 않습니다. VNet 통합을 사용하여 앱에 대한 인바운드 액세스를 제공할 수 없기 때문입니다.
* **경로 테이블(UDR)** : 통합 서브넷에 경로 테이블을 배치하여 원하는 위치에 아웃바운드 트래픽을 보낼 수 있습니다.

> [!NOTE]
> 모든 아웃바운드 트래픽을 VNet으로 라우팅하면 통합 서브넷에 적용되는 NSG 및 UDR이 적용됩니다. VNet을 통합 하는 경우 다른 곳에서 트래픽을 전달 하는 경로를 제공 하지 않는 한, 공용 IP 주소에 대 한 함수 앱의 아웃 바운드 트래픽은 앱 속성에 나열 된 주소에서 계속 전송 됩니다.
> 
> 지역 VNet 통합은 포트 25를 사용할 수 없습니다.

동일한 지역에서 VNet과 VNet 통합을 사용하는 경우 몇 가지 제한 사항이 있습니다.

* 글로벌 피어링 연결에서 리소스에 연결할 수 없습니다.
* 이 기능은 프리미엄 V2 및 프리미엄 V3의 모든 App Service 배율 단위에서 사용할 수 있습니다. 표준에서도 사용할 수 있지만 최신 App Service 배율 단위에서만 사용할 수 있습니다. 이전 배율 단위를 사용하는 경우 프리미엄 V2 App Service 요금제의 기능만 사용할 수 있습니다. 표준 App Service 요금제에서 이 기능을 사용할 수 있도록 하려면 프리미엄 V3 App Service 요금제에서 앱을 만듭니다. 이러한 플랜은 최신 배율 단위에서만 지원됩니다. 그 후에 원하는 경우 스케일 다운할 수 있습니다.
* 통합 서브넷은 App Service 요금제 하나에서만 사용할 수 있습니다.
* 이 기능은 App Service Environment에 있는 격리 요금제 앱에서 사용할 수 없습니다.
* 이 기능에는 Azure Resource Manager VNet에서 /28 이상의 사용하지 않는 서브넷이 필요합니다.
* 앱과 VNet은 동일한 지역에 있어야 합니다.
* 통합된 앱이 있는 VNet은 삭제할 수 없습니다. VNet을 삭제하기 전에 통합을 제거하세요.
* 지역 VNet 통합은 App Service 요금제당 하나만 가능합니다. 동일한 App Service 요금제의 여러 앱에서 동일한 VNet을 사용할 수 있습니다.
* 지역 VNet 통합을 사용하는 앱이 있는 경우에는 앱 또는 플랜의 구독을 변경할 수 없습니다.
* 앱은 구성 변경 없이 Azure DNS Private Zones의 주소를 확인할 수 없습니다.

## <a name="subnets"></a>서브넷

VNet 통합은 전용 서브넷에 따라 다릅니다. 서브넷을 프로비전할 때 Azure 서브넷은 처음부터 5개의 IP를 잃습니다. 각 플랜 인스턴스에 대해 통합 서브넷에서 하나의 주소가 사용됩니다. 앱을 4개의 인스턴스로 스케일링하면 4개의 주소가 사용됩니다. 

크기를 스케일 업 또는 스케일 다운하면, 짧은 시간 동안 필요한 주소 공간이 2배가 됩니다. 이렇게 되면 지정된 서브넷 크기에 대해 실제로 사용 가능한 지원되는 인스턴스에 영향을 줍니다. 다음 표는 CIDR 블록당 사용 가능한 최대 주소와 이것이 수평 스케일링에 미치는 영향을 보여줍니다.

| CIDR 블록 크기 | 사용 가능한 최대 주소 | 최대 수평 스케일링(인스턴스)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>어느 시점에서든 SKU 또는 크기를 스케일 업 또는 스케일 다운해야 한다고 가정합니다. 

할당 후에는 서브넷 크기를 변경할 수 없으므로 앱이 도달할 수 있는 모든 규모를 수용할 수 있을 정도로 충분히 큰 서브넷을 사용합니다. Functions Premium 계획의 서브넷 용량 문제를 방지하려면 Windows 256개의 주소가 있는 /24와 Linux의 경우 64개 주소가 있는 /26을 사용해야 합니다. 

다른 플랜의 앱이 다른 플랜의 앱을 통해 이미 연결된 VNet에 도달하도록 하려면 기존 VNet 통합에서 사용 중인 서브넷과 다른 서브넷을 선택합니다.

이 기능은 [사용자 지정 컨테이너](../app-service/configure-custom-container.md)를 포함하여 Windows 및 Linux 앱 모두에서 완벽하게 지원됩니다. 모든 동작은 Windows 앱과 Linux 앱 간에 동일하게 작동합니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

높은 수준의 보안을 제공하기 위해 서비스 엔드포인트를 사용하여 여러 Azure 서비스를 가상 네트워크로 제한할 수 있습니다. 지역 VNet 통합을 사용하면 함수 앱이 서비스 엔드포인트로 보호되는 Azure 서비스에 연결할 수 있습니다. 이 구성은 가상 네트워크 통합을 지원하는 모든 [요금제](functions-scale.md#networking-features)에서 지원됩니다. 서비스 엔드포인트 보안 서비스에 액세스하려면 다음을 수행해야 합니다.

1. 특정 서브넷에 연결하도록 함수 앱과 지역 VNet 통합을 구성합니다.
1. 대상 서비스로 이동하여 통합 서브넷에 대해 서비스 엔드포인트를 구성합니다.

자세한 내용은 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.

### <a name="network-security-groups"></a>네트워크 보안 그룹

네트워크 보안 그룹을 사용하여 VNet의 리소스에 대한 인바운드 및 아웃바운드 트래픽을 차단할 수 있습니다. 지역 VNet 통합을 사용하는 앱은 [네트워크 보안 그룹을][VNETnsg] 사용하여 VNet 또는 인터넷의 리소스에 대한 아웃바운드 트래픽을 차단할 수 있습니다. 공용 주소에 대한 트래픽을 차단하려면 모두 라우팅을 사용하도록 설정된 VNet 통합이 있어야 합니다. NSG의 인바운드 규칙은 앱에 적용되지 않습니다. VNet 통합은 앱의 아웃바운드 트래픽에만 영향을 미치기 때문입니다.

앱에 대한 인바운드 트래픽을 제어하려면 액세스 제한 기능을 사용합니다. 통합 서브넷에 적용된 NSG는 통합 서브넷에 적용된 경로에 관계없이 적용됩니다. 함수 앱이 Route All이 사용하도록 설정된 VNet이고 통합 서브넷의 공용 주소 트래픽에 영향을 주는 경로가 없는 경우 모든 아웃바운드 트래픽은 여전히 통합 서브넷에 할당된 NSG의 적용을 받습니다. 모두 라우팅을 사용하도록 설정하지 않으면 NSG는 RFC1918 트래픽에만 적용됩니다.

### <a name="routes"></a>경로

경로 테이블을 사용하여 앱의 아웃바운드 트래픽을 원하는 위치로 라우팅할 수 있습니다. 기본적으로 경로 테이블은 RFC1918 대상 트래픽에만 영향을 줍니다. 모두 라우팅을 사용하도록 설정하면 모든 아웃바운드 호출이 영향을 받습니다. [모두 라우팅을](../app-service/overview-vnet-integration.md#application-routing) 사용하지 않도록 설정하면 프라이빗 트래픽(RFC1918)만 경로 테이블의 영향을 받습니다. 통합 서브넷에 설정된 경로는 인바운드 앱 요청에 대한 응답에 영향을 주지 않습니다. 일반적인 대상에는 방화벽 디바이스나 게이트웨이가 포함될 수 있습니다.

모든 아웃바운드 트래픽을 온-프레미스로 라우팅하려면 경로 테이블을 사용하여 모든 아웃바운드 트래픽을 ExpressRoute 게이트웨이로 보낼 수 있습니다. 게이트웨이로 트래픽을 라우팅하는 경우에는 응답을 다시 보내도록 외부 네트워크에서 경로를 설정해야 합니다.

BGP(Border Gateway Protocol) 경로도 앱 트래픽에 영향을 줍니다. ExpressRoute 게이트웨이와 같은 항목의 BGP 경로가 있는 경우 앱 아웃바운드 트래픽이 영향을 받습니다. 기본적으로 BGP 경로는 RFC1918 대상 트래픽에만 영향을 줍니다. 함수 앱이 Route All과 통합된 VNet인 경우 모든 아웃바운드 트래픽은 BGP 경로의 영향을 받을 수 있습니다.

### <a name="azure-dns-private-zones"></a>Azure DNS 프라이빗 영역 

앱이 VNet과 통합되면 VNet이 구성된 것과 동일한 DNS 서버를 사용합니다. 기본적으로 앱은 Azure DNS 프라이빗 영역에서 작동하지 않습니다. Azure DNS 프라이빗 영역을 사용하려면 다음 앱 설정을 추가해야 합니다.

- 값이 `1`인 `WEBSITE_VNET_ROUTE_ALL`

이 설정은 앱의 모든 아웃바운드 호출을 VNet으로 보내고 앱이 Azure DNS 프라이빗 영역에 액세스할 수 있도록 합니다. 이러한 설정을 통해 앱은 작업자 수준에서 DNS 프라이빗 영역을 쿼리하여 Azure DNS를 사용할 수 있습니다.  

### <a name="private-endpoints"></a>프라이빗 엔드포인트

[프라이빗 엔드포인트][privateendpoints]를 호출하려면 DNS 조회가 프라이빗 엔드포인트로 확인되는지 확인해야 합니다. 이러한 동작은 다음 방법 중 하나로 적용할 수 있습니다. 

* Azure DNS 프라이빗 영역과 통합합니다. VNet에 사용자 지정 DNS 서버가 없으면 자동으로 수행됩니다.
* 앱에서 사용하는 DNS 서버에서 프라이빗 엔드포인트를 관리합니다. 이렇게 하려면 프라이빗 엔드포인트 주소를 알고 있어야 하며, A 레코드를 사용하여 해당 주소에 도달하려는 엔드포인트를 가리켜야 합니다.
* Azure DNS 프라이빗 영역으로 전달하도록 자체 DNS 서버를 [구성합니다.](#azure-dns-private-zones)

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>가상 네트워크에 대한 스토리지 계정 제한 

함수 앱을 만들 때 Blob, 큐 및 Table Storage을 지원하는 범용 Azure Storage 계정을 만들거나 연결해야 합니다. 이 스토리지 계정은 서비스 엔드포인트 또는 프라이빗 엔드포인트로 보호되는 계정으로 바꿀 수 있습니다. 

이 기능은 전용(App Service) 플랜으로 모든 Windows 가상 네트워크 지원 SKU에서 지원되며 프리미엄 플랜에서도 지원됩니다. Linux 가상 네트워크 지원 SKU에 대한 프라이빗 DNS에서도 지원됩니다. 소비 플랜 및 Linux 플랜의 사용자 지정 DNS는 지원되지 않습니다. 개인 네트워크로 제한된 스토리지 계정을 사용하여 함수를 설정하는 방법을 알아보려면 [가상 네트워크에 대한 스토리지 계정 제한](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network)을 참조하세요.

## <a name="use-key-vault-references"></a>Key Vault 참조 사용

Azure Key Vault 참조를 사용하여 코드 변경 없이도 Azure Functions 애플리케이션에서 Azure Key Vault의 비밀을 사용할 수 있습니다. Azure Key Vault는 액세스 정책 및 감사 기록에 대한 전체 제어와 함께 중앙 집중식 비밀 관리를 제공하는 서비스입니다.

앱에 대해 가상 네트워크 통합이 구성된 경우 [Key Vault 참조를](../app-service/app-service-key-vault-references.md) 사용하여 네트워크 제한 자격 증명 모음에서 비밀을 검색할 수 있습니다.

## <a name="virtual-network-triggers-non-http"></a>가상 네트워크 트리거(비 HTTP)

현재 다음 두 가지 방법 중 하나로 가상 네트워크 내에서 비 HTTP 트리거 함수를 사용할 수 있습니다.

+ 프리미엄 계획에서 함수 앱을 실행하고 가상 네트워크 트리거 지원을 사용 설정합니다.
+ App Service 계획 또는 App Service Environment에서 함수 앱을 실행합니다.

### <a name="premium-plan-with-virtual-network-triggers"></a>가상 네트워크 트리거를 사용하는 프리미엄 계획

프리미엄 계획을 실행하는 경우 비 HTTP 트리거 함수를 가상 네트워크 내에서 실행되는 서비스에 연결할 수 있습니다. 이렇게 하려면 함수 앱에 대한 가상 네트워크 트리거 지원을 사용하도록 설정해야 합니다. **런타임 크기 조정 모니터링** 설정은 **구성** > **함수 런타임 설정** 의 [Azure Portal](https://portal.azure.com)에서 확인할 수 있습니다.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

다음 Azure CLI 명령을 사용하여 가상 네트워크 트리거를 사용하도록 설정할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> 가상 네트워크 트리거를 사용하도록 설정하면 애플리케이션 성능에 영향을 줄 수 있습니다. App Service 요금제 인스턴스는 크기를 조정하는 시기를 결정하기 위해 트리거를 모니터링해야 하기 때문입니다. 이러한 영향은 매우 작을 가능성이 높습니다.

가상 네트워크 트리거는 Functions 런타임의 버전 2.x 이상에서 지원됩니다. 다음과 같은 비 HTTP 트리거 형식이 지원됩니다.

| 내선 번호 | 최소 버전 |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 이상 |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 이상|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 이상|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 이상|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 이상|

> [!IMPORTANT]
> 가상 네트워크 트리거 지원을 사용하도록 설정하면 이전 표에 표시된 트리거 유형만 애플리케이션을 사용하여 동적으로 확장됩니다. 표에 없는 트리거는 계속 사용할 수 있지만 예열된 인스턴스 수 이상으로 크기가 조정되지 않습니다. 트리거의 전체 목록은 [트리거 및 바인딩](./functions-triggers-bindings.md#supported-bindings)을 참조하세요.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>가상 네트워크 트리거를 사용하는 App Service 계획 및 App Service Environment

함수 앱이 App Service 계획 또는 App Service Environment에서 실행되는 경우 비 HTTP 트리거 함수를 사용할 수 있습니다. 함수가 올바르게 트리거되려면 트리거 연결에 정의된 리소스에 대한 액세스 권한이 있는 가상 네트워크에 연결되어야 합니다.

예를 들어 가상 네트워크의 트래픽만 허용하도록 Azure Cosmos DB를 구성하고자 한다고 가정하겠습니다. 이 경우 해당 가상 네트워크에 가상 네트워크 통합을 제공하는 App Service 계획에 함수 앱을 배포해야 합니다. 통합을 사용하면 해당 Azure Cosmos DB 리소스에서 함수를 트리거할 수 있습니다.

## <a name="hybrid-connections"></a>하이브리드 연결

[하이브리드 연결](../azure-relay/relay-hybrid-connections-protocol.md)은 다른 네트워크의 애플리케이션에 액세스하는 데 사용할 수 있는 Azure Relay의 기능입니다. 이를 통해 앱에서 애플리케이션 엔드포인트에 액세스할 수 있습니다. 이를 사용하여 애플리케이션 액세스할 수 없습니다. 하이브리드 연결은 사용 계획을 제외한 Windows에서 실행되는 함수에서 사용할 수 있습니다.

Azure Functions에서 사용되는 것처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합에 상호 연결됩니다. 즉, TCP 수신 대기 포트에 액세스하는 한, 하이브리드 연결 엔드포인트는 모든 운영 체제 및 모든 애플리케이션에 있을 수 있습니다. 하이브리드 연결 기능은 애플리케이션 프로토콜이 무엇인지 또는 사용자가 무엇에 액세스하고 있는지 인식하거나 상관하지 않습니다. 네트워크 액세스만 제공합니다.

자세한 내용은 [하이브리드 연결 관련 App Service 설명서](../app-service/app-service-hybrid-connections.md)를 참조하세요. 동일한 구성 단계는 Azure Functions를 지원합니다.

>[!IMPORTANT]
> 하이브리드 연결은 Windows 플랜에서만 지원됩니다. Linux는 지원되지 않습니다.

## <a name="outbound-ip-restrictions"></a>아웃바운드 IP 제한

아웃바운드 IP 제한은 프리미엄 계획, App Service 계획 또는 App Service Environment에서 사용할 수 있습니다. App Service Environment가 배포되는 가상 네트워크에 대한 아웃바운드 제한을 구성할 수 있습니다.

프리미엄 계획 또는 App Service 계획에서 함수 앱과 가상 네트워크를 통합하는 경우 앱에서 기본적으로 인터넷에 아웃바운드 호출을 수행할 수 있습니다. 사용 하도록 설정 된 경로를 사용 하는 VNet과 함수 앱을 통합 하 여 모든 아웃 바운드 트래픽이 가상 네트워크로 전송 되도록 합니다 .이 경우 네트워크 보안 그룹 규칙을 사용 하 여 트래픽을 제한할 수 있습니다.

가상 네트워크를 사용하여 아웃바운드 IP를 제어하는 방법을 알아보려면 [자습서: Azure 가상 네트워크 NAT 게이트웨이를 사용하여 Azure Functions 아웃바운드 IP 제어](functions-how-to-use-nat-gateway.md)를 참조하세요. 

## <a name="automation"></a>Automation
다음 API를 사용하면 프로그래매틱 방식으로 지역 가상 네트워크 통합을 관리할 수 있습니다.

+ **Azure CLI**: [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) 명령을 사용하여 지역 가상 네트워크 통합을 추가, 나열 또는 제거합니다.  
+ **ARM 템플릿**: 지역 가상 네트워크 통합은 Azure Resource Manager 템플릿을 사용하여 사용하도록 설정할 수 있습니다. 전체 예제를 보려면 [이 함수 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

네트워킹 및 Azure Functions에 대한 자세한 내용은 다음을 확인하세요.

* [가상 네트워크 통합 시작하기에 관한 자습서 따라하기](./functions-create-vnet.md)
* [Functions 네트워킹 FAQ 참조하기](./functions-networking-faq.yml)
* [App Service/Functions와 가상 네트워크 통합에 대해 자세히 알아보기](../app-service/overview-vnet-integration.md)
* [Azure의 가상 네트워크에 대해 자세히 알아보기](../virtual-network/virtual-networks-overview.md)
* [App Service Environment로 제어 및 네트워킹 기능 활성화](../app-service/environment/intro.md)
* [하이브리드 연결을 사용하여 방화벽 변경 없이 개별 온-프레미스 리소스에 연결](../app-service/app-service-hybrid-connections.md)


<!--Links-->
[VNETnsg]: ../virtual-network/network-security-groups-overview.md
[privateendpoints]: ../app-service/networking/private-endpoint.md