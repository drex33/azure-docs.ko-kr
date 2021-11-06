---
title: 응용 프로그램 라우팅과의 가상 네트워크 통합을 구성 합니다.
description: 이 방법 문서에서는 지역 가상 네트워크 통합에서 앱 라우팅을 구성 하는 과정을 안내 합니다.
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: b62a8c4bcef5e5556820f6f4e816f01566d44f72
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892043"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Azure App Service 가상 네트워크 통합 라우팅 관리

응용 프로그램 라우팅을 구성 하는 경우 모든 트래픽 또는 개인 트래픽 ( [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) 트래픽)을 Azure VNet (가상 네트워크)로 라우팅할 수 있습니다. 이 문서에서는 응용 프로그램 라우팅을 구성 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

앱이 이미 지역 VNet 통합 기능을 사용 하 여 통합 되었습니다.

## <a name="configure-in-the-azure-portal"></a>Azure Portal에서 구성

포털을 통해 앱에서 **모두 경로** 를 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="모든 경로를 사용 하도록 설정 하는 것을 보여 주는 스크린샷":::

1.   >  앱 포털에서 네트워킹 **VNet 통합** 으로 이동 합니다.
1. **모든 경로** 를 **사용 안 함** 으로 설정 합니다.
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="모든 경로를 사용 하지 않도록 설정 하는 것을 보여 주는 스크린샷":::

1. **예** 를 선택하여 확인합니다.

## <a name="configure-with-the-azure-cli"></a>Azure CLI를 사용 하 여 구성

또한 Azure CLI를 사용 하 여 **모든 경로** 를 구성할 수 있습니다. 필요한 최소 az version은 2.27.0입니다.

```azurecli-interactive
az webapp config set --resource-group <group-name> --name <app-name> --vnet-route-all-enabled [true|false]
```

## <a name="configure-with-azure-powershell"></a>을 사용 하 여 구성 Azure PowerShell

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'

# Configure VNet Integration
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
Set-AzResource -ResourceId ($webApp.Id + "/config/web") -Properties @{ vnetRouteAllEnabled = $true } -Force
```

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 통합 사용](./configure-vnet-integration-enable.md)
- [일반 네트워킹 개요](./networking-features.md)