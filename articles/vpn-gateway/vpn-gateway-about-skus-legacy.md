---
title: 레거시 Azure 가상 네트워크 VPN 게이트웨이 SKU
description: 이전 버전의 가상 네트워크 게이트웨이 SKU인 Basic, Standard 및 HighPerformance를 사용하는 방법입니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bb48d72c892723e7b6c3ca2009ea874f788bb5ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535797"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>가상 네트워크 게이트웨이 SKU(레거시 SKU) 사용

이 문서에는 레거시(이전) 가상 네트워크 게이트웨이 SKU에 대한 정보가 포함되어 있습니다. 레거시 SKU는 이미 작성된 VPN Gateway의 두 배포 모델에서 모두 계속 작동합니다. 클래식 VPN Gateway는 레거시 SKU를 계속 사용합니다(기존 게이트웨이와 새 게이트웨이 둘 다를 위해). 새 Resource Manager VPN Gateway를 만들 때는 새 게이트웨이 SKU를 사용합니다. 새 SKU에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

## <a name="gateway-skus"></a><a name="gwsku"></a>게이트웨이 SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

[ExpressRoute 가격 책정 페이지](https://azure.microsoft.com/pricing/details/expressroute)에 있는 **Virtual Network Gateway** 섹션에서 레거시 게이트웨이 가격 책정을 볼 수 있습니다.

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>SKU 기준으로 예상된 총 처리량

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>SKU와 VPN 형식별 지원되는 구성

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>게이트웨이 크기 조정

동일한 SKU 제품군 내에서 게이트웨이의 크기를 게이트웨이 SKU로 조정할 수 있습니다. 예를 들어 Standard SKU는 HighPerformance SKU로 크기를 조정할 수 있습니다. 하지만 이전 SKU와 새 SKU 제품군 간에 VPN Gateway의 크기를 조정할 수는 없습니다. 예를 들어 Standard SKU에서 VpnGw2 SKU로, 또는 Basic SKU에서 VpnGw1로 크기를 조정할 수는 없습니다.

### <a name="resource-manager"></a>리소스 관리자

PowerShell을 사용하여 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)의 게이트웨이 크기를 조정하려면 다음 명령을 사용합니다.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

또한 Azure Portal에서 게이트웨이 크기를 조정할 수도 있습니다.

### <a name="classic"></a><a name="classicresize"></a>클래식

클래식 배포 모델에 대한 게이트웨이 크기를 조정하려면 서비스 관리 PowerShell cmdlet을 사용해야 합니다. 다음 명령을 사용합니다.

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>새 게이트웨이 SKU로 변경

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>다음 단계

새 게이트웨이 SKU에 대한 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpngateways.md#gwsku)를 참조하세요.

구성 설정에 대한 자세한 내용은 [VPN Gateway 구성 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요.
