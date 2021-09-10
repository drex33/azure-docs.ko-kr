---
title: '자습서: ExpressRoute 회로에 VNet 연결 - Azure CLI'
description: 이 자습서에서는 Resource Manager 배포 모델 및 Azure CLI를 사용하여 VNet(가상 네트워크)을 ExpressRoute 회로에 연결하는 방법을 보여 줍니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: duau
ms.openlocfilehash: abca04eb60538921191b9c632ef6ef2849f959c5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424909"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>자습서: CLI를 사용하여 가상 네트워크를 ExpressRoute 회로에 연결

이 자습서에서는 Azure CLI를 사용하여 VNet(가상 네트워크)을 Azure ExpressRoute 회로에 연결하는 방법을 보여 줍니다. Azure CLI를 사용하여 연결하려면 Resource Manager 배포 모델을 사용하여 가상 네트워크를 만들어야 합니다. 해당 항목은 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다. VNet을 ExpressRoute 회로에 연결하는 다른 방법을 사용하려는 경우 다음 목록에서 문서를 선택할 수 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-linkvnet-classic.md)
> 

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 동일한 구독에 있는 가상 네트워크를 회로에 연결
> - 다른 구독에 있는 가상 네트워크를 회로에 연결
> - 가상 네트워크 연결 수정
> - ExpressRoute FastPath 구성

## <a name="prerequisites"></a>필수 구성 요소

* 최신 버전의 CLI(명령줄 인터페이스)가 필요합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.
* 활성화된 ExpressRoute 회로가 있어야 합니다. 
  * 지침을 수행하여 [ExpressRoute 회로를 만들고](howto-circuit-cli.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다. 
  * 회로에 구성된 Azure 프라이빗 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](howto-routing-cli.md) 을 참조하세요. 
  * Azure 프라이빗 피어이링 구성되어 있는지 확인합니다. 엔드투엔드 연결을 사용하도록 설정하려면 네트워크와 Microsoft 간의 BGP 피어링을 설정해야 합니다.
  * 가상 네트워크 및 가상 네트워크 게이트웨이를 만들어서 완전히 프로비전해야 합니다. 지침에 따라 [ExpressRoute에 대한 가상 네트워크 게이트웨이를 구성합니다](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md). `--gateway-type ExpressRoute`를 사용해야 합니다.
* 최대 10개의 가상 네트워크를 표준 ExpressRoute 회로에 연결할 수 있습니다. 표준 ExpressRoute 회로를 사용하는 경우 모든 가상 네트워크는 동일한 지역에 있어야 합니다. 
* 단일 VNet을 최대 16개의 ExpressRoute 회로에 연결할 수 있습니다. 다음 프로세스를 사용하여 연결하려는 각 ExpressRoute 회로에 대한 새 연결 개체를 만듭니다. ExpressRoute 회로는 동일한 구독, 서로 다른 구독 또는 두 가지가 혼합된 상태로 존재할 수 있습니다.
* ExpressRoute 프리미엄 추가 기능을 사용하도록 설정하면 ExpressRoute 회로의 지정학적 지역 외부에서 가상 네트워크를 연결할 수 있습니다. 또한 프리미엄 추가 항목을 사용하면 선택한 대역폭에 따라 10개가 넘는 가상 네트워크를 ExpressRoute 회로에 연결할 수도 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md) 에서 확인하세요.

* ExpressRoute 회로에서 대상 ExpressRoute 가상 네트워크 게이트웨이로 연결을 만들려면 로컬 또는 피어링된 가상 네트워크에서 보급된 주소 공간의 수가 **200** 개 이하여야 합니다. 연결이 성공적으로 만들어지면 로컬 또는 피어링된 가상 네트워크에 최대 1,000개의 추가 주소 공간을 추가할 수 있습니다.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>동일한 구독에 있는 가상 네트워크를 회로에 연결

다음 예제를 사용하여 ExpressRoute 회로에 가상 네트워크 게이트웨이를 연결할 수 있습니다. 명령을 실행하기 전에 가상 네트워크 게이트웨이가 연결을 위해 생성되고 준비되었는지 확인합니다.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>다른 구독에 있는 가상 네트워크를 회로에 연결

여러 구독에서 ExpressRoute 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독에서 ExpressRoute 회로에 대한 작업을 공유하는 방법의 간단한 계통도가 나와 있습니다.

큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서에서 자체 구독을 사용하여 서비스를 배포하지만, 단일 ExpressRoute 회로를 공유하여 온-프레미스 네트워크에 다시 연결할 수 있습니다. 단일 부서(이 예제에서는 IT)가 ExpressRoute 회로를 소유할 수 있습니다. 조직 내의 다른 구독은 ExpressRoute 회로를 사용할 수 있습니다.

> [!NOTE]
> 전용 회로에 대한 연결 및 대역폭 요금은 ExpressRoute 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.
> 
> 

