---
title: 애플리케이션 라우팅과 가상 네트워크 통합을 구성합니다.
description: 이 방법 문서에서는 지역 VNet 통합에서 앱 라우팅을 구성하는 방법을 안내합니다.
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: d286b8c0bb82c1501cdc77e8097ba6b7d5b2008f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273711"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Azure App Service 가상 네트워크 통합 라우팅 관리

애플리케이션 라우팅을 구성할 때 모든 트래픽 또는 프라이빗 [트래픽(RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) 트래픽이라고도 함)만 Azure VNet(가상 네트워크)으로 라우팅할 수 있습니다. 이 방법 문서에서는 애플리케이션 라우팅을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

앱은 지역 VNet 통합 기능을 사용하여 이미 통합되어 있습니다.

## <a name="configure-in-the-azure-portal"></a>Azure Portal에서 구성

다음 단계를 사용하여 포털을 통해 앱에서 모두 라우팅을 사용하지 않도록 설정할 수 있습니다. 

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="모두 라우팅 사용":::

1. 앱 포털에서 **네트워킹**  >  **VNet 통합** UI로 이동합니다.
1. **모두 라우팅** 을 사용 안 함으로 설정합니다.
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="모두 라우팅 사용 안 함":::

1. **예** 를 선택하여 확인합니다.

## <a name="configure-with-azure-cli"></a>Azure CLI 구성

Azure CLI 사용하여 모두 라우팅을 구성할 수도 `az version` 있습니다(필요한 최소값은 2.27.0).

```azurecli-interactive
az webapp config set --resource-group <group-name> --name <app-name> --vnet-route-all-enabled [true|false]
```

## <a name="configure-with-azure-powershell"></a>Azure PowerShell 구성

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'

# Configure VNet Integration
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
Set-AzResource -ResourceId ($webApp.Id + "/config/web") -Properties @{ vnetRouteAllEnabled = $true } -Force
```

## <a name="next-steps"></a>다음 단계

- [VNet 통합 사용](./configure-vnet-integration-enable.md)
- [일반 네트워킹 개요](./networking-features.md)