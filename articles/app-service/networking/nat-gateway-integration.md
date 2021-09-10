---
title: NAT 게이트웨이 통합 - Azure App Service | Microsoft Docs
description: NAT 게이트웨이가 Azure App Service와 통합되는 방법을 설명합니다.
services: app-service
author: madsd
ms.assetid: 0a84734e-b5c1-4264-8d1f-77e781b28426
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 08/04/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 851e1a04b7fa5ac14848c9d679118a8ebf4ada77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536416"
---
# <a name="virtual-network-nat-gateway-integration"></a>Virtual Network NAT 게이트웨이 통합

NAT 게이트웨이는 하나 이상의 서브넷과 연결될 수 있는 복원력이 뛰어난 완전 관리형 서비스로, 모든 아웃바운드 인터넷 연결 트래픽이 게이트웨이를 통해 라우팅되도록 합니다. App Service에서 NAT 게이트웨이를 사용할 수 있는 두 가지 중요한 시나리오가 있습니다. 

NAT 게이트웨이는 아웃바운드 인터넷 연결 트래픽에 대한 예측 가능한 고정 공용 IP를 제공합니다. 또한 동일한 공용 주소/포트 조합에 대한 동시 연결 수가 많은 시나리오에서 사용 가능한 [SNAT 포트](../troubleshoot-intermittent-outbound-connection-errors.md)를 크게 늘립니다.

자세한 내용과 가격 책정을 보려면 [NAT 게이트웨이 개요](../../virtual-network/nat-gateway/nat-overview.md)로 이동합니다.

:::image type="content" source="./media/nat-gateway-integration/nat-gateway-overview.png" alt-text="Azure Virtual Network에서 NAT 게이트웨이로 이동하는 인터넷 트래픽을 보여 주는 다이어그램":::

> [!Note] 
> 지역별 VNet 통합에 따라 App Service에서 NAT 게이트웨이를 사용할 수 있으므로 **Standard**, **Premium**, **PremiumV2** 또는 **PremiumV3** App Service 요금제가 필요합니다.

## <a name="configuring-nat-gateway-integration"></a>NAT 게이트웨이 통합 구성

App Service와 NAT 게이트웨이 통합을 구성하려면 다음 단계를 완료해야 합니다.

* [Azure 가상 네트워크와 앱 통합](../web-sites-integrate-with-vnet.md)에 설명된 대로 앱과 지역별 VNet 통합을 구성합니다.
* 인터넷 바인딩된 트래픽이 VNet 경로의 영향을 받도록 VNet 통합에 대해 [모두 라우팅](../web-sites-integrate-with-vnet.md#routes)이 사용하도록 설정되었는지 확인합니다.
* 공용 IP를 사용하여 NAT 게이트웨이를 프로비저닝하고 VNet 통합 서브넷과 연결합니다.

포털을 통해 NAT 게이트웨이를 설정합니다.

1. App Service 포털에서 **네트워킹** UI로 이동한 다음, 아웃바운드 트래픽 섹션에서 VNet 통합을 선택합니다. 앱이 서브넷과 통합되었으며 **모두 라우팅** 이 사용하도록 설정되었는지 확인합니다.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-route-all-enabled.png" alt-text="VNet 통합에 대해 모두 라우팅이 사용하도록 설정된 스크린샷":::
1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다. **새로 만들기** 창이 나타납니다.
1. “NAT 게이트웨이”를 검색하고 결과 목록에서 선택합니다.
1. **기본 사항** 정보를 입력하고 앱이 있는 지역을 선택합니다.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-basics.png" alt-text="NAT 게이트웨이 만들기의 기본 사항 탭 스크린샷":::
1. **아웃바운드 IP** 탭에서 새 공용 IP를 만들거나 기존 공용 IP를 선택합니다.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-outbound-ip.png" alt-text="NAT 게이트웨이 만들기의 아웃바운드 IP 탭 스크린샷":::
1. **서브넷** 탭에서 VNet 통합에 사용되는 서브넷을 선택합니다.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-subnet.png" alt-text="NAT 게이트웨이 만들기의 서브넷 탭 스크린샷":::
1. 필요한 경우 태그를 입력하고 NAT 게이트웨이 **만들기** 를 선택합니다. NAT 게이트웨이가 프로비저닝된 후 **리소스 그룹으로 이동** 을 클릭하고 새 NAT 게이트웨이를 선택합니다. 아웃바운드 IP 블레이드에서 앱이 아웃바운드 인터넷 연결 트래픽에 사용하는 공용 IP를 볼 수 있습니다.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-public-ip.png" alt-text="NAT 게이트웨이 포털의 아웃바운드 IP 블레이드 스크린샷"::: 

CLI를 사용하여 환경을 구성하려는 경우에 중요한 명령입니다. 필수 조건으로, VNet 통합이 구성된 웹앱을 만들어야 합니다.

VNet 통합에 대해 **모두 라우팅** 이 구성되었는지 확인합니다(‘참고’: `az version`은 2.27 이상이어야 함).

```azurecli-interactive
az webapp config set --resource-group [myResourceGroup] --name [myWebApp] --vnet-route-all-enabled
```

공용 IP와 NAT 게이트웨이를 만듭니다.

```azurecli-interactive
az network public-ip create --resource-group [myResourceGroup] --name myPublicIP --sku standard --allocation static

az network nat gateway create --resource-group [myResourceGroup] --name myNATgateway --public-ip-addresses myPublicIP --idle-timeout 10
```

VNet 통합 서브넷과 NAT 게이트웨이를 연결합니다.

```azurecli-interactive
az network vnet subnet update --resource-group [myResourceGroup] --vnet-name [myVnet] --name [myIntegrationSubnet] --nat-gateway myNATgateway
```

## <a name="scaling-nat-gateway"></a>NAT 게이트웨이 스케일링

동일한 Virtual Network의 여러 서브넷에서 동일한 NAT 게이트웨이를 사용하면 여러 앱과 App Service 요금제에서 NAT 게이트웨이를 사용할 수 있습니다.

NAT 게이트웨이는 공용 IP 주소와 공용 IP 접두사를 둘 다 지원합니다. NAT 게이트웨이는 개별 IP 주소와 접두사에서 최대 16개의 IP 주소를 지원할 수 있습니다. 각 IP 주소는 최대 1M의 사용 가능한 포트를 허용하는 64,000개의 포트(SNAT 포트)를 할당합니다. 자세한 내용은 NAT 게이트웨이의 [스케일링 섹션](../../virtual-network/nat-gateway/nat-gateway-resource.md#scaling)을 참조하세요.

## <a name="next-steps"></a>다음 단계
NAT 게이트웨이에 대한 자세한 내용은 [NAT 게이트웨이 설명서](../../virtual-network/nat-gateway/nat-overview.md)를 참조하세요.

VNet 통합에 대한 자세한 내용은 [VNet 통합 설명서](../web-sites-integrate-with-vnet.md)를 참조하세요.