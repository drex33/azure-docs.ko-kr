---
title: Azure 가상 네트워크와의 통합 사용
description: 이 방법 문서에서는 Azure App Service 웹앱에서 가상 네트워크 통합을 사용하도록 설정하는 방법을 안내합니다.
keywords: vnet 통합
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 73a148f205a36091f1df3b35a2d89ac51bf352ef
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894282"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Azure App Service 가상 네트워크 통합 사용

[Azure App Service 앱에서](./overview.md)Azure VNet(가상 네트워크)과 통합하면 가상 네트워크 내에서 앱의 프라이빗 리소스에 연결할 수 있습니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* **지역 가상 네트워크 통합:** 동일한 지역의 Azure 가상 네트워크에 커넥트. 통합하는 가상 네트워크에 전용 서브넷이 있어야 합니다.
* **게이트웨이 필수 가상 네트워크 통합:** 다른 지역의 가상 네트워크 또는 동일한 지역의 클래식 가상 네트워크에 직접 연결하는 경우 게이트웨이에 필요한 가상 네트워크 통합을 사용해야 합니다.

이 문서에서는 지역 가상 네트워크 통합을 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

VNet 통합 기능에는 다음이 필요합니다.

- 가상 네트워크 통합 [을 지원하는](./overview-vnet-integration.md)App Service 가격 책정 계층입니다.
- 빈 서브넷이 있는 동일한 지역의 가상 네트워크입니다.

서브넷은 Microsoft.Web/serverFarms에 위임되어야 합니다. 통합 전에 위임이 수행되지 않으면 프로비전 프로세스에서 이 위임을 구성합니다. 서브넷에는 IPv4 `/28` 블록(16개 주소)이 할당되어야 합니다. 최대 수평적 크기 조정을 허용하려면 최소 64개의 주소(IPv4 블록)가 있는 것이 `/26` 좋습니다.

## <a name="configure-in-the-azure-portal"></a>Azure Portal에서 구성

1. App Service 포털에서 **네트워킹으로** 이동합니다. **아웃바운드 트래픽** 아래에서 **VNet 통합을** 선택합니다.

1. **VNet 추가** 를 선택합니다.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="VNet 통합 선택을 보여 주는 스크린샷.":::

1. 드롭다운 목록에는 동일한 지역의 구독에 있는 모든 가상 네트워크가 포함됩니다. 빈 기존 서브넷을 선택하거나 새 서브넷을 만듭니다.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="가상 네트워크 선택을 보여 주는 스크린샷.":::

통합하는 동안에 앱이 다시 시작됩니다. 통합이 완료되면 통합된 가상 네트워크에 대한 세부 정보가 표시됩니다.

## <a name="configure-with-the-azure-cli"></a>Azure CLI 구성

Azure CLI 사용하여 가상 네트워크 통합을 구성할 수도 있습니다.

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> 이 명령은 서브넷이 Microsoft.Web/serverFarms에 위임되었는지 확인하고 구성되지 않은 경우 필요한 위임을 적용합니다. 서브넷이 구성되었고 서브넷을 확인할 권한이 없거나 가상 네트워크가 다른 구독에 있는 경우 *--skip-delegation-check* 매개 변수를 사용하여 유효성 검사를 무시할 수 있습니다.

## <a name="configure-with-azure-powershell"></a>Azure PowerShell 구성

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'
$vNetName = '<vnet-name>'
$integrationSubnetName = '<subnet-name>'
$subscriptionId = '<subscription-guid>'

# Configure VNet Integration
$subnetResourceId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Network/virtualNetworks/$vNetName/subnets/$integrationSubnetName"
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
$webApp.Properties.virtualNetworkSubnetId = $subnetResourceId
$webApp | Set-AzResource -Force
```

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 통합 라우팅 구성](./configure-vnet-integration-routing.md)
- [일반 네트워킹 개요](./networking-features.md)