![구독 간 연결](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>관리 - 회로 소유자 및 회로 사용자

'회로 소유자'는 ExpressRoute 회로 리소스의 인증된 고급 사용자입니다. 회로 소유자는 '회로 사용자'가 사용할 수 있는 권한 부여를 만들 수 있습니다. 회로 사용자는 ExpressRoute 회로와 동일한 구독 내에 있지 않은 가상 네트워크 게이트웨이의 소유자입니다. 회로 사용자는 가상 네트워크당 하나의 권한 부여를 사용할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크 연결이 삭제됩니다.

### <a name="circuit-owner-operations"></a>회로 소유자 작업

**권한 부여를 만들려면**

회로 소유자는 가상 네트워크 게이트웨이를 ExpressRoute 회로에 연결하기 위해 회로 사용자가 사용할 권한 부여 키를 만드는 권한 부여를 만듭니다. 권한 부여는 하나의 연결에만 유효합니다.

다음 예제에서는 권한 부여를 만드는 방법을 보여줍니다.

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

응답에는 권한 부여 키와 상태가 포함됩니다.

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**권한 부여를 검토하려면**

회로 소유자는 다음 예제를 실행하여 특정 회로에 발급한 모든 권한 부여를 검토할 수 있습니다.

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**권한 부여를 추가하려면**

회로 소유자는 다음 예제를 사용하여 권한 부여를 추가할 수 있습니다.

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**권한 부여를 삭제하려면**

회로 소유자는 다음 예제를 실행하여 권한 부여를 취소/삭제할 수 있습니다.

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>회로 사용자 작업

회로 사용자는 회로 소유자의 피어 ID 및 권한 부여 키가 필요합니다. 권한 부여 키는 GUID입니다.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**연결 권한 부여를 사용하려면**

회로 사용자는 다음 예제를 실행하여 링크 권한 부여를 사용할 수 있습니다.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**연결 권한 부여를 해제하려면**

ExpressRoute 회로와 가상 네트워크의 연결을 삭제하여 권한 부여를 해제할 수 있습니다.

## <a name="modify-a-virtual-network-connection"></a>가상 네트워크 연결 수정
가상 네트워크 연결의 특정 속성을 업데이트할 수 있습니다. 

**연결 무게를 업데이트하려면**

가상 네트워크를 여러 ExpressRoute 회로에 연결할 수 있습니다. 둘 이상의 ExpressRoute 회로에서 동일한 접두사를 수신할 수도 있습니다. 이 접두사를 대상으로 하는 트래픽을 전송할 연결을 선택하기 위해 연결의 *RoutingWeight* 를 변경할 수 있습니다. 트래픽은 제일 높은 *RoutingWeight* 를 사용한 연결로 전송됩니다.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

*RoutingWeight* 의 범위는 0에서 32000입니다. 기본값은 0입니다.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath 구성 
가상 네트워크 게이트웨이가 Ultra Performance 또는 ErGw3AZ인 경우 [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md)를 사용하도록 설정할 수 있습니다. FastPath는 온-프레미스 네트워크와 가상 네트워크 간의 초당 패킷 수 및 초당 연결 수와 같은 데이터 경로 성능을 향상시킵니다. 

**새 연결에서 FastPath 구성**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**FastPath를 사용하도록 기존 연결 업데이트**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

> [!NOTE]
> [연결 모니터](how-to-configure-connection-monitor.md)를 사용하여 트래픽이 FastPath를 사용하여 목적지에 도달하고 있는지 확인할 수 있습니다.
>

## <a name="enroll-in-expressroute-fastpath-features-preview"></a>ExpressRoute FastPath 기능에 등록(미리 보기)

가상 네트워크 피어링에 대한 FastPath 지원은 이제 공개 미리 보기로 제공됩니다. 등록은 Azure PowerShell을 통해서만 가능합니다. 등록 방법에 대한 지침은 [FastPath 미리 보기 기능](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview)을 참조하세요.

> [!NOTE] 
> 대상 구독에서 FastPath에 대해 구성된 모든 연결은 이 미리 보기에 등록됩니다. 프로덕션 구독에서는 이 미리 보기를 사용하도록 설정하지 않는 것이 좋습니다.
> 이미 FastPath가 구성되어 있고 미리 보기 기능에 등록하려면 다음을 수행해야 합니다.
> 1. 위의 Azure PowerShell 명령을 사용하여 FastPath 미리 보기 기능에 등록합니다.
> 1. 대상 연결에서 FastPath를 사용하지 않도록 설정했다가 다시 사용합니다.

## <a name="clean-up-resources"></a>리소스 정리

ExpressRoute 연결이 더 이상 필요하지 않은 경우 게이트웨이가 있는 구독에서 `az network vpn-connection delete` 명령을 사용하여 게이트웨이와 회로 간의 연결을 제거합니다.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 네트워크를 동일한 구독 및 다른 구독의 회로에 연결하는 방법을 알아보았습니다. ExpressRoute 게이트웨이에 대한 자세한 내용은 다음을 참조하세요. 

> [!div class="nextstepaction"]
> [ExpressRoute 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md)
