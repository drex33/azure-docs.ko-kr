---
title: Azure 가상 네트워크와의 통합을 사용 하도록 설정 합니다.
description: 이 방법 문서에서는 App Service 웹 앱에서 가상 네트워크 통합을 사용 하도록 설정 하는 과정을 안내 합니다.
keywords: vnet 통합
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 549c92b932e0d589aec2490512fc18713fa71546
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273765"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Azure App Service에서 가상 네트워크 통합 사용

[App Service 앱](./overview.md)에서 Azure VNet (virtual network)과 통합 하 여 가상 네트워크 내의 앱에서 개인 리소스에 연결할 수 있습니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* 지역 VNet 통합: 동일한 지역에 있는 Azure 가상 네트워크에 커넥트 합니다. 통합 하는 VNet에 전용 서브넷이 있어야 합니다.
* 게이트웨이-필수 VNet 통합: 다른 지역의 VNet 또는 동일한 지역의 클래식 가상 네트워크에 직접 연결 하는 경우 게이트웨이-필수 VNet 통합을 사용 해야 합니다.

이 방법 문서에서는 지역 VNet 통합을 설정 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

VNet 통합에는 다음이 필요 합니다.
- [VNet 통합을 지 원하는](./overview-vnet-integration.md)App Service 가격 책정 계층.
- 서브넷이 비어 있는 동일한 지역에 있는 가상 네트워크입니다.

서브넷은 Microsoft 웹/serverFarms에 위임 되어야 합니다. 통합 전에 위임이 수행 되지 않는 경우 프로 비전 프로세스에서이 위임을 구성 합니다. 서브넷에는 IPv4 `/28` 블록 (16 개 주소)을 할당 해야 합니다. 최대 가로 크기 조정을 허용 하려면 최소 64 주소 (IPv4 블록)를 사용 하는 것이 좋습니다 `/26` .

## <a name="configure-in-the-azure-portal"></a>Azure Portal에서 구성

1. App Service 포털에서 **네트워킹** 으로 이동합니다. **아웃 바운드 트래픽** 에서 **VNet 통합** 을 선택 합니다.

1. **VNet 추가** 를 선택합니다.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="VNET 통합 선택":::

1. 드롭다운 목록에는 동일한 지역에 있는 구독의 모든 가상 네트워크가 포함 되어 있습니다.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="VNet을 선택":::

    * 비어 있는 기존 서브넷을 선택 하거나 새 서브넷을 만드십시오.

통합하는 동안에 앱이 다시 시작됩니다. 통합이 완료되면 통합된 VNet에 대한 세부 정보가 표시됩니다.

## <a name="configure-with-azure-cli"></a>을 사용 하 여 구성 Azure CLI

Azure CLI를 사용 하 여 VNet 통합을 구성할 수도 있습니다.

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> 이 명령은 서브넷이 Microsoft 웹/serverFarms에 위임 되었는지 확인 하 고 구성 되지 않은 경우 필요한 위임을 적용 합니다. 이미 구성 되어 있고이를 확인할 수 있는 권한이 없거나 가상 네트워크가 다른 구독에 있는 경우 매개 변수를 사용 하 여 유효성 검사를 건너뛸 수 있습니다 `--skip-delegation-check` .

## <a name="configure-with-azure-powershell"></a>을 사용 하 여 구성 Azure PowerShell

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

- [VNet 통합 라우팅 구성](./configure-vnet-integration-routing.md)
- [일반 네트워킹 개요](./networking-features.md)